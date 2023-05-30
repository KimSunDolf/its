---
sidebar_position: 1
---

# 概要 1

OpenAI API は、自然言語、コード、または画像の生成を伴うほぼすべてのタスクに適用できます。さまざまなタスクに適したさまざまな機能レベルのさまざまな[モデル](https://openai.xiniushu.com/docs/models)、独自のカスタム モデル[を微調整](https://openai.xiniushu.com/docs/guides/fine-tuning)できます。これらのモデルは、コンテンツ生成からセマンティック検索および分類まで、あらゆる場面で使用できます。

## 重要な概念

クイック スタート チュートリアルと以下のインタラクティブな例を使用して、主要な概念を理解することをお勧めします。

[クイック スタート チュートリアル](https://openai.xiniushu.com/docs/quickstart)

### プロンプト Prompts

[プロンプトの設計は、](https://openai.xiniushu.com/docs/guides/completion#prompt-design)基本的にモデルを「プログラミング」することであり、通常はいくつかの指示またはいくつかの例を提供します。これは、感情分類や名前付きエンティティの認識など、単一のタスク用に設計された他のほとんどの NLP サービスとは異なります。対照的に、Completions と Chat Completions は、コンテンツまたはコードの生成、要約、拡張機能、会話、クリエイティブ ライティング、スタイルの転送など、ほぼすべてのタスクに使用できます。

### トークン Token

私たちのモデルは、テキストをトークンに分解することでテキストを理解し、処理します。トークンは単語または文字のブロックです。たとえば、"hamburger" という単語は "ham"、"bur"、"ger" というトークンに分解されますが、"pear" などの非常に短く一般的な単語はトークンです。多くのトークンは、"hello" や "bye" など、スペースで始まります。

特定の API リクエストで処理されるトークンの数は、入力と出力の長さによって異なります。大まかな経験則として、英語のテキストの場合、1 トークンはおよそ 4 文字または 0.75 語に相当します。注意すべき制限の 1 つは、テキスト プロンプトと生成された補完の組み合わせが、モデルの最大コンテキスト長を超えることができないことです (ほとんどのモデルでは、これは 2048 トークン、または約 1500 語です)。テキストがどのようにトークンに変換されるかについては、[トークナイザー](https://platform.openai.com/tokenizer)ツールをご覧ください。

### モデル Models

API は、機能と価格が異なる一連のモデルによって強化されています。GPT-4 は、当社の最新かつ最も強力なモデルです。GPT-3.5-Turbo は、会話モードに最適化された ChatGPT を強化するモデルです。これらのモデルとその他のモデルの詳細については、[モデルのドキュメント](https://openai.xiniushu.com/docs/models)をご参照。

## 次のステップ

-   アプリの作成を開始する前に、当社の[利用ポリシー](https://openai.com/policies/usage-policies)をご参照。
-   [サンプル](https://platform.openai.com/examples)ギャラリーを参照してインスピレーションを得てください。
-   アプリの構築を開始するには、以下のハウツー ガイドを確認してください。

## ユーザーガイド

#### [チャット補完](https://openai.xiniushu.com/docs/guides/chat) `Beta`

チャットベースの言語モデルの使用方法を学ぶ

___

#### [テキスト補完](https://openai.xiniushu.com/docs/guides/completion)

テキストを生成および変更する方法を学ぶ

___

#### [埋め込み](https://openai.xiniushu.com/docs/guides/embeddings)

テキストの検索、並べ替え、比較の方法を学ぶ

___

#### [スピーチからテキストへ](https://openai.xiniushu.com/docs/guides/speech-to-text) `Beta`

音声をテキストに変換する方法を学ぶ

___

#### [画像生成](https://openai.xiniushu.com/docs/guides/images) `Beta`

画像を生成および変更する方法を学ぶ

___

#### [コード補完](https://openai.xiniushu.com/docs/guides/code) `Limited Beta`

コードを生成、編集、解釈する方法を学ぶ

___

#### [微調整](https://openai.xiniushu.com/docs/guides/fine-tuning)

アプリケーション シナリオでモデルをトレーニングする方法を学習する