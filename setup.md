# 環境構築ツールのインストール
1. VirtualBox version 6.0.10
2. vagrant 2.2.5

## 要求ディスク容量
|ソフト|サイズ|
|:--:|--:|
|VirtualBox|328MB|
|Vagrant |956MB|


## VirtualBox 

以下のページよりVirtualBoxのインストーラをダウンロードする．   
[Oracle VM VirtualBox](https://www.oracle.com/virtualization/technologies/vm/downloads/virtualbox-downloads.html#vbox)

1. ダウンロードしたインストーラを実行する．

2. 「Welcome to the Oracle VM VirtualBox 6.0.10 Setup Wizard」  
ここは「Next」．

3. 「Custom Setup」(機能の選択)  
デフォルトの状態で「Next」．

4. 「Custom Setop」(リンクの作成)  
デフォルトの状態で「Next」．

5. 「Warning Network Interfaces」  
    仮想マシン用のネットワークインタフェースをインストールするので，作業中ネットの遮断が発生する．  
    確認のした上で「Yes」．

6. 「Ready to Install」  
最終確認．「Install」．

7. 何度か「ユーザアカウント制御」が表示される．  
    -> 許可する．  
    Windowsセキュリティで「このデバイスソフトウェアをインストールしますか？」と尋ねられるので，Oracle Corporationからのソフトウェアを常に信頼するをチェックの上「インストール」．

8. 「Oracle VM VirtualBox 6.0.10 installation is complete.」  
    「Start Oracle VM VirtualBox 6.0.10 after installation」のチェックを外し，「Finish」．

## Vagrant
以下のページよりVagrantのインストーラをダウンロードする．  
[Download Vagrant](https://www.vagrantup.com/downloads.html)


1. ダウンロードしたインストーラを実行する．
2. 「Welcome to the Vagrant Setup Wizard」  
ここは「Next」．
3. 「End-User License Agreement」  
ライセンスに同意する場合は，
「I accept the terms in the License Agreement」にチェックし，「Next」．
4. 「Destination Folder」  
デフォルトのまま「Next」．
5. 「Ready to install Vagrant」  
「Install」をクリック．

6. インストール中ユーザアカウント制御が表示される．  
発行元が「HashiCorp, Inc.」であることを確認した上で，「OK」

7. 「Completed the Vagrant Setup Wizard」  
「Finish」をクリック
8. 最後に再起動するかを確認するので，他の作業途中のファイルなど保存し，再起動．

