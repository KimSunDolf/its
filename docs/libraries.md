---
sidebar_position: 3
sidebar_label: ライブラリ
---

# ライブラリを呼び出す

## Pythonライブラリ

私たちはPythonライブラリを提供し、あなたは次のようにインストールすることができます。

```bash
$ pip install openai
```

インストール後、バインディング(the bindings)とAPI Keyを使って以下のコマンドを実行することができます。

```python
import os
import openai

# Load your API key from an environment variable or secret management service
openai.api_key = os.getenv("OPENAI_API_KEY")

response = openai.Completion.create(model="text-davinci-003", prompt="Say this is a test", temperature=0, max_tokens=7)
```

バインディング(the bindings)はコマンドラインユーティリティもインストールされ、以下の方法で使用できます。

```bash
$ openai api completions.create -m text-davinci-003 -p "Say this is a test" -t 0 -M 7 --stream
```

## Node.jsライブラリ

私たちにはNode.jsライブラリもあります。Node.jsプロジェクトディレクトリで次のコマンドを実行してインストールすることができます。

```bash
$ npm install openai
```

インストール後、このライブラリとあなたのAPI Keyを使って次のコマンドを実行することができます。

```javascript
const { Configuration, OpenAIApi } = require('openai')
const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY
})
const openai = new OpenAIApi(configuration)
const response = await openai.createCompletion({
  model: 'text-davinci-003',
  prompt: 'Say this is a test',
  temperature: 0,
  max_tokens: 7
})
```

## コミュニティライブラリ

以下のライブラリは、幅広い開発者コミュニティによって構築および維持されています。ここに新しいライブラリを追加する場合は[、](https://help.openai.com/en/articles/6684216-adding-your-api-client-to-the-community-libraries-page)コミュニティ ライブラリの追加に関するヘルプ センターの記事の手順に従ってください。

OpenAI は、これらのプロジェクトの正確性または安全性を検証しないことに注意してください。

### C# / .NET

- [Betalgo.OpenAI.GPT3](https://github.com/betalgo/openai) by [Betalgo](https://github.com/betalgo)

### Crystal

- [Openai-Crystal](https://github.com/sferik/openai-crystal) by [Sferik](https://github.com/sferik)

### Go

- [Go-GPT3](https://github.com/sashabaranov/go-gpt3) by [sashabaranov](https://github.com/sashabaranov)

### Java

- [openai-java](https://github.com/TheoKanning/openai-java) by [Theo Kanning](https://github.com/TheoKanning)

### Kotlin

- [openai-kotlin](https://github.com/Aallam/openai-kotlin) by [Mouaad Aallam](https://github.com/Aallam)

### Node.js

- [openai-api](https://www.npmjs.com/package/openai-api) by [Njerschow](https://github.com/Njerschow)
- [OpenAI-API-Node](https://www.npmjs.com/package/openai-api-node) by [Erlapso](https://github.com/erlapso)
- [GPT-X](https://www.npmjs.com/package/gpt-x) by [CEIFA](https://github.com/ceifa)
- [GPT3](https://www.npmjs.com/package/gpt3) by [Poteat](https://github.com/poteat)
- [GPTS](https://www.npmjs.com/package/gpts) by [thencc](https://github.com/thencc)
- [@dalenguyen/OpenAI](https://www.npmjs.com/package/@dalenguyen/openai) by [Dalenguyen](https://github.com/dalenguyen)
- [Tectalic/OpenAI](https://github.com/tectalichq/public-openai-client-js) by [tectalic](https://tectalic.com/)

### PHP

- [Orhanerday/Open-AI](https://packagist.org/packages/orhanerday/open-ai) by [Orhanerday](https://github.com/orhanerday)
- [Tectalic/OpenAI](https://github.com/tectalichq/public-openai-client-php) by [tectalic](https://tectalic.com/)

### Python

- [OthersideAI](https://www.othersideai.com/) by [OthersideAI](https://github.com/OthersideAI/chronology)

### R

- [RGPT3](https://github.com/ben-aaron188/rgpt3) by [Ben-Aaron188](https://github.com/ben-aaron188)

### Ruby

- [Nileshtrivedi](https://github.com/nileshtrivedi) by [OpenAI](https://github.com/nileshtrivedi/openai/)
- [Alexrudall](https://github.com/alexrudall) by [Ruby-Openai](https://github.com/alexrudall/ruby-openai)

### Scala

- [OpenAI-Scala-Client](https://github.com/cequence-io/openai-scala-client) by [Cequence-IO](https://github.com/cequence-io)

### Swift

- [OpenAIKit](https://github.com/dylanshine/openai-kit) by [dylanshine](https://github.com/dylanshine)

### Unity

- [OpenAi-Api-Unity](https://github.com/hexthedev/OpenAi-Api-Unity) by [hexthedev](https://github.com/hexthedev)

### Unreal Engine

- [OpenAI-API-Unreal](https://github.com/KellanM/OpenAI-Api-Unreal) by [KellanM](https://github.com/KellanM)