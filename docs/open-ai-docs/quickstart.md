# クイックスタート

OpenAI は、テキストの理解と生成に優れた主要な言語モデルをトレーニングしました。私たちの API は、これらのモデルへのアクセスを提供し、「言語処理」を含むほぼすべてのタスクを処理するために使用できます。

このクイックスタート チュートリアルでは、簡単なサンプル アプリケーションを作成します。その過程で、次のようなタスクに API を使用するための重要な基本概念とテクニックを学びます。

-   内容生成
-   要約
-   格付け、分類、感情分析
-   データ抽出
-   翻訳
-   その他

## 序章

[補完は](https://platform.openai.com/docs/api-reference/completions)API の中心であり、非常に柔軟で強力なシンプルなインターフェイスを提供します。テキストを**Prompt**と、API は指定した命令またはコンテキストに一致する Completion**を返します**。

> プロンプトを入力してください (Prompt):`アイスクリーム屋のためにスローガンを書く。`
> 
> 補完:`私たちはスプーン一杯で笑顔を提供します！`

これは非常に高レベルのオートコンプリートと考えることができます。モデルはテキスト プロンプトを処理し、予測を試みて、最も適切なコンテンツを返します。

## 1.やってみる

ペットの名前生成器を作りたいと仮定すると、最初から名前を考えるのは難しいです。

まず、あなたが何を望んでいるのかを明確に説明する提示語(Prompt)が必要だ。試してみて、次の提示語(Prompt)を提出して補完(Completion)を得ましょう。

> プロンプト（Prompt）：`Suggest one name for a horse.`
>
> 補完（Completion）：`Lightning`

あなたの指示をもっと具体的にしてみます。。

> プロンプト（Prompt）：`Suggest one name for a black horse.`
>
> 補完（Completion）：`Midnight`

ご覧のように、提示語(Prompt)に簡単な形容詞を追加すると、リターンの補完(Completion)が変わります。デザイン提示語(Prompt)は本質的にモデルを「プログラミング」することです。

## 2\. もう少し例を挙げる

デザインされた説明は良い結果を得るために重要だが、時には十分ではない。もっと複雑な指示を下してみよう。

> プロンプト（Prompt）：
>
> ```
> Suggest three names for a horse that is a superhero.
> ```
>
> 補完（Completion）：
>
> ```
> 1. Super Stallion
> 2. Captain Colt
> 3. Mighty Mustang
> ```

この完成は私たちが望んだものではない。これらの名称は非常に一般的で、モデルは私たちの指示の馬の部分を受け入れていないように見える。それがもっと関連のある提案をしてくれるのか見てみよう。

多くの場合、モデルに見せて、あなたが何を望んでいるのかを伝えることが役に立ちます。あなたのヒントに例を加えると、パターンやニュアンスを伝えるのに役に立ちます。いくつかの例を含むこのヒントを提出してみてください。

> プロンプト（Prompt）：
>
> ```
> Suggest three names for an animal that is a superhero.
> 
> Animal: Cat
> Names: Captain Sharpclaw, Agent Fluffball, The Incredible Feline
> > Animal: Dog
> Names: Ruff the Protector, Wonder Canine, Sir Barks-a-Lot
> Animal: Horse
> Names:
> ```
>
> 補完（Completion）：
>
> ```
> Super Stallion, Mighty Mare, The Magnificent Equine　
> スーパー種馬、強力な雌馬、壮麗な馬
> ```

とてもいいです！私たちが与えられた入力を期待する出力例を追加すると、モデルが私たちが探している名前タイプを提供するのに役立つ。

## 3\. 設定を調整

デザインのヒントはあなたが使用できる唯一のツールではない。また、設定を調整することで補完(Completions)をコントロールすることができます。最も重要な設定の一つは温度(Temperature)です。

上記の例で同じヒントを何度も提出すれば、モデルは常に同じまたは非常に類似した完成を返すことに気づいたかもしれません。なぜなら、あなたが設定した温度が0だからです。

温度(Temperature)を1に設定して、同じ提示語(Prompt)を数回再提出してみてください。

> 输入提示词（Prompt）：
>
> ```
> Suggest three names for an animal that is a superhero.
> 
> Animal: Cat
> Names: Captain Sharpclaw, Agent Fluffball, The Incredible Feline
> > Animal: Dog
> Names: Ruff the Protector, Wonder Canine, Sir Barks-a-Lot
> Animal: Horse
> Names:
> ```
>
> 返回补全（Completion）：
>
> ```
> Super Stallion, Mighty Equine, The Fabulous Thoroughbred
> スーパー種馬、強力な馬、神話のような純種馬
> ```

何が起こったのか見てみよう？温度が0を超えると、同じ提示語(Prompt)を提出するたびに異なる補完(Completion)が返されます。

このモデルは、どのテキストが前のテキストの後に続く可能性が高いかを予測することを覚えておいてください。温度(Temperature)は0と1の間の値で、基本的にモデルがこれらの予測を行う時の信頼度をコントロールすることができます。温度を下げる(Temperature)は、より少ないリスクを負い、完成がより正確で確実であることを意味する。温度(Temperature)を上げることは、より多様な完成に繋がるだろう。

ペットの名前生成器について、あなたは多くの名前のアイデアを生成したいかもしれません。0.6の中等温度(Temperature)は比較的良い効果があるはずです。

## 4\. アプリケーションを構築

今、あなたは良い提示語(Prompt)と設定を見つけたので、あなたは自分のペットの名前生成器を作ることができます!私たちはあなたが入門するのを助けるためにいくつかのコードを書きました-以下の説明に従ってコードをダウンロードしてアプリケーションを実行してください。

### インストール

Node.jsをインストールしていない場合は、[こちらからダウンロードしてインストール](https://nodejs.org/en/)してください。その後、この[コードベース](https://github.com/openai/openai-quickstart-node) を複製してコードをダウンロードします。

```bash
git clone https://github.com/openai/openai-quickstart-node.git
```

Gitを使いたくないなら、この[zipファイル](https://github.com/openai/openai-quickstart-node/archive/refs/heads/master.zip)を使ってコードをダウンロードすることもできます。

### API Keyを追加

プロジェクトディレクトリに移動し、サンプル環境変数ファイルをコピーします。

```bash
cd openai-quickstart-node
cp .env.example .env
```

あなたの秘密API Keyをコピーして、新しく作成された`.env`ファイルにある`OPENAI_API_KEY`に設定します。まだAPI Keyを作成していない場合は、[ここをクリックして作成](https://platform.openai.com/account/api-keys)してください。

**重要なヒント:Javascriptを使用する時、すべてのAPI呼び出しはサーバー側でのみ行うべきです。なぜなら、ブラウザ側で呼び出すと、ブラウザ側のコードがあなたのAPIキーを露出するからです。詳細情報については、[ここを参照](https://platform.openai.com/docs/api-reference/authentication)。**

### アプリケーションを実行

プロジェクトディレクトリで以下のコマンドを実行して依存項目をインストールし、アプリケーションを実行します。

```bash
npm install
npm run dev
```

ブラウザで[http://localhost:3000](http://localhost:3000/)を開くと、ペットの名前生成器が見えるはずです!

### コードを理解

`openai-quickstart-node/pages/api`フォルダで`generate.js`を開きます。下部には、私たちが上で使用したプロンプト(Prompt)を生成する関数が見えます。ユーザーがペットのタイプを入力すると、指定された動物の提示語(Prompt)を動的に出力します。

```javascript
function generatePrompt(animal) {
  const capitalizedAnimal = animal[0].toUpperCase() + animal.slice(1).toLowerCase();
  return `Suggest three names for an animal that is a superhero.

Animal: Cat
Names: Captain Sharpclaw, Agent Fluffball, The Incredible Feline
Animal: Dog
Names: Ruff the Protector, Wonder Canine, Sir Barks-a-Lot
Animal: ${capitalizedAnimal}
Names:`;
}
```

`generate.js`の9行目で、実際のAPIリクエストを送信するコードが表示されます。前述したように、それは温度0.6の完成端点を使用する。

```javascript
const completion = await openai.createCompletion({
  model: "text-davinci-003",
  prompt: generatePrompt(req.body.animal),
  temperature: 0.6,
});
```

そういうことです!あなたは今、ペットの名前生成器がOpenAIのAPIをどう使うかを完全に理解すべきです!

## 结语

これらの概念と技術は、あなた自身のアプリケーションを構築するのに大きく役立つだろう。つまり、この簡単な例は可能性を示すほんの一部に過ぎない！補完エンドポイント(Completions endpoint)は非常に柔軟で、コンテンツ生成、ダイジェスト、セマンティック検索、テーママーク、感情分析など、ほとんどどんな「言語処理」に関する任務も処理できます。

覚えておくべき一つの制限は、ほとんどの**モデル**に対して、単一API要求は提示語(Prompt)と補完(Completion)の間に最大2048個のトークン(約1500個の単語)しか処理できないということです。

::: tip モデル&価格 私たちは異なる機能と価格帯を持つ一連のモデルを提供します。本教程では、これは私たちの最も強力な自然言語モデルである「text-davinci-003」を使用した。私たちは実験でこのモデルを使用することをお勧めする、なぜならそれは最高の結果をもたらすからだ。すべてが正常になると、他のモデルがより低い遅延とコストで同じ結果を生み出すことができるかどうかを確認することができます。

1 回のリクエスト (プロンプトと完) で処理されるトークンの総数は、モデルの最大コンテキスト長を超えることはできません。ほとんどのモデルでは、これは 2048 トークンまたは約 1500 ワードです。大まかな目安として、1 トークンは約 4 文字、または英語のテキストでは 0.75 語です。

価格は1000 トークンごとに従量課金制で、最初の 3 か月間は $5 の無料クレジットが提供されます。[詳細をご覧ください](https://openai.com/pricing)。:::

より高度なタスクの場合、1 つのプロンプトでは対応しきれないほど多くの例やコンテキストを提供したいと思うかもしれません。[微調整 API](https://platform.openai.com/docs/guides/fine-tuning)は、このようなより高度なタスクに最適です。**微調整により、**数百または数千の例を提供して、特定のユース ケースに合わせてモデルを調整できます。

## 次のステップ

インスピレーションを得て、さまざまなタスクのプロンプトの設計についてさらに学ぶには:

-   [補完ガイドをお読みください](https://platform.openai.com/docs/guides/completion)。
-   [プロンプトのサンプル ギャラリーを](https://platform.openai.com/examples)参照します。
-   [Playground](https://platform.openai.com/playground)でテストしてください。
-   構築を開始するときは、[利用ポリシー](https://openai.com/policies/usage-policies)をご参照ください。