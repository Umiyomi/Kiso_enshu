# Part 4 CLI・フォルダ構成

# Pythonプログラミング入門：ターミナルでのフォルダ作成とスクリプト実行

Google Colabのターミナルを使い、**解析用のフォルダを作り、Pythonスクリプトを実行する**流れを学びます。

Part 3までは、同じColabのコードセルでコードを実行していました。今回は、ノートブック左下のターミナルからフォルダを作り、スクリプトを実行します。設定のまとめ方や、実行条件の記録は Part 5 で扱います。

## 今回の目標

- **`pwd` / `ls` / `cd` / `mkdir`** を使い、今いる場所の確認、一覧の表示、移動、フォルダ作成ができる。
- **`python`** でスクリプトを実行できる。実行する場所によって、スクリプトから見えるファイルが変わることを確認できる。
- **`cat`** と **`>`** を使い、実行結果をファイルに残して中身を表示できる。

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
mkdir -p outputs/P1
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
├── data/                 ← 生データ
│   └── survey_responses.csv
├── analyses/             ← 実行するスクリプト
│   └── P1_age_analysis.py
├── outputs/              ← 実行結果
│   └── P1/
│       └── result.txt
├── src/                  ← Part 5 で設定ファイルを置く
└── docs/                 ← Part 5 で仕様と考察を置く
```

| フォルダ | 置くもの |
|---|---|
| `data/` | 生データ。直接編集しない |
| `analyses/` | 実行するスクリプト |
| `outputs/` | スクリプトの実行結果 |
| `src/` | パスや境界条件（Part 5） |
| `docs/` | 仕様や考察の文章（Part 5） |

`outputs/P1/` の `P1` は、今回の実行結果を入れるフォルダの名前です。別の結果用のフォルダは Part 5 で増やします。

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

`analyses/P1_age_analysis.py` を作成します。Part 3と同じく、年齢30歳で行を分け、件数と平均満足度を表示します。

```python
import pandas as pd

df = pd.read_csv("data/survey_responses.csv")
df_older = df[df["age"] >= 30]
df_younger = df[df["age"] < 30]

print("count_all", len(df))
print("mean_all", df["satisfaction"].mean())
print("count_older", len(df_older))
print("mean_older", df_older["satisfaction"].mean())
print("count_younger", len(df_younger))
print("mean_younger", df_younger["satisfaction"].mean())
```

`survey_project` にいることを `pwd` で確認してから実行します。

```bash
pwd
python analyses/P1_age_analysis.py
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

30歳以上10件の平均は `3.8`、30歳未満10件の平均は `2.2` です。Part 3で同じ条件を画面に出したときの値と一致します。

### 演習1：実行する場所を変えてみる

1. `cd analyses` を実行する。
2. `python P1_age_analysis.py` を実行する。
3. `FileNotFoundError` が出ることを確認する。
4. `cd ..` で `survey_project` に戻り、`pwd` のあと `python analyses/P1_age_analysis.py` を再実行する。

`analyses` にいるときのエラーは、次のような表示です。

```text
FileNotFoundError: [Errno 2] No such file or directory: 'data/survey_responses.csv'
```

---

## 3. 実行結果をファイルに残す

### なぜ必要か

`print` の表示は、ターミナルの画面に出るだけです。実行結果は `outputs/P1/` にファイルとして残します。

### 使い方

`>` は、画面に出る表示を、指定したファイルへ書き込みます。フォルダが無いと失敗するため、`outputs/P1` がある状態で実行します。

```bash
pwd
python analyses/P1_age_analysis.py > outputs/P1/result.txt
ls outputs/P1
cat outputs/P1/result.txt
```

実行結果：

```text
/content/survey_project
result.txt
count_all 20
mean_all 3.0
count_older 10
mean_older 3.8
count_younger 10
mean_younger 2.2
```

`>` を付けると、実行中は件数が画面に出ません。`cat` でファイルの中身を表示します。同じファイル名でもう一度実行すると、中身は上書きされます。

この `result.txt` には、30歳で分けたことは書かれていません。条件と結果をセットで残す方法は、Part 5 で扱います。

---

## 4. まとめ

今回のゼミでは、ターミナルでフォルダを作り、その場所からスクリプトを実行しました。

| 学んだこと | 役割 | 代表的なコマンド |
|---|---|---|
| 今いる場所 | パスの起点を確認する | `pwd` |
| 一覧 | フォルダの中身を確認する | `ls` |
| 移動 | データやスクリプトのある場所へ移る | `cd`、`cd ..` |
| 作成 | 役割ごとのフォルダを作る | `mkdir`、`mkdir -p` |
| 実行 | `survey_project` からスクリプトを動かす | `python analyses/P1_age_analysis.py` |
| 結果の保存 | 画面の表示をファイルにして読む | `>`、`cat` |

スクリプトを実行する前に、`pwd` が `survey_project` で終わることを確認します。

---

## ゼミ後の課題

閾値を変えた結果を、別々のファイルに残してください。

1. `survey_project` で、次を実行する。

```bash
python analyses/P1_age_analysis.py > outputs/P1/result_age_30.txt
```

2. `analyses/P1_age_analysis.py` の `30` を `40` に変える。2か所とも変える。
3. 次を実行する。

```bash
python analyses/P1_age_analysis.py > outputs/P1/result_age_40.txt
```

4. `cat outputs/P1/result_age_30.txt` と `cat outputs/P1/result_age_40.txt` で、件数と平均の違いを確認する。
5. スクリプトの `40` を `30` に戻す。

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
