---
sidebar_position: 8
---

# レート制限

## 序章

### レート制限とは何ですか? 

レート制限は、指定された期間内にユーザーまたはクライアントがサーバーにアクセスできる回数に対して API によって課される制限です。

### レート制限があるのはなぜですか?

レート制限は API の一般的な方法であり、いくつかの異なる理由で設定されています。

-   API の乱用や誤用を防ぐのに役立ちます。たとえば、悪意のあるアクターは、API を過負荷にしたり、サービスの停止を引き起こしたりするために、リクエストで API をフラッディングする可能性があります。レート制限を設定することにより、OpenAI はこのアクティビティの発生を防ぎます。
-   レート制限により、誰もが API に公平にアクセスできるようになります。1 人または組織があまりにも多くのリクエストを行うと、他の全員の API の使用が遅くなる可能性があります。1 人のユーザーが行うことができるリクエストの数を規制することにより、OpenAI は、最大数の人が速度低下を経験することなく API にアクセスできるようにします。
-   レート制限は、OpenAI がインフラストラクチャの全体的な負荷を管理するのに役立ちます。API へのリクエストが大幅に増加すると、サーバーに負荷がかかり、パフォーマンスの問題が発生する可能性があります。レート制限を設定することで、OpenAI はすべてのユーザーに対してスムーズで一貫したエクスペリエンスを維持するのに役立ちます。

OpenAI の速度極値システムがどのように機能するかをよりよく理解するには、このドキュメント全体をお読みください。一般的な問題に対処するために必要なコード サンプルとソリューションを提供します。極端な成長リクエスト フォームに記入する前に、このガイドに従ってください。フォームの記入方法については、最後のセクションで詳しく説明します。

### 私たちの API の極端な点は何ですか?

特定のエンドポイントの使用と所有するアカウントの種類に基づいて、ユーザー レベルではなく組織レベルで極端な価値管理を実施します。極値は、RPM (1 分あたりの要求数) と TPM (1 分あたりの用語) の 2 つの方法で測定されます。以下の表は、API のデフォルトの極端な値を示していますが、「レート制限の引き上げリクエスト」フォームに記入することで、ユース ケースに基づいてこれらを引き上げることができます。

TPM (ティック/分) の単位はモデルによって異なります。

| タイプ | 1TPM相当 |
| --- | --- |
| davinci | 1 ワード/分 |
| curie | 25ワード/分 |
| babbage | 100ワード/分 |
| ada | 200ワード/分 |

実用的な観点からは、これは 1 分間に約 200 倍のトークンを ada モデルに送信できることを意味し、davinci モデルに比べてより多くのトークンを送信できます。

|  | テキストと埋め込み | チャット | 法典 | 編集 | 画像 | 音 |
| --- | --- | --- | --- | --- | --- | --- |
| 無料試用ユーザー | 20rpm |  |  |  |  |  |
| 150,000rpm | 20rpm |  |  |  |  |  |
| 40,000rpm | 20rpm |  |  |  |  |  |
| 40,000rpm | 20rpm |  |  |  |  |  |
| 150,000rpm | 50枚/分 | 50rpm |  |  |  |  |
| 従量制ユーザー (最初の 48 時間) | 60rpm |  |  |  |  |  |
| 250,000rpm | 60rpm |  |  |  |  |  |
| 60,000rpm | 20rpm |  |  |  |  |  |
| 40,000rpm | 20rpm |  |  |  |  |  |
| 150,000rpm | 50枚/分 | 50rpm |  |  |  |  |
| 従量制ユーザー (48 時間後) | 3,500rpm |  |  |  |  |  |
| 350,000rpm | 3,500rpm |  |  |  |  |  |
| 90,000rpm | 20rpm |  |  |  |  |  |
| 40,000rpm | 20rpm |  |  |  |  |  |
| 150,000rpm | 50枚/分 | 50rpm |  |  |  |  |

レート制限は、どちらが最初に発生したかに応じて、どちらのオプションでもトリガーできることに注意してください。たとえば、Codex エンドポイントに 20 個のリクエストを送信し、100 個のトークンのみを使用して制限を満たすことができますが、それらの 20 個のリクエストで 40,000 個のトークンは送信されません。

### レート制限はどのように機能しますか?

