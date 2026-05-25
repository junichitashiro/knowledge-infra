# GitをセットアップしGitHubリポジトリと連携する手順

## 1. Homebrewをインストールする

- Homebrewがインストールされていない場合はインストールする

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- Homebrewが正常に動作することを確認する

```bash
brew -v
```

> Homebrew 4.x.x

---

## 2. HomebrewでGitをインストールする

- Gitをインストールする

```bash
brew install git
```

- Gitのバージョンを確認する

```bash
git --version
```

> git version 2.x.x

---

## 3. GitHub認証用のSSHキーを作成する

- SSHキー作成コマンド

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- SSHキーの保存先を選択する

> Enter file in which to save the key

→ `Enter`（デフォルトの `~/.ssh/id_rsa` を使用する）

- SSHキー使用時のパスフレーズを設定する

> Enter passphrase (empty for no passphrase)

→ 任意のパスフレーズを入力するか、そのまま `Enter`

- 確認入力

> Enter same passphrase again

→ 同じパスフレーズを再入力

---

## 4. SSH キーを GitHub に登録する

- 公開鍵をクリップボードにコピーしておく

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

## 5. Git初期設定をする

- ユーザー名とメールアドレスを設定する

```bash
git config --global user.name "GitHubのアカウント名"
git config --global user.email "GitHubで設定しているメールアドレス"
```

- 設定を確認する

```bash
git config --global --list
```

> user.name=Your Name  \
  user.email=your_email@address.com

---

## 6. GitHubリモートリポジトリをクローンする

- GitHubのリポジトリページで `<> Code▼` をクリックし、SSHのURLをコピーする

```bash
git clone git@github.com:user_name/repository_name.git
```

- クローンしたリポジトリフォルダでリモートURLを設定（変更）する

```bash
cd repository_name
git remote set-url origin git@github.com:user_name/repository_name.git
```

> push 先がこの `repository_name` になる

- リモートリポジトリの設定を確認する

```bash
git remote -v
```

> origin  git@github.com:user_name/repository_name.git (fetch).  \
  origin  git@github.com:user_name/repository_name.git (push)

- ここで表示が `origin	https://github.com` ではなく `origin  git@github.com:` となっていれば認証にSSHキーが使われている
