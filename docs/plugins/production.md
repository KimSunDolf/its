---
sidebar_position: 5
---

# 本番環境

## プラグインを本番環境に適用する

## レート制限

公開された API エンドポイントにレート制限を実装することを検討してください。現在、規模は限られていますが、ChatGPT は広く使用されており、多数のリクエストを受け取ることが予想されます。リクエストの数を監視し、それに応じて制限を設定できます。

## プラグインを更新する

プラグインが承認され、ChatGPT ユーザーがアクセスできるようになった後、いつでもマニフェスト ファイルを更新する必要がある場合があります。ChatGPT は、リクエストが行われると定期的に OpenAPI 仕様を取得します。マニフェスト ファイルを手動で更新するには、プラグイン ストアに移動し、「独自のプラグインを開発する」プロセスをもう一度実行します。

## プラグイン規約

プラグインを登録するには、[プラグインの利用規約](http://openai.com/policies/plugin-terms)に同意する必要があります。

## ドメインの検証とセキュリティ

プラグインが制御するリソースに対してのみ操作を実行できるようにするために、OpenAI はプラグインのマニフェストと API 仕様に要件を適用します。

### プラグインのルートドメイン名を定義

マニフェスト ファイルは、ユーザーに表示される情報 (ロゴや連絡先情報など) と、プラグインの OpenAPI 仕様の URL を定義します。マニフェスト（マニフェスト）を取得すると、以下のルールに従ってプラグインとして使用されます`根域名`。

-   `www.`をサブドメイン名として使用する場合は、`ルートドメイン`は、`リスト(manifest)ファイルがあるドメイン`から`www.`を取り除いた後の部分になります
-   その他の場合は `ルートドメイン`と`リスト(manifest)ファイルがあるドメイン`と同じ。

リダイレクトに関する注意: マニフェストの解析中にリダイレクトがあった場合、現在のドメイン名の子サブドメインへのリダイレクトのみが許可されます。唯一の例外は、www サブドメインから www なしのドメインにリダイレクトできることです。

ルート ドメイン名の例:

-   ✅`https://example.com/.well-known/ai-plugin.json`
    -   ルート ドメイン名:`example.com`
-   ✅`https://www.example.com/.well-known/ai-plugin.json`
    -   ルート ドメイン名:`example.com`
-   ✅ `https://www.example.com/.well-known/ai-plugin.json`→ リダイレクト`https://example.com/.well-known/ai-plugin.json`
    -   ルート ドメイン名:`example.com`
-   ✅ `https://foo.example.com/.well-known/ai-plugin.json`→ リダイレクト`https://bar.foo.example.com/.well-known/ai-plugin.json`
    -   ルート ドメイン名:`bar.foo.example.com`
-   ✅ `https://foo.example.com/.well-known/ai-plugin.json`→ リダイレクト`https://bar.foo.example.com/baz/ai-plugin.json`
    -   ルート ドメイン名:`bar.foo.example.com`
-   ❌ `https://foo.example.com/.well-known/ai-plugin.json`→ リダイレクト`https://example.com/.well-known/ai-plugin.json`
    -   親ドメインへのリダイレクトを許可しない
-   ❌ `https://foo.example.com/.well-known/ai-plugin.json`→ リダイレクト`https://bar.example.com/.well-known/ai-plugin.json`
    -   同じレベルのサブドメインへのリダイレクトを許可しない
-   ❌ `https://example.com/.well-known/ai-plugin.json`→ リダイレクト`https://example2.com/.well-known/ai-plugin.json`
    -   別のドメインへのリダイレクトは許可されていません

### マニフェストの検証

マニフェスト自体の特定のフィールドは、次の要件を満たす必要があります。

-   `api.url`\- OpenAPI 仕様に提供される URL は、ルート ドメインと同じレベルまたはサブドメインでホストされている必要があります。
-   `legal_info`\-指定された URL の第 2 レベル ドメインは、ルート ドメイン名の[第 2 レベル](https://en.wikipedia.org/wiki/Second-level_domain)ドメインと同じである必要があります。
-   `contact_info`\-電子メール アドレスの第 2 レベル ドメインは、ルート ドメイン名の第 2 レベル ドメインと同じである必要があります。

### API仕様の解析

リスト(manifest)のフィールドはOpenAPI仕様へのリンクを提供し、プラグインが呼び出すことができるAPIを定義します。OpenAPIは複数の[サーバーBase URL](https://swagger.io/docs/specification/api-host-and-base-path/)を指定することを許可します。以下のロジックはサーバーURLの選択に使われます：`api.url`

-そのドメインとルートドメインが完全に一致するサーバーURLがある場合、そのURLを使用します。

-そうでなければ、ルートドメインのサブドメインとしての最初のサーバーURLを使用してください。

- 上記の2つの状況が適用されない場合、デフォルトはAPI仕様のあるドメインです。例えば、もし仕様が`api.example.com`にホストされている場合、`api.example.com`はOpenAPI仕様のルーティングの基本URLとして使われます。

注意:リダイレクトを使ってAPI仕様といかなるAPI Endpoint(特定のAPIを指す)をホストすることを避けてください。なぜなら、常にリダイレクトに従うことは保証できないからです。

### TLSとHTTPSを使用する

プラグインのすべてのトラフィック(例えば、`ai-plugin.json`ファイル、OpenAPI仕様、API呼び出し)はTLS 1.2またはそれ以上のバージョンと有効な公共証明書(public certificate)を使用する必要があります。

### IP 出力ポートの範囲

ChatGPTは[CIDRブロック](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) `23.102.140.112/28から`の中のIPアドレスがあなたのプラグインを呼び出します。あなたはこれらのIPアドレスを許可リストに明確に含めたいかもしれません。

OpenAIのウェブ閲覧プラグインについては、`23.98.142.176/28`IPアドレスブロックを通じてウェブサイトを呼び出します。

## よくある問題

### プラグインデータをどう使いますか?

プラグインはChatGPTを外部アプリケーションに接続します。もしユーザーがプラグインを有効にした場合、ChatGPTは彼らの会話の一部と彼らの国や州をあなたのプラグインに送る可能性があります。

###　もし私のAPIへのリクエストが失敗したらどうなりますか?

API リクエストが失敗した場合、モデルはリクエストを最大 10 回再試行してから、プラグインからのレスポンスを取得できないことをユーザーに通知します。

### プラグインを試すように人を招待できますか? 

はい、検証されていないプラグインはすべて、最大 15 人のユーザーがインストールできます。起動時には、アクセス権を持つ他の開発者のみがプラグインをインストールできます。時間の経過とともにアクセスを拡大し、最終的にはすべてのユーザーがプラグインを利用できるようにする前に、レビューのためにプラグインを送信するプロセスを展開する予定です.