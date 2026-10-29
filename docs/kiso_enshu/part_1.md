# Part 1 変数・list・dict

# Pythonプログラミング入門：変数・リスト・辞書

Google ColabでPythonを動かしながら、商品のアンケート調査データを集計・分析する流れで、データを扱う基本を学びます。

## 今回の目標

- **変数**を使い、数値や文字に名前を付けて再利用できる。
- **リスト**を使い、多数の回答を1つにまとめて集計できる。
- **辞書**を使い、項目名と値を対応させ、データの意味を分かりやすく管理できる。
- **辞書とリストを組み合わせ**、複数商品のアンケート結果を表形式で取り出し、集計できる。

### 進め方

GitHubでこのページを見ながら、コードを[Google Colab](https://colab.research.google.com/notebook?hl=ja)のコードセルにコピーして実行します。

演習では、指示に従って**自分でコードを入力**してください。

---

## まずは動かしてみる

以下のコードをGoogle Colabにコピーして実行してください。

```python
print("商品Aのアンケート集計")
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

## 1. 変数：数値に名前を付けて計算を楽にする

### なぜ必要か

商品Aのアンケートで、回答者が30人、そのうち購入希望者が18人だったとします。

購入希望率（%）を次のように計算できます。

```python
print(18 / 30 * 100)
```

実行結果：

```text
60.0
```

集計の途中で、回答者が40人（購入希望者28人）に増えたと連絡が来たとします。

数値をコードに直接書いている場合、該当する数値を探して書き直す必要があり、修正漏れや入力ミスの原因になります。

そこで、**データに名前を付けて保存する変数**を使用します。

### 使い方

`変数名 = 値`の形で値を代入します。

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

`=`は「右側の値を左側の変数に代入する」という意味です。

変数名は、保存している内容が分かるものにすると、コードを読みやすくなります。

ここでは、購入希望者の割合（%）を計算しています。

- `/`：割り算
- `*`：掛け算
- `+`：足し算
- `-`：引き算

データが更新された場合も、**最初に代入した値だけを書き換えれば、計算式はそのまま再利用**できます。

```python
respondents = 40
want_to_buy = 28

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

実行結果：

```text
70.0
```

同じ変数に別の値を代入すると、その後は新しい値が使用されます。

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

### 演習1：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 商品Aのアンケートについて、回答者数40人・購入希望者28人を、それぞれ`respondents`・`want_to_buy`に代入し、表示（print）する。
2. 上記2つの変数を使い、購入希望率（%）を計算し、結果を`purchase_rate`に保存して表示する。
3. 購入希望者数が32人に増えたとする。`want_to_buy`を更新し、購入希望率を再計算して表示する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
respondents = 40
want_to_buy = 28

print(respondents)
print(want_to_buy)

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)

want_to_buy = 32

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

**実行結果**

```text
40
28
70.0
80.0
```

</details>

### Paizaの復習課題

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

今回の内容に対応するChapterは以下の通りです。

- Chapter 1：変数とはなにか
- Chapter 2：変数への代入
- Chapter 3：変数への再代入

**全3チャプターを復習課題とします。**

---

## 2. リスト（list）：大量のデータをまとめて集計する

### なぜ必要か

5人が商品Aの満足度（5点満点）を回答したとします。

変数だけを使用すると、次のようになります。

```python
score_1 = 4
score_2 = 5
score_3 = 3
score_4 = 4
score_5 = 2
```

回答者が100人、1,000人と増えるたびに、同様の変数を増やしていくのは現実的ではありません。平均などの集計も、都度コードを書き足す必要があります。

そこで、複数の値を順番にまとめて保存できる**リスト（list）**を使用します。

### 使い方

リストは`[]`で囲み、値を`,`で区切って作成します。

```python
scores = [4, 5, 3, 4, 2]

print(scores)
```

実行結果：

```text
[4, 5, 3, 4, 2]
```

リストでは、位置（**インデックス**）を指定して値を取り出したり、変更したりできます。

**Pythonのリストでは、最初の要素の位置が0になります。**

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

| インデックス | 回答者 | 満足度 |
|---|---|---|
| 0 | 1人目 | 4 |
| 1 | 2人目 | 5 |
| 2 | 3人目 | 3 |
| 3 | 4人目 | 4 |
| 4 | 5人目 | 2 |

