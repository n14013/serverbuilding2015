# Section 0 講義の前のセットアップ

## 0-1 VirtualBoxのインストール

### virtualboxのダウンロード

https://www.virtualbox.org/wiki/Linux_Downloadsからダウンロード。

### レポジトリの追加

「ソフトウェア設定」の「ソフトウェアとアップデート」を開き、「他のソフトウェア」タブで「追加」をクリックして、

    deb http://download.virtualbox.org/virtualbox/debian oneiric contrib

を入力して「ソースを追加（A）」をクリック。

### virtualboxのインストール 

    sudo dpkg -iパッケージ　

でインストール。

インストール後は、virtualboxを起動して、インストールされているか確認する。

## 0-2 Vagrantのインストール

[Vagrantの公式サイト](http://www.vagrantup.com/downloads)からダウンロード。

    sudo dpkg -i パッケージ　

でインストール。
