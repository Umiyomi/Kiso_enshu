# Part 3 pandas・matplotlib

# Pythonプログラミング入門：CSVの読み込みと可視化

Google ColabでPythonを動かしながら、商品のアンケート調査データを**ファイルから読み込み、表として集計し、グラフにする**流れを学びます。

Part 1では辞書とリストで表形式のデータを組み立て、Part 2では条件分岐・繰り返し・関数で処理しました。今回は、同じアンケートを**CSVファイル**として扱い、`pandas`と`matplotlib`を使います。

## 今回の目標

- **`pandas`のDataFrame**を使い、行と列のある表としてデータを扱える。
- **`pd.read_csv()`** を使い、CSVファイルを読み込める。
- 列の取り出しや平均など、**表の集計**ができる。
- **`matplotlib`の散布図（`scatter`）** を使い、2つの数値の関係を確認できる。
- 読み込み → 集計 → 可視化を、**一連の手順**として実行できる。

### 進め方

GitHubでこのページを見ながら、コードを[Google Colab](https://colab.research.google.com/notebook?hl=ja)のコードセルにコピーして実行します。

`pandas`と`matplotlib`はColabに最初から入っているため、インストールは不要です。

今回の集計・可視化は、教材のCSVをColabへ**アップロードしてから**読み込む前提で進めます。

1. GitHubの[`data/survey_responses.csv`](../../data/survey_responses.csv)をダウンロードする。
2. Colab左のフォルダアイコンから、そのファイルをアップロードする。
3. ファイル名は`survey_responses.csv`のままにする。

ランタイムを再接続するとアップロードしたファイルは消えるため、その場合は再度アップロードしてください。Googleドライブ上のファイルを使う場合は、READMEの「ドライブへの接続」のあと、`pd.read_csv()`にそのパスを渡します。

---

## まずは動かしてみる

`survey_responses.csv`をアップロードしたうえで、以下のコードをGoogle Colabにコピーして実行してください。表の先頭と散布図が表示されます。

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("survey_responses.csv")

print(df.head())

plt.scatter(df["age"], df["satisfaction"])
plt.xlabel("Age")
plt.ylabel("Satisfaction")
plt.show()
```

`import`は、追加の機能（ライブラリ）を使えるようにする書き方です。`as pd`は、以降`pandas`を`pd`という短い名前で呼ぶ、という意味です。

`FileNotFoundError`が出る場合は、ファイル名が違うか、まだアップロードできていません。

実行結果（表の部分）：

```text
   respondent_id product  age  satisfaction
0              1     商品A   22             3
1              2     商品A   43             3
2              3     商品A   21             3
3              4     商品A   37             3
4              5     商品A   41             4
```

その下に、横軸が年齢、縦軸が満足度の散布図が表示されます。点が右上がりに並んでいれば、年齢が高いほど満足度が高い傾向がある、と読みます。

以降の節で、表の構造、CSVの読み込み、散布図の意味を順に確認します。

---

## 1. DataFrame：表形式のデータを扱う

### なぜ必要か

Part 1の総合演習では、複数商品の満足度を次のような辞書で表しました。

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

この形は表に近い一方、回答者が増えるとリストの入れ子が深くなり、平均や条件付きの取り出しを毎回自分で書く必要があります。

表計算ソフトのシートと同じく、**列の名前で値を取り出せる表**として扱うために、`pandas`の**DataFrame（データフレーム）**を使用します。

### 使い方

1行が1件の回答になる形でDataFrameを作ります。Part 1の「列指向の辞書」を、そのまま渡すイメージです。

```python
import pandas as pd

survey = {
    "age": [21, 24, 35, 42],
    "satisfaction": [3, 3, 4, 5],
}

df = pd.DataFrame(survey)

print(df)
```

実行結果：

```text
   age  satisfaction
0   21             3
1   24             3
2   35             4
3   42             5
```

| 表示 | 意味 |
|---|---|
| 左端の`0, 1, 2, 3` | 行番号（インデックス） |
| `age` / `satisfaction` | 列名 |
| 各行 | 回答1件 |

列名を指定すると、その列だけを取り出せます。辞書でキーを指定する書き方に近いです。

```python
print(df["satisfaction"])
```

実行結果：

```text
0    3
1    3
2    4
3    5
Name: satisfaction, dtype: int64
```

取り出した列に対して、平均などの集計ができます。

```python
print(df["satisfaction"].mean())
print(len(df))
```

実行結果：

```text
3.75
4
```

`mean()`は平均値、`len(df)`は行数（回答件数）です。Part 1で`sum(scores) / len(scores)`と書いていた計算を、列に対して一度に行えます。

---

## 2. CSVファイルを読み込む

### なぜ必要か

実際のアンケート結果は、プログラムの中に辞書として書くのではなく、**ファイルとして保存**されていることがほとんどです。Googleフォームや表計算ソフトから書き出せる形式の代表が**CSV**です。

CSVは、値をカンマ（`,`）で区切ったテキストファイルです。今回使う`survey_responses.csv`の先頭は、次のような内容です。

```text
respondent_id,product,age,satisfaction
1,商品A,22,3
2,商品A,43,3
3,商品A,21,3
```

1行目が列名、2行目以降が回答です。回答は20件で、各行に回答者番号・商品・年齢・満足度が入っています。進め方でアップロード済みであれば、このまま読み込めます。まだの場合は、先に`survey_responses.csv`をColabへアップロードしてください。

### 使い方

アップロードしたCSVを読み込みます。

```python
df = pd.read_csv("survey_responses.csv")

print(df.head())
print(df.shape)
```

実行結果：

```text
   respondent_id product  age  satisfaction
0              1     商品A   22             3
1              2     商品A   43             3
2              3     商品A   21             3
3              4     商品A   37             3
4              5     商品A   41             4
(20, 4)
```

| メソッド / 属性 | 意味 |
|---|---|
| `df.head()` | 先頭5行。件数が多いときに全体像を確認する |
| `df.shape` | `(行数, 列数)`。ここでは回答20件・列4つ |

文字化けや読み込みエラーが出る場合は、`encoding="utf-8"`や`encoding="shift_jis"`を指定します。今回の教材CSVはUTF-8です。

散布図に使う列だけを取り出すこともできます。

```python
print(df["age"])
print(df["satisfaction"].mean())
```

実行結果（平均の行）：

```text
3.0
```

特定の条件の行だけを取り出すには、列の条件で行を絞ります。内側が各行の条件、外側の`df[...]`がその条件を満たす行だけの表です。Part 2の`if`に相当する絞り込みを、表の一括操作として書いています。

商品Aの回答だけを取り出す例です。

```python
df_a = df[df["product"] == "商品A"]

print(df_a)
print(df_a["satisfaction"].mean())
```

実行結果：

```text
   respondent_id product  age  satisfaction
0              1     商品A   22             3
1              2     商品A   43             3
2              3     商品A   21             3
3              4     商品A   37             3
4              5     商品A   41             4
5              6     商品A   46             4
6              7     商品A   32             3
3.2857142857142856
```

年齢が30以上の回答だけを取り出す場合は、次のとおりです。

```python
df_older = df[df["age"] >= 30]

print(df_older)
print(df_older["satisfaction"].mean())
```

実行結果：

```text
    respondent_id product  age  satisfaction
1               2     商品A   43             3
3               4     商品A   37             3
4               5     商品A   41             4
5               6     商品A   46             4
6               7     商品A   32             3
7               8     商品B   41             5
9              10     商品B   43             3
10             11     商品B   44             5
17             18     商品C   37             4
19             20     商品C   42             4
3.8
```

条件を変えるときは、`==`や`>=`の左の列名と、右の値を書き換えます。30歳以上に限ると、全体平均`3.0`より満足度の平均が高くなります。

---

## 3. matplotlib：散布図で関係を見る

### なぜ必要か

2つの数値の関係は、数字を1つずつ見ても掴みにくいことがあります。同じデータを散布図にすると、点が右上がりか、ばらけているか、を視覚的に確認できます。Pythonでグラフを描く標準的なライブラリが`matplotlib`です。

### 使い方

散布図は、同じ長さの2つのリストを渡します。`x`のi番目と`y`のi番目が、1つの点になります。

```python
import matplotlib.pyplot as plt

x = [1, 2, 3, 4, 5]
y = [2, 1, 4, 3, 5]

plt.scatter(x, y)
plt.xlabel("x")
plt.ylabel("y")
plt.show()
```

| 関数 | 役割 |
|---|---|
| `plt.scatter(x, y)` | 横位置`x`、縦位置`y`の散布図を描く |
| `plt.xlabel` / `plt.ylabel` | 横軸・縦軸のラベル |
| `plt.show()` | グラフを表示する |

点が右上がりに並んでいれば、`x`が大きいほど`y`も大きい傾向です。点がばらけている場合は、2つの値に目立った関係は見えない、と判断します。

`x`や`y`の値を書き換えて再実行すると、点の位置が変わることを確認できます。

相関係数の計算、回帰線、検定はこの段階では扱いません。まずは散布図の見た目で、関係の有無を確認します。

---

## 4. 読み込みから可視化まで

ここまでの処理を、1つの流れとしてつなぎます。

1. CSVを読む（`read_csv`）
2. 件数と平均を確認する（`shape`、`mean`）
3. 年齢と満足度の列を取り出す
4. 散布図にする（`plt.scatter`）

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("survey_responses.csv")

print(df.head())
print(df.shape)
print(df["age"].mean())
print(df["satisfaction"].mean())

x = df["age"]
y = df["satisfaction"]

plt.scatter(x, y)
plt.xlabel("Age")
plt.ylabel("Satisfaction")
plt.title("Age and satisfaction")
plt.show()
```

実行結果（グラフ以外）：

```text
   respondent_id product  age  satisfaction
0              1     商品A   22             3
1              2     商品A   43             3
2              3     商品A   21             3
3              4     商品A   37             3
4              5     商品A   41             4
(20, 4)
32.1
3.0
```

平均年齢は`32.1`歳、平均満足度は`3.0`です。散布図では、年齢が高い側に満足度4〜5の点が寄りやすいことが分かります。平均だけを見ると分からない、2つの値の関係を確認している、という位置づけです。

---

## 5. まとめ

今回のゼミでは、アンケートCSVを表として読み、集計し、散布図にする流れを学びました。

| 学んだこと | 役割 | 代表的な書き方 |
|---|---|---|
| DataFrame | 行と列のある表としてデータを持つ | `df = pd.DataFrame(survey)` |
| CSVの読み込み | ファイルに保存された表をプログラムに渡す | `df = pd.read_csv("survey_responses.csv")` |
| 列の集計 | 平均や件数を列に対して求める | `df["satisfaction"].mean()` |
| 行の絞り込み | 条件に合う回答だけを取り出す | `df[df["product"] == "商品A"]` |
| 散布図 | 2つの数値の関係を視覚的に確認する | `plt.scatter(x, y)` |

Part 1の列指向の辞書は、DataFrameと同じ向きのデータです。件数が増えても、列名と条件で取り出せる形にしておくと、集計と可視化にそのままつなげられます。

研究データを扱うときも、次の順で確認すると処理を追いやすくなります。

- ファイルの列名と件数は、想定どおりか（`head()`、`shape`）
- 散布図の横軸・縦軸は、関係を見たい2つの量か
- 点の並びは、右上がり・右下がり・ばらつきのどれに近いか

数値とグラフの両方を見て、読み取った傾向がデータの内容と食い違っていないかを確かめることが、可視化の第一の目的です。

---

## ゼミ後の課題

1. `survey_responses.csv`の年齢または満足度を1件変更して再アップロードし、散布図の並びがどう変わるか確認する。
2. 余裕があれば、自分で小さなCSV（2つの数値列があるもの）を作り、同じ手順で読み込みと散布図まで行う。
