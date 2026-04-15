# Vagrant導入手順

---

## VirtualBoxのインストール

### Windows

* VirtualBoxのダウンロードページへアクセスする
* https://www.virtualbox.org/wiki/Downloads
* Windows hostsのリンクからインストーラをダウンロードして実行する

### Mac

#### Homebrewからインストールする

```bash
brew cask install VirtualBox
```

#### インストール済みの場合はアップデートする

```bash
brew cask reinstall VirtualBox
```

#### バージョンを表示して確認する

```bash
VirtualBox --version
```

---

## Vagrantのインストール

### Windows

* 下記Vagrantのダウンロードページへアクセスする
* http://www.vagrantup.com/downloads.html
* WINDOWSカテゴリから対象bit数のインストーラをダウンロードして実行する

### Mac

#### Homebrewからインストールする

```bash
brew cask install vagrant
# バージョンの確認
vagrant --version
```

#### インストール済みの場合はアップデートする

```bash
brew cask reinstall vagrant
```

---

## Boxイメージの検索

* [Box公開ページ](https://portal.cloud.hashicorp.com/vagrant/discover) へアクセスする
* 追加したいBoxイメージを検索する
* 検索結果よりイメージの概要と追加手順が確認できる