レート制限が 1 分あたり 60 リクエストで、1 分あたり 150,000 davinci トークンである場合、2 つのいずれか早い方で制限されます。たとえば、1 分あたりの最大リクエスト数が 60 の場合、1 秒あたり 1 つのリクエストを送信できるはずです。800 ミリ秒ごとに 1 つのリクエストを送信する場合、レート制限に達した後、別のリクエストを送信する前にプログラムを 200 ミリ秒スリープ状態にします。そうしないと、後続のリクエストは失敗します。デフォルト値の 3,000 リクエスト/分では、クライアントはデフォルトで 20 ミリ秒または 0.02 秒ごとに 1 つのリクエストを効果的に送信できます。

### レート制限エラーに遭遇したらどうなりますか？

レート制限エラーは次のようなものに似ています： Rate limit reached for default-text-davinci-002 in organization org-{id} on requests per min. Limit: 20.000000 / min. Current: 24.000000 / min. レート制限に遭遇することは、短時間で多すぎるリクエストを送信して、APIは一定の時間が経過しないと、リクエストを拒否するようになることを意味しています。

### 速度の上限とmax\_tokens

私たちが提供した各モデルには固定された数のトークンがあり、入力としてそれらに渡して処理することができます。モデル受信マーク数上界を増やすことはできません。例えば、text-ada-001を使用する場合、このモデルに最大2048語元各要求を送ることができます。

## エラーの処理

### このような問題を緩和するためにどのような措置が講じられますか？

