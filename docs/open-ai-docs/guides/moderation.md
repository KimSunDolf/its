# 審査（Moderation）

## 序章

[Moderation](https://platform.openai.com/docs/api-reference/moderations) Endpoint(エンドポイント)は内容をチェックできるツールです。OpenAI [使用戦略](https://openai.com/policies/usage-policies)に合致します。したがって、開発者は私たちの使用方針で禁止されている内容を識別し、例えばフィルタリングを通じて行動することができます。

これらのモデルは次のカテゴリーに分けられる：

| カテゴリ           | 説明                                                         |
| ------------------ | ------------------------------------------------------------ |
| `hate`             | 人種、性別、民族、宗教、国籍、性的指向、障害状況、またはカーストに基づいた憎しみを表現、扇動、または宣伝する内容。 |
| `hate/threatening` | 憎悪の内容は、対象集団に対する暴力や深刻な被害も含まれる。   |
| `self-harm`        | 自傷行為(例えば自殺、切り傷、摂食障害)を宣伝、奨励、または描写する内容。 |
| `sexual`           | 性的興奮を引き起こすことを目的とする内容、例えば、性活動の描写、または性サービス(性教育と健康を除く)を宣伝する内容。 |
| `sexual/minors`    | 18歳未満の個人のポルノコンテンツが含まれています。           |
| `violence`         | 暴力を宣伝したり美化したり、他人の苦難や屈辱を賛美する内容。 |
| `violence/graphic` | 死、暴力、または深刻な身体的傷害の暴力的な内容を極端に血なまぐさい細部で描写する。 |

OpenAI APIの入力と出力を監視する時、審査エンドポイントを無料で利用できます。私たちは現在、第三者のトラフィックの監視を支援していません。

私たちは分類器の正確性を高めるために努力しています。特に`hate`、`self-harm`と`violence/graphic`の内容の分類の改善に力を入れています。非英語言語に対する私たちの支援は現在限られている。

## クイックスタート

テキストの分類を取得するには、以下のコードの断片に示すように、[審査終了点](https://platform.openai.com/docs/api-reference/moderations)に要請してください。

```python
response = openai.Moderation.create(
    input="Sample text goes here"
)
output = response["results"][0]
```



以下は終点の例出力です。それは次のフィールドを返します。

- `flagged`:もしモデルが内容をOpenAIの使用戦略に違反すると分類すれば、設定します。`true``false`

- `categories`:各カテゴリーの二進法使用戦略衝突マークを含む辞書。各カテゴリーに対して、この値はモデルが該当カテゴリーを`true`違反とマークし、そうでなければ`false`です。

- `category_scores`:モデル出力の各カテゴリーの原始点数を含む辞書は、モデルが入力がOpenAIカテゴリー戦略に違反する信頼度を表す。この値は0と1の間であり、値が高いほど信頼度が高い。点数は確率として解釈されてはならない。

```python
{
  "id": "modr-XXXXX",
  "model": "text-moderation-001",
  "results": [
    {
      "categories": {
        "hate": false,
        "hate/threatening": false,
        "self-harm": false,
        "sexual": false,
        "sexual/minors": false,
        "violence": false,
        "violence/graphic": false
      },
      "category_scores": {
        "hate": 0.18805529177188873,
        "hate/threatening": 0.0001250059431185946,
        "self-harm": 0.0003706029092427343,
        "sexual": 0.0008735615410842001,
        "sexual/minors": 0.0007470346172340214,
        "violence": 0.0041268812492489815,
        "violence/graphic": 0.00023186142789199948
      },
      "flagged": false
    }
  ]
}
```



OpenAIは審査エンドポイントの基礎モデルを絶えずアップグレードします。したがって、時間が経つにつれて、`category_scores`に依存するカスタムポリシーは再調整する必要があるかもしれません。