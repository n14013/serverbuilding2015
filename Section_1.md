# Section 1 基本のサーバー構築

## 1-1 CentOS 7のインストール

### VirtualBoxへのインストール

CentOSの公式サイトよりCentOS 7 Minimal ISO(x86_64)のISOファイルをダウンロードし、
VirtualBox上にインストールしてください。

VirtualBoxで作成する仮想マシンのメモリのサイズは1GBにします。また、ストレージの容量は8GB程度に設定してください。

ネットワークアダプター2を設定します。割り当てを「ホストオンリーアダプター」にします。
(ネットワークアダプター1はデフォルト(NAT)で問題ありません)

インストール中に指示されるパーティションの設定は特に指定しません。

インストール中、root以外の作業用(管理者)のユーザーを作成してください。

### ネットワークアダプター1/2へのIPアドレスの設定とssh接続の確認

/etc/sysconfig/network-scriptにifcfg-enp0s?というファイルがあるので、
そのファイルを編集してネットワーク接続ができるように設定します。

DHCPでIPアドレスを取得できますので、[RedHat Enterprise Linux 7のマニュアル(英語)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html-single/Networking_Guide/index.html#sec-Configuring_a_Network_Interface_Using_ifcg_Files)を読んで設定してください。

### SSH接続の確認

Ubuntuからsshで仮想マシンに接続できることを確認してください。

	ssh [ユーザー名]@[ipアドレス]

でsshに接続できる。

### インストール後の設定

yumやwgetを使用する時のproxyの設定を行なってください。

yumは

	sudo vi /etc/yum.conf
 
に

	proxy=http://172.16.40.1:8888

と記入する。

wgetは

	sudo vi ~/.wgetrc

に

	http_proxy=http://172.16.40.1:8888
	https_proxy=http://172.16.40.1:8888

と記入する。

### アップデート

プロキシの設定後、アップデートができるようになっているのでアップデートを行なってください。

	sudo yum update

でアップデートできる。

## 1-2 Wordpressを動かす(1)

Wordpressを動作させるためには下記のソフトウェアが必要になります。 [※1](#LAMP)

* Apache HTTP Server
* MySQL
* PHP

###PHPのインストール

	sudo yum install php php-mbstring php-mysql

### Apacheのインストール

	sudo yum install httpd

インストール後

	sudo service httpd start

と叩いて起動していることを確認する。

###MySQLのインストール

MySQLのリポジトリファイルをインストール

	sudo yum install http://dev.mysql.com/get/mysql-community-release-el 7-5.noarch.rpm 

MySQLのインストール

	sudo yum install mysql mysql-server mysql-devel mysql-utilities

###MySQLの設定

MySQLを起動して

	mysql -u root -p

でログインする

MySQL内で

	CREATE database [データベース名];

	CREATE USER '[ユーザー名]'@'localhost' IDENTIFIED BY '[パスワード]';

データベースとユーザー、パスワードを作成後、以下のコマンドで操作権限を与える。

	GRANT ALL PRIVILEGES ON [データベース名].* TO 'ユーザー名'@'localhost';


###WordPressのインストール

パッケージをダウンロード

	wget http://wordprees.org/latest.tar.gz
解凍

	tar -xzvf latest.tar.gz

####wp-config.phpの設定

wordoressを以下のディレクトリに移動する。

	mv wordoress /var/www/html

wordpress内にあるwp-config-sample.phpをwp-config.phpとしてコピーする。

	cp wp-config-samle.php wp-config.php

viでwp-config.phpを開きMySQLで設定したデータベース名、ユーザー名、パスワードを記入する。

####WordPressの起動

ApacheとMySQLを起動して、以下のコマンドでfilewallを無効にする。

	service stop firewalld

ブラウザで

	http://172.16.40.1/wordpress

にアクセスしてWordPressが起動しているか確認する。


