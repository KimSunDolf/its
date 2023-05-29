# コード補完 `Limited Beta`

コードを生成または操作する方法を学ぶ

## 序章

モデルの Codex ファミリは、自然言語と数十億行のコードでトレーニングされた GPT-3 ファミリの子孫です。Python が得意で、JavaScript、Go、Perl、PHP、Ruby、Swift、TypeScript、SQL、さらには Shell など、10 を超える言語に精通しています。Codex の使用は、最初の限定テスト期間中は無料です。

Codex は、次のようなさまざまなタスクに使用できます。

-   コメントをコードに変換する
-   コンテキスト内で次の行または関数を完成させます
-   アプリケーションの有用なライブラリや API 呼び出しを見つけるなどの知識をもたらす
-   コメントを追加
-   コードを書き直して効率を改善する

## クイックスタート

自分で Codex を使い始めるには、    これらの例を[Playgroundで開いてみてください。](https://platform.openai.com/playground)

**「こんにちは」と言う (Python)**

```
"""
Ask the user for their name and say "Hello"
"""
```

**ランダムな名前を作成する (Python)**

```
"""
1. Create a list of first names
2. Create a list of last names
3. Combine them randomly into a list of 100 full names
"""
```

**MySQL クエリを作成する** **(Python)**

```
"""
Table customers, columns = [CustomerId, FirstName, LastName, Company, Address, City, State, Country, PostalCode, Phone, Fax, Email, SupportRepId]
Create a MySQL query for all customers in Texas named Jane
"""
query =
```

**解釈されたコード (JavaScript)**

```
// Function 1
var fullNames = [];
for (var i = 0; i < 50; i++) {
  fullNames.push(names[Math.floor(Math.random() * names.length)]
    + " " + lastNames[Math.floor(Math.random() * lastNames.length)]);
}

// What does Function 1 do?
```

#### その他の例

访问我们的[示例库](https://platform.openai.com/examples?category=code)，探索更多为  Codex  设计的提示。

## ベストプラクティス

コメント、データ、またはコードから始めます。私たちの playgroundでそのうちの1つのCodexモデルを使ってみてください(必要な時にスタイル説明をコメントにしてください。)

Codexが有用な完成を作るために、プログラマーが任務を遂行するのにどんな情報が必要かを考えるのが役に立ちます。これはただ明確な注釈や有用な関数を書くのに必要なデータかもしれません。例えば、変数名や関数処理のクラスです。

```
# Create a function called 'nameImporter' to add a first and last name to the database
```

この例では、私たちはCodexにその関数を呼び出す内容とそれが実行する任務を教えます。

この方法はCodexに注釈とデータベースパターンの例を提供し、各種データベースに有用な検索要求を書くように拡張できます。

```
# Table albums, columns = [AlbumId, Title, ArtistId]
# Table artists, columns = [ArtistId, Name]
# Table media_types, columns = [MediaTypeId, Name]
# Table playlists, columns = [PlaylistId, Name]
# Table playlist_track, columns = [PlaylistId, TrackId]
# Table tracks, columns = [TrackId, Name, AlbumId, MediaTypeId, GenreId, Composer, Milliseconds, Bytes, UnitPrice]

# Create a query for all albums by Adele
```

Codexにデータベースアーキテクチャを表示する時、どのように検索をフォーマットするかについて根拠のある推測をすることができます。

言語を指定する。Codexは数十種類の異なるプログラミング言語を理解する。多くの人は注釈、関数、その他のプログラミング文法について似たような約束を持っている。コメントで言語とバージョンを指定することで、Codexはあなたが望む内容をよりよく完成できます。つまり、Codexはスタイルと文法の面でかなり柔軟だ。

```
# R language
# Calculate the mean distance between an array of points
```

```
# Python 3
# Calculate the mean distance between an array of points
```

Codexに何をして欲しいのかヒント。Codexがウェブページを作りたいなら、あなたのコメントでCodexに次に何をすべきか教えてから、最初の行のコードをHTML文書(`<!DOCTYPE html>`)中に置きます。同じ方法はコメントから関数を作る(コメントの後にfuncやdefで始まる新しい行)に適用されます。

```
<!-- Create a web page with the title 'Kat Katman attorney at paw' -->
`<!DOCTYPE html>`
```

私たちのコメントの後に `<!DOCTYPE html>`はCodexに私たちが何をしたいのかを明確に知らせることができます。

```
# Create a function to count to 100def counter
```

もし私たちが関数を書き始めたら、Codexは次に何をすべきかを理解するだろう。

指定ライブラリはCodexがあなたのニーズを理解するのに役立ちます。Codexは大量のライブラリ、API、モジュールを知っている。コメントやあなたのコードに導入してCodexに何を使うかを教えることで、Codexは代替案ではなく、それらに基づいて提案します。

```
<!-- Use A-Frame version 1.2.0 to create a 3D website -->
<!-- https://aframe.io/releases/1.2.0/aframe.min.js -->
```

バージョンを指定することで、Codexが最新のライブラリを使用することを保証します。

注意:Codexは有用なライブラリとAPIを推薦できますが、常にあなたのアプリケーションに安全であることを確認するために、自分で研究してください。

注釈スタイルはコードの品質に影響を及ぼす。ある言語に対して、注釈のスタイルは出力の質を向上させることができる。例えば、Pythonを使用する時、場合によっては文書文字列(三引用符による注釈)を使って、井号(#)記号を使うより高品質な結果を提供できる。

```
"""
Create an array of users and email addresses
"""
```

注釈を関数の内部に置くことは役に立つかもしれない。推奨される符号化基準は通常、関数の説明を関数の内部に置くことを推奨する。このフォーマットを使用することは、Codexが関数が実行したい操作をより明確に理解するのに役立ちます。
```
def getUserBalance(id):
    """
    Look up the user in the database ‘UserData' and return their current account balance.
    """
```

より正確な結果を得るために例を提供する。Codexの使用が必要な特定のスタイルやフォーマットがあれば、要請の最初の部分で例やデモを提供してください。これはCodexがあなたのニーズをより正確に満たすのに役に立ちます。

```
"""Create a list of random animals and species"""animals  = [ {"name": "Chomper", "species": "Hamster"}, {"name":
```

低い温度は、より正確な結果を提供する。ほとんどの場合、API temperatureを0またはゼロに近い(例えば0.1または0.2)に設定すると、より良い結果を生み出すことが多い。GPT-3とは異なり、より高い温度は有用な創造性とランダムな結果を提供し、Codexのより高い温度は、本当にランダムまたは不安定な反応を与える可能性があります。

Codexで異なる潜在的な結果を提供する必要があるなら、ゼロから始めて、適切な変化が見つかるまで0.1を上に増やしてください。

任務を成功に組織する。私たちはコメントで関数が何をすべきかをできるだけ正確に指定することで、Codexに関数を書かせることができる。以下のコメントを書くことで、Codexはユーザーがボタンを押す時に発動するJavascriptタイマー関数を作成しました。

簡単なJavaScriptタイマー

```
// Create a timer that creates an alert in 10 seconds
```

私たちはCodexを使ってよく知られたライブラリと一緒に一般的な任務を遂行することができます。例えば、Stripe APIを使って顧客を作ることができます。

PythonでStripe顧客を作成します。

```
# Create a Stripe customer from an email address
```

サンプルデータを作成します。テストアプリケーションは、通常、サンプルデータを使用する必要があります。Codgenは自然言語をどのように理解して書くかを理解する言語モデルなので、Codexにデータ、例えば架空の名前、製品、その他の変数の配列を作成するように要求することができます。

```
/* Create an array of weather temperatures for San Francisco */
```

Codexにこの任務を実行するよう要求すると、以下の表が生成される。

```
var weather = [
  { month: 'January', high: 58, low: 48 },
  { month: 'February', high: 61, low: 50 },
  { month: 'March', high: 64, low: 53 },
  { month: 'April', high: 67, low: 55 },
  { month: 'May', high: 70, low: 58 },
  { month: 'June', high: 73, low: 61 },
  { month: 'July', high: 76, low: 63 },
  { month: 'August', high: 77, low: 64 },
  { month: 'September', high: 76, low: 63 },
  { month: 'October', high: 73, low: 61 },
  { month: 'November', high: 68, low: 57 },
  { month: 'December', high: 64, low: 54 }
];
```

複合関数と小型アプリケーション。私たちはCodexに複雑な要求を含むコメントを提供することができます。例えば、ランダム名前生成器を作成したり、ユーザー入力を使って任務を遂行したりします。十分なトークンがあれば、Codexは残りの内容を生成することができます。

```
/*
Create a list of animals
Create a list of cities
Use the lists to generate stories about what I saw at the zoo in each city
*/
```

より正確な結果やより低い遅延を得るために完成サイズを制限する。Codexでより長い完了時間を要求すると、不正確な答えと重複を引き起こす可能性がある。Max\_tokensを減らし、停止マークを設定することで、クエリのサイズを制限します。例えば、停止シーケンスとして\\nを追加して完成を一行のコードに制限する。より小さな完成はまた、より少ない遅延をもたらす。

ストリーミングを使用して遅延を減らす。大型Codex検索は数十秒かかるかもしれません。自動完了のコーディングアシスタントなど、低い遅延が必要なアプリケーションを構築するには、フロー処理の使用を検討してください。モデルが完成して全体が完成する前に応答が返される。一部完成が必要なアプリケーションは、プログラミング方式や創造的な停止値で完成を断ち切ることで遅延を減らすことができる。

ユーザーはAPIから複数の解決策を要請し、返された最初の応答を使用することで、ストリーミング処理と複製を結合して遅延を減らすことができます。N > 1 を設定することでこれをする。この方法はより多くのトークン割当量を消費するので、慎重に使用してください(例えば、max\_tokensとstopの使用を通じて合理的な設定を通じて)。

Codexを使ってコードを解釈する。Codexがコードを作成して理解する能力は、ファイル内のコードの役割を解釈するなど、それを使って任務を遂行できるようにしてくれる。この目的を達成する一つの方法は「This function」または「This application is」で始まる関数の後にコメントを追加することです。食典委員会は通常、これを解釈の始まりとテキストの残りの部分を完成することと解釈する。

```
/* Explain what the previous function is doing: It
```

SQLクエリを解釈する。この例では、私たちはCodexを使って人間が読めるフォーマットでSQLクエリの役割を説明します。

```
SELECT DISTINCT department.name
FROM department
JOIN employee ON department.id = employee.department_id
JOIN salary_payments ON employee.id = salary_payments.employee_id
WHERE salary_payments.date BETWEEN '2020-06-01' AND '2020-06-30'
GROUP BY department.name
HAVING COUNT(employee.id) > 10;
-- Explanation of the above query in human readable format
--
```

ユニットテストを作成する。「ユニットテスト」というコメントを追加して関数を起動するだけで、Pythonでユニットテストを作成できます。

```
# Python 3def sum_numbers(a, b):  return a + b# Unit testdef
```

コードにエラーがないか確認する。例を使用することで、Codexにコードのエラーを認識する方法を示すことができます。場合によっては例は必要ありませんが、説明のレベルと詳細を提供する展示は、法典が探している内容を理解し、どのように解釈するかに役に立ちます。(Codexのエラーに対する検査はユーザーの慎重な審査に取って代わるべきではない。)

```
/* Explain why the previous function doesn't work. */
```

ソースデータを使ってデータベース関数を作成する。人間のプログラマーがデータベース構造と列名を知ることから利益を得るように、Codexはこのデータを使って正確な問い合わせ要請を書くのに役立つ。この例では、データベースのパターンを挿入し、Codexにデータベースの内容を調べるように伝えます。

```
# Table albums, columns = [AlbumId, Title, ArtistId]
# Table artists, columns = [ArtistId, Name]
# Table media_types, columns = [MediaTypeId, Name]
# Table playlists, columns = [PlaylistId, Name]
# Table playlist_track, columns = [PlaylistId, TrackId]
# Table tracks, columns = [TrackId, Name, AlbumId, MediaTypeId, GenreId, Composer, Milliseconds, Bytes, UnitPrice]

# Create a query for all albums by Adele
```

言語間の転換。Codexを一つの言語から別の言語に転換させることができます。方法は簡単なフォーマットに従うことです。コメントで変換するコードの言語を列挙し、次にコード、そして翻訳したい言語を含むコメントです。

```
# Convert this from Python to R# Python version[ Python code ]# End# R version
```

ライブラリやフレームワークのためにコードを書き換える。Codexが特定の機能の効率を向上させたいなら、書き換えるコードを提供し、どんなフォーマットを使うか説明を提供します。

```
// Rewrite this as a React component
var input = document.createElement('input');
input.setAttribute('type', 'text');
document.body.appendChild(input);
var button = document.createElement('button');
button.innerHTML = 'Say Hello';
document.body.appendChild(button);
button.onclick = function() {
  var name = input.value;
  var hello = document.createElement('div');
  hello.innerHTML = 'Hello ' + name;
  document.body.appendChild(hello);
};

// React version:
```

コードを挿入します。

完成エンドポイントは、プレフィックスヒント以外のサフィックスヒントを提供することで、コードにコードを挿入することもサポートします。これは関数やファイルの間に補完を挿入するのに使用できる。

```
def get_largest_prime_factor(n):
    if n < 2:
        return False
    def is_prime(n): >  for i in range(2, n): >  if n % i == 0: >  return False >  return True >     largest = 1
    for j in range(2, n + 1):
        if n % j == 0 and is_prime(j):
    return largest
```

モデルに追加的な文脈を提供することで、それはよりコントロールできる。しかし、これはモデルにとってより拘束的で挑戦的な課題だ。

### ベストプラクティス

挿入コードはベータ版の新しい機能であり、より良い結果を得るためにAPIを使用する方法を修正する必要があるかもしれません。以下はいくつかのベストプラクティスです：

Max\_tokens > 256を使用する。このモデルは、より長い補完を挿入するのがもっと上手だ。Max\_tokensが小さすぎると、モデルはサフィックスに接続する前に切断される可能性がある。注意してください。より大きなmax\_tokensを使っても、生成されたトークンの数によってのみ支払います。

Finish\_reason ==「stop」がもっと好きです。モデルが自然停止点やユーザーが提供した停止シーケンスに達すると、 finish\_reasonを「停止」に設定します。これは、このモデルがサフィックスにうまく接続され、品質を完成するための良い信号であることを示している。これはn > 1や再サンプリングを使用する時、いくつかの完成の間で選択することに特に関係があります(次の点を参照してください)。

再サンプリング 3-5回。ほとんどすべての補完がプレフィックスに接続されているが、より難しい場合、モデルはサフィックスを接続するのが難しいかもしれない。この場合、3回または5回(またはk=3,5のbest\_of)を再サンプリングし、「stop」をその finish\_reasonのサンプルとして選択することが有効な方法であることを発見しました。再サンプリングの時、あなたは通常多様性を増加させるためにより高い温度を必要とする。

注意:もし返されたすべてのサンプルにfinish\_reason == "length"があれば、max\_tokensが小さすぎる可能性が高いです。モデルはヒントとサフィックスを自然に接続する前にマークを使い果たしました。再サンプリングの前にmax\_tokensを追加することを考えてください。

## コード編集


Editsエンドポイントは、単にそれを完成させるのではなく、コードを編集することができる。あなたはいくつかのコードとそれを修正する方法の説明を提供し、code-davinci-edit-001モデルはそれに合わせて編集しようとします。これはコードを再構築して調整する自然なインターフェースだ。この初期テスト期間中、編集エンドポイントの使用は無料です。

### 例

反復構築プログラム

コードを書くことは通常反復的な過程であり、テキストを完璧にする必要がある。編集は、最終結果が完成するまで、自然にモデルの出力を絶えず改善します。この例では、私たちはフィボナッチを反復的にコードを構築する方法の例として使います。

関数を書く。

!

それを再構築する。

!

関数の名前を変更します。

!

文書を追加します。

!


ベストプラクティス

Editsエンドポイントはまだアルファ段階にあり、私たちはこれらのベストプラクティスに従うことをお勧めします。

1.空ヒントの使用を検討してください!この場合、編集は使用完了と類似することができる。

2.説明はできるだけ具体的にする。

3.時々、モデルは解決策を見つけることができず、エラーを引き起こす。私たちはあなたの説明を書き換えたり、入力することを提案します。