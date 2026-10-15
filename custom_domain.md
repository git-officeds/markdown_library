# XAMPP カスタムドメイン（サブドメイン）設定手順

XAMPP環境において、ローカル開発用のカスタムドメインおよびサブドメインを設定する手順書です。

---

## 概要

本手順では、以下の独自ドメイン／サブドメインをローカル環境でアクセス可能に設定します。

| ドメイン名 | ドキュメントルート |
| :--- | :--- |
| `localhost` | `C:/xampp/htdocs` |
| `xxx.jp.test` | `C:/xampp/htdocs/xxx.jp` |
| `yyy.jp.test` | `C:/xampp/htdocs/yyy.jp` |

---

## 手順

### 1. hostsファイルの編集（ドメインとIPの紐付け）

Windowsの `hosts` ファイルを編集し、テスト用ドメインをローカルIP（`127.0.0.1`）に関連付けます。

1. テキストエディタ（メモ帳やVS Codeなど）を**管理者権限で実行**します。
2. 次のファイルを開きます:
   ```plaintext
   C:\Windows\System32\drivers\etc\hosts
   ```
3. ファイルの末尾に以下の設定を追加して保存します:
   ```plaintext
   127.0.0.1 xxx.jp.test
   127.0.0.1 yyy.jp.test
   ```

**動作確認:**  
コマンドプロンプトを開き、以下のコマンドを実行して `127.0.0.1` から応答が返ることを確認します。
```bash
ping xxx.jp.test
```

---

### 2. 公開用フォルダの作成（DocumentRootの準備）

各ドメイン用の公開フォルダとテスト用ファイルを準備します。

1. XAMPPの `htdocs` 配下に以下のフォルダを作成します:
   - `C:\xampp\htdocs\xxx.jp`
   - `C:\xampp\htdocs\yyy.jp`
2. 各フォルダ内に、動作確認用の `index.html`（または `index.php`）を配置します。
   - 例: `C:\xampp\htdocs\xxx.jp\index.html` に「wakobussan.jp テストページ」と記載

**動作確認:**  
エクスプローラーで各フォルダとファイルが正しく存在していることを確認します。

---

### 3. Apacheのバーチャルホスト設定（httpd-vhosts.confの編集）

Apacheで各ドメインへのアクセスを振り分けるためのバーチャルホスト設定を行います。

1. 次の設定ファイルを開きます:
   ```plaintext
   C:\xampp\apache\conf\extra\httpd-vhosts.conf
   ```
2. ファイルの末尾に以下の設定を追記して保存します:
   ```apache
   <VirtualHost *:80>
       DocumentRoot "C:/xampp/htdocs"
       ServerName localhost
   </VirtualHost>

   <VirtualHost *:80>
       DocumentRoot "C:/xampp/htdocs/xxx.jp"
       ServerName xxx.jp.test
   </VirtualHost>

   <VirtualHost *:80>
       DocumentRoot "C:/xampp/htdocs/yyy.jp"
       ServerName yyy.jp.test
   </VirtualHost>
   ```

> [!NOTE]
> 既存の `localhost` へのアクセスを維持するため、先頭に `localhost` 用の `<VirtualHost>` 設定を含めています。

---

### 4. Apacheの再起動とブラウザ確認

設定を反映させるためにApacheを再起動し、ブラウザで表示確認を行います。

1. **XAMPP Control Panel** を開きます。
2. Apacheの **「Stop」** ボタンを押し、停止後に **「Start」** ボタンを押して再起動します。
3. ブラウザを開き、各URLにアクセスします:
   - [http://xxx.jp.test](http://xxx.jp.test)
   - [http://yyy.jp.test](http://yyy.jp.test)

**動作確認:**  
それぞれのURLで、手順2で配置した対応するフォルダ内のファイルが表示されれば設定完了です。
