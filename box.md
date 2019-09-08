# Ubuntu18.04 server VagrantBox作成手順

|データ|サイズ|
|:--:|--:|
|ubuntu 18.04 iso| 848MB|
|仮想マシンボリューム|10GB(4GB？)|
|package|1GBぐらい|

## 素材の作成
### ubuntu 18.04 イメージをダウンロード

http://releases.ubuntu.com/18.04.3/

このページから以下のファイルをダウンロードする．
 - ubuntu-18.04.3-live-server-amd64.iso
 - SHA256SUMS
 - SHA256SUMS.gpg

ダウンロードを完了した後，以下のページでファイルを検証する．

[How to verify your Ubuntu download](https://tutorials.ubuntu.com/tutorial/tutorial-how-to-verify-ubuntu?_ga=2.25597054.271205924.1567565680-498833213.1567565680#0)


### 仮想マシンの設定

1. Virtualbox マネージャを起動し，新規をクリック，

    「仮想マシンの作成」
    
    |項目|パラメータ|
    |:--:|--:|
    |名前|ubuntu18.04-server-base|
    |タイプ|Linux|
    |バージョン|Ubuntu 64bit|
    |メモリーサイズ|512MB|
    |ハードディスク|仮想ハードディスクを作成する|

2. 「作成」をクリック

    「仮想ハードディスクの作成」
    
    |項目|パラメータ|
    |:--:|--:| 
    |ファイルサイズ|10GB|
    |ハードディスクのファイルタイプ|VDI (VirtualBox Disk Image)|
    |物理ハードディスクにあるストレージ|可変サイズ|


3. 「作成」をクリック．

4. 出来上がった仮想マシンの詳細設定を変更する．
    - 「システム」から「マザーボード」のページ．
        - EFIを有効化(一部 OS飲み)をチェック．
        - プロセッサー数はお好み(2コアぐらいがいいかな？)

    - 「ストレージ」
        1. 「コントローラSATA」をクリックし，右側の「Disk」マークをクリック．
        2. ディスクをクリックし，「追加」をクリック．
        3. ダウンロードしたubuntu18.04 image を選択．

5. 「OK」で閉じる．


### 仮想マシンへのOSインストール
1. 仮想マシンを起動する．
2. 「GNU GRUB version 2.02」  
    「Install Ubuntu Server」を選択しEnter．
3. 「Please choose your prefered language.」  
    「English」を選択し，Enter．
4. 「Keyboard configuration」  
    「Layout」，「Variant」ともに，「Japanese」をセット，「Done」を選択Enter．
5. 「Network connections」  
    デフォルトの状態で「Done」を選択Enter．
6. 「Configure poxy」  
    デフォルト(空)の状態で選択Enter．
7. 「Configure Ubuntu archive mirror」  
    デフォルト(jp.archive.ubuntu.com)の状態で，「Done」選択Enter．
8. 「Filesystem setup」
    1. 「Use an Entire Disk」を選択．
    2. 「Chose the disk to install to:」はデフォルトのままEnter．
    3. 「File system summary」を確認した上で，「Done」を選択Enter．

9. 「Confirm destructive action」  
    ディスクの中身が消えることを了承の上で，「Continue」を選択Enter．

10. 「Profile setup」

    |項目|パラメータ|
    |:--:|--:|
    |Your name|vagrant|
    |Your server's name|vagrantSrv|
    |Pick a username|vagrant|
    |Choose a password|vagrant|
    |Confirm your password|vagrant|

    「Done」を選択Enter．

11. SSH Setup  
    「Install OpenSSH server」をスペースキーでチェックし，「Done」を選択Enter．

12. Featured Server Snaps  
    何も選択せず，「Done」を選択Enter．  
    チェックしてしまったらスペースキーでチェックを外す．  
    「Done」を選択Enter．

13. 「Installing system」  
    時間がかかる．  
    Security Updateもついでにインストール  
    ログ画面の行末が「Copying logs to installed system」となれば，「Reboot」を選択Enter．

### OSの設定

#### ログイン
1. ログイン画面が表示されてもしばらく放置  
    「Reached target Cloud-init target」となれば，Enterキーでログイン画面を再描画．  
    pcm-server login: vagrat  
    Password: 表示されないが「vagrant」

#### ssh

vagrantの共有の公開鍵をauthorized_keysに保存する．  
```
    mkdir /home/vagrant/.ssh
    chmod 700 /home/vagrant/.ssh
    cd /home/vagrant/.ssh
    curl -L -o authorized_keys https://raw.githubusercontent.com/hashicorp/vagrant/master/keys/vagrant.pub
    chmod 600 authorized_keys
```

#### vagrantユーザにsudoのパスワード入力省略設定する．

1. visudoで変更．
    ``` 
    sudo visudo 
    [sudo] password for vagrant: vagrant

    #%sudo ALL ...の直下
    vagrant ALL=(ALL:ALL) NOPASSWD:ALL
    ```
2. Ctrl+O で上書き保存
3. エラーメッセージが表示されていないことを確認する．

#### VirtualBox Guest Additionsをインストール
1. VirtualBox上で，GuestAdditionイメージを挿入しておく．
2. chromをマウントできるようにする．
    ```
    apt install -y xserver-xorg xserver-xorg-core gcc make perl
    sudo mkdir -p /mnt/cdrom
    sudo mount /dev/cdrom /mnt/cdrom/
    ```
3. Guest Additionsをインストールする．
    ```
    cd/mnt/cdrom
    sudo ./VBoxLinuxAdditions.run
    ```

#### package最適化
1. パッケージのアップグレード及びクリーン
    ```
    sudo apt update
    sudo apt upgrade -y
    sudo apt clean
    sudo apt autoremove
    ```
2. ディスクデータの調整
    ```
    sudo dd if=/dev/zero of=/tmp/ZERO bs=1M
    sudo rm /tmp/ZERO
    ```
3. コマンド履歴の削除 `history -c`
4. VirtualBox側で，ACPI シャットダウン

#### Vagarant Boxファイルの作成

以下のコマンドをホスト側で実行する．
1. パッケージの作成  
`vagrant package --base "ubuntu-18.04-server-base" --output "ubuntu1804LTS_server"`

以上完了．

## 参考
http://yaru-yaru.net/reizo/vagrant%E3%81%A7%E3%82%AA%E3%83%AA%E3%82%B8%E3%83%8A%E3%83%ABbox%E4%BD%9C%E6%88%90-ubuntu18-04%E7%B7%A8/
