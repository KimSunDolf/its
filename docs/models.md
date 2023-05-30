---
sidebar_position: 4
---

# モデル

## 概要

OpenAI API は、機能と価格が異なるさまざまなモデルによって強化されています。また、[微調整](https://platform.openai.com/docs/guides/fine-tuning)を通じて、特定のユース ケースに合わせてオリジナルのベース モデルを限定的にカスタマイズすることもできます。

| 模型 | 説明 |
| --- | --- |
| [GPT-4](https://platform.openai.com/docs/models/gpt-4) `Limited Beta` | 自然言語またはコードを理解して生成できる、改良された GPT-3.5 モデルのセット |
| [GPT-3.5](https://platform.openai.com/docs/models/gpt-3-5) | 自然言語またはコードを理解して生成するために GPT-3 を改善する一連のモデル |
| [DALL·E](https://platform.openai.com/docs/models/dall-e) `Beta` | 自然言語の手がかりを与えられた画像を生成および編集できるモデル |
| [Whisper](https://platform.openai.com/docs/models/whisper) `Beta` | 音声をテキストに変換できるモデル |
| [埋め込み](https://platform.openai.com/docs/models/embeddings) | テキストをデジタル形式に変換できるモデルのセット |
| [Codex](https://platform.openai.com/docs/models/codex) `Deprecated` | 自然言語をコードに変換するなど、コードを理解して生成できる一連のモデル |
| [Moderation](https://platform.openai.com/docs/models/moderation) | テキストが機密か安全でないかを検出できる微調整されたモデル |
| [GPT-3](https://platform.openai.com/docs/models/gpt-3) | 自然言語を理解して生成できるモデルのセット |

また、 [Point-E](https://github.com/openai/point-e)、[Whisper](https://github.com/openai/whisper)、[Jukebox](https://github.com/openai/jukebox)、 CLIP などのオープン ソース モデルもリリースしました[。](https://github.com/openai/CLIP)

[研究者向けのモデル インデックスにアクセスして、](https://platform.openai.com/docs/model-index-for-researchers)研究論文で取り上げられているモデルや、InstructGPT と GPT-3.5 などのモデル ファミリーの違いについて詳しく学んでください。

## GPT-4`Limited Beta`

GPT-4 は、大規模なマルチモーダル モデル (現在はテキスト入力を受け入れ、テキスト出力を発行し、将来的には画像入力) であり、より広範な一般知識と高度な推論機能により、以前のどのモデルよりも正確になる可能性があります。`gpt-3.5-turbo`と同様に、GPT-4は、チャットに最適化されていますが、従来の完了 (Completion) タスクにも適しています。[セッション](https://platform.openai.com/docs/guides/chat)完了ガイドでGPT-4の使用方法を学びましょう。

::: ヒント GPT-4 は現在`Limited beta`ステージで、アクセス権を付与された人だけがアクセスできることに注意してください。定員になり次第、順番[待ちリストに](https://openai.com/waitlist/gpt-4)ご登録ください。:::

| モデル | 説明 | 最大 Token 数 | 訓練データ |
| --- | --- | --- | --- |
| gpt-4 | どの GPT-3.5 モデルよりも強力で、より複雑なタスクに対応でき、チャット用に最適化されています。モデルの最新の反復で更新されます。 | 8,192トークン | 2021年9月現在 |
| gpt-4-0314 | 2023 年 3 月 14 日のスナップショット`gpt-4`。とは異なり`gpt-4`、このモデルは更新を受け取らず、2023 年 6 月 14 日までの 3 か月間のみサポートされます。 | 8,192トークン | 2021年9月現在 |
| gpt-4-32k | 基本モードと`gpt-4`同じ機能ですが、コンテキストの長さが 4 倍になります。モデルの最新の反復で更新されます。 | 32,768トークン | 2021年9月現在 |
| gpt-4-32k-0314 | 2023 年 3 月 14 日のスナップショット`gpt-4-32`。とは異なり`gpt-4-32k`、このモデルは更新を受け取らず、2023 年 6 月 14 日までの 3 か月間のみサポートされます。 | 32,768トークン | 2021年9月現在 |

多くの基本的なタスクでは、GPT-4 モデルと GPT-3.5 モデルの違いは重要ではありません。ただし、より複雑な推論状況では、GPT-4 は以前のどのモデルよりも強力です。

## GPT- 3.5

GPT-3.5 モデルは、自然言語またはコードを理解して生成できます。GPT-3.5 シリーズで最も強力で費用対効果の高いモデルは`gpt-3.5-turbo`で、チャットに最適化されていますが、従来の完了 (完了) タスクにも適しています。

| モデル | 説明 | 最大 Token 数 | 訓練データ |
| --- | --- | --- | --- |
| gpt-3.5-turbo | チャット向けに最適化された最も強力な GPT-3.5 モデルを 1/10 のコスト`text-davinci-003`で。モデルの最新の反復で更新されます。 | 4096トークン | 2021年9月現在 |
| gpt-3.5-turbo-0301 | `gpt-3.5-turbo`2023 年 3 月 1 日のスナップショット。`gpt-3.5-turbo`とは異なり、このモデルは更新されず、2023 年 6 月 1 日までの 3 か月間のみサポートされます。 | 4096トークン | 2021年9月現在 |
| text-davinci-003 | キュリー、バベッジ、ADA モデルよりも優れた品質、より長いアウトプット、一貫した指示に従うことで、あらゆる言語タスクを完了することができます。テキストへの補完の挿入[も](https://platform.openai.com/docs/guides/completion/inserting-text)サポートされています。 | 4097トークン | 2021年6月現在 |
| text-davinci-002 | と`text-davinci-003`同様のが、強化学習の代わりに教師付き微調整を使用してトレーニングされています | 4097トークン | 2021年6月現在 |
| code-davinci-002 | コード補完タスク用に最適化 | 8001トークン | 2021年6月現在 |

`gpt-3.5-turbo`のコストが低いため、他の GPT-3.5 モデルよりも優先に使用することがおすすめです。

::: ヒント OpenAI モデルは非決定論的であることに注意してください。つまり、同じ入力が異なる出力を生成する可能性があります。[温度を 0 に設定すると、](https://platform.openai.com/docs/api-reference/completions/create#completions/create-temperature)出力はほぼ確定的になりますが、わずかな変動性が保持される場合があります。:::

OpenAIモデルは非確実性であり、これは同じ入力が異なる出力を生み出すことを意味する。[温度](https://platform.openai.com/docs/api-reference/completions/create#completions/create-temperatuRe)を0に設定すると、出力の大部分が確実性を持つが、少量の可変性が残る可能性がある。

### 特定機能モデル

新しい`gpt-3.5-turbo`モデルはセッションに最適化されていますが、伝統的な任務遂行にも非常に効果的です。オリジナルのGPT-3.5モデルは[テキスト補完](https://platform.openai.com/docs/guides/completion)に最適化されました。

私たちは[埋め込み作成(Embedding)](https://platform.openai.com/docs/guides/embeddings)と[テキスト編集](https://platform.Openai.com/docs/guides/completion/editing-text)のエンドポイントは独自の専用モデルです。

##　適切なモデルを見つける

`gpt-3.5-turbo`を使って実験することはAPI機能を理解する良い方法です。達成すべき目標を理解した後、引き続き「gpt-3.5-turbo」や他のモデルを使って、その機能をめぐって最適化してみてください。

[GPT比較ツール](https://gpttools.com/comparisontool)を使って、異なるモデルを並んで実行して出力、設定、応答時間を比較し、データをExcelスプレッドシートにダウンロードすることができます。
## DALL・E

DALL・Eは人工知能システムで、自然言語の描写によってリアルな画像と芸術作品を作ることができます。私たちは現在、提示された状態で特定のサイズを持つ新しい画像を作成したり、既存の画像を編集したり、ユーザーが提供した画像の変種を作成する能力をサポートしています。

私たちのAPIを通じて提供された現在のDALL・EモデルはDALL・Eの第2世代で、オリジナルモデルよりリアルで正確で解像度が4倍高い画像を持っています。私たちの[実験室インターフェース](https://labs.openai.com/)または[API](https://platform.openai.com/docs/guides/images/inTroduction)試用を行う。

## Whisper

Whisperは一般的な音声認識モデルである。異なるオーディオの大型データセットで訓練され、マルチタスクモデルでもあり、多言語音声認識と音声翻訳と言語認識を実行することができます。現在、私たちのAPI(モデル名`whisper-1`)を通じてWhisper v2-largeモデルを使用できます。

現在、[Whisperのオープンソースバージョン](https://github.com/openai/whisper)と私たちのAPIを通じて提供されたバージョンの間には違いはありません。しかし、私たちのAPIを通じて、私たちは最適化された推論過程を提供し、これは私たちのAPIを通じてWhisperを実行するのが他の方法で実行するよりはるかに速いです。Whisperに関するより多くの技術的詳細は、[論文を読む](https://arxiv.org/abs/2212.04356)することができます。

## 埋め込み(Embedding)

埋め込み(Embedding)はテキストの数字表現で、2つのテキストの間の関連性を測定するのに使われます。私たちの第2世代埋め込みモデル`text-embedding-ada-002`は、以前の16種類の第1世代埋め込み(Embedding)モデルを小さなコストで置き換えることを目指しています。埋め込み(Embedding)は検索、クラスタリング、推薦、異常検知、分類任務に使えます。[公告ブログ記事](https://openai.com/blog/new-and-improved-embedding-model)で私たちの最新の埋め込みモデルについてより多くの情報を読むことができます。

## Codex

Codexモデルは私たちのGPT-3モデルの子孫であり、コードを理解して生成することができます。彼らの訓練データには自然言語とGitHubからの数十億行の公共コードが含まれています。もっと詳しく知る](https://help.openai.com/en/articles/5480054)。

JavaScript、Go、Perl、PHP、Ruby、Swift、TypeScript、SQL、さらにShellなど十数種類の言語に精通しています。

私たちは現在2つのCodexモデルを提供しています。

| モデル           | 説明                                                         | 最大 Token 数     | 訓練データ    |
| ---------------- | ------------------------------------------------------------ | ----------------- | ------------- |
| code-davinci-002 | 最も強力なCodexモデルです。特に自然言語をコードに翻訳するのが得意です。コードを補完するだけでなく、コードに[挿入](https://platform.openai.com/docs/guides/code/inserting-code)を挿入して補完することをサポートします。 | 8001 Token        | 截至2021年6月 |
| code-cushman-001 | Davinci Codexとほぼ同じくらい強力だが、少し速い。この速度の利点は、リアルタイムアプリケーションの第一選択になるかもしれない。 | 最多 2048 个Token |               |

詳細については、私たちの[Codex使用ガイド](https://platform.openai.com/docs/guides/code)をご覧ください。

Codexモデルは限定テスト期間に無料で使用でき、低下した[速度制限](https://help.openai.com/en/articles/5955598-is-api-usage-subject-tO-any-rate-limits)の制約。私たちが使用状況を知る時、私たちは幅広いアプリケーションを支援するための価格を求めるだろう。

この期間中、私たちの[使用政策](https://openai.com/policies/usage-policies)に合致すれば、あなたのアプリをご利用ください。私たちはこれらのモデルを早期に使用した時、どんなフィードバックも歓迎し、コミュニティとの相互作用を期待します。

### 特定機能モデル

主なCodexモデルは[テキスト補完(Completion)](https://platform.openai.com/docs/guides/completion)エンドポイントと一緒に使うことを目指しています。私たちはまた、私たちのエンドポイント専用の[埋め込み作成(Embedding)](https://platform.openai.com/docs/guides/embeddings)と[編集コード](https)を提供します。://Platform.openai.com/docs/guides/code/editing-code)。

___

## 審査(Moderation)

審査モデルは内容がOpenAIの[使用戦略](https://openai.com/policies/usage-policies)に合致しているかどうかを確認することを目的としています。これらのモデルは、憎しみ、憎しみ/脅威、自傷、性、性/未成年者、暴力、暴力/写真などの分類機能を提供します。私たちの[審査ガイド](https://platform.openai.com/docs/guides/moderation/overview)でより多くの情報を見つけることができます。

審査モデルは任意のサイズの入力を採用し、その入力は自動的に分解してモデル特定のコンテキストウィンドウを修復します。

| モデル                 | 説明                                     |
| ---------------------- | ---------------------------------------- |
| text-moderation-latest | 最も有能な審査モデルは、精度が安定モデルより少し高いです。 |
| text-moderation-stable | ほぼ最新モデルと同じくらい強力ですが、少し古いです。         |

## GPT- 3

GPT-3 モデルは、自然言語を理解して生成できます。これらのモデルは、より強力な GPT-3.5 世代モデルに置き換えられました。ただし、元の GPT-3 ベース モデル ( `davinci`、`curie`、`ada`および`babbage`) は、現在、微調整に使用できるモデルのみです。

| モデル | 説明 | 最大Token数 | 訓練データ |
| --- | --- | --- | --- |
| text-curie-001 | davinci よりも非常に有能で、高速で低コストです。 | 2,049トークン | 2019年10月現在 |
| text-babbage-001 | 単純なタスクを非常に迅速かつ低コストで完了する能力。 | 2,049トークン | 2019年10月現在 |
| text-ada-001 | 非常に単純なタスクを実行でき、通常は GPT-3 ファミリで最速のモデルであり、コストが最も低い。 | 2,049トークン | 2019年10月現在 |
| davinci | 最も強力な GPT-3 モデル。通常はより高い品質で、他のモデルができることは何でもできます。 | 2,049トークン | 2019年10月現在 |
| curie | 非常に有能ですが、davinci よりも高速で低コストです。 | 2,049トークン | 2019年10月現在 |
| babbage | 単純なタスクを非常に迅速かつ低コストで完了する能力。 | 2,049トークン | 2019年10月現在 |
| ada | 非常に単純なタスクを実行でき、通常は GPT-3 ファミリで最速のモデルであり、コストが最も低い。 | 2,049トークン | 2019年10月現在 |

## モデル エンドポイント (特定の API を参照)の互換性

| エンドポイント (特定の API を参照) | モデル名 |  |
| --- | --- | --- |
| /v1/chat/completions | gpt-4、gpt-4-0314、gpt-4-32k、gpt-4-32k-0314、gpt-3.5-turbo、gpt-3.5-turbo-0301 |  |
| /v1/completions | text-davinci-003、text-davinci-002、text-curie-001、text-babbage-001、text-ada-001、davinci、curie、babbage、ada |  |
| /v1/edits text-davinci-edit-001 | text-davinci-edit-001, code-davinci-edit-001 |  |
| /v1/audio/transcriptions | whisper-1 |  |
| /v1/audio/translations | whisper-1 |  |
| /v1/fine-tunes | davinci, curie, babbage, ada |  |
| /v1/embeddings | text-embedding-ada-002, text-search-ada-doc-001 |  |
| /v1/moderations | テキストレビューは安定しており、テキストレビューは最新です |  |

[このリストには、当社の第 1 世代の埋め込みモデル](https://platform.openai.com/docs/guides/embeddings/similarity-embeddings)も[DALL·E モデルも](https://platform.openai.com/docs/guides/images/image-generation-beta)含まれていません。

## 継続的なモデルのアップグレード

`gpt-3.5-turbo`のリリースに伴い、一部のモデルは継続的に更新されています。モデルの変更がユーザーに予期しない影響を与える可能性を減らすために、3 か月間静的なモデル バージョンも提供しています。モデルの更新の新しいリズムにより、さまざまなユースケースのモデルを改善するために、人々が評価に貢献できるようにもなっています。興味がある場合は、[OpenAI Evals](https://github.com/openai/evals)リポジトリをチェックしてください。

次のモデルは、指定された日付で廃止される一時的なスナップショットです。最新のモデルバージョンを使用する場合は、`gpt-4`または など`gpt-3.5-turbo`。

| モデル | 廃止日 |  |
| --- | --- | --- |
| gpt-3.5-turbo-0301 | 2023年6月1日 |  |
| gpt-4-0314 | 2023年6月14日 |  |
| gpt-4-32k-0314 | 2023年6月14日 |  |