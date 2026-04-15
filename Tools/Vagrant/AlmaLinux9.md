# VagrantでローカルにAlmaLinux9環境をつくる

---

## Box作成の準備

コマンドプロンプト、ターミナルから作業する

### Vagrant用のディレクトリを作成する

```bash
mkdir vagrant
cd vagrant
```

### Boxごとにディレクトリ分けしたほうが管理しやすいため各Box用のディレクトリを作成する

```bash
mkdir AlmaLinux
cd AlmaLinux
```

* このディレクトリ名がVirtualBoxの仮想マシン名に割り当てられる
* **Vagrantfile** で指定した場合はそちらが割り当てられる

---

## Boxの追加手順

### Box追加コマンド

```bash
vagrant box add {Box名} {BoxのURL}
```

### 下記のBoxはVagrantCloudにあるためBox名だけで追加できる

```bash
vagrant box add almalinux/9
```

* VagrantCloud URL : https://portal.cloud.hashicorp.com/vagrant/discover/almalinux/9

### Boxを初期化する

```bash
# vagrant init {Box名}
vagrant init almalinux/9
```

* 初期化すると Vagrantfile が作成される

---

## Vagrantfileの編集

### 作成された **Vagrantfile** の下記の行をコメントインして保存する

```ruby
Vagrant.configure('2') do |config|
  config.vm.box = 'almalinux/9'
  config.vm.hostname = 'dev-alma9'
  config.vm.network 'private_network', ip: '192.168.33.10'

  # デフォルト共有(プロジェクトルート -> /vagrant)を無効化
  config.vm.synced_folder '.', '/vagrant', disabled: true
  # 明示的に share フォルダだけを /vagrant に共有
  config.vm.synced_folder './share', '/vagrant', create: true

  # VirtualBoxの設定
  config.vm.provider 'virtualbox' do |vb|
    vb.name = 'dev-alma9-vagrant'
    vb.memory = 4096
    vb.cpus = 2
  end
end
```

* ブラウザからのWebサービス利用をするためIPアドレスを有効化
* ホストとゲスト間で利用する共有フォルダを有効化
* 上記の[VagrantCloud](https://portal.cloud.hashicorp.com/vagrant/discover/kalilinux/rolling)にもバージョン番号を含む手順が公開されている

---

## Boxの起動

### Vagrantfile と同一のディレクトリでコマンドを実行する

```bash
vagrant up
```

---

## ターミナルからBoxにアクセスする

### Boxが起動している状態で下記コマンドでアクセスする

```bash
vagrant ssh
```

* ログインユーザ：vagrant
* パスワード：vagrant


### ホスト・ゲスト間のファイル共有に必要なカーネルモジュールをインストールする

```bash
sudo dnf -y install epel-release
sudo dnf -y install gcc make perl kernel-devel kernel-headers dkms elfutils-libelf-devel bzip2
```
