# Part 1 変数・list・dict
# Pythonプログラミング入門：変数・リスト・辞書

Google ColabでPythonを動かしながら、アンケート調査のデータを扱う基本を学びます。

## 今回の目標

- 変数に値を保存し、計算に使える。
- リスト（`list`）で複数の回答をまとめ、取り出し・変更・追加ができる。
- 辞書（`dict`）で項目名と値を対応させ、必要な情報を取り出せる。

### 進め方

GitHubでこのページを見ながら、コードを[Google Colab](https://colab.research.google.com/notebook?hl=ja)のコードセルにコピーして実行します。

演習では、指示に従って**自分でコードを入力**してください。

---

## まずは動かしてみる

以下のコードをGoogle Colabにコピーして実行してください。

```python
print("アンケート調査")
print(100)
print(100 + 50)
```

`print()`は、文字や数値、計算結果を画面に表示する関数です。

文字は`" "`で囲みます。

Pythonのコードは基本的に上から順番に実行されます。

また、`#`から行末まではコメントとして扱われ、実行されません。

```python
# これはコメントです
print("こんにちは")
```

コメントは、コードの説明や処理内容のメモなどに利用できます。

### やってみよう 0

`print()`を使って、次の内容をそれぞれ表示してください。

```text
商品Aのアンケート
30
```

<details>
<summary>解答例・実行結果（自分で入力してから確認しよう）</summary>

解答例

```python
print("商品Aのアンケート")
print(30)
```

</details>

---

## 1. 変数：データに名前を付ける

### 1.1 変数とは

変数とは、**データに名前を付けて、後から利用できるようにする仕組み**です。

`変数名 = 値`という形で、変数に値を代入します。

例えば、商品Aのアンケートで、回答者が30人、そのうち購入したい人が18人だったとします。

```python
respondents = 30
want_to_buy = 18

print(respondents)
print(want_to_buy)
```

実行結果：

```text
30
18
```

`=`は「右側の値を左側の変数に代入する」という意味です。

変数名は、保存している内容が分かるものにすると、コードを読みやすくできます。

例えば、`a = 30`よりも`respondents = 30`の方が、何を表す値なのか分かりやすくなります。

### 1.2 変数を使って計算する

変数に保存した値を使って計算することもできます。

```python
respondents = 30
want_to_buy = 18

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

実行結果：

```text
60.0
```

ここでは、購入希望者の割合（%）を計算しています。

- `/`：割り算
- `*`：掛け算
- `+`：足し算
- `-`：引き算

計算結果を別の変数に代入することで、後からその値を利用できます。

### 1.3 変数の値を変更する

変数には、新しい値を代入することもできます。

```python
want_to_buy = 18

print(want_to_buy)

want_to_buy = 21

print(want_to_buy)
```

実行結果：

```text
18
21
```

同じ変数に別の値を代入すると、その後は新しい値が使用されます。

### 1.4 値には種類がある

Pythonでは、文字や数値など、さまざまな種類のデータを扱えます。

```python
product_name = "商品A"
respondents = 30
purchase_rate = 60.0

print(product_name)
print(respondents)
print(purchase_rate)
```

代表的なデータ型には、次のようなものがあります。

| データ型 | 説明 | 例 |
|---|---|---|
| `str` | 文字列 | `"商品A"` |
| `int` | 整数 | `30` |
| `float` | 小数を扱う数値 | `60.0` |
| `bool` | 真偽値 | `True`, `False` |

`type()`を使用すると、データ型を確認できます。

```python
respondents = 30

print(type(respondents))
```

実行結果：

```text
<class 'int'>
```

例えば、`"30"`は文字列、`30`は整数です。

```python
respondents = "30"

print(type(respondents))
```

実行結果：

```text
<class 'str'>
```

データ型が異なると、実行できる処理も異なるため、コードを理解するうえで重要です。

### 1.5 演習1：自分で書いてみよう

Google Colabで、演習1-1から順番にコードを入力・実行してください。

**演習1-1：変数への代入**

商品Aのアンケートについて、次の値を変数に代入し、それぞれ表示（print）してください。

- 回答者数：40人
- 購入希望者数：28人

変数名は以下を使用してください。

- `respondents`
- `want_to_buy`

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
respondents = 40
want_to_buy = 28

print(respondents)
print(want_to_buy)
```

**実行結果**

```text
40
28
```

</details>

---

#### 演習1-2：変数を使った計算

演習1-1で作成した2つの変数を使用して、購入希望者の割合（%）を計算してください。

計算結果は`purchase_rate`という変数に保存し、表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

**実行結果**

```text
70.0
```

</details>

---

#### 演習1-3：変数への再代入

購入希望者数が28人から32人に増えたとします。

`want_to_buy`の値を変更し、購入希望者の割合をもう一度計算してください。

計算結果を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
want_to_buy = 32

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

**実行結果**

```text
80.0
```

</details>

---

### 1.6 Paizaの復習課題

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

今回の内容に対応するChapterは以下の通りです。

- Chapter 1：変数とはなにか
- Chapter 2：変数への代入
- Chapter 3：変数への再代入

**全3チャプターを復習課題とします。**

---

## 2. リスト（list）：複数の回答をまとめる

### 2.1 リストとは

変数を使用すると、データに名前を付けて保存できます。

しかし、アンケート調査では複数人の回答を扱うことがあります。

例えば、5人が商品Aの満足度を1〜5点で回答したとします。

変数だけを使用すると、次のようになります。

```python
score_1 = 4
score_2 = 5
score_3 = 3
score_4 = 4
score_5 = 2
```

このように、回答者が増えるたびに変数を作成するのは大変です。

そこで使用するのが**リスト（list）**です。

リストは、複数の値を順番にまとめて保存できる仕組みです。

```python
scores = [4, 5, 3, 4, 2]

print(scores)
```

実行結果：

```text
[4, 5, 3, 4, 2]
```

リストは`[]`で囲み、値を`,`で区切って作成します。

### 2.2 リストから値を取り出す

リストでは、データの位置を指定して値を取り出すことができます。

```python
scores = [4, 5, 3, 4, 2]

print(scores[0])
print(scores[2])
```

実行結果：

```text
4
3
```

ここで重要なのは、**Pythonのリストでは、最初の要素の位置が0になること**です。

| インデックス | 回答者 | 満足度 |
|---|---|---|
| 0 | 1人目 | 4 |
| 1 | 2人目 | 5 |
| 2 | 3人目 | 3 |
| 3 | 4人目 | 4 |
| 4 | 5人目 | 2 |

この位置を表す番号を**インデックス**といいます。

### 2.3 リストの値を変更する

リストの要素は、インデックスを指定して変更できます。

例えば、2人目の回答を5から4に変更する場合です。

```python
scores = [4, 5, 3, 4, 2]

scores[1] = 4

print(scores)
```

実行結果：

```text
[4, 4, 3, 4, 2]
```

### 2.4 リストに値を追加する

新しい回答者のデータを追加したい場合は、`append()`を使用します。

```python
scores = [4, 5, 3, 4, 2]

scores.append(5)

print(scores)
```

実行結果：

```text
[4, 5, 3, 4, 2, 5]
```

`append()`は、リストの末尾に新しい要素を追加するためのメソッドです。

### 2.5 リストの要素数を確認する

`len()`を使用すると、リストに保存されている要素の数を確認できます。

```python
scores = [4, 5, 3, 4, 2]

print(len(scores))
```

実行結果：

```text
5
```

今回の例では、回答者が5人いることが分かります。

### 2.6 リストを使って平均を求める

リストに保存された数値の合計は、`sum()`を使って計算できます。

```python
scores = [4, 5, 3, 4, 2]

print(sum(scores))
```

実行結果：

```text
18
```

`sum()`と`len()`を組み合わせることで、平均値を計算できます。

```python
scores = [4, 5, 3, 4, 2]

average_score = sum(scores) / len(scores)

print(average_score)
```

実行結果：

```text
3.6
```

このように、リストを使用すると、複数の回答をまとめて管理し、計算に利用できます。

### 2.7 演習2：自分で書いてみよう

Google Colabで、演習2-1から順番にコードを入力・実行してください。

**演習2-1：リストの作成**

5人の満足度が次のように得られました。

```text
3, 4, 5, 2, 4
```

これらを`scores`というリストに保存し、表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
scores = [3, 4, 5, 2, 4]

print(scores)
```

**実行結果**

```text
[3, 4, 5, 2, 4]
```

</details>

---

#### 演習2-2：要素の取り出し

演習2-1で作成したリストから、3人目の満足度を取り出して表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
print(scores[2])
```

**実行結果**

```text
5
```

</details>

---

#### 演習2-3：要素の変更

演習2-1で作成したリストについて、2人目の満足度を4から5に変更してください。

変更後のリスト全体を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
scores[1] = 5

print(scores)
```

**実行結果**

```text
[3, 5, 5, 2, 4]
```

</details>

---

#### 演習2-4：要素の追加

演習2-3で変更したリストに、新しい回答者の満足度`3`を追加してください。

追加後のリスト全体を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
scores.append(3)

print(scores)
```

**実行結果**

```text
[3, 5, 5, 2, 4, 3]
```

</details>

---

#### 演習2-5：平均値の計算

演習2-4で新しい回答を追加したリストを使用して、次の値を表示（print）してください。

1. 回答者数
2. 平均満足度

平均満足度は`average_score`という変数に保存してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
print(len(scores))

average_score = sum(scores) / len(scores)

print(average_score)
```

**実行結果**

```text
6
3.6666666666666665
```

※Pythonでは、小数の計算結果が長く表示される場合があります。

</details>

---

### 2.8 Paizaの復習課題

[新・Python入門編7：リストを学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-7)

**今回の復習課題**

- Chapter 1：リストとはなにか
- Chapter 2：リストを生成
- Chapter 3：リストの要素の型
- Chapter 4：リストのインデックスiの要素を取得
- Chapter 6：リストのインデックスiの要素を変更
- Chapter 7：リストの長さを取得（len）
- Chapter 10：リストの末尾に要素を追加（append）

**余裕があれば取り組むChapter**

- Chapter 5：リストのインデックスがi～j-1の要素を取得

リストの結合・反復・削除・並べ替えについては、今回の基礎演習では扱いません。

---

## 3. 辞書（dict）：項目名と値を対応させる

### 3.1 辞書とは

これまで、変数やリストを使用してアンケートのデータを保存しました。

しかし、アンケート調査では、回答者の番号・年齢・満足度など、異なる種類の情報をまとめたい場合があります。

そのような場合に使用できるのが**辞書（dict）**です。

辞書は、**キー（key）と値（value）を組み合わせてデータを保存する仕組み**です。

例えば、1人の回答者の情報を保存してみます。

```python
respondent = {
    "id": 1,
    "age": 21,
    "satisfaction": 4
}

print(respondent)
```

実行結果

```text
{'id': 1, 'age': 21, 'satisfaction': 4}
```

この例では、次のようにデータが保存されています。

| キー（key） | 値（value） |
|---|---|
| `"id"` | 1 |
| `"age"` | 21 |
| `"satisfaction"` | 4 |

辞書は`{}`を使用し、`キー: 値`の形式で作成します。

### 3.2 辞書から値を取り出す

辞書では、キーを指定して値を取り出すことができます。

```python
respondent = {
    "id": 1,
    "age": 21,
    "satisfaction": 4
}

print(respondent["age"])
print(respondent["satisfaction"])
```

実行結果：

```text
21
4
```

リストと辞書では、値の取り出し方が異なります。

**リストの場合**

```python
scores = [4, 5, 3]

print(scores[0])
```

実行結果

```text
4
```

リストでは、インデックス（位置）を指定して値を取り出します。

**辞書の場合**

```python
respondent = {"age": 21, "satisfaction": 4}

print(respondent["age"])
```

実行結果

```text
21
```

辞書では、キーを指定して値を取り出します。

### 3.3 辞書の値を変更する

辞書の値は、キーを指定して変更できます。

例えば、満足度を4から5に変更する場合です。

```python
respondent = {
    "id": 1,
    "age": 21,
    "satisfaction": 4
}

respondent["satisfaction"] = 5

print(respondent)
```

実行結果

```text
{'id': 1, 'age': 21, 'satisfaction': 5}
```

### 3.4 辞書に新しい項目を追加する

辞書には、新しいキーと値を追加することもできます。

例えば、購入意向に関する情報を追加します。

```python
respondent = {
    "id": 1,
    "age": 21,
    "satisfaction": 4
}

respondent["purchase_intention"] = "あり"

print(respondent)
```

実行結果：

```text
{'id': 1, 'age': 21, 'satisfaction': 4, 'purchase_intention': 'あり'}
```

このように、辞書を使用すると、回答者に関する複数の情報を項目名と対応させて管理できます。

### 3.5 演習3：自分で書いてみよう

Google Colabで、演習3-1から順番にコードを入力・実行してください。

**演習3-1：辞書の作成**

次の回答者情報を`respondent`という辞書に保存してください。

| 項目 | 値 |
|---|---|
| id | 2 |
| age | 22 |
| satisfaction | 5 |

キーは文字列として作成してください。
作成した辞書全体を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
respondent = {
    "id": 2,
    "age": 22,
    "satisfaction": 5
}

print(respondent)
```

**実行結果**

```text
{'id': 2, 'age': 22, 'satisfaction': 5}
```

</details>

---

#### 演習3-2：値の取り出し

演習3-1で作成した辞書から、`satisfaction`の値を取り出して表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
print(respondent["satisfaction"])
```

**実行結果**

```text
5
```

</details>

---

#### 演習3-3：値の変更

演習3-1で作成した辞書について、`satisfaction`の値を5から4に変更してください。

変更後の辞書全体を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
respondent["satisfaction"] = 4

print(respondent)
```

**実行結果**

```text
{'id': 2, 'age': 22, 'satisfaction': 4}
```

</details>

---

#### 演習3-4：項目の追加

演習3-3で変更した辞書に、次の項目を追加してください。

- キー：`purchase_intention`
- 値：`"あり"`

追加後の辞書全体を表示（print）してください。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
respondent["purchase_intention"] = "あり"

print(respondent)
```

**実行結果**

```text
{'id': 2, 'age': 22, 'satisfaction': 4, 'purchase_intention': 'あり'}
```

</details>

### 3.6 Paizaの復習課題

[新・Python入門編16：辞書を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-16)

**今回の復習課題**

- Chapter 1：辞書とはなにか
- Chapter 2：辞書を生成
- Chapter 3：keyを指定してvalueを取得
- Chapter 6：辞書での要素の追加・更新

**余裕があれば取り組むChapter**

- Chapter 4：辞書のkeyにできる型
- Chapter 5：辞書の長さを取得（len）

---

## 4. 総合演習：アンケートデータを整理する

ここまでに学んだ、変数・リスト・辞書を組み合わせてみましょう。

商品Aのアンケート調査で、5人の満足度を調べたとします。

商品名と満足度のリストを、1つの辞書にまとめます。

```python
survey = {
    "product": "商品A",
    "scores": [4, 5, 3, 4, 5]
}
```

このデータを使用して、以下の演習に取り組んでください。

### 演習4-1：商品名を取り出す

辞書`survey`から商品名を取り出して表示（print）してください。

### 演習4-2：満足度を取り出す

辞書の中にあるリストから、3人目の満足度を取り出して表示（print）してください。

### 演習4-3：平均満足度を計算する

`survey["scores"]`を変数`scores`に代入してください。

その後、平均満足度を計算し、`average_score`という変数に保存してください。

### 演習4-4：計算結果を表示する

`average_score`を表示（print）してください。

### 演習4-5：発展問題

新しい回答者の満足度`2`を追加してください。

その後、平均満足度をもう一度計算して表示（print）してください。

<details>
<summary>解答例（自分で入力してから確認しよう）</summary>

```python
survey = {
    "product": "商品A",
    "scores": [4, 5, 3, 4, 5]
}

# 演習4-1
print(survey["product"])

# 演習4-2
print(survey["scores"][2])

# 演習4-3
scores = survey["scores"]

average_score = sum(scores) / len(scores)

# 演習4-4
print(average_score)

# 演習4-5
survey["scores"].append(2)

scores = survey["scores"]

average_score = sum(scores) / len(scores)

print(average_score)

# 変更後の辞書全体を確認
print(survey)
```

実行結果

```text
商品A
3
4.2
3.8333333333333335
{'product': '商品A', 'scores': [4, 5, 3, 4, 5, 2]}
```

</details>

---

## 5. まとめ

今回のゼミでは、Pythonでデータを扱うための基本的な仕組みを学びました。

| 仕組み | 役割 | 例 |
|---|---|---|
| 変数 | データに名前を付ける | `respondents = 30` |
| リスト | 複数の値を順番にまとめる | `scores = [4, 5, 3]` |
| 辞書 | キーと値を対応させる | `survey = {"product": "商品A"}` |

変数には数値だけでなく、リストや辞書も代入できます。

例えば、次のように組み合わせることができます。

```python
survey = {
    "product": "商品A",
    "scores": [4, 5, 3, 4, 5]
}
```

このように、複数の仕組みを組み合わせることで、さまざまなデータを整理して扱えるようになります。

研究でPythonコードを読むときも、

- この変数には何が保存されているのか
- リストの何番目の要素を取り出しているのか
- 辞書のどのキーを指定しているのか

を確認すると、処理の内容を理解しやすくなります。

---

## ゼミ後の課題

Paizaラーニング「新・Python入門編」の以下の講座に取り組んでください。

### 1. 変数

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

対象：Chapter 1〜3

### 2. リスト

[新・Python入門編7：リストを学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-7)

対象：Chapter 1〜4、6、7、10

### 3. 辞書

[新・Python入門編16：辞書を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-16)

対象：Chapter 1〜3、6

今回扱わなかった内容についても、必要に応じて学習してみてください。