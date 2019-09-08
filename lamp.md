# Vagrant Box ファイルから lamp環境構築

## Boxファイルから仮想マシンの生成及び起動．
1. BoxファイルをVagrantに追加．  
    `vagrant box add --name pcm-server ubuntu1804LTS_server`

2. Vagrant ファイルの作成．
    `vagrant init`

3. Vagrant ファイルの設定
    ```
    #環境構築中のみ
    config.ssh.insert_key = false

    config.vm.box = "pcm-server"
    
    config.vm.network "forwarded_port", guest: 22, host:2223
    config.vm.network "forwarded_port", guest: 80, host: 8081
    config.vm.network "forwarded_port", guest: 5000, host: 5200
    config.vm.network "forwarded_port", guest: 27017, host: 29017

    config.vm.network "private_network", ip: "192.168.33.11"
    ```
4. Vagrant経由でVMの設定及び起動．  `vagrant up`


## (L)AMPインストール

### apache, php
1. phpのリポジトリを追加し，apache,phpをインストール．
    ```
    sudo add-apt-repository -y ppa:ondrej/php
    sudo apt install -y apache2 php5.6 php5.6-mysql
    ```

### mysql

1. 必要なパッケージをインストール  
    `sudo apt install libaio1`

2. mysqlのインストール準備
    ```
    mkdir mysql
    cd mysql
    wget https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-server_5.6.45-1debian9_amd64.deb-bundle.tar
    tar -xvf mysql-server_5.6.45-1debian9_amd64.deb-bundle.tar
    ```
3. mysqlのインストール
    ```
    sudo dpkg -i libmysqlclient18_5.6.45-1debian9_amd64.deb
    sudo dpkg -i libmysqlclient-dev_5.6.45-1debian9_amd64.deb  
    sudo dpkg -i libmysqld-dev_5.6.45-1debian9_amd64.deb  
    sudo dpkg -i mysql-common_5.6.45-1debian9_amd64.deb  
    sudo dpkg -i mysql-client_5.6.45-1debian9_amd64.deb  
    sudo dpkg -i mysql-community-client_5.6.45-1debian9_amd64.deb 
    sudo dpkg -i mysql-community-server_5.6.45-1debian9_amd64.deb 
    ```
4. インストール途中「Configuring mysql-community-server」画面になる．

    |項目|パラメータ|
    |:--:|--:|
    |Enter root password| vagrant|
    |Re-enter root password| vagrant|

5. 接続テスト  
    以下の様にコマンドを実行する．
    ```
    mysql -u root -p
    Enter password: vagrant #表示されない
    mysql> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | test               |
    +--------------------+
    ```
    結果が同様であればOK

6.  `mysql>quit`を実行し，終了する．

## 動作テスト
1. mysqlにログイン  
    `mysql -u root -pvagrant`

2. 動作テスト用のデータベース及びテーブル，データ作成
    ```
    mysql> create database mydb;
    mysql> create table user(id int, name varchar(10));
    mysql> insert into user values(1,'yomogi');
    mysql> select * from user;
    +------+--------+
    | id   | name   |
    +------+--------+
    |    1 | yomogi |
    +------+--------+
    1 row in set (0.00 sec)
    ```

3. サンプルプログラムを`/var/www/html/test.php`として作成．  
    手元にあるpackageはpassword:rootにしてしまった．
    以下はデータベースへのパラメータ記載方法例
    ```php
    $db = new PDO('mysql:host=localhost;port=3306;dbname=mydb', 'root', 'vagrant');
    ```

4. ホスト側で「localhost:8081/test.php」にアクセスし，「1 yomogi」が表示できれば完了．

## 再パッケージ化
### package最適化
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

### package化
```
vagrant package 
```

### Vagrantファイルの共有
1. 環境構築中のみの部分(公開鍵の差し替え)をコメントにして，配布．


## mysqlのパスワードを変更する際のメモ

https://www.aiship.jp/knowhow/archives/28257

```
mysql> use mysql;
mysql> truncate table user;
Query OK, 0 rows affected (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
mysql> grant all privileges on *.* to root@localhost identified by 'パスワード' with grant option;
Query OK, 0 rows affected (0.01 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

最後にパッケージ化すればOK


 



