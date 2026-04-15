# Git をセットアップし GitHub リポジトリと連携する手順

## 1. Homebrew をインストール

### Homebrewがインストールされていない場合はインストールする

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Homebrew が正常に動作することを確認する

```bash
brew -v
```

> Homebrew 4.x.x

---

## 2. Homebrew で Git をインストール

### Git をインストールする

```bash
brew install git
```

### Git のバージョンを確認する

```bash
git --version
```

> git version 2.x.x

---

## 3. SSH キーの作成と GitHub への登録

### GitHub との認証に SSH を使用するため、SSH キーを作成する

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

### プロンプトが表示されたら、次のように対応する

```bash
Enter file in which to save the key
```

 → `Enter`（デフォルトの `~/.ssh/id_rsa` を使用する）

```bash
Enter passphrase (empty for no passphrase)
```

 → 任意のパスフレーズを入力するか、そのまま `Enter`

```bash
Enter same passphrase again
```

 → 同じパスフレーズを再入力

### SSH キーを GitHub に登録する

#### 公開鍵をクリップボードにコピーしておく

```bash
pbcopy < ~/.ssh/id_rsa.pub
```

### GitHub での SSH キー登録作業

1. GitHub のアイコンから `Settings` → `SSH and GPG keys` を辿る
2. `New SSH key` をクリックして新しい SSH キーを追加する
3. 任意のタイトルをつける
4. クリップボードの内容を貼り付ける
5. `Add SSH key` をクリックして追加

#### 追加後に接続をテストする

```bash
ssh -T git@github.com
```

> Hi your-username! You've successfully authenticated, but GitHub does not provide shell access.

---

## 4. Git の初期設定

### Git のユーザー名とメールアドレスを設定する

```bash
git config --global user.name "GitHubのアカウント名"
git config --global user.email "GitHubで設定しているメールアドレス"
```

### 設定を確認する

```bash
git config --global --list
```

> user.name=Your Name  
  user.email=your_email@address.com

---

## 5. GitHub リポジトリのクローン

### SSH で GitHub のリポジトリをローカルにクローンする

#### GitHub のリポジトリページで `<> Code▼` をクリックし、SSH の URL をコピーする

```bash
git clone git@github.com:user_name/repository_name.git
```

### リモートリポジトリの設定を確認する

* クローンしたリポジトリフォルダで実行する

```bash
git remote -v
```

> origin  git@github.com:user_name/repository_name.git (fetch)  
  origin  git@github.com:user_name/repository_name.git (push)