例えば、2人目の回答を5から4に変更する場合は次のとおりです。

```python
scores[1] = 4

print(scores)
```

実行結果：

```text
[4, 4, 3, 4, 2]
```

新しい回答を追加する場合は、`append()`を使用します。

```python
scores.append(5)

print(scores)
```

実行結果：

```text
[4, 4, 3, 4, 2, 5]
```

`len()`で要素数、`sum()`で合計を求められます。これらを組み合わせると、平均値を計算できます。

```python
scores = [4, 5, 3, 4, 2]

print(len(scores))
print(sum(scores))

average_score = sum(scores) / len(scores)

print(average_score)
```

実行結果：

```text
5
18
3.6
```

### 演習2：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 満足度 `3, 4, 5, 2, 4` を`scores`というリストに保存し、表示する。
2. 3人目の満足度を取り出して表示する。
3. 2人目の満足度を4から5に変更し、リスト全体を表示する。
4. 新しい回答者の満足度`3`を追加し、リスト全体を表示する。
5. 回答者数と平均満足度を表示する（平均は`average_score`に保存する）。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
scores = [3, 4, 5, 2, 4]

print(scores)

print(scores[2])

scores[1] = 5

print(scores)

scores.append(3)

print(scores)

print(len(scores))

average_score = sum(scores) / len(scores)

print(average_score)
```

**実行結果**

```text
[3, 4, 5, 2, 4]
5
[3, 5, 5, 2, 4]
[3, 5, 5, 2, 4, 3]
6
3.6666666666666665
```

※Pythonでは、小数の計算結果が長く表示される場合があります。

</details>

### Paizaの復習課題

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

## 3. 辞書（dict）：データに項目名（意味）を付ける

### なぜ必要か

回答者1人分の「年齢」と「満足度」を、リストだけで表すと次のようになります。

```python
user_a = [21, 4]
```

この並びだけでは、`21`が年齢なのか満足度なのか、後から見返したときに分かりにくくなります。位置（0番目・1番目）に依存して値を取り出す方法も、項目が増えるほど誤りやすくなります。

アンケートでは、回答者の番号・年齢・満足度など、**異なる種類の情報を項目名と対応させて**管理したい場面が多くあります。

そこで、**キー（key）と値（value）を組み合わせて保存する辞書（dict）**を使用します。

### 使い方

辞書は`{}`を使用し、`キー: 値`の形式で作成します。

```python
respondent = {
    "id": 1,
    "age": 21,
    "satisfaction": 4
}

print(respondent)
```

実行結果：

```text
{'id': 1, 'age': 21, 'satisfaction': 4}
```

| キー（key） | 値（value） |
|---|---|
| `"id"` | 1 |
| `"age"` | 21 |
| `"satisfaction"` | 4 |

キーを指定して値を取り出します。

```python
print(respondent["age"])
print(respondent["satisfaction"])
```

実行結果：

```text
21
4
```

リストではインデックス（位置）を、辞書ではキーを指定する点が異なります。

値の変更や、新しい項目の追加も、キーを指定して行います。

```python
respondent["satisfaction"] = 5

respondent["purchase_intention"] = "あり"

print(respondent)
```

実行結果：

```text
{'id': 1, 'age': 21, 'satisfaction': 5, 'purchase_intention': 'あり'}
```

### 演習3：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 次の回答者情報を`respondent`という辞書に保存し、辞書全体を表示する。

   | 項目 | 値 |
   |---|---|
   | id | 2 |
   | age | 22 |
   | satisfaction | 5 |

   キーは文字列として作成してください。

2. `satisfaction`の値を取り出して表示する。
3. `satisfaction`の値を5から4に変更し、辞書全体を表示する。
4. キー`purchase_intention`、値`"あり"`を追加し、辞書全体を表示する。

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

print(respondent["satisfaction"])

respondent["satisfaction"] = 4

print(respondent)

respondent["purchase_intention"] = "あり"

print(respondent)
```

**実行結果**

```text
{'id': 2, 'age': 22, 'satisfaction': 5}
5
{'id': 2, 'age': 22, 'satisfaction': 4}
{'id': 2, 'age': 22, 'satisfaction': 4, 'purchase_intention': 'あり'}
```

