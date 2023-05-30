---
sidebar_position: 4
---
# プラグインの例

## プラグインの例

構築を開始するには、さまざまな認証モードとユースケースをカバーする一連のシンプルなプラグインを提供します。シンプルな認証なしの To-Do リスト プラグインから、より堅牢な検索プラグインまで、これらの例は、プラグインで実現したいことを垣間見せてくれます。

開発中は、プラグインを[コンピューターでローカルに実行](https://platform.openai.com/docs/plugins/getting-started/running-a-plugin)するか、GitHub Codespaces、Replit、CodeSandbox などのクラウド開発環境を介して実行できます。

## 認証なしで簡単な todo リストプラグインを作成する方法を学ぶ

まず、次のフィールドを含む manifest.json ファイルを定義します。

```json
{
  "schema_version": "v1",
  "name_for_human": "TODO Plugin (no auth)",
  "name_for_model": "todo",
  "description_for_human": "Plugin for managing a TODO list, you can add, remove and view your TODOs.",
  "description_for_model": "Plugin for managing a TODO list, you can add, remove and view your TODOs.",
  "auth": {
    "type": "none"
  },
  "api": {
    "type": "openapi",
    "url": "PLUGIN_HOSTNAME/openapi.yaml",
    "is_user_authenticated": false
  },
  "logo_url": "PLUGIN_HOSTNAME/logo.png",
  "contact_email": "dummy@email.com",
  "legal_info_url": "http://www.example.com/legal"
}
```

次に、いくつかの単純な Python エンドポイントを定義して、特定のユーザーの To Do リスト項目を作成、削除、および取得します。

```python
import json

import quart
import quart_cors
from quart import request

app = quart_cors.cors(quart.Quart(__name__), allow_origin="https://chat.openai.com")

_TODOS = {}


@app.post("/todos/<string:username>")
async def add_todo(username):
    request = await quart.request.get_json(force=True)
    if username not in _TODOS:
        _TODOS[username] = []
    _TODOS[username].append(request["todo"])
    return quart.Response(response='OK', status=200)


@app.get("/todos/<string:username>")
async def get_todos(username):
    return quart.Response(response=json.dumps(_TODOS.get(username, [])), status=200)


@app.delete("/todos/<string:username>")
async def delete_todo(username):
    request = await quart.request.get_json(force=True)
    todo_idx = request["todo_idx"]
    # fail silently, it's a simple plugin
    if 0 <= todo_idx < len(_TODOS[username]):
        _TODOS[username].pop(todo_idx)
    return quart.Response(response='OK', status=200)


@app.get("/logo.png")
async def plugin_logo():
    filename = 'logo.png'
    return await quart.send_file(filename, mimetype='image/png')


@app.get("/.well-known/ai-plugin.json")
async def plugin_manifest():
    host = request.headers['Host']
    with open("manifest.json") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/json")


@app.get("/openapi.yaml")
async def openapi_spec():
    host = request.headers['Host']
    with open("openapi.yaml") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/yaml")


def main():
    app.run(debug=True, host="0.0.0.0", port=5002)


if __name__ == "__main__":
    main()
```

最後に、ローカルまたはリモート サーバーで定義されたエンドポイント (特定の API を参照) に一致するように OpenAPI 仕様を設定および定義する必要があります。仕様を通じて API の全機能を公開する代わりに、ChatGPT に特定の機能のみへのアクセスを許可することを選択できます。

サーバー定義コードを OpenAPI 仕様に自動的に変換するツールもいくつかあるので、手動で行う必要はありません。上記の Python コードの場合、OpenAPI 仕様は次のようになります。

```yaml
openapi: 3.0.1
info:
  title: TODO Plugin
  description: A plugin that allows the user to create and manage a TODO list using ChatGPT. If you do not know the user's username, ask them first before making queries to the plugin. Otherwise, use the username "global".
  version: 'v1'
servers:
  - url: PLUGIN_HOSTNAME
paths:
  /todos/{username}:
    get:
      operationId: getTodos
      summary: Get the list of todos
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/getTodosResponse'
    post:
      operationId: addTodo
      summary: Add a todo to the list
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/addTodoRequest'
      responses:
        "200":
          description: OK
    delete:
      operationId: deleteTodo
      summary: Delete a todo from the list
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/deleteTodoRequest'
      responses:
        "200":
          description: OK

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
    addTodoRequest:
      type: object
      required:
      - todo
      properties:
        todo:
          type: string
          description: The todo to add to the list.
          required: true
    deleteTodoRequest:
      type: object
      required:
      - todo_idx
      properties:
        todo_idx:
          type: integer
          description: The index of the todo to delete.
          required: true
```

## サービス レベル認証を使用してシンプルな todoリストプラグインを作成する方法を学ぶ

まず、次のフィールドを含む manifest.json ファイルを定義します。

```json
{
  "schema_version": "v1",
  "name_for_human": "TODO Plugin (service http)",
  "name_for_model": "todo",
  "description_for_human": "Plugin for managing a TODO list, you can add, remove and view your TODOs.",
  "description_for_model": "Plugin for managing a TODO list, you can add, remove and view your TODOs.",
  "auth": {
    "type": "service_http",
    "authorization_type": "bearer",
    "verification_tokens": {
      "openai": "758e9ef7984b415688972d749f8aa58e"
    }
  },
   "api": {
    "type": "openapi",
    "url": "https://example.com/openapi.yaml",
    "is_user_authenticated": false
  },
  "logo_url": "https://example.com/logo.png",
  "contact_email": "dummy@email.com",
  "legal_info_url": "http://www.example.com/legal"
}
```

サービス レベル認証プラグインには認証トークンが必要であることに注意してください。このトークンは、ChatGPT Web UI でのプラグインのインストール中に生成されます。

次に、特定のユーザーの todo リスト項目を作成、削除、および取得するためのいくつかの単純な Python エンドポイントを定義できます。エンドポイントは、認証が必要なため、簡単な認証チェックも行います。

```python
import json

import quart
import quart_cors
from quart import request

app = quart_cors.cors(quart.Quart(__name__), allow_origin="https://chat.openai.com")

_SERVICE_AUTH_KEY = "TEST"
_TODOS = {}


def assert_auth_header(req):
    assert req.headers.get(
        "Authorization", None) == f"Bearer {_SERVICE_AUTH_KEY}"


@app.post("/todos/<string:username>")
async def add_todo(username):
    assert_auth_header(quart.request)
    request = await quart.request.get_json(force=True)
    if username not in _TODOS:
        _TODOS[username] = []
    _TODOS[username].append(request["todo"])
    return quart.Response(response='OK', status=200)


@app.get("/todos/<string:username>")
async def get_todos(username):
    assert_auth_header(quart.request)
    return quart.Response(response=json.dumps(_TODOS.get(username, [])), status=200)


@app.delete("/todos/<string:username>")
async def delete_todo(username):
    assert_auth_header(quart.request)
    request = await quart.request.get_json(force=True)
    todo_idx = request["todo_idx"]
    # fail silently, it's a simple plugin
    if 0 <= todo_idx < len(_TODOS[username]):
        _TODOS[username].pop(todo_idx)
    return quart.Response(response='OK', status=200)


@app.get("/logo.png")
async def plugin_logo():
    filename = 'logo.png'
    return await quart.send_file(filename, mimetype='image/png')


@app.get("/.well-known/ai-plugin.json")
async def plugin_manifest():
    host = request.headers['Host']
    with open("manifest.json") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/json")


@app.get("/openapi.yaml")
async def openapi_spec():
    host = request.headers['Host']
    with open("openapi.yaml") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/yaml")


def main():
    app.run(debug=True, host="0.0.0.0", port=5002)


if __name__ == "__main__":
    main()
```

最後に、ローカルまたはリモート サーバーで定義されたエンドポイントと一致するように、OpenAPI 仕様をセットアップおよび定義する必要があります。一般に、OpenAPI の仕様は、認証方法に関係なく同じように見えます。自動 OpenAPI ジェネレーターを使用すると、OpenAPI 仕様を作成するときにエラーが発生する可能性が減るため、これらのオプションを検討する価値があります。

```yaml
openapi: 3.0.1
info:
  title: TODO Plugin
  description: A plugin that allows the user to create and manage a TODO list using ChatGPT. If you do not know the user's username, ask them first before making queries to the plugin. Otherwise, use the username "global".
  version: 'v1'
servers:
  - url: https://example.com
paths:
  /todos/{username}:
    get:
      operationId: getTodos
      summary: Get the list of todos
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/getTodosResponse'
    post:
      operationId: addTodo
      summary: Add a todo to the list
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/addTodoRequest'
      responses:
        "200":
          description: OK
    delete:
      operationId: deleteTodo
      summary: Delete a todo from the list
      parameters:
      - in: path
        name: username
        schema:
            type: string
        required: true
        description: The name of the user.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/deleteTodoRequest'
      responses:
        "200":
          description: OK

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
    addTodoRequest:
      type: object
      required:
      - todo
      properties:
        todo:
          type: string
          description: The todo to add to the list.
          required: true
    deleteTodoRequest:
      type: object
      required:
      - todo_idx
      properties:
        todo_idx:
          type: integer
          description: The index of the todo to delete.
          required: true
```

## シンプルなスポーツ統計プラグインを作成する方法を学ぶ

このプラグインは、単純なスポーツ統計 API の例です。何を構築するかを検討する際は、ドメイン ポリシーと使用ポリシーを念頭に置いてください。

まず、私たちは manifest.jsonファイルを定義できます。

```json
{
  "schema_version": "v1",
  "name_for_human": "Sport Stats",
  "name_for_model": "sportStats",
  "description_for_human": "Get current and historical stats for sport players and games.",
  "description_for_model": "Get current and historical stats for sport players and games. Always display results using markdown tables.",
  "auth": {
    "type": "none"
  },
  "api": {
    "type": "openapi",
    "url": "PLUGIN_HOSTNAME/openapi.yaml",
    "is_user_authenticated": false
  },
  "logo_url": "PLUGIN_HOSTNAME/logo.png",
  "contact_email": "dummy@email.com",
  "legal_info_url": "http://www.example.com/legal"
}
```



次に、簡単なスポーツサービスプラグインのためにシミュレーションAPIを定義します。

```python
import json
import requests
import urllib.parse

import quart
import quart_cors
from quart import request

app = quart_cors.cors(quart.Quart(__name__), allow_origin="https://chat.openai.com")
HOST_URL = "https://example.com"

@app.get("/players")
async def get_players():
    query = request.args.get("query")
    res = requests.get(
        f"{HOST_URL}/api/v1/players?search={query}&page=0&per_page=100")
    body = res.json()
    return quart.Response(response=json.dumps(body), status=200)


@app.get("/teams")
async def get_teams():
    res = requests.get(
        "{HOST_URL}/api/v1/teams?page=0&per_page=100")
    body = res.json()
    return quart.Response(response=json.dumps(body), status=200)


@app.get("/games")
async def get_games():
    query_params = [("page", "0")]
    limit = request.args.get("limit")
    query_params.append(("per_page", limit or "100"))
    start_date = request.args.get("start_date")
    if start_date:
        query_params.append(("start_date", start_date))
    end_date = request.args.get("end_date")
    
    if end_date:
        query_params.append(("end_date", end_date))
    seasons = request.args.getlist("seasons")
    
    for season in seasons:
        query_params.append(("seasons[]", str(season)))
    team_ids = request.args.getlist("team_ids")
    
    for team_id in team_ids:
        query_params.append(("team_ids[]", str(team_id)))
    print(query_params)
    
    res = requests.get(
        f"{HOST_URL}/api/v1/games?{urllib.parse.urlencode(query_params)}")
    body = res.json()
    return quart.Response(response=json.dumps(body), status=200)


@app.get("/stats")
async def get_stats():
    query_params = [("page", "0")]
    limit = request.args.get("limit")
    query_params.append(("per_page", limit or "100"))
    start_date = request.args.get("start_date")
    if start_date:
        query_params.append(("start_date", start_date))
    end_date = request.args.get("end_date")
    
    if end_date:
        query_params.append(("end_date", end_date))
    player_ids = request.args.getlist("player_ids")
    
    for player_id in player_ids:
        query_params.append(("player_ids[]", str(player_id)))
    game_ids = request.args.getlist("game_ids")
    
    for game_id in game_ids:
        query_params.append(("game_ids[]", str(game_id)))
    res = requests.get(
        f"{HOST_URL}/api/v1/stats?{urllib.parse.urlencode(query_params)}")
    body = res.json()
    return quart.Response(response=json.dumps(body), status=200)


@app.get("/season_averages")
async def get_season_averages():
    query_params = []
    season = request.args.get("season")
    if season:
        query_params.append(("season", str(season)))
    player_ids = request.args.getlist("player_ids")
    
    for player_id in player_ids:
        query_params.append(("player_ids[]", str(player_id)))
    res = requests.get(
        f"{HOST_URL}/api/v1/season_averages?{urllib.parse.urlencode(query_params)}")
    body = res.json()
    return quart.Response(response=json.dumps(body), status=200)


@app.get("/logo.png")
async def plugin_logo():
    filename = 'logo.png'
    return await quart.send_file(filename, mimetype='image/png')


@app.get("/.well-known/ai-plugin.json")
async def plugin_manifest():
    host = request.headers['Host']
    with open("manifest.json") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/json")


@app.get("/openapi.yaml")
async def openapi_spec():
    host = request.headers['Host']
    with open("openapi.yaml") as f:
        text = f.read()
        text = text.replace("PLUGIN_HOSTNAME", f"https://{host}")
        return quart.Response(text, mimetype="text/yaml")


def main():
    app.run(debug=True, host="0.0.0.0", port=5001)


if __name__ == "__main__":
    main()
```



最後に、私たちは私たちのOpenAPI仕様を定義します。

```yaml
openapi: 3.0.1
info:
  title: Sport Stats
  description: Get current and historical stats for sport players and games.
  version: 'v1'
servers:
  - url: PLUGIN_HOSTNAME
paths:
  /players:
    get:
      operationId: getPlayers
      summary: Retrieves all players from all seasons whose names match the query string.
      parameters:
      - in: query
        name: query
        schema:
            type: string
        description: Used to filter players based on their name. For example, ?query=davis will return players that have 'davis' in their first or last name.
      responses:
        "200":
          description: OK
  /teams:
    get:
      operationId: getTeams
      summary: Retrieves all teams for the current season.
      responses:
        "200":
          description: OK
  /games:
    get:
      operationId: getGames
      summary: Retrieves all games that match the filters specified by the args. Display results using markdown tables.
      parameters:
      - in: query
        name: limit
        schema:
            type: string
        description: The max number of results to return.
      - in: query
        name: seasons
        schema:
            type: array
            items:
              type: string
        description: Filter by seasons. Seasons are represented by the year they began. For example, 2018 represents season 2018-2019.
      - in: query
        name: team_ids
        schema:
            type: array
            items:
              type: string
        description: Filter by team ids. Team ids can be determined using the getTeams function.
      - in: query
        name: start_date
        schema:
            type: string
        description: A single date in 'YYYY-MM-DD' format. This is used to select games that occur on or after this date.
      - in: query
        name: end_date
        schema:
            type: string
        description: A single date in 'YYYY-MM-DD' format. This is used to select games that occur on or before this date.
      responses:
        "200":
          description: OK
  /stats:
    get:
      operationId: getStats
      summary: Retrieves stats that match the filters specified by the args. Display results using markdown tables.
      parameters:
      - in: query
        name: limit
        schema:
            type: string
        description: The max number of results to return.
      - in: query
        name: player_ids
        schema:
            type: array
            items:
              type: string
        description: Filter by player ids. Player ids can be determined using the getPlayers function.
      - in: query
        name: game_ids
        schema:
            type: array
            items:
              type: string
        description: Filter by game ids. Game ids can be determined using the getGames function.
      - in: query
        name: start_date
        schema:
            type: string
        description: A single date in 'YYYY-MM-DD' format. This is used to select games that occur on or after this date.
      - in: query
        name: end_date
        schema:
            type: string
        description: A single date in 'YYYY-MM-DD' format. This is used to select games that occur on or before this date.
      responses:
        "200":
          description: OK
  /season_averages:
    get:
      operationId: getSeasonAverages
      summary: Retrieves regular season averages for the given players. Display results using markdown tables.
      parameters:
      - in: query
        name: season
        schema:
            type: string
        description: Defaults to the current season. A season is represented by the year it began. For example, 2018 represents season 2018-2019.
      - in: query
        name: player_ids
        schema:
            type: array
            items:
              type: string
        description: Filter by player ids. Player ids can be determined using the getPlayers function.
      responses:
        "200":
          description: OK
```

## セマンティック検索プラグインを作成する方法を学ぶ

[ChatGPT 検索プラグインは、](https://github.com/openai/chatgpt-retrieval-plugin)私たちが入手できる最も詳細なコード例です。プラグインの範囲を考えると、リポジトリを参照してコードを読み、いくつかの単純な例と何が異なるかをよりよく理解することをお勧めします.

検索プラグインの例は次のとおりです。

-   複数のベクター データベース プロバイダーのサポート
-   4 つの異なる認証方法すべて
-   多くの異なる機能