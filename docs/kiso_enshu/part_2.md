# Part 2 if・for・関数

Google ColabでPythonを実際に動かしながら、**条件分岐（if）・繰り返し（for）・関数（def）**を学びます。

コードをセルにコピーして実行し、表示された結果を確認しましょう。次に、数字や文字を変更して結果がどう変わるか試してみましょう。

## 今回の目標

- 変数に入れた値を利用できる。
- `if` を使って、条件によって処理を変えられる。
- `for` を使って、同じ処理を繰り返せる。
- `def` を使って、処理を関数にまとめて呼び出せる。
- `if`・`for`・関数を組み合わせ、簡単な観測データを処理できる。

> **実行方法**：以下のコードをGoogle Colabのコードセルに貼り付け、上から順に実行します。`#` から始まる行は説明用のコメントです。

---

## 1. 変数を復習する

変数は、数字や文字などの値に名前を付けて利用する仕組みです。

```python
water_temp = 20
place = "若狭湾"

print(place)
print(water_temp)

# 変数の値を変更する
water_temp = 22
print(water_temp)
```

`=` は、右側の値を左側の変数に代入する記号です。数学の「等しい」とは意味が異なります。

### やってみよう

`water_temp` を `18` に変更して、出力がどう変わるか確かめましょう。

### Paiza演習

[新・Python入門編4：変数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-4)

- Chapter 1：変数とはなにか
- Chapter 2：変数への代入
- Chapter 3：変数への再代入

---

## 2. if文：条件によって処理を変える

`if` は、**条件を満たした場合だけ特定の処理を実行する**構文です。

```python
water_temp = 22

if water_temp >= 20:
    print("水温は20℃以上です")
```

`water_temp >= 20` は「水温が20以上」という条件です。条件が成り立つときだけ、字下げ（インデント）した `print()` が実行されます。

Pythonでは、`if` の行末に `:` を書き、その下の処理を通常 **半角スペース4個** 分字下げします。

### 条件が成り立たない場合にも処理する

```python
water_temp = 18

if water_temp >= 20:
    print("水温は20℃以上です")
else:
    print("水温は20℃未満です")
```

`else` は、`if` の条件が成り立たない場合の処理です。

### 複数の条件に分ける

```python
water_temp = 22

if water_temp >= 25:
    print("水温は25℃以上です")
elif water_temp >= 20:
    print("水温は20℃以上25℃未満です")
else:
    print("水温は20℃未満です")
```

`elif` は、上の条件が成り立たなかったときに、別の条件を確認します。複数の条件に当てはまる場合でも、上から判定し、最初に成立した分岐だけ実行します。

| 記号 | 意味 | 例 |
| --- | --- | --- |
| `==` | 等しい | `water_temp == 20` |
| `!=` | 等しくない | `water_temp != 20` |
| `>` | より大きい | `water_temp > 20` |
| `<` | より小さい | `water_temp < 20` |
| `>=` | 以上 | `water_temp >= 20` |
| `<=` | 以下 | `water_temp <= 20` |

> **注意**：値を代入するのは `=`、値が等しいかを比較するのは `==` です。

### やってみよう

`water_temp` を `18`、`22`、`27` に変更し、表示がどう変わるか確かめましょう。

### Paiza演習

[新・Python入門編10：条件分岐を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-10)

- Chapter 1：if文とはなにか
- Chapter 2：if文のルール
- Chapter 3：はじめてのif文
- Chapter 4：else節
- Chapter 5：elif節
- Chapter 7：if文の正しくないインデント（エラーを学ぶ）

---

## 3. for文：同じ処理を繰り返す

複数のデータを扱うとき、同じ命令を何回も書くのは大変です。`for` を使うと、処理を繰り返せます。

### 決まった回数だけ繰り返す

```python
for i in range(3):
    print("観測データを確認します")
```

`range(3)` は `0`、`1`、`2` という3つの整数を順番に生成します。

```python
for i in range(3):
    print(i)
```

**出力**

```text
0
1
2
```

### 複数のデータを順番に処理する

`[]` で囲んだ複数の値を**リスト**と呼びます。

```python
water_temps = [18, 22, 25]

for temp in water_temps:
    print(temp)
```

`temp` にリストの値が1つずつ入るため、3回 `print()` が実行されます。

### for文とif文を組み合わせる

```python
water_temps = [18, 22, 25]

for temp in water_temps:
    if temp >= 20:
        print(temp, "℃：20℃以上")
    else:
        print(temp, "℃：20℃未満")
```

これで、**複数の観測値を順番に確認し、条件に応じて分類する**処理ができました。

### やってみよう

`water_temps` に `16` と `28` を追加し、5個のデータすべてが表示されることを確認しましょう。

### Paiza演習

[新・Python入門編13：for文を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-13)

- Chapter 1：for文とはなにか
- Chapter 2：for文のルール
- Chapter 3：はじめてのfor文
- Chapter 10：while文とfor文の比較

