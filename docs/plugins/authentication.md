---
sidebar_position: 3
---
# プラグイン認証 (Authentication)

## プラグイン認証 (Authentication)

プラグインは、さまざまなユースケースに合わせて複数の認証モードを提供します。プラグインの認証モードを指定するには、マニフェスト ファイルを使用します。当社の[プラグイン](https://openai.xiniushu.com/docs/plugins/production#domain-verification-and-security)ドメイン ポリシーは、ドメイン セキュリティに対処するための当社の戦略を概説しています。使用可能な認証オプションの例については、 [「例」](https://platform.openai.com/docs/plugins/examples)。さまざまな選択肢がすべて示されています。

## 身分なしの認証

認証を必要としないアプリの認証なしフローがサポートされており、ユーザーは制限なしで API に直接リクエストを送信できます。これは、OpenAI プラグイン リクエスト以外のソースからのトラフィックを許可するため、誰でも利用できるようにしたいオープン API がある場合に特に便利です。

```json
"auth": {
  "type": "none"
},
```

## サービスレベル

OpenAI プラグインが API を使用できるようにする場合は、プラグインのインストール プロセス中にクライアント シークレットを指定できます。つまり、OpenAI プラグインからのすべてのトラフィックは認証されますが、ユーザー レベルでは認証されません。このプロセスは、シンプルなエンド ユーザー エクスペリエンスの恩恵を受けますが、API の観点からはあまり制御できません。

-   まず、開発者はアクセストークン（アクセストークン）（グローバルキー）を取得します
-   次に、開発者はアクセス トークンをマニフェスト ファイルに追加します。
-   暗号化されたアクセストークン（アクセストークン）を保管します
-   ユーザーはプラグインをインストールするために何もする必要はありません
-   最後に、プラグインがインターフェイス リクエストを送信すると、ヘッダーに Authorization が含まれます ("Authorization": " \[Bearer/Basic\]\[user's token\] ")。

```json
"auth": {
  "type": "service_http",
  "authorization_type": "bearer",
  "verification_tokens": {
    "openai": "cb7cdfb8a57e45bc8ad7dea5bc2f8324"
  }
},
```

## ユーザーレベル

ユーザーが既に API を使用している場合と同様に、エンド ユーザーはプラグインのインストール中に API キーをコピーして ChatGPT UI に貼り付け、ユーザー レベルの認証を有効にすることができます。キーをデータベースに保存する際に暗号化しますが、ユーザー エクスペリエンスが低下するため、このアプローチはお勧めしません。

-   まず、ユーザーはプラグインをインストールするときにアクセストークンを貼り付けます
-   暗号化されたアクセストークン（アクセストークン）を保管します
-   最後に、プラグインがインターフェイス リクエストを送信すると、ヘッダーに Authorization が含まれます ("Authorization": " \[Bearer/Basic\]\[user's token\] ")。

```json
"auth": {
  "type": "user_http",
  "authorization_type": "bearer",
},
```

## OAuth

プラグインプロトコルはOAuthと互換性があります。私たちがリスト(manifest)で期待するOAuthストリームの簡単な例は次の通りです。

- まず、開発者はそのOAuth client idとclient secretを貼り付けます。


　　その後、彼らは検証トークン(verification token)をそのリスト(manifest)ファイルに追加しなければならない。

- 私たちは暗号化されたクライアントキー(client secret)を保存します。

- ユーザーはプラグインをインストールする時、プラグインのウェブサイトを通じてログインします。


　　これはユーザーにOAuthアクセストークン(そしてオプションのrefresh token)を提供し、それを暗号化して保存します。

- 最後に、プラグインがインタフェースのリクエストを出す時、HeaderにAuthorization ("Authorization": "[Bearer/Basic]\[user’s token]")を付属します。

```json
"auth": {
  "type": "oauth",
  "client_url": "https://my_server.com/authorize",
  "scope": "",
  "authorization_url": "https://my_server.com/token",
  "authorization_content_type": "application/json",
  "verification_tokens": {
    "openai": "abc123456"
  }
},
```

OAuth の URL 構造をよりよく理解するために、フィールドの簡単な説明を次に示します。

-   ChatGPT でプラグインを設定すると、OAuth`client_id`と`client_secret`の提供を要求します。
-   ユーザーがプラグインにログインすると、ChatGPT はユーザーのブラウザを`"[client_url]?response_type=code&client_id=[client_id]&scope=[scope]&redirect_uri=https%3A%2F%2Fchat.openai.com%2Faip%2F[plugin_id]%2Foauth%2Fcallback"`に自動的にリダイレクトします。
-   プラグインが指定された redirect\_uri にリダイレクトされた後、ChatGPTは`authorization_url`にcontent-typeが`authorization_content_type`で、パラメータが`{ “grant_type”: “authorization_code”, “client_id”: [client_id], “client_secret”: [client_secret], “code”: [the code that was returned with the redirect], “redirect_uri”: [the same redirect uri as before] }`での POST リクエストを送信することにより、OAuth フローを完了します。