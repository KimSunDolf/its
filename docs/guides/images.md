---
sidebar_position: 2
sidebar_label: 画像生成(Image generation)
---

# 画像生成`Beta`

DALL・Eモデルで画像を生成または処理する方法を学ぶ

## 序章

画像API には、画像を操作するための 3 つのメソッドが用意されています。

1.  テキスト プロンプトに基づいてゼロからイメージを作成する
2.  新しいテキスト プロンプトに基づいて既存の画像の編集を作成する
3.  既存の画像のバリアントを作成する

このガイドでは、これら 3 つの API エンドポイントを使用するための基本を、有用なコード サンプルで説明します。それらの動作を確認するには、[DALL・E プレビュー アプリケーション](https://labs.openai.com/)をチェックしてください。

Image API はベータ版です。この間、API とモデルはフィードバックに基づいて改善されます。すべてのユーザーが簡単にプロトタイピングできるように、デフォルトのレート制限は 1 分あたり 50 画像です。レート制限を引き上げる場合は、この[ヘルプ センターの記事](https://help.openai.com/en/articles/6696591)を確認してください。使用量と容量の要件についてさらに学習するにつれて、デフォルトのレート制限を引き上げます。

## 用法

### 生成

イメージ[生成](https://platform.openai.com/docs/api-reference/images/create)エンドポイントを使用すると、テキスト プロンプトを指定して生のイメージを作成できます。結果の画像は、256x256、512x512、または 1024x1024 ピクセルのサイズになります。サイズが小さいほど生成が速くなります。[n パラメータを使用して、](https://platform.openai.com/docs/api-reference/images/create#images/create-n)一度に 1 ～ 10 個の画像をリクエストできます。

```text
response = openai.Image.create(
  prompt="a white siamese cat",
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

説明が詳細になればなるほど、あなたやエンドユーザーが望む結果が得られる可能性が高くなります。[DALL·E プレビュー アプリケーション](https://labs.openai.com/)で例を調べて、さらにヒントを得ることもできます。簡単な例を次に示します。

各画像は、[response\_format](https://platform.openai.com/docs/api-reference/images/create#images/create-response_format) パラメーターを使用してURL または Base64 データとして返すことができます。URL は 1 時間で期限切れになります。

### 編集

画像[編集](https://platform.openai.com/docs/api-reference/images/create-edit)エンドポイントを使用すると、マスクをアップロードして画像を編集および拡張できます。マスクの透明な領域は、画像を編集する必要がある場所を示し、**消去された領域だけでなく**、完全な新しい画像を記述する必要があることを示唆しています。このエンドポイントにより、[DALL・E プレビュー アプリケーション](https://labs.openai.com/editor)でエディターのようなエクスペリエンスが可能になります。

```
response = openai.Image.create_edit(
  image=open("sunlit_lounge.png", "rb"),
  mask=open("mask.png", "rb"),
  prompt="A sunlit indoor lounge area with a pool containing a flamingo",
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

ヒント: フラミンゴがいるプールのある日当たりの良い屋内シーティングエリア

アップロードする画像とマスクは、4MB 未満の正方形の PNG 画像で、同じサイズである必要があります。マスクの非透明領域は出力の生成時に使用されないため、上記の例のように元の画像と必ずしも一致する必要はありません。

### バリエーション

イメージ [バリエーション](https://platform.openai.com/docs/api-reference/images/create-variation)エンドポイントを使用すると、特定の画像のバリエーションを生成できます。

```
response = openai.Image.create_variation(
  image=open("corgi_and_cat_paw.png", "rb"),
  n=1,
  size="1024x1024"
)
image_url = response['data'][0]['url']
```

編集用エンドポイントと同様に、入力画像はサイズが 4MB 未満の正方形の PNG 画像である必要があります。

### コンテンツのモデレーション

提示と画像は[コンテンツ ポリシー](https://labs.openai.com/policies/content-policy)に従ってフィルタリングされ、ヒントまたは画像にフラグが立てられるとエラーが返されます。誤検知または関連する問題についてフィードバックがある場合は、[ヘルプ センター](https://help.openai.com/)からご連絡ください。

## 言語を指定するプロンプト

### メモリ内画像データ

上記のガイドの Node.js の例では、この`fs`モジュールを使用してディスクから画像データを読み取ります。場合によっては、画像データをメモリに保持することがあります。Node.js オブジェクトに格納された`Buffer`画像データを使用した API 呼び出しの例を次に示します。

```
// This is the Buffer object that contains your image data
const buffer = [your image data];
// Set a `name` that ends with .png so that the API knows it's a PNG image
buffer.name = "image.png";
const response = await openai.createImageVariation(
  buffer,
  1,
  "1024x1024"
);
```

### タイプスクリプトの使用

TypeScript を使用している場合、一部の画像ファイル パラメータに問題がある可能性があります。引数を明示的にキャストして型の不一致を解決する例を次に示します。

```
// Cast the ReadStream to `any` to appease the TypeScript compiler
const response = await openai.createImageVariation(
  fs.createReadStream("image.png") as any,
  1,
  "1024x1024"
);
```

メモリ内の画像データを使用した同様の例を次に示します。

```
// This is the Buffer object that contains your image dataconst buffer: Buffer = [your image data];// Cast the buffer to `any` so that we can set the `name` propertyconst file: any = buffer;// Set a `name` that ends with .png so that the API knows it's a PNG imagefile.name = "image.png";const response = await openai.createImageVariation(  file,  1,  "1024x1024");
```

### エラー処理

API リクエストは、無効な入力、レート制限、またはその他の問題が原因でエラーを返す場合があります。これらのエラーは、単一の`try...catch`ステートメントで処理できます。エラーの詳細は、次の場所にあります：`error.response`または、`error.message`

```
try {
  const response = await openai.createImageVariation(
    fs.createReadStream("image.png"),
    1,
    "1024x1024"
  );
  console.log(response.data.data[0].url);
} catch (error) {
  if (error.response) {
    console.log(error.response.status);
    console.log(error.response.data);
  } else {
    console.log(error.message);
  }
}
```