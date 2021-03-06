# Section 2 その他のWebサーバー環境

## 2-1 Vagrantを使用したCentOS 7環境の起動

サーバーを構築するたびにOSのインストールからやってられないので事前に用意したCentOS 7の環境をVagrantで起動します。

### Vagrantで起動できるCentOS 7のイメージを登録

USBストレージにVagrant用CentOS boxを用意してありますので登録して使用します。

    vagrant box add CentOS7 コピーしたboxファイル --force

### Vagrantの初期設定

作業用ディレクトリを作成し、その中で初期設定を行ないます。

   vagrant init

上記コマンドを実行するとVagrantfileというファイルが作成されます。このファイルにVagrantの設定が書かれています。
そのままではデフォルトのOS(存在しない)を起動してしまうので、CentOS 7を起動するようにします。

viでVagrantfileを開き、

    config.vm.box = "base"
d
と書かれているのを

    config.vm.box = "CentOS7"

とします。ここで指定するものはvagrant box addで指定したものの名前(上記の例だとCentOS7)を指定します。

### 仮想マシンの起動

    vagrant up

### 仮想マシンの停止

    vagrant halt

### 仮想マシンの一時停止

    vagrant suspend

### 仮想マシンの破棄

最初からやり直したい…そんな時に破棄するとCentOSが初期化されます。
また`vagrant up`をすると立ち上がります…

    vagrant destroy

### 仮想マシンへ接続

実際の仮想マシンへはsshで接続します。

    vagrant ssh

### ホストオンリーアダプターの設定

サーバーを設定したあと、動作確認するために接続するためのIPアドレスを設定します。
また、そのためのNICを追加します。

Vagrantfileの

    Vagrant.configure(2) do |config|

から一番最後の

    end

の間に

    config.vm.network "private_network", ip:"192.168.56.129"

と書くと仮想マシンのNIC2に192.168.56.129のIPアドレスが振られます。
`config.vm.box = "CentOS7"` の下にでも書くといいと思います。

※ 当然のことながら、複数台の仮想マシンを立ち上げる時には異なるIPアドレスを割り当てる必要があります。

### Vagrantfileの反映

Vagrantfileで変更した設定を反映させるには

    vagrant reload

すると反映されます。ただし、再起動されますので注意してね。

## 2-2 Wordpressを動かす(2)

1-2ではWordpressをApache + PHP + MySQLで動作させたが、今度はNginx + PHP + MariaDBで動作させます。

###yumとwgetのプロキシを設定する

Section1に記入した方法で設定する。

###Nginxのインストール

Nginxの公式からリポジトリ追加用のrpmファイルをダウンロード

	wget http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

Nginxのリポジトリを追加

	sudo yum install nginx-release-centos-7-0.e17.ngx.noarch.rpm

Nginxのインストール

	sudo yum install nginx

####Nginxの設定

	sudo vi /etc/nginx/conf.d/default.conf

default.confを編集する。

	location / {
	root /usr/share/nginx/html;
	index index.html index.htm;
	}

にindex.phpを追記

	location / {
	root /usr/share/nginx/html;
	index index.php index.html index.htm;
	}

	location ~ .php$ {
	root html;
	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index index.php;
	fastcgi_param SCRIPT_FILENAME /scripts$fastcgi_script_name;
	include fastcgi_params;
	}

[/scripts$fastcgi_script_name;]を[ $document_root$fastcgi_script_name;]に変更、[root thml;]を[root /usr/share/nginx/html]に変更。

	location ~ .php$ {
	root /usr/share/nginx/html;
	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index index.php;
	fastcgi_param SCRIPT_FILENAME $document_root		$fastcgi_script_name;
	include fastcgi_params;
	}

###phpのインストール

	sudo yum install php-mysql php-mbstring php-fpm

#####php-fpmの設定

	sudo vi /etc/php-fpm.d/www.conf

	user = apache
	group = apache
を
	user = nginx
	group = nginx

に書き換える

###mariadbをインストールする

	sudo yum install mariadb mariadb-server

####データベース作成

mariadbを起動
	sudo service mariadb start

rootのパスワードを設定

	mysql -u root

	SET PASSWORD FOR 'root'@'localhost' = PASSWORD('パスワード');
	
rootでログイン

	mysql -u root -p

データベース作成

	CREATE DATABESE [データベース名];

ユーザー作成

	GRANT ALL PRIVEGES ON [データベース名].* TO '[ユーザー名]'@'localhost' IDENTIFIED BY '[パスワード]';

###WordPressのインストール

Wordpressをwgetで落としてきて、解凍を行い、

	mv wordpress /usr/share/nginx/html

に移動して後はいつもどうりに設定をして

	192.168.33.129/wordpress/wp-admin/install.php

にアクセス。
	
## 2-3 Wordpressを動かす(3)

###apache2.2のインストール

ソースをダウンロード

	wget http://ftp.riken.jp/net/apache//httpd/httpd-2.2.29.tar.gz

展開

	gizp -d httpd-NN.tar.gz

	tar xvf httpd-NN.tar

展開されたディレクトリに移動して

	./configure

	make

	make install

###php5.5のインストール

## 2-4 ベンチマークを取る

サーバーの性能測定のためにベンチマークを取ることがあります。

[別ページ](misc/Benchmark.md)にまとめてありますのでそちらを参照してください。

## 2-5 セキュリティチェック

サーバーを構築したとしても、セキュリティがガバガバではいろんな意味で駄目です。
Webアプリケーションの脆弱性を突かれたり、設定したサーバーに脆弱性があったりした場合、
情報漏洩とか乗っ取りとか踏み台とかされるとアレです。

定期的にセキュリティチェックを行なう必要があります。

(やり方は[別ページ](misc/SecurityScan.md))

セキュリティチェックを行ない、不具合があるようでしたら修正を行なって再度セキュリティチェックを行ないます。
(可能な限り不具合がなくなるまでチェック&fixを行ないます。)
