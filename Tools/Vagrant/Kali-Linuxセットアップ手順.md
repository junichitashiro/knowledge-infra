# VagrantでローカルにCentOS7環境をつくる

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
mkdir kali-linux
cd kali-linux
```

---

## Boxの追加手順

### Box追加コマンド

```bash
vagrant box add {Box名} {BoxのURL}
```

### 下記のBoxはVagrantCloudにあるためBox名だけで追加できる

```bash
vagrant box add kalilinux/rolling
```

* VagrantCloud URL : https://portal.cloud.hashicorp.com/vagrant/discover/kalilinux/rolling

### Boxを初期化する

```bash
# vagrant init {Box名}
vagrant init kalilinux/rolling
```

* 初期化すると Vagrantfile が作成される

---

## Vagrantfileの編集

### 作成された **Vagrantfile** の下記の行をコメントインして保存する

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "kalilinux/rolling"
end
```

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
