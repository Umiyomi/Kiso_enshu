# Part 4 CLI・フォルダ構成

# Pythonプログラミング入門：ターミナルでのフォルダ作成とスクリプト実行

Google Colabのターミナルを使い、**解析用のフォルダを作り、Pythonスクリプトを実行する**流れを学びます。

Part 3までは、同じColabのコードセルでコードを実行していました。今回は、ノートブック左下のターミナルからフォルダを作り、スクリプトを実行します。設定のまとめ方や、実行条件の記録は Part 5 で扱います。

## 今回の目標

- **`pwd` / `ls` / `cd` / `mkdir`** を使い、今いる場所の確認、一覧の表示、移動、フォルダ作成ができる。
- **`python`** でスクリプトを実行できる。実行する場所によって、スクリプトから見えるファイルが変わることを確認できる。
- **`matplotlib.pyplot`** を使い、散布図を PNG 画像ファイルとして保存できる。

### 進め方

Part 1〜3と同じく、[Google Colab](https://colab.research.google.com/notebook?hl=ja)のノートブックを使います。コマンドはコードセルではなく、Colabのターミナルに入力します。

1. ノートブックを開く。
2. ランタイムに接続する。まだなら、セルを1つ実行するか、右上の「接続」を押す。
3. 画面左下の「ターミナル」を押す。黒い入力欄が開きます。
4. `pwd` を実行する。`/content` と表示されれば、ここが作業の起点です。

`pandas` は Colab に入っているため、インストールは不要です。

ファイルの中身（スクリプトなど）は、左のフォルダ一覧で作ります。置きたいフォルダを右クリックして「新しいファイル」を選び、ダブルクリックして開いてから、本文のコードを貼り付けます。

ランタイムを再接続すると、`/content` に作ったフォルダやアップロードしたファイルは消えます。消えた場合は、フォルダ作成からやり直してください。

---

## 1. 場所を確認し、フォルダを作る

### なぜ必要か

コードセルから実行するときも、ファイルはColabの中のフォルダにあります。ターミナルでは、今いるフォルダによって見えるファイルが変わります。データ、スクリプト、実行結果を、それぞれのフォルダに置きます。

### 使い方

迷ったときは、**`pwd` で今いる場所を確認し、`ls` で中身を見ます。**

| コマンド | 役割 |
|---|---|
| `pwd` | 今いるフォルダのパスを表示する |
| `ls` | ファイルとフォルダの一覧を表示する |
| `cd フォルダ名` | そのフォルダへ移動する。`cd ..` で1つ上へ戻る |
| `mkdir フォルダ名` | フォルダを作る。`mkdir -p` は途中の階層もまとめて作る |

開いたターミナルで、次を順に実行します。

```bash
mkdir survey_project
cd survey_project
mkdir data src analyses docs
mkdir -p outputs/age_analysis
pwd
ls
```

実行結果：

```text
/content/survey_project
analyses  data  docs  outputs  src
```

Colabのターミナルでは、パスは `/content` から始まります。

作る構成は次のとおりです。`src/` と `docs/` は、この回では空のままにします。中のファイルは Part 5 で置きます。

```text
survey_project/
├── data/                      ← 生データ
│   └── survey_responses.csv
├── analyses/                  ← 実行するスクリプト
│   └── age_analysis.py
├── outputs/                   ← 実行結果
│   └── age_analysis/
│       └── age_analysis.png
├── src/                       ← Part 5 で設定ファイルを置く
└── docs/                      ← Part 5 で仕様と考察を置く
```

| フォルダ | 置くもの |
|---|---|
| `data/` | 生データ。直接編集しない |
| `analyses/` | 実行するスクリプト |
| `outputs/` | スクリプトの実行結果 |
| `src/` | パスや境界条件（Part 5） |
| `docs/` | 仕様や考察の文章（Part 5） |

`outputs/age_analysis/` は、年齢分析（`age_analysis`）の結果を入れるフォルダです。別の解析結果用のフォルダは Part 5 で増やします。

移動できることを確認します。

```bash
cd data
pwd
cd ..
pwd
```

実行結果：

```text
/content/survey_project/data
/content/survey_project
```

Part 3で使った [`survey_responses.csv`](../../data/survey_responses.csv) を、`survey_project/data/` に置きます。左のフォルダ一覧で `data` を右クリックし、「アップロード」からファイルを選んでください。置いたあと、ターミナルが `survey_project` にいることを確認します。

```bash
pwd
ls data
```

実行結果：

```text
/content/survey_project
survey_responses.csv
```

以降のコマンドは、`survey_project` にいる状態で実行します。別の場所にいるときは `cd ..` や `cd survey_project` で戻ってください。

---

## 2. スクリプトを実行する

### なぜ必要か

スクリプトの中の `data/survey_responses.csv` は、ファイル名だけでは場所が決まりません。**今いるフォルダから見た位置**です。`survey_project` にいるときはこのパスで読めます。`analyses` に移動した状態で同じスクリプトを実行すると、`analyses/data/` を探しにいき、ファイルが見つかりません。

### 使い方

ターミナルでファイルを作成します。まず、`analyses/` ディレクトリにファイルを作ります。

```bash
touch analyses/age_analysis.py
```

ファイルが作られたことを確認します。

```bash
ls analyses/
```

次に、以下のコードをコピーして、テキストエディタで `analyses/age_analysis.py` に貼り付けます。左のフォルダ一覧から `age_analysis.py` をダブルクリックして開き、下のコード全体をコピーして貼り付けてください。

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("data/survey_responses.csv")
df_older = df[df["age"] >= 30]
df_younger = df[df["age"] < 30]

plt.figure(figsize=(8, 6))
plt.scatter(df_older["age"], df_older["satisfaction"], color="red", label="age >= 30", alpha=0.7)
plt.scatter(df_younger["age"], df_younger["satisfaction"], color="blue", label="age < 30", alpha=0.7)
plt.xlabel("Age")
plt.ylabel("Satisfaction")
plt.title("Age and Satisfaction (threshold: 30)")
plt.legend()
plt.grid(True, alpha=0.3)
plt.savefig("outputs/age_analysis/age_analysis.png", dpi=100, bbox_inches="tight")
plt.close()

print("count_all", len(df))
print("mean_all", df["satisfaction"].mean())
print("count_older", len(df_older))
print("mean_older", df_older["satisfaction"].mean())
print("count_younger", len(df_younger))
print("mean_younger", df_younger["satisfaction"].mean())
```

ファイルを保存したことを確認します。

`survey_project` にいることを `pwd` で確認してから実行します。

```bash
pwd
python analyses/age_analysis.py
```

実行結果：

```text
/content/survey_project
count_all 20
mean_all 3.0
count_older 10
mean_older 3.8
count_younger 10
mean_younger 2.2
```

30歳以上10件の平均は `3.8`、30歳未満10件の平均は `2.2` です。Part 3で同じ条件を画面に出したときの値と一致します。同時に、`outputs/age_analysis/age_analysis.png` に散布図が保存されます。

### 演習1：実行する場所を変えてみる

1. `cd analyses` を実行する。
2. `python age_analysis.py` を実行する。
3. `FileNotFoundError` が出ることを確認する。
4. `cd ..` で `survey_project` に戻り、`pwd` のあと `python analyses/age_analysis.py` を再実行する。

`analyses` にいるときのエラーは、次のような表示です。

```text
FileNotFoundError: [Errno 2] No such file or directory: 'data/survey_responses.csv'
```

---

## 3. 実行結果をファイルに残す

### なぜ必要か

スクリプトが散布図を生成するとき、`plt.savefig()` を使うと画像ファイルとして保存できます。解析結果は `outputs/` に画像として残すことで、あとからいつでも確認できます。ファイルの**格納先**と**名付け方**を工夫することで、異なる条件での実行結果を整理・比較しやすくなります。

### 使い方

スクリプト内の `plt.savefig("outputs/age_analysis/age_analysis.png")` により、PNG形式の散布図が自動的に保存されます。

```python
plt.savefig("outputs/age_analysis/age_analysis.png")
```

ここで指定している **`"outputs/age_analysis/age_analysis.png"`** は、パスとファイル名です。

| 要素 | 意味 |
|---|---|
| `outputs/` | スクリプト実行位置（`survey_project`）から見た、実行結果の保存先フォルダ |
| `age_analysis/` | 解析の種類を示すフォルダ。ここでは年齢分析（`age_analysis`） |
| `age_analysis.png` | 結果ファイル。解析名と同じ名前でPNG形式の画像 |

フォルダが無いと失敗するため、`outputs/age_analysis` がある状態で実行します。

```bash
pwd
python analyses/age_analysis.py
ls outputs/age_analysis
```

実行結果：

```text
/content/survey_project
age_analysis.png
count_all 20
mean_all 3.0
count_older 10
mean_older 3.8
count_younger 10
mean_younger 2.2
```

スクリプト実行後、`outputs/age_analysis/` に `age_analysis.png` が保存されます。

### ファイル名に情報を残す

同じ条件で複数回実行すると、ファイルは上書きされます。**異なる条件の結果を比較するときは、ファイル名に条件を含める**ことが大切です。

例えば、30歳を境界とした結果なら `age_analysis_threshold_30.png`、40歳を境界とした結果なら `age_analysis_threshold_40.png` のように、**条件が分かるファイル名**にします。

```python
# 条件を含めた名付け
plt.savefig("outputs/age_analysis/age_analysis_threshold_30.png")
```

ファイル名に「何を・どの条件で・どの結果なのか」が分かる情報を入れておくと、あとからファイルを見るだけで、何の実行結果か判断できます。

このスクリプトは `print` で統計量も画面に出すため、数値確認とグラフの両方を活用できます。条件と結果をセットで残す方法は、Part 5 で扱います。

---

## 4. まとめ

今回のゼミでは、ターミナルでフォルダを作り、その場所からスクリプトを実行しました。結果は散布図として画像ファイルに保存されます。

| 学んだこと | 役割 | 代表的なコマンド |
|---|---|---|
| 今いる場所 | パスの起点を確認する | `pwd` |
| 一覧 | フォルダの中身を確認する | `ls` |
| 移動 | データやスクリプトのある場所へ移る | `cd`、`cd ..` |
| 作成 | 役割ごとのフォルダを作る | `mkdir`、`mkdir -p` |
| 実行 | `survey_project` からスクリプトを動かす | `python analyses/age_analysis.py` |
| 結果の保存 | スクリプトから散布図を PNG 画像として保存 | `plt.savefig()`、`ls` で確認 |

スクリプトを実行する前に、`pwd` が `survey_project` で終わることを確認します。

---

## ゼミ後の課題

閾値を変えた散布図を、別々のファイルに残してください。

1. `survey_project` で、現在のスクリプトを実行する。

```bash
python analyses/age_analysis.py
```

2. `outputs/age_analysis/age_analysis.png` が生成されたことを確認する。

```bash
ls outputs/age_analysis
```

3. `analyses/age_analysis.py` の `30` をすべて `40` に変える。
   - `df_older = df[df["age"] >= 30]` を `df_older = df[df["age"] >= 40]` に
   - `label="age >= 30"` を `label="age >= 40"` に
   - `title="Age and Satisfaction (threshold: 30)"` を `title="Age and Satisfaction (threshold: 40)"` に

4. `outputs/age_analysis/age_analysis.png` をリネームして保存しておく。左のフォルダ一覧で右クリック → 「名前を変更」で `age_analysis_threshold_30.png` に変更。

5. 編集したスクリプトを実行する。

```bash
python analyses/age_analysis.py
```

6. 新しい `age_analysis.png`（40歳で分けた図）と `age_analysis_threshold_30.png` を左のフォルダ一覧で比較する。ダブルクリックでプレビューを開く。

7. スクリプトの `40` をすべて `30` に戻す。

<details>
<summary>確認用の数値（40歳で分けた場合）</summary>

```text
count_all 20
mean_all 3.0
count_older 7
mean_older 4.0
count_younger 13
mean_younger 2.4615384615384617
```

</details>
