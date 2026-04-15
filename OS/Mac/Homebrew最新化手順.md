# Homebrewのインストールと最新化

---

## インストール

### [Homebrew](https://brew.sh/ja/) のページからインストールコマンドを確認する


### インストールを実行する

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
### PATH を通す

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

---

## 最新化

### Homebrew 本体を最新化する

```bash
brew update
```

### Homebrew でインストールしたパッケージを最新化する

```bash
brew upgrade
```

---

## 確認

### バージョンを表示する

```bash
brew --version
```
> Homebrew X.X.XX

### Homebrew の状態や整合性などに問題がないか表示する

```bash
brew doctor
```
> Your system is ready to brew.
