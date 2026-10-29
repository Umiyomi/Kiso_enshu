# Part 2 if・for・関数

# Pythonプログラミング入門：条件分岐・繰り返し・関数

Google ColabでPythonを動かしながら、商品のアンケート調査データを集計・分析する流れで、**条件分岐（if）・繰り返し（for）・関数（def）**を学びます。

## 今回の目標

- Part 1で学んだ**変数**を使い、購入希望率などの集計に再利用できる。
- **`if`** を使い、満足度などの値に応じて処理を分けられる。
- **`for`** を使い、複数の回答を順番に処理できる。
- **`def`** を使い、判定や集計の処理を関数にまとめて呼び出せる。
- **`if`・`for`・関数を組み合わせ**、Part 1の`survey`データを処理できる。

### 進め方

GitHubでこのページを見ながら、コードを[Google Colab](https://colab.research.google.com/notebook?hl=ja)のコードセルにコピーして実行します。

演習では、指示に従って**自分でコードを入力**してください。

Part 1の総合演習で用いた`survey`（3商品×各5人分の満足度）を、本パートでもそのまま利用します。

---

## 1. 変数を復習する

Part 1では、回答者数や購入希望者数に名前を付け、購入希望率を計算しました。

```python
respondents = 40
want_to_buy = 28

print(respondents)
print(want_to_buy)

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

実行結果：

```text
40
28
70.0
```

`=`は「右側の値を左側の変数に代入する」という意味です。数学の「等しい」とは異なります。

同じ変数に別の値を代入すると、その後は新しい値が使用されます。

```python
want_to_buy = 32

purchase_rate = want_to_buy / respondents * 100

print(purchase_rate)
```

実行結果：

```text
80.0
```

### やってみよう

`want_to_buy`を`25`に変更し、購入希望率の表示がどう変わるか確かめましょう。

### Paizaの復習課題

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

今回の内容に対応するChapterは以下の通りです。

- Chapter 1：変数とはなにか
- Chapter 2：変数への代入
- Chapter 3：変数への再代入

**全3チャプターを復習課題とします。**

---

## 2. if文：条件によって処理を変える

### なぜ必要か

商品Aのアンケートで、1人分の満足度が4点（5点満点）だったとします。

「4点以上なら高評価として記録する」といった**条件に応じた処理**を、プログラムで表現するには`if`を使用します。

### 使い方

`if`は、**条件を満たした場合だけ**字下げ（インデント）した処理を実行する構文です。

```python
satisfaction = 4

if satisfaction >= 4:
    print("満足度は4点以上です（高評価）")
```

`satisfaction >= 4`は「満足度が4以上」という条件です。条件が成り立つときだけ、`print()`が実行されます。

Pythonでは、`if`の行末に`:`を書き、その下の処理を通常**半角スペース4個**分字下げします。

条件が成り立たない場合にも別の処理を行うには、`else`を使用します。

```python
satisfaction = 3

if satisfaction >= 4:
    print("満足度は4点以上です（高評価）")
else:
    print("満足度は4点未満です")
```

3段階以上に分ける場合は、`elif`を使用します。

```python
satisfaction = 3

if satisfaction >= 4:
    print("高評価（4点以上）")
elif satisfaction >= 3:
    print("普通（3点）")
else:
    print("低評価（2点以下）")
```

`elif`は、上の条件が成り立たなかったときに、別の条件を確認します。複数の条件に当てはまる場合でも、上から判定し、最初に成立した分岐だけ実行します。

| 記号 | 意味 | 例 |
|---|---|---|
| `==` | 等しい | `satisfaction == 4` |
| `!=` | 等しくない | `satisfaction != 4` |
| `>` | より大きい | `satisfaction > 3` |
| `<` | より小さい | `satisfaction < 3` |
| `>=` | 以上 | `satisfaction >= 4` |
| `<=` | 以下 | `satisfaction <= 2` |

値を代入するのは`=`、値が等しいかを比較するのは`==`です。

### 演習1：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 満足度`4`を`satisfaction`に代入する。
2. `satisfaction >= 4`のとき「高評価」、そうでないとき「4点未満」と表示する（`if`と`else`）。
3. `satisfaction`を`2`、`3`、`5`に変更し、表示がどう変わるか確認する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
satisfaction = 4

if satisfaction >= 4:
    print("高評価")
else:
    print("4点未満")

satisfaction = 2
if satisfaction >= 4:
    print("高評価")
else:
    print("4点未満")

satisfaction = 5
if satisfaction >= 4:
    print("高評価")
else:
    print("4点未満")
```

**実行結果（例）**

```text
高評価
4点未満
高評価
```

</details>

### Paizaの復習課題

[新・Python入門編10：条件分岐を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-10)

**今回の復習課題**

- Chapter 1：if文とはなにか
- Chapter 2：if文のルール
- Chapter 3：はじめてのif文
- Chapter 4：else節
- Chapter 5：elif節
- Chapter 7：if文の正しくないインデント（エラーを学ぶ）

---

## 3. for文：同じ処理を繰り返す

### なぜ必要か

商品Aについて、5人分の満足度 `[4, 5, 3, 4, 5]` を1件ずつ確認したいとします。

同じ`print()`や`if`を5回書くのは手間がかかり、回答者が増えるほど現実的ではありません。

**リストの要素を順番に取り出し、同じ処理を繰り返す**ために`for`を使用します。

### 使い方

決まった回数だけ繰り返す場合は、`range()`と組み合わせます。

```python
for i in range(3):
    print("回答を確認します")
```

`range(3)`は`0`、`1`、`2`という3つの整数を順番に生成します。

```python
for i in range(3):
    print(i)
```

実行結果：

```text
0
1
2
```

アンケートの満足度リストを順番に処理する例は次のとおりです。

```python
scores = [4, 5, 3, 4, 5]

for score in scores:
    print(score)
```

`score`にリストの値が1つずつ入るため、5回`print()`が実行されます。

`for`と`if`を組み合わせると、**複数の満足度を順番に確認し、条件に応じて分類する**処理ができます。

```python
scores = [4, 5, 3, 4, 5]

for score in scores:
    if score >= 4:
        print(score, "点：高評価（4点以上）")
    else:
        print(score, "点：4点未満")
```

実行結果：

```text
4 点：高評価（4点以上）
5 点：高評価（4点以上）
3 点：4点未満
4 点：高評価（4点以上）
5 点：高評価（4点以上）
```

### 演習2：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 満足度 `[3, 4, 5, 2, 4]` を`scores`に代入し、`for`で1件ずつ表示する。
2. 上記と同じ`scores`について、4点以上なら「高評価」、未満なら「4点未満」と表示する（`for`と`if`）。
3. リストに`1`と`5`を追加（`append`）し、2の分類表示を再度実行する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
scores = [3, 4, 5, 2, 4]

for score in scores:
    print(score)

for score in scores:
    if score >= 4:
        print(score, "点：高評価")
    else:
        print(score, "点：4点未満")

scores.append(1)
scores.append(5)

for score in scores:
    if score >= 4:
        print(score, "点：高評価")
    else:
        print(score, "点：4点未満")
```

</details>

### Paizaの復習課題

[新・Python入門編13：for文を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-13)

**今回の復習課題**

- Chapter 1：for文とはなにか
- Chapter 2：for文のルール
- Chapter 3：はじめてのfor文
- Chapter 10：while文とfor文の比較

`break`、`continue`、二重ループなどは、今回の基礎演習では扱いません。

---

## 4. 関数：一連の処理をまとめる

### なぜ必要か

「満足度が4点以上かどうか」を、商品ごと・回答者ごとに何度も判定する場合、同じ`if`のブロックを繰り返し書くことになります。

**判定の手順を1つの名前にまとめ、必要なときに呼び出す**ために関数（`def`）を使用します。

### 使い方

```python
def show_product_name():
    print("商品Aのアンケート")

show_product_name()
show_product_name()
```

`def`は関数を定義するためのキーワードです。関数は、定義しただけでは中の処理が実行されません。`show_product_name()`のように**呼び出す**と実行されます。

呼び出すときに値を渡す**引数**を使うと、同じ関数を異なるデータに利用できます。

```python
def show_satisfaction(score):
    print("満足度は", score, "点です")

show_satisfaction(4)
show_satisfaction(5)
```

関数の処理結果を後の計算などに使うには、`return`で値を返します。

```python
def purchase_rate(want_to_buy, respondents):
    rate = want_to_buy / respondents * 100
    return rate

rate_a = purchase_rate(28, 40)
print(rate_a)
```

実行結果：

```text
70.0
```

`print()`は結果を画面に表示しますが、`return`は結果を変数に代入したり、別の計算に使ったりできる点が異なります。

### 演習3：自分で書いてみよう

Google Colabで、次の手順に従ってコードを入力・実行してください。

1. 2つの数を掛け算する`kakezan(a, b)`を定義し、結果を`return`する。`kakezan(3, 5)`の結果を表示する。
2. 満足度`score`を引数に取り、4点以上なら`"高評価"`、未満なら`"4点未満"`を`return`する`classify_score(score)`を定義する。
3. `classify_score(3)`と`classify_score(5)`の戻り値をそれぞれ表示する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例**

```python
def kakezan(a, b):
    return a * b

print(kakezan(3, 5))

def classify_score(score):
    if score >= 4:
        return "高評価"
    else:
        return "4点未満"

print(classify_score(3))
print(classify_score(5))
```

**実行結果**

```text
15
4点未満
高評価
```

</details>

### Paizaの復習課題

[新・Python入門編18：関数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-18)

**今回の復習課題**

- Chapter 1：関数とはなにか
- Chapter 2：関数の書き方
- Chapter 3：関数を定義
- Chapter 5：関数を呼び出す場所
- Chapter 7：返り値を書かなかったとき
- Chapter 8：関数の処理を強制的に終了(return)

---

## 5. 総合演習：`survey`の満足度を判定する

Part 1と同じ列指向の辞書`survey`を用い、`if`・`for`・関数を組み合わせます。

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

1件の満足度を判定する関数を定義します。

```python
def classify_score(score):
    if score >= 4:
        return "高評価（4点以上）"
    else:
        return "4点未満"
```

1商品分の満足度リストを順番に表示・判定する関数を定義します。

```python
def show_scores_for_product(product_index):
    product = survey["products"][product_index]
    scores = survey["scores"][product_index]

    print(product)
    for score in scores:
        label = classify_score(score)
        print(score, "点：", label)
```

商品A（インデックス`0`）について呼び出します。

```python
show_scores_for_product(0)
```

実行結果（例）：

```text
商品A
4 点： 高評価（4点以上）
5 点： 高評価（4点以上）
3 点： 4点未満
4 点： 高評価（4点以上）
5 点： 高評価（4点以上）
```

このプログラムは、次の処理に分けられます。

1. Part 1の`survey`から商品名と満足度リストを取り出す（辞書とリスト）
2. 各回答を1件ずつ取り出す（`for`）
3. 4点以上かどうか判定する（`if`）
4. 判定方法を関数にまとめる（`def`・`return`）
5. 結果を画面に表示する（`print`）

ここでの「4点以上を高評価とする」条件は、文法練習のための仮の基準です。実際の調査では、研究目的に応じて閾値や区分を設計してください。

### 演習4：自分で書いてみよう

Google Colabで、上記の`survey`と`classify_score`・`show_scores_for_product`を定義したうえで、次の手順に従ってコードを入力・実行してください。

1. 商品B（インデックス`1`）について`show_scores_for_product(1)`を実行する。
2. `classify_score`の閾値を`4`から`3`に変更し（3点以上を「高評価」）、商品Aの表示を再度実行する。
3. 余裕があれば、`classify_score`が「2点以下」「3点」「4点以上」の3区分を`return`するように変更し、商品Cの結果を表示する。

<details>
<summary>解答例・実行結果（クリックして確認）</summary>

**解答例（1のみ）**

```python
survey = {
    "products": ["商品A", "商品B", "商品C"],
    "scores": [
        [4, 5, 3, 4, 5],
        [3, 4, 4, 5, 4],
        [5, 3, 4, 3, 4],
    ],
}

def classify_score(score):
    if score >= 4:
        return "高評価（4点以上）"
    else:
        return "4点未満"

def show_scores_for_product(product_index):
    product = survey["products"][product_index]
    scores = survey["scores"][product_index]
    print(product)
    for score in scores:
        label = classify_score(score)
        print(score, "点：", label)

show_scores_for_product(1)
```

</details>

---

## 6. まとめ

今回のゼミでは、アンケートデータの集計・判定の流れで、制御構文と関数の基本を学びました。

| 学んだこと | 役割 | 代表的な書き方 |
|---|---|---|
| 変数 | 回答者数や購入希望率などに名前を付けて再利用する | `purchase_rate = want_to_buy / respondents * 100` |
| if文 | 満足度などの条件によって処理を分ける | `if score >= 4:` |
| for文 | 満足度リストを順番に処理する | `for score in scores:` |
| 関数 | 判定や計算をまとめ、繰り返し利用する | `def classify_score(score):` |

Part 1の`survey`のように表形式のデータを用意し、**取り出す → 繰り返す → 条件で分ける → 関数にまとめる**という流れは、研究データを扱うときにも同型です。

最初からすべて暗記する必要はありません。コードを実行し、数値や条件を少し変更し、結果を確かめることから始めてください。

---

## ゼミ後の課題

Paizaラーニング「新・Python入門編」の以下の講座に取り組んでください。

### 1. 変数（復習）

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

対象：Chapter 1〜3

### 2. 条件分岐

[新・Python入門編10：条件分岐を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-10)

対象：Chapter 1〜5、7

### 3. for文

[新・Python入門編13：for文を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-13)

対象：Chapter 1〜3、10

### 4. 関数

[新・Python入門編18：関数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-18)

対象：Chapter 1〜3、5、7、8

今回扱わなかった内容についても、必要に応じて学習してみてください。
