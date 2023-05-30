---
sidebar_position: 5
---

# 埋め込み（Embeddings）

## 埋め込みとは？

OpenAI のテキスト埋め込みは、テキスト文字列の関連性を測定します。埋め込みは、一般的に次の目的で使用されます。

-   検索 (クエリ文字列との関連性で並べ替えられた結果)
-   クラスタリング (テキスト文字列が類似度によってグループ化される)
-   Recommend (関連するテキスト文字列を持つアイテムをお勧めします)
-   異常検出 (ほとんど相関のない外れ値を特定)
-   多様性尺度（類似度分布の分析）
-   分類 (テキスト文字列が最も類似したラベルによって分類される場合)

埋め込みは、float のベクトル (リスト) です。2 つのベクトル間の距離は、それらの関連性を測定します。距離が小さいほど相関が高く、距離が大きいほど相関が低いことを示します。

[組み込みの価格については、価格ページを](https://openai.com/api/pricing/)ご覧ください。リクエストは、送信された入力の[トークン](https://platform.openai.com/tokenizer)数に基づいて課金されます。

## 埋め込む方法

埋め込みを取得するには、選択した埋め込みモデル ID (たとえば、text-embedding-ada-002) と共にテキスト文字列を埋め込み API エンドポイントに送信します。応答には、抽出、保存、および使用できる埋め込みが含まれます。

リクエストの例:

```curl
curl https://api.openai.com/v1/embeddings \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "input": "Your text string goes here",
    "model": "text-embedding-ada-002"
  }'
```

応答例:

```
{
  "data": [
    {
      "embedding": [
        -0.006929283495992422,
        -0.005336422007530928,
        ...
        -4.547132266452536e-05,
        -0.024047505110502243
      ],
      "index": 0,
      "object": "embedding"
    }
  ],
  "model": "text-embedding-ada-002",
  "object": "list",
  "usage": {
    "prompt_tokens": 5,
    "total_tokens": 5
  }
}
```

[OpenAI クックブック](https://github.com/openai/openai-cookbook/)で  その他の Python コード例を確認してください  。

## 組み込みモデル

OpenAI  提供了一个第二代嵌入模型（在模型  ID  中用  -002  表示）和  16  个第一代模型（在模型  ID  中用  -001  表示）。

我们建议对几乎所有用例使用  text-embedding-ada-002。它更好、更便宜、更易于使用。

| モデル生成 | 分詞器 | 最大入力トークン | データの締切日 |
| --- | --- | --- | --- |
| V2 | cl100k\_base | 8191 | Sep 2021 |
| V1 | GPT-2/GPT-3 | 2046 | Aug 2020 |

使用量は入力トークンによって定価され、1000個のトークンごとに0.0004ドル、またはドル当たり約3,000ページ(ページごとに約800個のトークンを仮定する):

| モデル                 | ドル当たりの大まかなページ数 | BEIR SEARCH EVAL の性能例 |
| ---------------------- | ---------------------------- | ------------------------- |
| text-embedding-ada-002 | 3000                         | 53.9                      |
| \*\-davinci-\*\-001    | 6                            | 52.8                      |
| \*\-curie-\*\-001      | 60                           | 50.9                      |
| \*\-babbage-\*\-001    | 240                          | 50.4                      |
| \*\-ada-\*\-001        | 300                          | 49.0                      |

### 第二世代モデル

| モデル名 | 分詞器 | 最大入力トークン | アウトプット |
| --- | --- | --- | --- |
| text-embedding-ada-002 | cl100k\_base | 8191 | 1536 |

### 第一世代モデル(おすすめしない)

すべての第一世代モデル(-001で終わるモデル)はGPT-3分詞器を使用し、最大入力は2046個の分詞です。

第一世代の埋め込みは5つの異なるモデルシリーズによって生成され、これらのモデルシリーズは3つの異なる任務に対して調整された：テキスト検索、テキスト類似性、コード検索。検索モデルがペアで現れる：一つは短い検索用、もう一つは長い文書用。各シリーズには最大4つの品質と速度の異なるモデルが含まれています。

| モデル | アウトプット |
| --- | --- |
| Ada | 1024 |
| Babbage | 2048 |
| Curie | 4096 |
| Davinci | 12288 |

Davinciは最も有能だが、他のモデルより遅くて高い。Adaの能力は最悪だが、速度がもっと速くてコストがもっと安い。

### 類似性埋め込み

類似性モデルは、テキストの断片間の意味的類似性を捉えるのが最も得意である。

| ユースケース | 利用可能なモデル |
| --- | --- |
| Clustering, regression, anomaly detection, visualization | `text-similarity-ada-001` |

`text-similarity-babbage-001`  
`text-similarity-curie-001`  
`text-similarity-davinci-001` |

### テキスト検索埋め込み

テキスト検索モデルは、どの長い文書が短い検索検索に最も関連しているかを測定するのに役立つ。二つのモデルが使われています。一つは検索検索を埋め込むためのもので、もう一つはランキングする文書を埋め込むためのものです。検索埋め込みに最も近い文書埋め込みは最も関連があるべきだ。

| ユースケース | 利用可能なモデル |
| --- | --- |
| Search, context relevance, information retrieval | `text-search-ada-doc-001` |

`text-search-ada-query-001`  
`text-search-babbage-doc-001`  
`text-search-babbage-query-001`  
`text-search-curie-doc-001`  
`text-search-curie-query-001`  
`text-search-davinci-doc-001`  
`text-search-davinci-query-001` |

### コード検索埋め込み

検索埋め込みと似ていて、2つのタイプがあります。一つは自然言語検索検索を埋め込み、もう一つは検索するコードの断片を埋め込みます。

| ユースケース | 利用可能なモデル |
| --- | --- |
| Code search and relevance | `code-search-ada-code-001` |

`code-search-ada-text-001`  
`code-search-babbage-code-001`  
`code-search-babbage-text-001` |

-001テキスト埋め込み(-002ではなく、コード埋め込みでもない)については、入力中の改行(\n)を単一スペースに置き換えることを提案します。改行がある場合、私たちはすでにもっと悪い結果を見たからです。

## ユースケース

在这里，我们展示了一些有代表性的用例。我们将在以下示例中使用[亚马逊美食评论数据集](https://www.kaggle.com/snap/amazon-fine-food-reviews)。

### 埋め込みを取得する

このデータセットには2012年10月までにアマゾンユーザーが残した合計568,454件の食品レビューが含まれています。私たちは1000個の最新コメントのサブセットを説明目的に使用する。論評は英語で、しばしば肯定的または否定的だ。各コメントにはProductId、UserId、Score、コメントタイトル(Summary)とコメント本文(Text)があります。例えば：

| PRODUCT ID | USER ID | SCORE | SUMMARY | TEXT |
| --- | --- | --- | --- | --- |
| B001E4KFG0 | A3SGXH7AUHU8GW | 5 | Good Quality Dog Food | I have bought several of the Vitality canned... |
| B00813GRG4 | A1D87F6ZCVE5NK | 1 | Not as Advertised | Product arrived labeled as Jumbo Salted Peanut... |

私たちはコメント要約とコメントテキストを一つの組み合わせテキストに統合します。このモデルは、この組み合わせのテキストをエンコードし、単一のベクトル埋め込みを出力する。

[Obtain\_dataset.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Obtain_dataset.ipynb)

```
def get_embedding(text, model="text-embedding-ada-002"):
   text = text.replace("\n", " ")
   return openai.Embedding.create(input = [text], model=model)['data'][0]['embedding']

df['ada_embedding'] = df.combined.apply(lambda x: get_embedding(x, model='text-embedding-ada-002'))
df.to_csv('output/embedded_1k_reviews.csv', index=False)
```

保存されたファイルからデータをロードするには、次のコマンドを実行することができます。

```
import pandas as pd

df = pd.read_csv('output/embedded_1k_reviews.csv')
df['ada_embedding'] = df.ada_embedding.apply(eval).apply(np.array)
```

-   二次元データ可視化

[Visualizing\_embeddings\_in\_2D.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Visualizing_embeddings_in_2D.ipynb)

埋め込みの大きさは下層モデルの複雑性によって変わる。このような高次元データを可視化するために、私たちはt-SNEアルゴリズムを使ってデータを2次元に変換する。

私たちは評論家がくれた星評価に基づいて各コメントを着色します。

- 1-star:赤
- 2-star:ダークオレンジ
- 3-star:金色
- 4-star:ミントグリーン
- 5-star:濃い緑

![t-SNE を使用して言語で視覚化された Amazon の評価](https://atts.w3cschool.cn/attachments/day_230317/202303171534048246.png)

可視化は約3つのクラスターを生み出したようで、そのうちの1つのクラスターのコメントはほとんど否定的だった。

```
import pandas as pd
from sklearn.manifold import TSNE
import matplotlib.pyplot as plt
import matplotlib

df = pd.read_csv('output/embedded_1k_reviews.csv')
matrix = df.ada_embedding.apply(eval).to_list()

# Create a t-SNE model and transform the data
tsne = TSNE(n_components=2, perplexity=15, random_state=42, init='random', learning_rate=200)
vis_dims = tsne.fit_transform(matrix)

colors = ["red", "darkorange", "gold", "turquiose", "darkgreen"]
x = [x for x,y in vis_dims]
y = [y for x,y in vis_dims]
color_indices = df.Score.values - 1

colormap = matplotlib.colors.ListedColormap(colors)
plt.scatter(x, y, c=color_indices, cmap=colormap, alpha=0.3)
plt.title("Amazon ratings visualized in language using t-SNE")
```

-   MLアルゴリズムとして埋め込まれたテキスト特徴エンコーダ

[Regression\_using\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Regression_using_embeddings.ipynb)

機械学習モデルに埋め込まれた汎用フリーテキスト特徴エンコーダとして使用できる。もしいくつかの関連入力がフリーテキストなら、合併埋め込みはいかなる機械学習モデルの性能を向上させる。埋め込みはMLモデルの分類特徴エンコーダとしても使えます。分類変数の名前が意味があり、数が多い場合、例えば職名など、これは最大の価値を増加させる。この任務に対して、類似性埋め込みは通常検索埋め込みより優れている。

私たちは、通常、埋め込まれた表現が非常に豊富で情報集約的であることを観察した。例えば、SVDやPCAを使って入力の次元を下げると、10%下げても、通常特定のタスクの下流性能が悪くなります。

このコードはデータを訓練セットとテストセットに分割し、次の2つのユースケース、すなわち回帰と分類によって使われます。

```
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    list(df.ada_embedding.values),
    df.Score,
    test_size = 0.2,
    random_state=42
)
```

#### 埋め込み特徴を使って回帰する

埋め込みは数値を予測する優雅な方法を提供する。この例では、私たちはコメントのテキストに基づいて批評家の星を予測する。埋め込みに含まれる意味情報が高いので、コメントが少なくても予測は悪くない。

私たちは分数が1から5の間の連続変数であると仮定し、アルゴリズムが任意の浮動小数点値を予測することを許す。MLアルゴリズムは予測値と実際の分数の距離を最小化し、0.39の平均絶対誤差を実現し、これは平均予測偏差が半星未満であることを意味する。

```
from sklearn.ensemble import RandomForestRegressor

rfr = RandomForestRegressor(n_estimators=100)
rfr.fit(X_train, y_train)
preds = rfr.predict(X_test)
```

-   埋め込み特徴を使って分類します。

[Classification\_using\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Classification_using_embeddings.ipynb)

今回、私たちはアルゴリズムに1から5までのいかなる値も予測させず、コメントの正確な星数を1から5つ星まで5つのバレルに分類しようとします。

訓練後、このモデルは1星と5星のコメントがもっと細かいコメント(2-4星)より優れていると予測することを学びました。これはもっと極端な感情表現のせいかもしれません。

```
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report, accuracy_score

clf = RandomForestClassifier(n_estimators=100)
clf.fit(X_train, y_train)
preds = clf.predict(X_test)
```

-   ゼロサンプル分類

[Zero-shot\_classification\_with\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Zero-shot_classification_with_embeddings.ipynb)

私たちは何のマーキング訓練データもなく、埋め込みを使ってゼロレンズを分類することができます。各クラスに対して、私たちはクラス名やクラスの短い説明を埋め込む。ゼロサンプル方式でいくつかの新しいテキストを分類するために、その埋め込みをすべてのクラス埋め込みと比較し、最も高い類似度を持つクラスを予測します。

```
from openai.embeddings_utils import cosine_similarity, get_embedding

df= df[df.Score!=3]
df['sentiment'] = df.Score.replace({1:'negative', 2:'negative', 4:'positive', 5:'positive'})

labels = ['negative', 'positive']
label_embeddings = [get_embedding(label, model=model) for label in labels]

def label_score(review_embedding, label_embeddings):
   return cosine_similarity(review_embedding, label_embeddings[1]) - cosine_similarity(review_embedding, label_embeddings[0])

prediction = 'positive' if label_score('Sample Review', label_embeddings) > 0 else 'negative'
```

-   コールドスタートにおすすめのユーザーと製品の埋め込みを取得します。

[User\_and\_product\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/User_and_product_embeddings.ipynb)

私たちは彼らの全てのコメントを平均的にすることで、ユーザーの埋め込みを得ることができる。同様に、私たちはその製品に関するすべてのレビューを平均することで、製品の埋め込みを得ることができる。この方法の実用性を示すために、私たちは50kコメントのサブセットを使って各ユーザーと各製品のより多くのコメントをカバーします。

私たちは単独のテストセットでこれらの埋め込みの有用性を評価し、ユーザーと製品の埋め込みの類似性を採点関数として描きます。興味深いことに、この方法に基づいて、ユーザーが製品を受け取る前に、私たちはランダム予測より彼らがその製品が好きかどうかを予測することができます。
![スコアでグループ化された箱ひげ図](https://atts.w3cschool.cn/attachments/day_230317/202303171540147718.png)

```
user_embeddings = df.groupby('UserId').ada_embedding.apply(np.mean)prod_embeddings = df.groupby('ProductId').ada_embedding.apply(np.mean)
```

-   クラスタリング

[Clustering.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Clustering.ipynb)

クラスタリングは大量のテキストデータを理解する一つの方法だ。埋め込みは、各テキストの意味的に意味のあるベクトル表現を提供するため、このタスクに有用である。したがって、無監督方式で、クラスターは私たちのデータセットに隠されているグループ化を明らかにする。

この例で、私たちは4つの異なるクラスターを発見しました。一つは犬の餌に集中し、もう一つは否定的なコメントに集中し、二つは肯定的なコメントに集中します。

![t-SNE を使用して言語 2d で視覚化された識別されたクラスター](https://atts.w3cschool.cn/attachments/day_230317/202303171541017376.png)

```
import numpy as np
from sklearn.cluster import KMeans

matrix = np.vstack(df.ada_embedding.values)
n_clusters = 4

kmeans = KMeans(n_clusters = n_clusters, init='k-means++', random_state=42)
kmeans.fit(matrix)
df['Cluster'] = kmeans.labels_
```

-   埋め込まれたテキストを使って検索します。

[Semantic\_text\_search\_using\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Semantic_text_search_using_embeddings.ipynb)

最も関連のある文書を検索するために、検索の埋め込みベクトルと各文書の間の余弦類似度を使用し、得点が一番高い文書を返します。

```
from openai.embeddings_utils import get_embedding, cosine_similarity

def search_reviews(df, product_description, n=3, pprint=True):
   embedding = get_embedding(product_description, model='text-embedding-ada-002')
   df['similarities'] = df.ada_embedding.apply(lambda x: cosine_similarity(x, embedding))
   res = df.sort_values('similarities', ascending=False).head(n)
   return res

res = search_reviews(df, 'delicious beans', n=3)
```

-   埋め込まれたコードを使って検索します。

[Code\_search.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Code_search.ipynb)

コード検索の仕組みは埋め込みベースのテキスト検索と似ている。私たちは与えられたリポジトリにあるすべてのPythonファイルからPython関数を抽出する方法を提供します。そして各関数はtext-embedding-ada-002モデルによって索引される。

コード検索を実行するために、私たちは同じモデルを使ってクエリを自然言語に埋め込む。そして、埋め込みと各関数埋め込みの間の余弦類似度を調べる結果を計算します。最も高い余弦類似度の結果が最も関連がある。

```
from openai.embeddings_utils import get_embedding, cosine_similarity

df['code_embedding'] = df['code'].apply(lambda x: get_embedding(x, model='text-embedding-ada-002'))

def search_functions(df, code_query, n=3, pprint=True, n_lines=7):
   embedding = get_embedding(code_query, model='text-embedding-ada-002')
   df['similarities'] = df.code_embedding.apply(lambda x: cosine_similarity(x, embedding))

   res = df.sort_values('similarities', ascending=False).head(n)
   return res
res = search_functions(df, 'Completions API tests', n=3)
```

-   埋め込みを使う推薦

[Recommendation\_using\_embeddings.ipynb](https://github.com/openai/openai-cookbook/blob/main/examples/Recommendation_using_embeddings.ipynb)

埋め込みベクトル間の距離が短いほど類似度が高いので、埋め込みは推薦に使えます。

次に、私たちは基本的な推薦システムについて説明した。それは文字列リストと「ソース」文字列を受け入れ、それらの埋め込みを計算し、最も似ているものから最も似ていないものまで文字列のランキングを返します。具体的な例として、以下のリンクされたノートは、この関数のバージョンをAGニュースデータセット(2,000個のニュース記事の説明にサンプリング)に適用し、与えられたソース記事に最も似た最初の5つの記事を返します。

```
def recommendations_from_strings(
   strings: List[str],
   index_of_source_string: int,
   model="text-embedding-ada-002",
) -> List[int]:
   """Return nearest neighbors of a given string."""

   # get embeddings for all strings
   embeddings = [embedding_from_string(string, model=model) for string in strings]

   # get the embedding of the source string
   query_embedding = embeddings[index_of_source_string]

   # get distances between the source embedding and other embeddings (function from embeddings_utils.py)
   distances = distances_from_embeddings(query_embedding, embeddings, distance_metric="cosine")

   # get indices of nearest neighbors (function from embeddings_utils.py)
   indices_of_nearest_neighbors = indices_of_nearest_neighbors_from_distances(distances)
   return indices_of_nearest_neighbors
```

## 限界とリスク

私たちの埋め込みモデルは信頼できないか、場合によっては社会的リスクをもたらし、緩和措置なしに被害をもたらす可能性があります。

#### 社会偏見

> 限界:モデルは社会偏見をコードします。例えば、特定のグループに対する固定観念や否定的な感情を通じて。

SEAT(Mayら、2019年)とWinogender(Rudingerら、2018年)のベンチマークテストを通じてモデルに偏差がある証拠を発見しました。これらの基準には7つのテストが含まれており、モデルが性別名、地域名、特定の固定観念に適用される場合、隠れた偏見が含まれているかどうかを測定します。

例えば、アフリカ系アメリカ人の名前より、私たちのモデルは(a)ヨーロッパ系アメリカ人の名前と肯定的な感情、そして(b)黒人女性に対する否定的な固定観念を強く結びつけることを発見しました。

これらの基準はいくつかの面で限界があります。(a)彼らはあなたの特定のユースケースに広がらないかもしれないし、(b)彼らはごく一部の社会偏見だけをテストします。

これらのテストは初歩的であり、私たちはあなたの特定のユースケースに対してテストを実行することを提案します。これらの結果は、あなたのユースケースの明確な説明ではなく、その現象の存在の証拠とみなされなければならない。詳細については、私たちの使用方針を参照してください。

何か質問があれば、チャットで私たちのサポートチームに連絡してください。私たちは喜んでアドバイスを提供します。

####　最近起こった事件に目をつぶる。

>限界:モデルは2020年8月以降に発生した事件に対する理解が足りない。

私たちのモデルは8/2020以前の現実世界事件のいくつかの情報を含むデータセットで訓練します。もしあなたが最近の事件を代表するモデルに依存するなら、それらはうまくいかないかもしれない。

## よくある問題

### 文字列を埋め込む前に、どれだけ多くのマークがあるかどうやってわかりますか?

Pythonでは、OpenAIの分詞器[tiktoken](https://github.com/openai/tiktoken)を使って文字列を分詞に分割することができます。

サンプルコード:

```
import tiktoken

def num_tokens_from_string(string: str, encoding_name: str) -> int:
    """Returns the number of tokens in a text string."""
    encoding = tiktoken.get_encoding(encoding_name)
    num_tokens = len(encoding.encode(string))
    return num_tokens

num_tokens_from_string("tiktoken is great!", "cl100k_base")
```

Text-embedding-ada-002のような第二世代埋め込みモデルについては、cl100k\_baseコードを使用します。

詳細情報とサンプルコードはOpenAI Cookbookガイドにあります[tiktokenを使ってトークンを計算するか](https://github.com/openai/openai-cookbook/blob/main/examples/How_to_count_tokens_with_tiktoken.ipynb)。

### どうやってK個の最近の埋め込みベクトルを素早く検索しますか?

複数のベクトルを素早く検索するために、ベクトルデータベースの使用をお勧めします。GitHubの[Cookbook](https://github.com/openai/openai-cookbook/tree/main/examples/vector_databases)でベクトルデータベースとOpenAI APIを使用する例を見つけます。

ベクトルデータベースオプションは以下を含む:

- [Pinecone](https://github.com/openai/openai-cookbook/tree/main/examples/vector_databases/pinecone)、完全にホストされたベクトルデータベース

- [Weaviate](https://github.com/openai/openai-cookbook/tree/main/examples/vector_databases/weaviate)、オープンソースベクトル検索エンジン

- [Redis](https://github.com/openai/openai-cookbook/tree/main/examples/vector_databases/redis) ベクトル数として拠庫

- [Qdrant](https://github.com/openai/openai-cookbook/tree/main/examples/vector_databases/qdrant)、ベクトル検索エンジン

- [Milvus](https://github.com/openai/openai-cookbook/blob/main/examples/vector_databases/Using_vectoR_databases_for_embeddings_search.ipynb)、拡張可能な類似性検索のために構築されたベクトルデータベース

### どのdistance関数を使うべきですか?

私たちは余弦類似度を推薦する。距離関数の選択は普通無関係だ。

OpenAI埋め込みは長さ1に一元化され、これは:

-点積だけで少し速く余弦類似度を計算できます。

-余弦類似度とユークリッド距離は同じランキングに繋がります。

### 私の埋め込みをオンラインで共有できますか?

顧客は埋め込みの場合を含め、私たちのモデルのインプットとアウトプットを持っています。あなたは私たちのAPIに入力した内容がいかなる適用法や私たちの[使用条項](https://openai.com/policies/terms-of-use)に違反しないことを保証する責任があります。