> `break`、`continue`、二重ループなどは今回の必須範囲には含めません。

---

## 4. 関数：一連の処理をまとめる

処理が増えると、同じコードを何度も書いたり、どこで何をしているか分かりにくくなったりします。

関数を定義すると、**複数の命令を1つの名前で呼び出せる**ようになります。

### 関数を定義して呼び出す

```python
# 関数を定義する
def say_hello():
    print("こんにちは")

# 関数を呼び出す
say_hello()
say_hello()
```

`def` は関数を定義するためのキーワードです。関数は、定義しただけでは中の処理が実行されません。`say_hello()` のように呼び出すと実行されます。

### 引数を使う

```python
def show_temperature(temp):
    print("水温は", temp, "℃です")

show_temperature(18)
show_temperature(22)
```

`temp` は**引数**です。呼び出すときに値を渡すことで、同じ関数を異なるデータに利用できます。

### returnで結果を返す

```python
def tashizan(a, b):
    result = a + b
    return result

answer = tashizan(100, 200)
print(answer)
```

`return` は、関数の処理結果を呼び出し元に返します。

`print()` は結果を画面に表示しますが、`return` は結果を後の計算などに利用できるよう返す点が異なります。

### やってみよう

上の `tashizan()` を参考に、2つの数を掛け算する `kakezan(a, b)` を作成し、`kakezan(3, 5)` の答えを表示しましょう。

### Paiza演習

[新・Python入門編18：関数を学習しよう](https://paiza.jp/works/python/new-primer/python-new-primer-18)

- chapter 1 : 関数とはなにか

- chapter 2 : 関数の書き方

- chapter 3 : 関数を定義

- chapter 5 : 関数を呼び出す場所

- chapter 7 : 返り値を書かなかったとき

- chapter 8 : 関数の処理を強制的に終了(return)

このレッスンのうち、**関数の定義・呼び出し・引数・戻り値**に関するチャプターを演習対象とします。

---

## 5. 総合演習：観測データを判定するプログラム

ここまでの `if`・`for`・関数を組み合わせて、複数の水温観測値を判定するプログラムを作ります。

まず、1つの水温を判定する関数を作ります。

```python
def check_temperature(temp):
    if temp >= 20:
        return "20℃以上"
    else:
        return "20℃未満"
```

次に、複数の観測値を順番に調べる関数を作ります。

```python
def main_process():
    water_temps = [18, 22, 25, 16]

    for temp in water_temps:
        result = check_temperature(temp)
        print(temp, "℃：", result)
```

最後に、メイン処理を呼び出します。

```python
main_process()
```

**出力例**

```text
18 ℃： 20℃未満
22 ℃： 20℃以上
25 ℃： 20℃以上
16 ℃： 20℃未満
```

このプログラムは、以下の処理に分けられています。

1. 観測データを用意する（変数とリスト）
2. 観測データを1つずつ取り出す（for）
3. 20℃以上か判定する（if）
4. 判定方法を関数にまとめる（def・return）
5. 結果を画面に表示する（print）

> **注意**：ここでの「20℃以上」は文法練習のために設定した仮の条件です。特定の生物の適水温や漁獲条件を表すものではありません。

### 最後の課題

- `water_temps` に新しい観測値を2個追加してみましょう。
- 判定する水温を `20` ℃から `23` ℃に変更してみましょう。
- 余裕があれば、`check_temperature()` が `15℃未満`、`15℃以上25℃未満`、`25℃以上` の3区分を返すように変更しましょう。

---

## まとめ

| 学んだこと | 役割 | 代表的な書き方 |
| --- | --- | --- |
| 変数 | データに名前を付けて保存する | `temp = 22` |
| if文 | 条件によって処理を分ける | `if temp >= 20:` |
| for文 | データを順番に処理する | `for temp in water_temps:` |
| 関数 | 処理をまとめ、繰り返し利用する | `def check_temperature(temp):` |

研究でデータを扱う際にも、**データを用意する → 繰り返し処理する → 条件を判定する → 関数にまとめる**という考え方が役立ちます。

最初からすべて暗記する必要はありません。コードを実行し、少し変更し、結果を確かめることから始めましょう。

## 参考・演習リンク

- [paizaラーニング：新・Python入門編](https://paiza.jp/works/python/new-primer)
- [第4回：変数](https://paiza.jp/works/python/new-primer/python-new-primer-4)
- [第10回：条件分岐](https://paiza.jp/works/python/new-primer/python-new-primer-10)
- [第13回：for文](https://paiza.jp/works/python/new-primer/python-new-primer-13)
- [第18回：関数](https://paiza.jp/works/python/new-primer/python-new-primer-18)

※ paizaの一部動画・演習には会員登録または有料プランが必要な場合があります。実習前に利用できる範囲を確認してください。
