# インストール後の環境整備手順

* 作業アカウント：vagrant
* 各種パッケージのバージョンは動作確認したものを記載

---

## 既存システムの最新化

```bash
sudo dnf update -y
```

## 基本パッケージのインストール

```bash
sudo dnf -y install glibc-langpack-ja firewalld httpd
```

* 本作業で必要となるパッケージがない前提でインストールする

---

## ホスト名の設定

### 任意のホスト名（dev-alma9）に変更する

* **Vagrantfile** で指定している場合は不要

```bash
sudo hostnamectl set-hostname dev-alma9
```

### 確認

```bash
hostnamectl
```

>  Static hostname: dev-alma9

---

## システム言語の設定

### システム言語を日本語に設定する

```bash
sudo localectl set-locale LANG=ja_JP.UTF-8
```

### 確認

```bash
localectl status
```

* 反映に再ログインか再起動が必要になる場合がある

```
System Locale: LANG=ja_JP.UTF-8
    VC Keymap: us
   X11 Layout: us
```

---

## タイムゾーンの設定

### タイムゾーンを日本に設定する

```bash
sudo timedatectl set-timezone Asia/Tokyo
```

### タイムゾーン設定を表示して確認する

```bash
timedatectl
```

```
               Local time: Fri YYYY-MM-DD hh:mm:ss JST
           Universal time: Fri YYYY-MM-DD hh:mm:ss UTC
                 RTC time: Fri YYYY-MM-DD hh:mm:ss
                Time zone: Asia/Tokyo (JST, +0900)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

---

## ファイアウォールの設定

### ファイアウォールを有効化する

```bash
sudo systemctl enable --now firewalld
```

### httpとssh通信を許可する

```bash
sudo firewall-cmd --zone=public --add-service=http --permanent
```

> success

```bash
sudo firewall-cmd --zone=public --add-service=ssh  --permanent
```

> Warning: ALREADY_ENABLED: ssh

* ログインしている時点で ssh は有効なのでエラーになる想定

```bash
sudo firewall-cmd --reload
```

> success


---

## Webサーバの設定

### Apacheの起動と自動起動を設定する

```bash
sudo systemctl start httpd.service
```

```bash
sudo systemctl enable httpd.service
```

> Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.

### 確認

```bash
systemctl status httpd
```

```
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Fri YYYY-MM-DD hh:mm:ss JST; 26s ago
       Docs: man:httpd.service(8)
   Main PID: 82767 (httpd)
     Status: "Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec"
      Tasks: 177 (limit: 24383)
     Memory: 14.1M (peak: 14.4M)
        CPU: 111ms
     CGroup: /system.slice/httpd.service
             ├─82767 /usr/sbin/httpd -DFOREGROUND
             ├─82768 /usr/sbin/httpd -DFOREGROUND
             ├─82769 /usr/sbin/httpd -DFOREGROUND
             ├─82770 /usr/sbin/httpd -DFOREGROUND
             └─82771 /usr/sbin/httpd -DFOREGROUND
```

* ブラウザから **192.168.33.10** にアクセスすると "Web Server Test Page" が表示されることでも確認できる

---

## 設定を反映する

### shellにログインしなおす

```bash
exec $SHELL -l
```