</details>

### Paizaの復習課題

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

## 4. 総合演習：表形式のデータを扱う

変数・リスト・辞書は、組み合わせて使用することで効果を発揮します。

1商品分の結果を辞書にまとめる場合、次のように「項目名（キー）」と「値」を対応させて表現できます。

```python
survey_one = {
    "product": "商品A",
    "scores": [4, 5, 3, 4, 5],
}
```

商品A・B・Cの3商品を同時に扱う場合、**1つの辞書の中に、列データとしてリストを並べる**形が扱いやすくなります。これを**列指向の辞書（dict of list）**と呼びます。スプレッドシートでは、列見出しに対応するデータが縦一列に並ぶ関係と同型です。

### データの構造

商品A・B・Cについて、それぞれ5人分の満足度（5点満点）を集計したとします。

```python
survey = {
    "products": ["商品A", "商品B", "商品C"],
    "scores": [
        [4, 5, 3, 4, 5],
        [3, 4, 4, 5, 4],
        [5, 3, 4, 3, 4],
    ],
}
```

| キー | 内容 |
|---|---|
| `"products"` | 商品名のリスト（行ごとの商品に対応） |
| `"scores"` | 各商品の満足度リストを並べたリスト |

同じインデックスの要素が、1商品分のデータに対応します。

| インデックス | `products` | `scores` |
|---|---|---|
| `0` | 商品A | `[4, 5, 3, 4, 5]` |
| `1` | 商品B | `[3, 4, 4, 5, 4]` |
| `2` | 商品C | `[5, 3, 4, 3, 4]` |

`"scores"`の各要素はリストであるため、**リストの中にリストが入った構造**になります。取り出すときは、辞書のキー指定のあと、リストのインデックス指定を続けます。

```text
survey
  └── ["scores"]     … キーで列全体（リストのリスト）を取り出す
         └── [1]     … インデックスで商品Bの行（リスト）を取り出す
              └── [2] … インデックスで3人目の回答（数値）を取り出す
```

### 値の取り出しと変数への代入

処理の段階を変数に分けて記述すると、内容を追いやすくなります。

```python
all_scores = survey["scores"]
scores_b = all_scores[1]
score_b_3rd = scores_b[2]

print(score_b_3rd)
```

実行結果：

```text
4
```

1行で記述する場合は、左から順に「辞書 → 外側のリスト → 内側のリスト」と読みます。

```python
print(survey["scores"][1][2])
```

商品名も、キーとインデックスを指定して取り出します。

```python
product_b = survey["products"][1]

print(product_b)
```

実行結果：

```text
商品B
```

合計・平均は、取り出したリストを変数に代入してから、§2で学んだ`sum()`と`len()`を用いて計算します。

```python
scores_a = survey["scores"][0]
total_a = sum(scores_a)
average_a = total_a / len(scores_a)

scores_b = survey["scores"][1]
average_b = sum(scores_b) / len(scores_b)

print(total_a)
print(average_a)
print(average_b)
```

実行結果：

```text
21
4.2
4.0
```

3商品すべての回答を合算する場合も、各商品のリストを変数に取り出したうえで、合計値を足し合わせます。

```python
scores_a = survey["scores"][0]
scores_b = survey["scores"][1]
scores_c = survey["scores"][2]

grand_total = sum(scores_a) + sum(scores_b) + sum(scores_c)
response_count = len(scores_a) + len(scores_b) + len(scores_c)
grand_average = grand_total / response_count

print(grand_total)
print(grand_average)
```

実行結果：

```text
59
3.933333333333333
```

### 演習4：自分で書いてみよう

Google Colabで、上記の`survey`を定義したうえで、次の手順に従ってコードを入力・実行してください。

いずれも、**辞書のキーとリストのインデックスで値を取り出し、指示された変数に代入**してから、集計結果を表示（print）します。

#### 取り出し（インデックス指定と変数への代入）

1. `products`の1番目（インデックス`0`）の商品名を`product_a`に代入して表示する。
2. `products`の2番目（インデックス`1`）の商品名を`product_b`に代入して表示する。
3. `survey["scores"]`を`all_scores`に代入する。続けて`all_scores[1]`を`scores_b`に代入し、`scores_b[2]`を`score_b_3rd`に代入して表示する。
4. 商品Aの1人目の満足度を`score_a_1st`に代入して表示する（`survey["scores"][0][0]`としてよい）。

