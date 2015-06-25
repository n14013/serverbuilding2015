# Section 3 Ansibleによる自動化とテスト

毎回毎回手動で

    yum install もにょもにょ

とやるのも非効率なので、それらを自動化してくれるツールを使って今迄の作業を何回でもできるようにします。

今回の講義ではAnsibleを使用します。

## 3-0 Ansibleのインストール

[公式サイト](http://docs.ansible.com/intro_installation
.html#latest-releases-via-apt-ubuntu)に手順載ってるのでそのとおりにやってください。

	
	sudo apt-get install software-properties-common
	sudo apt-add-repository ppa:ansible/ansible
	sudo apt-get update
	sudo apt-get install ansible

## 3-1 ansibleでWordpressを動かす(2)を行なう

###shhpassのインストール

sshpassをインストールする。

	sudo apt-get install sshpass

インストール後ansibleの疎通確認

	ansible -k [ipアドレス] -m ping

###vagrantの設定

以下のプラグインをvagrantに追加する。

	vagrant plugin install vagrant-proxyconf
	vagrant plugin install vagrant-vbguest

次に、Vagrantfileに
	config.vm.provision "ansible" do |ansible|
		ansible.playbook = "playbook.yml"
	end
を追記する。

###playbookを書く

playbook.ymlに2-1のWordPressをインストールした手順どおりに実行できるように記入する。

###playbookの実行

	vagrant up