OpenAI Cookbookには[Pythonノート]がある(https://github.com/openai/openai-cookbook/blob/main/examples/How_to_Handle_rate_limits.ipynb)は周波数が極めて高い(rate limit)エラーを避ける方法を詳しく説明する。

プログラミングアクセス、バッチ処理機能、自動化ソーシャルメディアの発表を提供する場合、慎重に考慮してください。信頼できる顧客に対してのみこれらの機能を有効にすることを考慮することができます。

自動化と大容量の濫用を防ぐため、指定された時間範囲(日、週、月)に単一ユーザー使用量上界を設定します。この上界を超えたユーザーについては、ハード規定や手動審査プロセスの実施を検討してください。

###　指数で後退して再試行

APIメソッドを頻繁に呼び出して頻繁にエラーを報告するのを避けるのも簡単です。ランダムに待ってAPIメソッドの呼び出しをやり直せばいいです!具体的なやり方は、APIが「429 Too Many Requests」状態コードを返す時、コードの断片の実行を一時停止し、現在何回か再試行したことに基づいて待機時間tを計算し、API方法を呼び出すことを試みます。もし依然として「429」に戻る場合。Too Many Requests」ステータスコードはt秒を一時停止した後、上記の操作を繰り返します...データの取得が成功するまで!

指数リターンとは、最初の rate limitエラーに命中した時に短い休眠を実行し、失敗したリクエストを再試行することを意味する。Requestがまだ成功しない場合、sleepの長さを増やし、その過程を繰り返します。これはリクエストが成功するか、最大再試行回数に達するまで続きます。この方法には多くの長所がある：

- 自動再試行は、速度制限エラーから回復でき、クラッシュしたり、データを紛失したりしないという意味です。

- 指数後退は、まずショートカットのretryを試し、同時に長い遅延からretryの利益を得ることを意味するので、最初の数回のretryは失敗しました。

- ランダム振動を追加してretriesを遅延させ、異なる瞬間に全てのretriesに当たる効果。


ご注意ください。成功しない要請は1分間の制限に影響を及ぼすので、継続して要請を再送信することはうまくいきません。以下は指数回避を使用するPythonの例の解決策です。

#### 例1：Tenacitライブラリを使用する

TenacityはApache 2.0ライセンスの汎用再試行ライブラリで、Pythonで作成され、再試行行為をほぼすべての内容に追加するタスクを簡素化することを目的としています。あなたの要請に指数退避を追加するには、tenacity.retryデコレータを使用してください。次の例はtenacity.wait\_random\_exponential関数を使って要求にランダム指数退避を追加します。

```python
import openai
from tenacity import (
    retry,
    stop_after_attempt,
    wait_random_exponential,
)  # for exponential backoff

@retry(wait=wait_random_exponential(min=1, max=60), stop=stop_after_attempt(6))
def completion_with_backoff(**kwargs):
    return openai.Completion.create(**kwargs)

completion_with_backoff(model="text-davinci-003", prompt="Once upon a time,")
```

Tenacityライブラリは第三者ツールであり、OpenAIはその信頼性や安全性についていかなる保証もしません。

#### 例2：backoffライブラリを使用する

もう一つの退避と再試行機能修飾を提供するPythonライブラリはbackoffです。
```
import backoff
import openai
@backoff.on_exception(backoff.expo, openai.error.RateLimitError)
def completions_with_backoff(**kwargs):
    return openai.Completion.create(**kwargs)

completions_with_backoff(model="text-davinci-003", prompt="Once upon a time,")
```

#### 例3:手動で指数の後退を実現する。

第三者ライブラリを使いたくないなら、この例に従って自分の退避論理を実現できます。

```python
# imports
import random
import time

import openai

# define a retry decorator
def retry_with_exponential_backoff(
    func,
    initial_delay: float = 1,
    exponential_base: float = 2,
    jitter: bool = True,
    max_retries: int = 10,
    errors: tuple = (openai.error.RateLimitError,),
):
    """Retry a function with exponential backoff."""

    def wrapper(*args, **kwargs):
        # Initialize variables
        num_retries = 0
        delay = initial_delay

        # Loop until a successful response or max_retries is hit or an exception is raised
        while True:
            try:
                return func(*args, **kwargs)

            # Retry on specific errors
            except errors as e:
                # Increment retries
                num_retries += 1

                # Check if max retries has been reached
                if num_retries > max_retries:
                    raise Exception(
                        f"Maximum number of retries ({max_retries}) exceeded."
                    )

                # Increment the delay
                delay *= exponential_base * (1 + jitter * random.random())

                # Sleep for the delay
                time.sleep(delay)

            # Raise exceptions for any errors not specified
            except Exception as e:
                raise e

    return wrapper

@retry_with_exponential_backoff
def completions_with_backoff(**kwargs):
    return openai.Completion.create(**kwargs)
```

もう一度、OpenAIはこの解決策の安全性や効率について何の保証もしませんが、あなた自身の解決策の良い出発点になることができます。

### バッチのリクエスト

OpenAI APIは1分間のリクエスト数とトークン数に個別の制限があります。

もし1分間のリクエスト回数の制限に達したが、1分間のトークンに利用可能な容量があれば、複数のタスクを各リクエストに分けて処理してスループットを増やすことができます。これは、特に私たちの小さなモデルのために、より多くのトークンを処理できるようにしてくれるだろう。

一連のヒントを送るのは正常なAPI呼び出しと全く同じで、文字列リストをpromptパラメータに渡すだけでいいです。

#### バッチ処理を使わない例:

```python
import openai

num_stories = 10
prompt = "Once upon a time,"

# serial example, with one story completion per request
for _ in range(num_stories):
    response = openai.Completion.create(
        model="curie",
        prompt=prompt,
        max_tokens=20,
    )
    # print story
    print(prompt + response.choices[0].text)
```

バッチ処理を使用する例

```python
import openai  # for making OpenAI API requests


num_stories = 10
prompts = ["Once upon a time,"] * num_stories

# batched example, with 10 story completions per request
response = openai.Completion.create(
    model="curie",
    prompt=prompts,
    max_tokens=20,
)

# match completions to prompts by index
stories = [""] * len(prompts)
for choice in response.choices:
    stories[choice.index] = prompts[choice.index] + choice.text

# print stories
for story in stories:
    print(story)
```

>警告:応答対象は提示された順序で完了状況に戻らないかもしれないので、常に索引フィールドを使って応答と提示を一致させることを覚えておいてください。

## リクエスト増加

###　いつ速度制限の増加を申請することを考えるべきですか?

私たちのデフォルトの速度制限は安定性を最大化し、私たちのAPIの濫用を防ぐのに役立つ。私たちは制限を増やして高トラフィックアプリケーションを起動します。なので、申請速度制限の増加に最適な時間は、あなたが必要なトラフィックデータを持って速度制限の向上をサポートする強力な理由があると思う時です。サポートデータなしで大幅な速度制限の増加要請が承認される可能性は低い。製品発表を準備しているなら、10日間の段階的な発表を通じて関連データを得てください。

速度制限の増加は7-10日かかることを覚えてください。なので、現在の成長数字があなたの速度制限に達するのに必要なデータがあれば、早めに計画して提出するのが賢明です。

###　速度制限増加要請は拒否されますか?

ありふれた原因は、その妥当性を証明するのに必要なデータの不足による拒絶だ。以下は数値例を提供し、速度上昇要求を最適にサポートする方法を示し、セキュリティ戦略と表示支援データ要求に合致するすべての要求をできるだけ承認します。私たちは開発者が私たちのAPIを使って規模化と成功できるように努力しています。

###　すでにテキスト/コードAPIのために指数回避アルゴリズムを実現しましたが、まだエラーが出ています。どうやって私のオンライン頻度を上げるのですか？

現在、私たちは無料テストエンドポイント(例えば編集エンドポイント)などの機能の向上を支援していません。私たちはChatGPTのオンライン頻度も上げませんが、ChatGPTプロ版アクセスリストに参加できます。

私たちは頻繁にオンラインに拘束されることがどれだけの挫折感をもたらすかを知っていて、すべての人のためにデフォルト値を上げたいです。しかし、共有容量の制約のため、 Rate Limit Increase Requestフォームでは有料顧客証明のみ承認され、審査を通過した後、頻度オンライン調整が可能です。本当にどんな内容が必要なのかを評価するために、「需要証拠を共有する」部分で現在の使用状況や歴史ユーザー活動予測に基づいた統計情報を提供してください。これらの情報がなければ、段階的なリリース方法を採用することをお勧めします。まず、現在の比例で一部のユーザーにサービスを解放し、10営業日以内に使用状況データを収集し、そのデータに基づいて正式な頻度のオンライン調整要請を提出して審査と承認を提供します。

申請を提出して承認を受けたら、7-10営業日以内に承認結果をお知らせします。以下はこのフォームを記入するいくつかの例です。

#### DALL-E API 示例

| モデル | 推定トークン/分 | 推定リクエスト数 | ユーザー数 | 必要な証拠 | 1時間の最大スループットコスト |
| --- | --- | --- | --- | --- | --- |
| DALL-E API | N/A | 50 | 1000 | 私たちのアプリケーションは現在生産中です。過去の流量状況によって、1分間に約10個の要請を出します。 | $60 |
| DALL-E API | N/A | 150 | 10,000 | 私たちのアプリはApp Storeでますます人気になり、私たちは速度制限に遭遇し始めた。私たちはデフォルトの制限の3倍、つまり1分間に50個の画像を得ることができますか？もっと必要であれば、私たちは新しいフォームを提出するつもりだ。ありがとうございます！ | $180 |

言語モデルの例

| モデル | 推定トークン/分 | ESTIMATE 推定リクエスト数 REQUESTS/MINUTE | ユーザー数 | 必要な証拠 | 1時間の最大スループットコスト |
| --- | --- | --- | --- | --- | --- |
| text-davinci-003 | 325,000 | 4,0000 | 50 | 私たちは初期アルファテスターのグループに発表し、彼らの初期使用に適応するためにより高い制限が必要です。私たちはここで私たちのGoogle Driveへのリンクを提供し、分析とAPIの使用状況を表示します。 | $390 |
| text-davinci-002 | 750,000 | 10,000 | 10,000 | 私たちのアプリは多くの注目を集めており、50,000 人が順番待ちリストに載っています。50,000 人のユーザーに到達するまで、1 日あたり 1,000 人のグループにロールアウトしたいと考えています。過去 30 日間の現在のトークン/分トラフィックについては、このリンクを参照してください。これは 500 ユーザーの場合で、ユーザーの使用状況に基づいて、1 分あたり 750,000 トークン、1 分あたり 10,000 リクエストが適切な出発点になると考えています。 | $900 |

#### コードモデルの[例](https://openai.xiniushu.com/docs/guides/rate-limits#code-%E6%A8%A1%E5%9E%8B%E7%A4%BA%E4%BE%8B "コード モデルの例への直接リンク")

| 模型 | 推定トークン/分 | ESTIMATE 推定リクエスト数 REQUESTS/MINUTE | ユーザー数 | 必要な証拠 | 1 時間の最大スループット コスト |
| --- | --- | --- | --- | --- | --- |
| コードダヴィンチ-002 | 150,000 | 1,000 | 15 | 私たちは論文に取り組んでいる研究者のグループです。今月末までに調査を完了するには、code-davinci-002 のレート制限を引き上げる必要があると見積もっています。これらの見積もりは、次の計算に基づいています\[ …\] | Codex モデルは現在無料のベータ版であるため、これらのモデルの増分をすぐに提供できない場合があります。 |

これらの例は一般的な使用例のシナリオのみであり、実際の使用法は特定の実装と使用例によって異なることに注意してください。