#### 合計・平均などの演算

5. 商品Aの満足度リストを`scores_a`に代入する（インデックス`0`）。`total_a`に合計、`average_a`に平均を保存して、両方を表示する。
6. 商品Bの満足度リストを`scores_b`に代入する（インデックス`1`）。`total_b`に合計、`average_b`に平均を保存して、両方を表示する。
7. 商品Cの満足度リストを`scores_c`に代入する（インデックス`2`）。`total_c`に合計、`average_c`に平均を保存して、両方を表示する。

#### データの更新と全体集計

8. 商品Cの満足度リスト（`survey["scores"][2]`）に、新しい回答`2`を`append`する。`scores_c`を`survey["scores"][2]`から取り出し直し、`average_c`を再計算して表示する。
9. 演習5〜7で用いた（または再代入した）`scores_a`・`scores_b`・`scores_c`について、合計値の総和を`grand_total`、要素数の総和を`response_count`、全体平均を`grand_average`に保存して、3つとも表示する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
survey = {
    "products": ["商品A", "商品B", "商品C"],
    "scores": [
        [4, 5, 3, 4, 5],
        [3, 4, 4, 5, 4],
        [5, 3, 4, 3, 4],
    ],
}

product_a = survey["products"][0]
print(product_a)

product_b = survey["products"][1]
print(product_b)

all_scores = survey["scores"]
scores_b = all_scores[1]
score_b_3rd = scores_b[2]
print(score_b_3rd)

score_a_1st = survey["scores"][0][0]
print(score_a_1st)

scores_a = survey["scores"][0]
total_a = sum(scores_a)
average_a = total_a / len(scores_a)
print(total_a)
print(average_a)

scores_b = survey["scores"][1]
total_b = sum(scores_b)
average_b = total_b / len(scores_b)
print(total_b)
print(average_b)

scores_c = survey["scores"][2]
total_c = sum(scores_c)
average_c = total_c / len(scores_c)
print(total_c)
print(average_c)

survey["scores"][2].append(2)

scores_c = survey["scores"][2]
average_c = sum(scores_c) / len(scores_c)
print(average_c)

grand_total = sum(scores_a) + sum(scores_b) + sum(scores_c)
response_count = len(scores_a) + len(scores_b) + len(scores_c)
grand_average = grand_total / response_count

print(grand_total)
print(response_count)
print(grand_average)
```

**実行結果**

```text
商品A
商品B
4
4
21
4.2
20
4.0
19
3.8
3.5
62
16
3.875
```

</details>

---

## 5. まとめ

今回のゼミでは、Pythonでデータを扱うための基本的な仕組みを、アンケート分析の流れで学びました。

| 仕組み | 解決する課題 | 例 |
|---|---|---|
| 変数 | 同じ数値を繰り返し書かず、更新にも対応しやすい | `respondents = 30` |
| リスト | 多数の値を1つにまとめ、件数・合計・平均などを一括で扱える | `scores = [4, 5, 3]` |
| 辞書 | 項目名と値を対応させ、データの意味を明確にできる | `{"age": 21, "satisfaction": 4}` |

変数には数値だけでなく、リストや辞書も代入できます。複数商品の結果は、列ごとにリストを並べた辞書（dict of list）として保持すると、表形式のデータに近い形で扱えます。

```python
survey = {
    "products": ["商品A", "商品B", "商品C"],
    "scores": [
        [4, 5, 3, 4, 5],
        [3, 4, 4, 5, 4],
        [5, 3, 4, 3, 4],
    ],
}
```

データ分析では、まず「どの構造でデータを保持すれば扱いやすいか」を考えることが出発点になります。本格的な集計・可視化では`pandas`などのライブラリを用いる場面が多く、そのデータフレームも同型の列指向データから構築されることがあります。

研究でPythonコードを読むときも、

- この変数には何が保存されているのか
- 辞書のどのキーを指定しているのか
- リストの何番目の要素を取り出しているのか（`[]`が続く場合は左から順に確認する）

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
