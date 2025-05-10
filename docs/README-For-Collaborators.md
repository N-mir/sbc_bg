# sbc\_bg リポジトリ コラボレータ向けマニュアル

このマニュアルでは、GitHub コラボレータとして招待された方向けに、リポジトリへの初回セットアップから日常的な作業手順までを解説します。

## 前提

* **Collaborator として招待されていること**  
  招待状を受け取り、GitHub 上のリポジトリにアクセスできる権限が必要です。  
  まだ招待されていない場合は、リポジトリ管理者に相談してください。

* **リモートリポジトリ**  
  [https://github.com/N-mir/sbc\_bg](https://github.com/N-mir/sbc_bg)  
  ここにソースコードがホストされています。

* **作業して良いブランチ**  
  `task/edit-game-rule`  
  誤って他のブランチを編集しないよう、作業用のブランチをあらかじめ決めています。

* **推奨エディタ**  
  [Visual Studio Code](https://code.visualstudio.com/) (以下 VS Code)  
  ソースコードの編集やGit操作をGUIで管理しやすいため推奨しています。  
  ダウンロード: [https://code.visualstudio.com/](https://code.visualstudio.com/)

## 初回セットアップ

初回に必要な環境構築手順です。以降の作業がスムーズに行えるようにしましょう。

### 1. リポジトリのクローン

```bash
# リモートリポジトリをローカルにコピー
git clone git@github.com:N-mir/sbc_bg.git
cd sbc_bg
```

* **※操作目的**  
  リモート上の最新ソースコードを手元にダウンロードし、作業を開始できる状態にするためです。

### 2. origin の確認・設定

```bash
# 現在の origin URL を確認
git remote -v
# 出力例:
# origin	git@github.com:N-mir/sbc_bg.git (fetch)
# origin	git@github.com:N-mir/sbc_bg.git (push)
```

もしURLがSSHではなくHTTPSになっている場合、SSH接続に切り替えます：

```bash
git remote set-url origin git@github.com:N-mir/sbc_bg.git
```

* **※操作目的**  
  SSH接続にすることで、毎回パスワード入力せずに安全にプッシュ・プルが可能になります。

### 3. 接続アカウント（ユーザー名）の確認・変更

```bash
# 現在のGitユーザー名とメールアドレスを確認
git config --global user.name
git config --global user.email

# 設定変更（必要に応じて）
git config --global user.name "Your Name"
git config --global user.email you@example.com
```

* **※操作目的**  
  コミット履歴に正しい名前・メールアドレスを残すことで、誰がどの変更を行ったかを明確にするためです。

### 4. SSHキーの設定（Windows PC / PowerShell）

Windows PC の PowerShell で操作を行います。

1. **SSHキー用フォルダの確認／作成**

   ```powershell
   # デフォルトの SSH フォルダ (~/.ssh) が存在するか確認
   Test-Path -Path $HOME\.ssh
   # 存在しない場合はフォルダを作成
   if (-not (Test-Path -Path $HOME\.ssh)) {
     New-Item -ItemType Directory -Path $HOME\.ssh
   }
   ```

2. **SSHキーの生成**

   ```powershell
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   * コマンド実行時に以下のように質問されます：

     1. 保存先のパス（デフォルト: `$HOME\.ssh\id_ed25519`）
     2. パスフレーズ
   * **操作方法**：すべて何も入力せず（空のまま）Enterキーを押すと、デフォルト設定が適用されます。

3. **SSHエージェントの起動**

   ```powershell
   # OpenSSH エージェントサービスを起動し、自動起動設定
   Start-Service ssh-agent
   Set-Service -Name ssh-agent -StartupType Automatic

   # 秘密鍵をエージェントに追加
   ssh-add $HOME\.ssh\id_ed25519
   ```

4. **公開鍵を GitHub に登録**

   * 生成された公開鍵の内容をクリップボードにコピー

     ```powershell
     Get-Content $HOME\.ssh\id_ed25519.pub | Set-Clipboard
     ```
   * GitHub の Settings → SSH and GPG keys → New SSH key に貼り付け

* **※操作目的**  
  SSH鍵で認証することで、安全かつパスワード不要で Git 操作が行えるようになります。

> **注意**: SSH で `git push` を利用するには、あらかじめこの手順でキーを作成し、GitHub に登録しておく必要があります。

## 作業準備（毎回行うこと）

リポジトリへ新しい変更を取り込む手順です。常に最新の状態を保ちましょう。

```bash
# 作業用ブランチに移動
git checkout task/edit-game-rule

# リモートの最新コミットを取得してマージ
git pull origin task/edit-game-rule
```

* **※操作目的**  
  他のコラボレータの変更をローカルに取り込み、競合や古い情報で作業ミスを防ぐためです。

## 編集方法

実際にファイルを編集し、変更をリモートに反映する手順です。

1. **ファイルを編集**  
   VS Code などのエディタで必要なファイルを修正します。

2. **ステージング**

```bash
git add .
```

* **※操作目的**  
  どの変更を次のコミットに含めるかを指定します。

3. **コミット**

```bash
git commit -m "作業内容を簡潔に説明"
```

* **※操作目的**  
  変更履歴を分かりやすいメッセージとともに記録するためです。

4. **プッシュ**

```bash
git push -u origin task/edit-game-rule
```

* **※操作目的**  
  ローカルの変更をリモートに送信し、他のコラボレータと共有するためです。

  * SSHプッシュを行う場合は、事前に「4. SSHキーの設定」でキー登録を完了させてください。
    ローカルの変更をリモートに送信し、他のコラボレータと共有するためです。

## 編集作業中の完成版Webページの確認方法

Jekyllで実装した静的Webサイトのページをローカルで確認する手順です。

### 1. Jekyll のビルド環境の構築

ローカルで Jekyll をビルドするには Ruby と Bundler が必要です。以下の手順で環境を準備します。

1. **Ruby のバージョン確認**

   ```bash
   ruby -v
   ```

   出力例: `ruby 2.7.1p83` など。インストールされていない場合は [https://www.ruby-lang.org/ja/downloads/](https://www.ruby-lang.org/ja/downloads/) からインストールしてください。

2. **Bundler のインストール確認**

   ```bash
   bundle -v
   ```

   もし `command not found` や `bundle: command not found` のようなエラーが出た場合、Bundler をインストールします:

   ```bash
   gem install bundler
   ```

3. **必要な gem のインストール（Gemfile があるディレクトリで実行）**

   ```bash
   bundle install
   ```

* **※操作目的**  
  Jekyllサイトをビルド・サーブするために必要なパッケージをインストールします。

### 2. サイトのビルドとサーブ サイトのビルドとサーブ

```bash
bundle exec jekyll serve
```

* **※操作目的**  
  ローカルサーバーを起動し、変更をリアルタイムで反映しながらプレビューします。

### 3. サーバーへのアクセス

ブラウザで以下のURLにアクセスします：

```
http://127.0.0.1:4000/sbc_bg/
```

* **※操作目的**  
  編集したページの動作確認・プレビューを行うためです。

### 4. 注意事項

一部ページで `404 Not Found` が表示される場合があります。これは、URL上で `sbc_bg/` が二重に付与されることが原因です。ローカルアクセス時は、URLから余分な `sbc_bg/` を削除すれば正常に表示されます。
例: `http://127.0.0.1:4000/sbc_bg/sbc_bg/games/open-the-price/` → `http://127.0.0.1:4000/sbc_bg/games/open-the-price/`
GitHub Pages 上では正常に動作するため、このエラーは無視して構いません。

## トラブルシューティング

* プッシュ時に権限エラーが出る場合:
  SSHキーが正しく登録・読み込まれているか確認してください。

* マージコンフリクトが発生した場合:
  VS Code のマージツールやコマンドラインで競合箇所を解消し、再度ステージング・コミットしてください。

---

以上が基本的な作業手順です。不明点があれば、直接管理者にお問い合わせください。
