# 微調整

## 微調整（Fine-tuning）

アプリケーションのモデルをカスタマイズする方法を学びます。

## 序章

以下を提供する微調整により、API によって提供されるモデルをさらに活用できます。

1.  Instant Design よりも高品質の結果
2.  提案されているよりも多くの例でトレーニングできる
3.  プロンプトが短いため、トークンが節約されました
4.  低レイテンシのリクエスト

GPT-3 は、オープン インターネットからの大量のテキストで事前トレーニングされています。ほんの数例のプロンプトが表示されると、多くの場合、何をしようとしているのかを直感的に理解し、適切な補完を生成できます。これはしばしば「数ショット学習」と呼ばれます。

微調整は、ヒントよりも多くの例でトレーニングすることにより、少数ショット学習を改善し、多数のタスクでより良い結果を達成できるようにします。**モデルを微調整した後は、プロンプトで例を提供する必要がなくなります。**これにより、コストが節約され、リクエストの待ち時間が短縮されます。

大まかに言うと、微調整には次の手順が含まれます。

1.  トレーニング データの準備とアップロード
2.  微調整された新しいモデルをトレーニングする
3.  微調整されたモデルを使用する

微調整されたモデルのトレーニングと使用に対する料金の詳細については、[料金ページ](https://openai.com/api/pricing)をご覧ください。

## 微調整できるモデルは？

微調整は現在、以下の基礎モデルにのみ適用されます：`davinci`、`curie`、`babbage`、`ada`。これらはオリジナルモデルで、訓練後には何の説明もない(例えば`text-davinci-003`)。また[引き続き微調整モデル](https://platform.openai.com/docs/guides/fine-tuning/continue-fine-tuning-from-a-fine-Tuned-model)は、ゼロから始めることなく、他のデータを追加します。

## インストール

OpenAIコマンドラインインターフェース(CLI)の使用をお勧めします。これをインストールして、運行します。

```
pip install --upgrade openai
```

(以下の説明は**0.9.4**及びそれ以上のバージョンに適用されます。また、OpenAI CLIにはpython 3が必要です。)

`OPENAI_API_KEY`は、以下の行をあなたのシェル初期化スクリプト(例えば.bashrc、zshrcなど)に追加したり、コマンドを微調整する前のコマンドラインで実行することで、あなたの環境変数を設定します。

```
export OPENAI_API_KEY="<OPENAI_API_KEY>"
```

## 訓練データを準備

訓練データは、あなたがGPT-3に何を言いたいのかをどう教えるかです。

あなたのデータは[JSONL](https://jsonlines.org/)文書でなければならず、その中の各行は完成のヒントで、訓練例に対応します。私たちの[CLIデータ準備ツール](https://platform.openai.com/docs/guides/fine-tuning/cli-data-preparation-tool)を軽く使えます。あなたのデータをこのファイル形式に緩く変換してください。

```
{"prompt": "<prompt text>", "completion": "<ideal generated text>"}
{"prompt": "<prompt text>", "completion": "<ideal generated text>"}
{"prompt": "<prompt text>", "completion": "<ideal generated text>"}
...
```

微調整のためのヒントと補完は、私たちの基本モデル(Davinci、Curie、Babbage、Ada)のためのヒントとは違います。特に、基礎モデルのヒントは通常複数の例(「小さなサンプル学習」)が含まれていますが、微調整の場合、各訓練例は通常1つの入力例とその関連出力を含み、詳細な説明や同じヒントに複数の例を含む必要はありません。

各種任務のために訓練データを準備する方法についてもっと詳しい指導については、私たち[データセットを準備する](https://platform.openai.com/docs/guides/fine-tuning/preparing-y)を参照してください。Our-dataset)ベストプラクティス。

あなたが持っている訓練例が多ければ多いほどいい。私たちは少なくとも数百個の例示を提案します。一般的に、データセットの大きさが2倍になるたびに、モデルの質量が線形的に増加することを発見しました。

### CLI データ準備ツール

私たちはあなたのデータを検証し、アドバイスを提供し、再フォーマットするツールを開発しました。

```
openai tools fine_tunes.prepare_data -f <LOCAL_FILE>
```

このツールは異なるフォーマットを受け入れ、唯一の要求はヒントと完成列/キーが含まれていることです。**CSV、TSV、XLSX、JSON**または**JSONL**ファイルを転送することができます。提案された変更過程を案内した後、微調整のために出力をJSONLファイルに保存します。

## 微調整モデルを作成

以下は[上記の説明](https://platform.openai.com/docs/guides/fine-tuning/prepare-training-data)に従って訓練データを準備したと仮定します。

OpenAI CLIを使って微調整作業を始める：

```
openai api fine_tunes.create -t <TRAIN_FILE_ID_OR_PATH> -m <BASE_MODEL>
```

どこから`BASE_MODEL`から始まる基本モデルの名前(ada、babbage、curie、または davinci)。[拡張子パラメータ](https://platform.openai.com/docs/guides/fine-tuning/customize-your-model-name)でモデルの名前をカスタマイズできます。

上記のコマンドを実行すると、いくつかのことをする：

1.[ファイルAPIを使用する](https://platform.openai.com/docs/api-reference/files)ファイルをアップロードする(またはすでにアップロードしたファイルを使用する)

2.微調整作業を作成します。

3.ストリーミングイベントは作業が完了するまで(これは通常数分かかりますが、キューに多くの作業やあなたのデータセットが大きい場合、数時間かかることがあります)

各微調整作業はデフォルトで`curie`の基本モデルから始まります。モデルの選択は、モデルの性能とモデルの微調整を実行するコストに影響を及ぼす。あなたのモデルは次のいずれかである：`ada`、`babbage`、`curie`または`davinci`。私たちの[定価ページ](https://openai.com/api/pricing/#faq-fine-tuning-pricing-calculation)にアクセスして、微調整率について詳しく調べてください。

微調整作業を始めたら、完成するのに時間がかかるかもしれません。私たちのシステムでは、あなたの仕事は他の仕事の後に並べるかもしれません。モデルとデータセットの大きさによって、私たちのモデルを訓練するのに数分または数時間かかるかもしれません。イベントの流れが何らかの理由で中断された場合、次のコマンドを実行して回復することができます。

```
openai api fine_tunes.follow -i <YOUR_FINE_TUNE_JOB_ID>
```

作業が終わった後、それは微調整モデルの名前を表示しなければならない。

微調整作業を作成するほか、既存の作業を列挙したり、作業状態を検索したり、作業をキャンセルしたりすることができます。

```
# List all created fine-tunes
openai api fine_tunes.list

# Retrieve the state of a fine-tune. The resulting object includes
# job status (which can be one of pending, running, succeeded, or failed)
# and other information
openai api fine_tunes.get -i <YOUR_FINE_TUNE_JOB_ID>

# Cancel a job
openai api fine_tunes.cancel -i <YOUR_FINE_TUNE_JOB_ID>
```

## 微調整モデルを使用

宿題が成功すれば、この`fine_tuned_model`フィールドはモデル名を埋めます。[あなたは今このモデルを私たちのCompletions APIの](https://platform.openai.com/docs/api-reference/completions)パラメータに指定し、使用することができます。[Playground](https://platform.openai.com/playground)はそれに要請する。

あなたの仕事が初めて完了した後、あなたのモデルは要請を処理する準備に数分かかるかもしれません。もしあなたのモデルの完成要請がタイムアウトしたら、あなたのモデルがまだロード中だからかもしれません。もしこのようなことが起こったら、数分後にもう一度やり直してください。

モデル名を`model`完了要請のパラメータとして渡すことで、要請を始めることができます。

OpenAI CLI:

```
openai api completions.create -m <FINE_TUNED_MODEL> -p <YOUR_PROMPT>
```

cURL:

```
curl https://api.openai.com/v1/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompt": YOUR_PROMPT, "model": FINE_TUNED_MODEL}'
```

Python：

```
import openai
openai.Completion.create(
    model=FINE_TUNED_MODEL,
    prompt=YOUR_PROMPT)
```

Node.js:

```
const response = await openai.createCompletion({
  model: FINE_TUNED_MODEL
  prompt: YOUR_PROMPT,
});
```

引き続き他のすべての[完成](https://platform.openai.com/docs/api-reference/completions)パラメータ、例えば`temperature`、 、 など、これらの`frequency_penalty`要求`presence_penalty`を微調整することができます。

## 微調整モデルを削除

微細調整モデルを削除するには、あなたの組織で「所有者」に指定されなければなりません。

OpenAI CLI:

```
openai api models.delete -i <FINE_TUNED_MODEL>
```

cURL:

```
curl -X "DELETE" https://api.openai.com/v1/models/<FINE_TUNED_MODEL> \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

Python：

```
import openai
openai.Model.delete(FINE_TUNED_MODEL)
```

## データセットを準備します。

微調整は、あなたのユースケースに固有の新しいモデルを作ることができる強力な技術です。**あなたのモデルを微調整する前に、あなたのユースケースに対する以下のベストプラクティスと[具体的なガイド](https://platform.openai.com/docs/guides/fine-tuning/specific-guidelines)を読むことを強くお勧めします。**

## データフォーマット

モデルを微調整するには、訓練例のセットが必要です。各訓練例には入力(「ヒント」)とその関連出力(「完成」)が含まれています。これは私たちの基本モデルを使うのとは明らかに違います。基本モデルでは、単一ヒントに詳細な説明や複数の例を入力するかもしれません。

- 各ヒントは固定区切りで終わり、ヒント終了と完了開始時にモデルに通知します。通常、効果の良い簡単な区切り文字は`\n\n###\n\n`です。区切り文字はどんなヒントの他の場所にも現れてはならない。

- [私たちのマーク化のため、](https://platform.openai.com/tokenizer)各完成はスペースで始まり、前のスペースでほとんどの単語をマークします。

- 毎回の完成は固定された停止シーケンスで終わり、完成終了時にモデルに通知します。停止シーケンスは`\n`、`###`または他の完成に現れないマークである。

-推論については、トレーニングデータセットを作成する時と同じ方法でヒントをフォーマットし、同じ区切り文字を含むべきです。また、同じ停止シーケンスを指定して正確に切断して完成させる。

## 一般のベストプラクティス

より多くの高品質の例を使って微調整する方が効果的だ。私たちの基本モデルを使って高品質なヒントを使うよりよく実行できるモデルを微調整するには、少なくとも数百個の高品質な例を提供し、人間の専門家が審査したほうがいいです。そこから、性能は例の数が2倍に増えるごとに直線的に増加する傾向がある。例の数を増やすことは、通常、性能を向上させるための最も確実で信頼できる方法だ。

分類器は最も使いやすいモデルです。分類問題については、adaを使用することをお勧めします。微調整後、通常は機能が強いモデルより少し劣り、同時に速度が速く、コストも安いです。

最初からヒントを書くのではなく、既存のデータセットを微調整するなら、必ず可能な場合、あなたのデータに不快な内容や不正確な内容があるかどうかを手動で確認してください。または、データセットが大きい場合、できるだけ多くのランダムサンプルをチェックしてください。

## 具体的なガイド

微調整は様々な問題を解決でき、最適な使用方法はあなたの具体的な使用例によって決まるかもしれません。以下に、最も一般的なマイクロコール例とそれに対応するガイドを列挙します。

[分類](https://platform.openai.com/docs/guides/fine-tuning/classification)

- [このモデルは真実ではない陳述をしましたか?](Https://platform.openai.com/docs/guides/fine-tuning/case-study-is-the-model-making-untrue-statements)
- [感情分析](https://platform.openai.com/docs/guides/fine-tuning/case-study-sentiment-analysis)
- [電子メール分類の分類](https://platform.openai.com/docs/guides/fine-tuning/case-study-categorization-for-emaiL-triage)

[条件生成](https://platform.openai.com/docs/guides/fine-tuning/conditional-generation)

- [ウィキペディアの記事に基づいて魅力的な広告を書く](https://platform.openai.com/docs/guides/fine-tuning/case-study-write-an-engagiNg-ad-based-on-a-wikipedia-article)
- [実体抽出](https://platform.openai.com/docs/guides/fine-tuning/case-study-entity-extraction)
- [顧客サポートチャットボット](https://platform.openai.com/docs/guides/fine-tuning/case-study-customer-support-chatboT)
- [技術属性リストに基づいた製品説明](https://platform.openai.com/docs/guides/fine-tuning/case-study-product-description-Based-on-a-technical-list-of-properties)

### 分類

分類問題では、ヒントの各入力は事前に定義されたカテゴリーの一つに分類されるべきだ。このような問題に対して、私たちは以下を提案します。

- ヒントの最後に区切り文字を使う、例えば`\n\n###\n\n`。最終的にあなたのモデルに要請する時、この区切り文字も添付することを覚えておいてください。

- 単一[マーク](https://platform.openai.com/tokenizer)にマッピングされたクラスを選択します。推理する時、`max_tokens=1`を指定してください。なぜなら、最初のマークだけで分類する必要があるからです。
- ヒント+完成が2048個のマークを超えないことを確認してください。区切り文字を含む。

- 目標は各クラスに少なくとも~100個の例です。

- `logprobs=5`クラスログ確率を得るには、モデルを使用する時に指定できます(5つのクラスに対して)
- 微調整のためのデータセットが構造とタスクタイプにおいて、モデルが使用するデータセットと非常に似ていることを確保します。


#### ケーススタディ:モデルは真実ではない陳述をしましたか?

あなたのウェブサイトの広告文字が正しい製品と会社を言及することを確認したいと仮定します。言い換えると、あなたはモデルがでっち上げされていないことを確実にしなければならない。あなたは間違った広告をフィルタリングする分類器を微調整したいかもしれません。

データセットは以下の内容と似ているかもしれない。

```
{"prompt":"Company: BHFF insurance\nProduct: allround insurance\nAd:One stop shop for all your insurance needs!\nSupported:", "completion":" yes"}{"prompt":"Company: Loft conversion specialists\nProduct: -\nAd:Straight teeth in weeks!\nSupported:", "completion":" no"}
```

上記の例では、会社名、製品、関連広告を含む構造化された入力を使用した。区切りとして、私たちは`\nSupported:`を使って、ヒントと完成を明確に分離します。十分な数の例の場合、区切り文字はヒントや完成に現れない限り、あまり影響を及ぼさない(通常0.4%未満)。

このユースケースに対して、私たちはadaモデルを微調整しました。なぜなら、それはより速く、より安く、性能はより大きなモデルに匹敵するからです。なぜなら、それは分類任務だからです。

もう私たちは完了要請を送ることで私たちのモデルを調べることができる。

```
curl https://api.openai.com/v1/completions \  -H "Content-Type: application/json" \  -H "Authorization: Bearer $OPENAI_API_KEY" \  -d '{    "prompt": "Company: Reliable accountants Ltd\nProduct: Personal Tax help\nAd:Best advice in town!\nSupported:",    "max_tokens": 1,    "model": "YOUR_FINE_TUNED_MODEL_NAME"  }'
```

どれが`yes`or `no`を返すか。

#### ケーススタディ:感情分析

特定のツイートのポジティブまたはネガティブな程度を知りたいとします。データセットは以下の内容と似ているかもしれない。

```
{"prompt":"Overjoyed with the new iPhone! ->", "completion":" positive"}{"prompt":"@lakers disappoint for a third straight night https://t.co/38EFe43 ->", "completion":" negative"}
```

モデルを微調整した後、`logprobs=2`完了要求に設定して最初の完成トークンの対数確率を取り戻すことができます。正カテゴリーの確率が高いほど、相対的な感情が高くなります。

もう私たちは完了要請を送ることで私たちのモデルを調べることができる。

```
curl https://api.openai.com/v1/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "prompt": "Company: Reliable accountants Ltd\nProduct: Personal Tax help\nAd:Best advice in town!\nSupported:",
    "max_tokens": 1,
    "model": "YOUR_FINE_TUNED_MODEL_NAME"
  }'
```

どれが戻ってくるか:

```
{
  "id": "cmpl-COMPLETION_ID",
  "object": "text_completion",
  "created": 1589498378,
  "model": "YOUR_FINE_TUNED_MODEL_NAME",
  "choices": [
    {
      "logprobs": {
        "text_offset": [
          19
        ],
        "token_logprobs": [
          -0.03597255
        ],
        "tokens": [
          " positive"
        ],
        "top_logprobs": [
          {
            " negative": -4.9785037,
            " positive": -0.03597255
          }
        ]
      },

      "text": " positive",
      "index": 0,
      "finish_reason": "length"
    }
  ]
}
```

#### ケーススタディ:電子メール分類の分類

あなたが受け取ったメールを大量の事前定義されたカテゴリーの一つに分類したいとします。大量のカテゴリーの分類については、これらのカテゴリーを数字に変換し、最大約500個のカテゴリーを処理することをお勧めします。私たちは、マーク化のため、数字の前にスペースを追加すると、性能に少し役立つことがあると観察しました。あなたは次のように訓練データを構築したいかもしれません。

```
{"prompt":"Subject: <email_subject>\nFrom:<customer_name>\nDate:<date>\nContent:<email_body>\n\n###\n\n", "completion":" <numerical_category>"}
```

例えば：

```
{"prompt":"Subject: Update my address\nFrom:Joe Doe\nTo:support@ourcompany.com\nDate:2021-06-03\nContent:Hi,\nI would like to update my billing address to match my delivery address.\n\nPlease let me know once done.\n\nThanks,\nJoe\n\n###\n\n", "completion":" 4"}
```

上記の例では、私たちは2043個のトークンの受信メールを入力として使用しました。これは4つのマーク区切りと1つのマークで完成でき、合計2048です。)区切り文字として、私たちはメールの`\n\n\n###\n\n`に現れるすべての内容を使用し、削除しました。`###`

### 条件生成

条件生成は、ある入力が与えられた状態で内容を生成する必要がある問題である。これは解釈、総括、実体抽出、与えられた仕様の製品説明の作成、チャットボットなどが含まれます。このような問題に対して、私たちは以下を提案します。

- ヒントの最後に区切り文字を使う、例えば`\n\n###\n\n`。最終的にあなたのモデルに要請する時、この区切り文字も添付することを覚えておいてください。

- 完了終了時に終了マークを使う、例えば`END`
- 推理過程で終了マークを停止シーケンスに追加することを覚えておいてください。例えば`stop=[" END"]`

- 目標は少なくとも~500個の例です。

- ヒント+完成が2048個のマークを超えないことを確認してください。区切り文字を含む。

- サンプルが高品質で、同じ必要なフォーマットに従うことを確保します。

- 微調整のためのデータセットが構造とタスクタイプにおいて、モデルが使用するデータセットと非常に似ていることを確保します。

- 低い学習率と1-2個の期間だけを使うと、これらのユースケースにもっと適しています。


#### ケーススタディ:ウィキペディアの文章に基づいて魅力的な広告を書く。

これは生成用例なので、提供されたサンプルが最高の品質であることを確認する必要があります。なぜなら、微調整モデルは与えられた例のスタイル(とエラー)を模倣しようとするからです。良い出発点は約500個の例示だ。サンプルデータセットは次の通りかもしれない：

```
{"prompt":"<Product Name>\n<Wikipedia description>\n\n###\n\n", "completion":" <engaging ad> END"}
```

例えば：

```
{"prompt":"Samsung Galaxy Feel\nThe Samsung Galaxy Feel is an Android smartphone developed by Samsung Electronics exclusively for the Japanese market. The phone was released in June 2017 and was sold by NTT Docomo. It runs on Android 7.0 (Nougat), has a 4.7 inch display, and a 3000 mAh battery.\nSoftware\nSamsung Galaxy Feel runs on Android 7.0 (Nougat), but can be later updated to Android 8.0 (Oreo).\nHardware\nSamsung Galaxy Feel has a 4.7 inch Super AMOLED HD display, 16 MP back facing and 5 MP front facing cameras. It has a 3000 mAh battery, a 1.6 GHz Octa-Core ARM Cortex-A53 CPU, and an ARM Mali-T830 MP1 700 MHz GPU. It comes with 32GB of internal storage, expandable to 256GB via microSD. Aside from its software and hardware specifications, Samsung also introduced a unique a hole in the phone's shell to accommodate the Japanese perceived penchant for personalizing their mobile phones. The Galaxy Feel's battery was also touted as a major selling point since the market favors handsets with longer battery life. The device is also waterproof and supports 1seg digital broadcasts using an antenna that is sold separately.\n\n###\n\n", "completion":"Looking for a smartphone that can do it all? Look no further than Samsung Galaxy Feel! With a slim and sleek design, our latest smartphone features high-quality picture and video capabilities, as well as an award winning battery life. END"}
```

ウィキペディアの記事には複数の段落とタイトルが含まれているため、ここで私たちは複数行の区切り文字を使用する。私たちはまた、モデルがいつ完成すべきかを確実にするために、簡単な終了マークを使用した。

#### ケーススタディ:実体抽出

これは言語転換の任務と似ている。性能を高めるために、アルファベット順または元のテキストに現れる同じ順序で異なる抽出実体をソートしたほうがいいです。これはモデルが順番に生成される必要があるすべての実体を追跡するのに役立つだろう。データセットは次の通りかもしれない：

```
{"prompt":"<any text, for example news article>\n\n###\n\n", "completion":" <list of entities, separated by a newline> END"}
```

例えば：

```
{"prompt":"Portugal will be removed from the UK's green travel list from Tuesday, amid rising coronavirus cases and concern over a \"Nepal mutation of the so-called Indian variant\". It will join the amber list, meaning holidaymakers should not visit and returnees must isolate for 10 days...\n\n###\n\n", "completion":" Portugal\nUK\nNepal mutation\nIndian variant END"}
```

テキストが複数行を含む可能性があるため、複数行の区切りが最も効果的です。理想的には、入力ヒントの種類は高度に多様化(ニュース記事、ウィキペディアページ、ツイッター、法律文書)で、これは実体を抽出する時に遭遇する可能性のあるテキストを反映しています。

#### ケーススタディ:顧客支援チャットボット

チャットボットは通常、会話に関する文脈(注文詳細)、今までの会話の要約、そして最近のニュースが含まれています。このユースケースに対して、同じ過去の会話はデータセットで複数行を生成でき、毎回少し異なる文脈があり、各エージェント生成が完了します。このユースケースは、異なる種類の要求と顧客問題を扱う可能性があるため、数千個の例を必要とするだろう。高品質な性能を確保するために、代理メッセージの品質を確保するために、会話サンプルを審査することをお勧めします。単独のテキスト変換微調整モデルでダイジェストを作成できます。データセットは次の通りかもしれない：

```
{"prompt":"Summary: <summary of the interaction so far>\n\nSpecific information:<for example order details in natural language>\n\n###\n\nCustomer: <message1>\nAgent: <response1>\nCustomer: <message2>\nAgent:", "completion":" <response2>\n"}
{"prompt":"Summary: <summary of the interaction so far>\n\nSpecific information:<for example order details in natural language>\n\n###\n\nCustomer: <message1>\nAgent: <response1>\nCustomer: <message2>\nAgent: <response2>\nCustomer: <message3>\nAgent:", "completion":" <response3>\n"}
```

ここで、私たちは意図的に異なるタイプの入力情報を分離しますが、提示と完成の間に同じフォーマットで顧客代理ダイアログボックスを維持します。すべての完成はエージェントによってのみ行われるべきで、私たちは推論を行う時に停止シーケンスとして使うことができる。

#### ケーススタディ:技術属性リストに基づいた製品説明

ここでは、入力データを自然言語に変換することが重要であり、これは優れた性能をもたらす可能性がある。例えば、以下のフォーマット：

```
{"prompt":"Item=handbag, Color=army_green, price=$99, size=S->", "completion":" This stylish small green handbag will add a unique touch to your look, without costing you a fortune."}
```

次のようには働かない：

```
{"prompt":"Item is a handbag. Colour is army green. Price is midrange. Size is small.->", "completion":" This stylish small green handbag will add a unique touch to your look, without costing you a fortune."}
```

高性能を得るために、提供された説明に基づいて完了することを確認してください。外部コンテンツを頻繁に閲覧すると、自動的にそのようなコンテンツを追加すると性能が向上します。もし記述が画像に基づいているなら、アルゴリズムを使って画像のテキスト記述を抽出することが役に立つかもしれない。完成は一文だけ長いので、私たちは`.`推論の過程で停止シーケンスとして使われる。

## 高級用法

## モデル名をカスタマイズ

[サフィックスを使用できます](https://platform.openai.com/docs/api-reference/fine-tunes/create#fine-tunes/create-suffix)パラメータで最大40文字のサフィックスを微調整されたモデル名に追加できます。

OpenAI CLI:

```
openai api fine_tunes.create -t test.jsonl -m ada --suffix "custom model name"
```

結果の名称は:

```
ada:ft-your-org:custom-model-name-2022-02-15-04-21-04
```

## 微調整モデルを分析

私たちは各作業が完了した後、結果の文書を添付するつもりだ。微調整を検索する時と、微調整中のイベントを確認する時、この結果ファイルIDが列挙されます。これらのファイルをダウンロードできます。

OpenAI CLI:

```
openai api fine_tunes.results -i <YOUR_FINE_TUNE_JOB_ID>
```

CURL:

```
curl https://api.openai.com/v1/files/$RESULTS_FILE_ID/content \  -H "Authorization: Bearer $OPENAI_API_KEY" > results.csv
```

この`_results.csv`ファイルは各訓練ステップに一行が含まれており、そのうちの一つは一連のデータに対する前向きと逆転送を指します。ステップ番号以外に、各行にはそのステップに対応する以下のフィールドが含まれています。

- **elapsed\_tokens**:モデルが今まで見たトークン数(重複を含む)

- **elapsed\_examples**:モデルが今まで見た例の数(重複を含む)、そのうちの一つはあなたのバッチの要素です。例えば、もし`batch_size = 4`なら、ステップごとに`elapsed_examples`4が追加されます。

- **training\_loss**:訓練バッチの損失

- **training\_sequence\_accuracy**:訓練バッチのモデルの予測マークと実際の完成マークが完全に一致する**完成**パーセント。例えば、もしaが`batch_size`3なら、もしあなたのデータに補完 \[ \[1, 2\] , \[0, 5\] , \[4, 2\] \] とモデル予測 \[ \[1, 1\] , \[0, 5\]、\[4, 2\] \]、この正確度は2/3 = 0.67になります。

- **training\_token\_accuracy**:モデルが正確に予測した訓練バッチにマークされた**パーセント。**例えば、もしaが`batch_size`3なら、もしあなたのデータに補完 \[ \[1, 2\] , \[0, 5\] , \[4, 2\] \]とモデル予測 \[ \[1, 1\] , \[0, 5\]、\[4, 2\] \]、この正確度は5/6 = 0.83になります。

### 分類特定指標

私たちはまた、結果ファイルで他の分類特定の指標を生成するオプションを提供します。例えば、正確性と加重F1スコアなどです。これらの指標は完全な検証セットと微調整の終了時に定期的に計算される。あなたはそれらを追加列として結果ファイルで見るだろう。

この機能を有効にするには、パラメータ`--compute_classification_metrics`を設定してください。また、認証ファイルを提供し、`classification_n_classes`は多類分類にパラメータを設定し、または`classification_positive_class`は二元分類にパラメータを設定しなければなりません。

OpenAI CLI:

```
# For multiclass classification
openai api fine_tunes.create \
  -t <TRAIN_FILE_ID_OR_PATH> \
  -v <VALIDATION_FILE_OR_PATH> \
  -m <MODEL> \
  --compute_classification_metrics \
  --classification_n_classes <N_CLASSES>

# For binary classification
openai api fine_tunes.create \
  -t <TRAIN_FILE_ID_OR_PATH> \
  -v <VALIDATION_FILE_OR_PATH> \
  -m <MODEL> \
  --compute_classification_metrics \
  --classification_n_classes 2 \
  --classification_positive_class <POSITIVE_CLASS_FROM_DATASET>
```

以下の指標を設定すれば、あなたの[結果ファイル](https://platform.openai.com/docs/guides/fine-tuning/analyzing-your-fine-tuned-model)`--compute_classification_metrics`に表示されます。

##### 多種類の分類について

- **分類/正確度**:正確度
- **classification/weighted\_f1\_score** : 加重 F-1 スコア

##### バイナリ分類について

以下の指標は0.5の分類閾値に基づいている(つまり、確率が0.5の場合、例は正類に分類される。)

- **分類/正確性**
- **分類/精度**
- **分類/リコール**
- **分類/f{beta}**
- **分類/auroc** - AUROC
- **分類/auprc** - AUPRC

これらの評価は、上記のように、あなたが単一タグにマーク化するクラスにテキストラベルを使用していると仮定していることに注意してください。もしこのような条件が成り立たなければ、あなたが得た数値は間違っている可能性が高い。

### 検証

あなたは検証のためにいくつかのデータを保持することができます。検証ファイルは訓練ファイルと全く同じフォーマットを持ち、あなたの訓練データと検証データは相互排他的であるべきです。

微細調整作業を作成する時に検証ファイルを含む場合、生成された結果ファイルには、微細調整モデルの訓練期間中に定期的に検証データの実行状況の評価が含まれます。

OpenAI CLI:

```
openai api fine_tunes.create -t <TRAIN_FILE_ID_OR_PATH> \
  -v <VALIDATION_FILE_ID_OR_PATH> \
  -m <MODEL>
```

検証書類を提供していただければ、訓練期間中に定期的に大量検証データの指標を計算します。結果ファイルに以下の追加指標が表示されます。

- **validation\_loss**:バッチの損失を検証します。

- **validation\_sequence\_accuracy**:バッチのモデルの予測マークと実際の完成マークが完全に一致する完成パーセントを検証します。例えば、aが`batch_size`3なら、もしあなたのデータに完成 \[ \[1, 2\] , \[0, 5\] , \[4, 2\] \]とモデル予測 \[ \[1, 1\] , \[0, 5\]、\[4, 2\] \]、この正確度は2/3 = 0.67になります。

- **validation\_token\_accuracy**:モデルが正確に予測した検証バッチに表記されたパーセント。例えば、aが`batch_size`3なら、もしあなたのデータに完成 \[ \[1, 2\] , \[0, 5\] , \[4, 2\] \]とモデル予測 \[ \[1, 1\] , \[0, 5\]、\[4, 2\] \]、この正確性は5/6 = 0.83になります。

## ハイパーパラメータ

私たちは一連のユースケースに適用されるデフォルトのハイパーパラメータを選択しました。必要な唯一のパラメータは訓練ファイルです。

つまり、微調整用の超パラメータを調整すると、通常、より高品質の出力を生み出すモデルが生成される。特に、以下の内容を配置する必要があるかもしれません。

- `model`：微調整する基本モデルの名前。あなたは「ada」、「babbage」、「curie」または「davinci」のいずれかを選択することができます。これらのモデルの詳細については、[モデル](https://platform.openai.com/docs/models)のドキュメントを参照してください。

- `n_epochs`\- デフォルトは4です。モデルを訓練する期間の数。紀元とは訓練データセットの完全な周期を意味する。

- `batch_size`\- デフォルトは訓練集中の例数の0.2%で、上限は256です。バッチサイズは、単一の正方向と逆方向の伝達を訓練するための訓練例の数である。全体的に、私たちはより大きなバッチサイズがより大きなデータセットにもっと適していることを発見しました。

- `learning_rate_multiplier`\- デフォルトは0.05、0.1または0.2で、具体的にはfinal `batch_size`によって異なります。微調整学習率とは、予備訓練のための原始学習率にその乗数を掛けることです。0.02から0.2の範囲の値を使ってテストして、最適な結果を生み出す値を確認することをお勧めします。経験から、私たちは大きな学習率が通常大きなロットサイズでより良いパフォーマンスを発揮することを発見しました。

- `compute_classification_metrics`\- デフォルトは偽です。Trueの場合、分類任務を微調整するために、各 epoch終了時に検証セットで分類特定の指標(正確性、F-1点数など)を計算します。

これらの追加のスーパーパラメータを設定するには、OpenAI CLIのコマンドラインマークを通じてそれらを渡してください。例えば：

```
openai api fine_tunes.create \
  -t file-JD89ePi5KMsB3Tayeli5ovfW \
  -m ada \
  --n_epochs 1
```

##　微調整モデルから引き続き微調整します。

もしあなたの任務のためにモデルを微調整し、今統合したい追加訓練データがあれば、モデルから引き続き微調整することができます。これは最初から再訓練することなく、すべての訓練データから学ぶモデルを作るだろう。

そのため、新しい微調整作業を作成する時、微調整後のモデル名(例えば`-m curie:ft-<org>-<date>`)を入力してください。他の訓練パラメータは変更する必要はありませんが、もしあなたの新しい訓練データが以前の訓練データよりずっと小さい場合、`learning_rate_multiplier`2倍から4倍に減らすのが役に立つかもしれません。

## 重みと偏差

微調整を[重みと偏差同期](https://wandb.me/openai-docs)で実験、モデル、データセットを追跡することができます。

使い始めるには、[Weights & Biases](https://wandb.me/openai-docs)アカウントと有料のOpenAIプランが必要です。最新バージョンの`openai`and `wandb`を使用していることを確認するために、実行してください：

```
pip install --upgrade openai wandb
```

微調整と重みと偏差を同期するには、実行してください：

[重みと偏差文書](https://wandb.me/openai-docs)を読んで、この統合についてより多くの情報を得ることができます。

## サンプルノート

[微調整分類.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Fine-tuned_classification.Ipynb)

このノートはどうやってモデルを微調整するかを実証します。このモデルは入力テキストが野球やホッケーと関係があるかどうかを分類することができます。[私たちはノートにいます](https://github.com/openai/openai-cookbook/blob/main/examples/Fine-tuned_classification.ipynB)中四つの段階に分けてこの任務を遂行する：

1.**データ探索**はデータソースと例を概観します。
2.**データ準備**は、データソースを微調整に使用できるjsonlファイルに変換します
3.**ファインチューニング**は、微調整作業を開始し、結果のモデルのパフォーマンスを説明します
4.**モデルを使用する**は、予測を得るために微調整されたモデルに要求を行うことを示します。

[olympics-1-collect-data.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-1-collect-data.ipynb)[olympics-2-create-qa.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-2-create-qa.ipynb)[olympics-3-train-qa.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-3-train-qa.ipynb)

このプロジェクトのアイデアは、提供されたテキストのいくつかの段落に基づいて、質問回答モデルを作成することです。ベースGPT-3モデルは、答えが段落内に含まれているときに質問に答えるのに良い仕事をしますが、答えが含まれていない場合、ベースモデルはとにかく答えるために最善を尽くす傾向があり、しばしば混乱した答えにつながります。

十分なコンテキストがある場合にのみ質問に答えるモデルを作成するには、まずテキストの段落に基づいて質問と回答のデータセットを作成します。答えが存在する場合にのみ答えるようにモデルを訓練するために、質問が文脈と一致しない敵対的な例も追加します。そのような場合、モデルに「質問に答えるのに十分なコンテキストがない」と出力するように依頼します。

このタスクを3つのノートブックで実行します。

1.[最初のノートブック](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-1-collect-data.ipynb)は、GPT-3がプレトレーニング中に見なかった最近のデータの収集に焦点を当てています。2020年オリンピック(実際には2021年の夏に開催された)のトピックを選び、713のユニークなページをダウンロードしました。個々のセクションごとにデータセットを整理し、質問をして回答するためのコンテキストとして機能します。

2.[2番目のノートブック](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-2-create-qa.ipynb)は、Davinci-instructを利用して、ウィキペディアのセクションに基づいていくつかの質問をし、そのセクションに基づいてそれらの質問に答えます。
3.[3 番目のノートブック](https://github.com/openai/openai-cookbook/blob/main/examples/fine-tuned_qa/olympics-3-train-qa.ipynb)は、コンテキスト、質問、および回答のペアのデータセットを利用して、そのコンテキストで質問が生成されなかった敵対的な質問とコンテキストのペアをさらに作成します。そのような場合、モデルは「質問に答えるのに十分なコンテキストがありません」と答えるように求められます。また、コンテキストに基づいて質問に回答できるかどうかを予測する識別モデルをトレーニングします。