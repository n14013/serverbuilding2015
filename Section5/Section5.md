# Section 5 DNSサーバーを動作させる

インターネットにおいて、これが無いとほぼ成り立たないのがDNSサーバー。
最近は特に不人気なんだけど、一応基本のDNSサーバーであるbindを設定してみよう。

## 5-1 bindのインストール

yumで入るので適当にインストールしてください。

[ansible化](Section5)しとくと楽ですよ。

## 5-2 bindの設定

###自分のゾーンを作成

	vi ansible/zone.cloneko.com

でclonekoをすべてn14013に変更。

###masterファイルとsalveファイルを編集

それぞれ

zone "cloneko.com" {

のclonekoをn14013に変更する。

###DNSに問い合わせる

	dig @192.168.33.14 ns.n14013.com

