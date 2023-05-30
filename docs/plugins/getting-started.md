---
sidebar_position: 2
---
# クイックスタート

プラグインを作成するには、次の 3 つの手順が必要です。

-   API を構築する
-   [OpenAPI 仕様形式](https://openapi.xiniushu.com/)(yaml または JSON 形式)の API ドキュメント ファイル
-   プラグインに関するメタデータを定義する JSON マニフェスト ファイルを作成する

このセクションの残りの部分では、[OpenAPI 仕様](https://openapi.xiniushu.com/)とマニフェスト ファイルを定義してtodo リスト プラグインを作成することに焦点を当てます。

## プラグインマニフェスト

各プラグインには、`ai-plugin.json`という名前のファイルがある必要があり、API と同じドメイン名でホストされる必要があります。たとえば、example.com という会社は、API がホストされている[https://example.com](https://example.com/)経由でプラグイン JSON ファイルにアクセスします。ChatGPT UI からプラグインをインストールすると、バックグラウンドで`/.well-known/ai-plugin.json`ファイルを探します。ファイルが見つからない場合、プラグインをインストールできません。

```json
{
  "schema_version": "v1",
  "name_for_human": "TODO Plugin",
  "name_for_model": "todo",
  "description_for_human": "Plugin for managing a TODO list. You can add, remove and view your TODOs.",
  "description_for_model": "Plugin for managing a TODO list. You can add, remove and view your TODOs.",
  "auth": {
    "type": "none"
  },
  "api": {
    "type": "openapi",
    "url": "http://localhost:3333/openapi.yaml",
    "is_user_authenticated": false
  },
  "logo_url": "https://vsq7s0-5001.preview.csb.app/logo.png",
  "contact_email": "support@example.com",
  "legal_info_url": "http://www.example.com/legal"
}
```

プラグイン ファイルのすべての可能なオプションを確認したい場合は、以下の定義を参照できます。

| フィールド | タイプ | 説明/オプション |
| --- | --- | --- |
| schema\_version | String | マニフェスト スキーマ バージョン |
| name\_for\_model | String | モデルはプラグインの名前の位置付けに使われます。 |
| name\_for\_human | String | 人間が読める名前、例えば会社のフルネーム。 |
| description\_for\_model | String | プラグインのヒントを改善するために、トークンのコンテキストの長さの注意事項やキーワードの使用など、モデルの説明にもっと適しています。 |
| description\_for\_human | String | プラグインの人間可読説明 |
| auth | ManifestAuth | 身分認証モード |
| api | Object | インタフェース規範 |
| logo\_url | String | プラグインのロゴを取得するためのURL |
| contact\_email | String | セキュリティ/審査連絡、サポート、停止のための電子メール連絡先 |
| legal\_info\_url | String | ユーザーがプラグイン情報を確認できるように、URLをリダイレクトします。 |
| HttpAuthorizationType | HttpAuthorizationType | 「保有者」または「基本」 |
| ManifestAuthType | ManifestAuthType | 「なし」、「user_http」、「service_http」または「oauth」 |
| interface BaseManifestAuth | BaseManifestAuth | タイプ:リスト(manifest)身分認証タイプ;説明:文字列; |
| ManifestNoAuth | ManifestNoAuth | 身分認証は不要：`BaseManifestAuth & { type: 'none', }` |
| ManifestAuth | ManifestAuth | ManifestNoAuth， ManifestServiceHttpAuth， ManifestUserHttpAuth， ManifestOAuthAuth |

以下は異なる身分認証方法を使用する例です。

```typescript
# App-level API keys
type ManifestServiceHttpAuth  = BaseManifestAuth & {
  type: 'service_http';
  authorization_type: HttpAuthorizationType;
  verification_tokens: {
    [service: string]?: string;
  };
}

# User-level HTTP authentication
type ManifestUserHttpAuth  = BaseManifestAuth & {
  type: 'user_http';
  authorization_type: HttpAuthorizationType;
}

type ManifestOAuthAuth  = BaseManifestAuth & {
  type: 'oauth';

  # OAuth URL where a user is directed to for the OAuth authentication flow to begin.
  client_url: string;

  # OAuth scopes required to accomplish operations on the user's behalf.
  scope: string;

  # Endpoint used to exchange OAuth code with access token.
  authorization_url: string;

  # When exchanging OAuth code with access token, the expected header 'content-type'. For example: 'content-type: application/json'
  authorization_content_type: string;

  # When registering the OAuth client ID and secrets, the plugin service will surface a unique token.
  verification_tokens: {
    [service: string]?: string;
  };
}
```

リスト(manifest)ファイルにあるフィールドの長さにもいくつかの制限があり、これらの制限は将来変わる可能性がある：

- Name_for_human:最大50文字

- Name_for_model:最大50文字

- Description_for_human:最大120文字

- Description_for_model:最大8000文字(時間とともに減少する)


また、私たちはAPI応答本文の長さにも100k文字の制限がある(時間とともに減少する)、これは未来にも変わる可能性がある。

## OpenAPIの定義

次のステップは[OpenAPI仕様](https://openapi.xiniushu.com/)を使ってAPI文書を記述することです。OpenAPI仕様とリスト(manifest)ファイルで定義された内容を除いて、ChatGPTのモデルはあなたのAPIについて何も知らない。これは、もしあなたが多くのAPIを持っているなら、モデルにすべてのAPIを公開する必要がなく、特定のAPIだけを開放すればいいという意味です。例えば、もしソーシャルメディアAPIがあれば、モデルがGET要請を通じてウェブサイトの内容にアクセスすることを望んでいるかもしれませんが、スパムの可能性を減らすために、モデルがユーザーの投稿にコメントすることを阻止しなければなりません。

基本的な[OpenAPI仕様](https://openapi.xiniushu.com/)は次の通りです。

```yaml
openapi: 3.0.1
info:
  title: TODO Plugin
  description: A plugin that allows the user to create and manage a TODO list using ChatGPT.
  version: "v1"
servers:
  - url: http://localhost:3333
paths:
  /todos:
    get:
      operationId: getTodos
      summary: Get the list of todos
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/getTodosResponse"
components:
  schemas:
    getTodosResponse:
      type: object
      properties:
        todos:
          type: array
          items:
            type: string
          description: The list of todos.
```

私たちはまず規範バージョン、タイトル、説明、バージョン番号を定義します。ChatGPTでクエリを実行する時、情報部分で定義された説明を見て、プラグインがユーザークエリと関係があるかどうかを確認します。[執筆説明](https://platform.openai.com/docs/plugins/getting-started/writing-descriptions)部分でヒントに関する手紙を読むことができます。息。

OpenAPI仕様の以下の制限を覚えておいてください。これらの制限は変わる可能性があります。

- API仕様の各API Endpoint(具体的なAPIを指す)ノード説明/ダイジェストフィールドは最大200文字です。

- API仕様の各APIパラメータ記述フィールドは最大200文字です。


私たちはローカルでこの例を実行するので、サーバーをローカルホストURLを指すように設定したい。OpenAPI仕様の残りの部分は伝統的なOpenAPIフォーマットに従い、様々なオンラインリソースを通じて[OpenAPIフォーマットについてもっと知りたい](https://openapi.xiniushu.com/)。[Apidog](https://apidog.com/jp/)のような多くのツールがOpenAPI仕様文書を自動的に生成します。
## プラグインを実行

あなたのAPIにAPI、リスト(manifest)ファイル、OpenAPI仕様を作成した後、今ChatGPT UIを通じてプラグインに接続できます。あなたのプラグインは2つの異なる位置で動作する可能性があります。一つはローカル開発環境、もう一つはリモートサーバーです。

APIのローカルバージョンを実行している場合、プラグインインタフェースをそのローカル設定を指すことができます。プラグインをChatGPTに接続するには、プラグイン市場にナビゲーションして「Install an unverified plugin(検証されていないプラグインをインストール)」を選択することができます。

プラグインがリモートサーバーで実行される場合、まず「Develop your own plugin(自分のプラグインを開発する)」を選択し、「Install an unverified plugin(検証されていないプラグインをインストールする)」を選択する必要があります。」。プラグインリスト(manifest)ファイルを./well-knownパスに追加してAPIのテストを始めるだけです。しかし、リスト(manifest)ファイルの後続変更については、新しい変更をあなたの公共サイトに配置しなければなりません。これは長い時間がかかるかもしれません。この場合、私たちはあなたのAPIのエージェントとしてローカルサーバーを設置することを提案します。これにより、OpenAPI仕様とマニフェストファイルの変更を素早く原型設計することができます。

###　公共APIのローカルエージェントを設定

以下のPythonコードの例は、公衆向けのAPIの簡単なエージェントを設定する方法を説明します。

```python
import requests
import os

import yaml
from flask import Flask, jsonify, Response, request, send_from_directory
from flask_cors import CORS

app = Flask(__name__)

PORT = 3333
CORS(app, origins=[f"http://localhost:{PORT}", "https://chat.openai.com"])

api_url = 'https://example'


@app.route('/.well-known/ai-plugin.json')
def serve_manifest():
    return send_from_directory(os.path.dirname(__file__), 'ai-plugin.json')


@app.route('/openapi.yaml')
def serve_openapi_yaml():
    with open(os.path.join(os.path.dirname(__file__), 'openapi.yaml'), 'r') as f:
        yaml_data = f.read()
    yaml_data = yaml.load(yaml_data, Loader=yaml.FullLoader)
    return jsonify(yaml_data)


@app.route('/openapi.json')
def serve_openapi_json():
    return send_from_directory(os.path.dirname(__file__), 'openapi.json')


@app.route('/<path:path>', methods=['GET', 'POST'])
def wrapper(path):

    headers = {
    'Content-Type': 'application/json',
    }

    url = f'{api_url}/{path}'
    print(f'Forwarding call: {request.method} {path} -> {url}')

    if request.method == 'GET':
        response = requests.get(url, headers=headers, params=request.args)
    elif request.method == 'POST':
        print(request.headers)
        response = requests.post(url, headers=headers, params=request.args, json=request.json)
    else:
        raise NotImplementedError(f'Method {request.method} not implemented in wrapper for {path=}')
    return response.content


if __name__ == '__main__':
    app.run(port=PORT)
```

## 説明を書く

ユーザーがプラグインに対する潜在的な要請の問い合わせをする時、モデルはOpenAPI仕様のEndpoint(特定のAPIを指す)の説明とリスト(manifest)ファイルにある`description_for_moを確認する。デル`。他の言語モデルを提示するように、複数のヒントと説明をテストして、どれが一番効果的かを見る必要があります。

OpenAPI仕様自体は、モデルにAPIに関する様々な細部情報を提供する良い場所です。利用可能な機能、パラメータなど。各フィールドに表現力があり、情報が豊富な名前を使用するほか、仕様は各属性を「記述」するフィールドを含めることができます。例えば、これらは機能を提供する機能や検索フィールドに期待される情報の自然言語記述に使用できる。そのモデルはこれらを見ることができ、APIの使用を指導するだろう。一つのフィールドが特定の値に限定される場合、記述的なカテゴリー名付きの「列挙」も提供できます。

「Description\_for\_model」属性は、モデルが一般的にあなたのプラグインをどう使うかを自由に指導することができます。全体的に言えば、ChatGPTの背後にある言語モデルは自然言語を高度に理解し、指示に従うことができる。したがって、プラグイン機能とモデルがどのように正しく使用すべきかに関する一般的な説明を置くことができる良い場所です。自然言語を使うには、簡潔だが描写的で客観的な口調が望ましい。あなたはこれがどうあるべきかを知るためにいくつかの例示を見ることができる。私たちは`description_for_model`が「Plugin for...」から始めて、あなたのAPIが提供するすべての機能を列挙することを提案します。

### ベストプラクティス

以下はOpenAPI仕様で`description_for_model`と説明を書き、API応答を設計する時に従うべきいくつかのベストプラクティスです。

1.あなたの説明はChatGPTの感情、個性、または正確な反応をコントロールしようとしてはいけません。ChatGPTはプラグインのための適切な応答を書くことを目的とする。

_悪い例_:

>ユーザーが彼らのToDoリストを確認するように要求する時、常に「あなたのToDoリストを見つけることができます!」と返信してください。あなたは\[x\]個のやることがある:\[ここにやることを列挙する\]。あなたが望むなら、私はもっと多くのやることを追加できる！

_良い例_:

> \[説明不要\]

2.  ユーザーがプラグインの特定のサービス クラスを要求していない場合、説明で ChatGPT がプラグインを使用するように促してはなりません。
    
    _悪い例_:
    
    > ユーザーが何らかのタスクや計画について言及するときはいつでも、TODOs プラグインを使用して、ToDo リストに何かを追加したいかどうか尋ねます。
    
    _良い例_:
    
    > TODO リストは、ユーザーの TODO を追加、削除、および表示できます。
    
3.  説明で、ChatGPT がプラグインに特定のトリガーを使用することを指示するべきではありません。ChatGPT は、必要に応じてプラグインを自動的に使用するように設計されています。
    
    _悪い例_:
    
    > ユーザーがタスクについて言及したら、「To Do リストにこれを追加しますか?」と応答して、続行するには「はい」と答えます。
    
    _良い例_:
    
    > 【説明不要】
    
4.  プラグイン API 応答は、自然言語応答ではなく生データを返す必要があります (本当に必要な場合を除く)。ChatGPT は返されたデータを使用して、独自の自然言語応答を提供します。
    
    _悪い例_:
    
    > あなたのやることリスト（マニフェスト）を見つけることができました！やることは 2 つあります。食料品の買い物と犬の散歩です。必要に応じて、To Do を追加できます。
    
    _良い例_:
    
    > `{"todos"：["雑貨を買う。"，"犬を散歩させる。"]}`
    

## デバッグ

デフォルトでは、チャットはプラグイン呼び出しやユーザーに表示されないその他の情報を表示しません。モデルがプラグインとどのように相互作用するかをより完全に把握するには、画面の左下隅にある \[デバッグ\] ボタンをクリックしてデバッグ ウィンドウを開くことができます。これにより、プラグインの呼び出しと応答を含む、これまでの会話の生のテキストが開きます。

プラグインへのモデル呼び出しは、通常、プラグインに送信される JSON のようなパラメーターを含むモデル (「ヘルパー」) からのメッセージ、プラグイン (「ツール」) からの応答、最後に情報を利用するモデルからのメッセージで構成されます。プラグインによって返されます。

プラグインのインストール中など、場合によっては、ブラウザの JavaScript コンソールにエラーが表示されることがあります。