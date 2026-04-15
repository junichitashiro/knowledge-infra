# 既存のPHPを削除してバージョンアップする手順

* 作業アカウント：vagrant
* wordpressインストール用の手順として作成

---

## リポジトリの追加

### EPELのリポジトリを追加する

```bash
sudo dnf -y install epel-release
```

### REMIのリポジトリを追加する

```bash
sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
```

---

## 既存のPHPをリセットする

### すでにPHPがある場合は削除する

```bash
sudo dnf module reset php -y
```

---

## 新しいバージョンのPHPをインストールする

### 利用可能なPHPバージョンを確認する

```bash
sudo dnf module list php
```

* 一覧が表示されるので確認する（[d]はデフォルト）

```
Name  Stream   Profiles        Summary
php   remi-8.2 common [d]      PHP scripting language
php   remi-8.3 common
php   remi-8.4 common
```

### 特定のバージョンを指定して有効化する（ここでは8.1）

```bash
sudo dnf module enable php:remi-8.1 -y
```

### PHPをインストールする

```bash
sudo dnf install -y php php-cli php-fpm
```

### バージョンを確認する

```bash
php -v
```

> PHP 8.1.34 (cli) (built: Dec 16 2025 18:33:34) (NTS gcc x86_64)  
> Copyright (c) The PHP Group  
> Zend Engine v4.1.34, Copyright (c) Zend Technologies  
> with Zend OPcache v8.1.34, Copyright (c), by Zend Technologies  

### PHP-FPMを起動する

```bash
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
```

### 起動を確認する

```bash
sudo systemctl status php-fpm
```

```
● php-fpm.service - The PHP FastCGI Process Manager
     Loaded: loaded (/usr/lib/systemd/system/php-fpm.service; enabled; preset: disabled)
     Active: active (running) since Tue 2026-03-03 20:37:01 JST; 35min ago
   Main PID: 5350 (php-fpm)
     Status: "Processes active: 0, idle: 5, Requests: 0, slow: 0, Traffic: 0req/sec"
      Tasks: 6 (limit: 11914)
     Memory: 14.3M (peak: 14.8M)
        CPU: 190ms
     CGroup: /system.slice/php-fpm.service
             ├─5350 "php-fpm: master process (/etc/php-fpm.conf)"
             ├─5352 "php-fpm: pool www"
             ├─5356 "php-fpm: pool www"
             ├─5357 "php-fpm: pool www"
             ├─5358 "php-fpm: pool www"
             └─5359 "php-fpm: pool www"

 3月 03 20:37:01 dev-alma9 systemd[1]: Starting The PHP FastCGI Process Manager...
 3月 03 20:37:01 dev-alma9 systemd[1]: Started The PHP FastCGI Process Manager.
```

---

## 補足：関連性の高いMySQLの拡張機能をインストールしておく

### WordPressに必要な最小PHPパッケージのインストール

```bash
sudo dnf install php php-fpm php-mysqlnd php-gd php-mbstring php-xml php-json php-opcache
```

### PHPモジュールを表示して確認する

```bash
php -m | grep -E 'mysqli|pdo_mysql'
```

> mysqli  
> pdo_mysql

### Apacheと連携する場合の対応

```bash
sudo dnf install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd
```

---

## アンインストール方法

```bash
sudo dnf remove php*
sudo dnf module reset php
```
