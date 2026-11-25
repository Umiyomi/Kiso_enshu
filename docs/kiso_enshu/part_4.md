# Part 4 CLI・フォルダ構成

# Pythonプログラミング入門：複数のデータを、フォルダで分けて解析する

Google Colabのターミナルを使い、**複数のデータを対象に、同じ解析を繰り返す**流れを学びます。

今回扱うデータは4つです。アンケート、店舗の売上、漁獲、学習時間と得点。どれも「ある値が基準以上か未満か」で点を2色に分けて散布図にします。データが増えると、スクリプトも結果の画像も増えていきます。どこに何を置いたかを、ターミナルで管理できるようにします。

設定や実行条件の記録は Part 5 で扱います。

### Colabのターミナルと Linux

Colabのターミナルは、Linux という OS の**コマンドライン（シェル）**です。今回入力する `pwd`、`ls`、`cd`、`mkdir` は、Linux で共通に使うコマンドです。

研究室では、Linux のワークステーションに SSH で接続して、実際の解析を行います。接続先で入力するコマンドは、ここで打つものと同じです。Colab で身につけた操作は、そのまま研究の作業に使えます。

## 今回の目標

- **`pwd` / `ls` / `cd` / `mkdir` / `cp`** を使い、場所の確認、移動、フォルダ作成、ファイルの複製ができる。
- **`python`** でスクリプトを実行できる。実行する場所によって、スクリプトから見えるファイルが変わることを確認できる。
- **散布図を PNG 画像ファイルとして保存**し、データごとのフォルダに置ける。

### 進め方

Part 1〜3と同じく、[Google Colab](https://colab.research.google.com/notebook?hl=ja)のノートブックを使います。コマンドはコードセルではなく、Colabのターミナルに入力します。

1. ノートブックを開く。ランタイムに接続する。まだなら、セルを1つ実行するか、右上の「接続」を押す。
2. [`data.zip`](../../data/data.zip) をダウンロードし、左のフォルダ一覧で `/content` を右クリックして「アップロード」から選ぶ。
3. 画面左下の「ターミナル」を押す。黒い入力欄が開きます。
4. `pwd` を実行する。`/content` と表示されれば、ここが作業の起点です。

`pandas` と `matplotlib` は Colab に入っているため、インストールは不要です。

ランタイムを再接続すると、`/content` に作ったフォルダやアップロードしたファイルは消えます。消えた場合は、この手順からやり直してください。

---

## 1. 場所を確認し、フォルダを作る

### なぜ必要か

データが1つなら、ファイルはどこに置いても困りません。データが4つになると、どのデータがどのフォルダにあるか、どの結果がどのデータのものか、を決めて置かないと混ざります。

### 使い方

迷ったときは、**`pwd` で今いる場所を確認し、`ls` で中身を見ます。**

| コマンド | 役割 |
|---|---|
| `pwd` | 今いるフォルダのパスを表示する |
| `ls` | ファイルとフォルダの一覧を表示する |
| `cd フォルダ名` | そのフォルダへ移動する。`cd ..` で1つ上へ戻る |
| `mkdir フォルダ名` | フォルダを作る |
| `unzip ファイル名 -d 場所` | zipの中身を、指定した場所に展開する |

ターミナルで、次を順に実行します。

```bash
mkdir survey_project
unzip data.zip -d survey_project
cd survey_project
mkdir analyses
mkdir -p outputs/{survey,shop,fishing,study}
pwd
ls
ls data
```

`outputs/{survey,shop,fishing,study}` の `{...}` は、中のフォルダ名を4つ一度に作る書き方です。`outputs/survey`、`outputs/shop`、`outputs/fishing`、`outputs/study` の4つができます。`-p` は、途中のフォルダ（`outputs`）がなくても一緒に作るための指定です。

実行結果（`ls` と `ls data`）：

```text
/content/survey_project
analyses  data  outputs
fishing_catch.csv  shop_sales.csv  study_scores.csv  survey_responses.csv
```

`unzip` の表示は省略します。`data/` の中に4つのCSVが並んでいれば成功です。

作った構成は次のとおりです。

```text
survey_project/
├── data/                  ← 4つのCSV（編集しない）
│   ├── survey_responses.csv     アンケート
│   ├── shop_sales.csv           店舗の売上
│   ├── fishing_catch.csv        漁獲
│   └── study_scores.csv         学習時間と得点
├── analyses/              ← 解析スクリプト
└── outputs/               ← 結果のPNG
    ├── survey/
    ├── shop/
    ├── fishing/
    └── study/
```

移動できることも確認します。

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

以降のコマンドは、`survey_project` にいる状態で実行します。別の場所にいるときは `cd ..` や `cd survey_project` で戻ってください。

---

## 2. 解析スクリプトを1本作って実行する

### なぜ必要か

4つのデータは、列の名前も、基準にする値も違います。アンケートは `age`（年齢）と `satisfaction`（満足度）、店舗は `temperature`（気温）と `sales`（売上）です。

そこで、**データごとに変える値は、スクリプトの先頭にまとめて書きます。** 残りの処理は全データで同じです。

### 使い方

ターミナルで、アンケート用のスクリプトのファイルを作ります。

```bash
touch analyses/survey_scatter.py
ls analyses
```

左のフォルダ一覧から `survey_scatter.py` をダブルクリックして開き、次のコードを貼り付けます。

```python
import pandas as pd
import matplotlib.pyplot as plt

# ---- 設定（データごとに書き換える部分）----
DATA_PATH = "data/survey_responses.csv"
X_COL = "age"
Y_COL = "satisfaction"
THRESHOLD = 30
OUT_PATH = "outputs/survey/survey_scatter_th30.png"
# -----------------------------------------

df = pd.read_csv(DATA_PATH)
high = df[df[X_COL] >= THRESHOLD]
low = df[df[X_COL] < THRESHOLD]

plt.scatter(high[X_COL], high[Y_COL], color="red", label=f"{X_COL} >= {THRESHOLD}", alpha=0.7)
plt.scatter(low[X_COL], low[Y_COL], color="blue", label=f"{X_COL} < {THRESHOLD}", alpha=0.7)
plt.xlabel(X_COL)
plt.ylabel(Y_COL)
plt.legend()
plt.savefig(OUT_PATH, dpi=100, bbox_inches="tight")
plt.close()

print(DATA_PATH, "high", len(high), round(high[Y_COL].mean(), 3), "low", len(low), round(low[Y_COL].mean(), 3))
```

`# ----` で囲んだ部分が、データごとの設定です。

| 名前 | 意味 |
|---|---|
| `DATA_PATH` | 読むCSVのパス（`survey_project` から見た位置） |
| `X_COL` / `Y_COL` | 横軸・縦軸にする列の名前 |
| `THRESHOLD` | 基準の値。以上と未満で2色に分ける |
| `OUT_PATH` | 保存するPNGのパス |

`survey_project` にいることを確認してから、実行します。

```bash
pwd
python analyses/survey_scatter.py
ls outputs/survey
```

実行結果：

```text
/content/survey_project
data/survey_responses.csv high 10 3.8 low 10 2.2
survey_scatter_th30.png
```

`high` は基準以上の件数と平均、`low` は基準未満の件数と平均です。Part 3 で同じ条件で絞り込んだときの値と一致します。

### 演習1：実行する場所を変えてみる

1. `cd analyses` を実行する。
2. `python survey_scatter.py` を実行する。
3. `FileNotFoundError` が出ることを確認する。
4. `cd ..` で `survey_project` に戻り、`pwd` のあと `python analyses/survey_scatter.py` を再実行する。

`analyses` にいるときのエラーは、次のような表示です。

```text
FileNotFoundError: [Errno 2] No such file or directory: 'data/survey_responses.csv'
```

スクリプトの中身は同じでも、今いる場所が違うと、`data/` を見つけられません。

---

## 3. スクリプトをコピーして、データごとに増やす

### なぜ必要か

新しいデータを分析するとき、ゼロからスクリプトを書く必要はありません。同じ形のスクリプトを**コピーして、設定の部分だけを書き換えます**。

### 使い方

`cp` は、ファイルを複製するコマンドです。`cp コピー元 コピー先` の形で書きます。

```bash
cp analyses/survey_scatter.py analyses/shop_scatter.py
cp analyses/survey_scatter.py analyses/fishing_scatter.py
cp analyses/survey_scatter.py analyses/study_scatter.py
ls analyses
```

実行結果：

```text
fishing_scatter.py  shop_scatter.py  study_scatter.py  survey_scatter.py
```

コピーした3つのファイルを、左のフォルダ一覧から開いて、**設定の部分だけ**を書き換えます。それぞれの値は次のとおりです。

| ファイル | `DATA_PATH` | `X_COL` | `Y_COL` | `THRESHOLD` | `OUT_PATH` |
|---|---|---|---|---|---|
| `survey_scatter.py` | `data/survey_responses.csv` | `age` | `satisfaction` | `30` | `outputs/survey/survey_scatter_th30.png` |
| `shop_scatter.py` | `data/shop_sales.csv` | `temperature` | `sales` | `25` | `outputs/shop/shop_scatter_th25.png` |
| `fishing_scatter.py` | `data/fishing_catch.csv` | `water_temp` | `catch_kg` | `18` | `outputs/fishing/fishing_scatter_th18.png` |
| `study_scatter.py` | `data/study_scores.csv` | `study_hours` | `score` | `3` | `outputs/study/study_scatter_th3.png` |

`shop_scatter.py` を例にすると、設定の部分は次のようになります。

```python
DATA_PATH = "data/shop_sales.csv"
X_COL = "temperature"
Y_COL = "sales"
THRESHOLD = 25
OUT_PATH = "outputs/shop/shop_scatter_th25.png"
```

設定以外の行は、**1行も書き換えません。** 4つのスクリプトは、同じ処理を別のデータに当てているだけです。

### 4つを順に実行する

```bash
python analyses/survey_scatter.py
python analyses/shop_scatter.py
python analyses/fishing_scatter.py
python analyses/study_scatter.py
ls outputs/*
```

実行結果：

```text
data/survey_responses.csv high 10 3.8 low 10 2.2
data/shop_sales.csv high 9 85.889 low 11 41.818
data/fishing_catch.csv high 11 210.0 low 9 99.444
data/study_scores.csv high 12 73.75 low 8 45.875
outputs/fishing:
fishing_scatter_th18.png

outputs/shop:
shop_scatter_th25.png

outputs/study:
study_scatter_th3.png

outputs/survey:
survey_scatter_th30.png
```

4つのPNGが、データごとのフォルダに保存されました。左のフォルダ一覧で `outputs` の中を開き、ダブルクリックしてプレビューを確認してください。

### 演習2：どのファイルがどの設定か

次の問いに、`ls` と `cat` を使わず、スクリプトを開いて答えてください。

1. `outputs/shop/shop_scatter_th25.png` は、どのスクリプトで作られ、横軸には何の列を使っていますか。
2. `shop_scatter.py` の `THRESHOLD` を `30` に変えたとき、`OUT_PATH` を直し忘れると、どのファイルが何に上書きされますか。

---

## 4. まとめ

今回のゼミでは、4つのデータを、データごとのフォルダに分けて散布図を保存しました。

| 学んだこと | 役割 | 代表的なコマンド |
|---|---|---|
| 今いる場所 | パスの起点を確認する | `pwd` |
| 一覧 | フォルダの中身を確認する | `ls`、`ls data` |
| 移動 | データやスクリプトのある場所へ移る | `cd`、`cd ..` |
| 作成 | 役割ごとのフォルダを作る。`{...}` で複数まとめて作る | `mkdir`、`mkdir -p outputs/{survey,shop}` |
| 展開 | zipの中身を置く | `unzip data.zip -d survey_project` |
| 複製 | 既存のスクリプトを、別のデータ用に写す | `cp` |
| 実行 | `survey_project` からスクリプトを動かす | `python analyses/shop_scatter.py` |

スクリプトを実行する前に、`pwd` が `survey_project` で終わることを確認します。

---

## ゼミ後の課題

基準の値を変えて、4つの散布図を作り直してください。

1. 4つのスクリプトの `THRESHOLD` と `OUT_PATH` を、次のように変える。

| ファイル | `THRESHOLD` | `OUT_PATH` |
|---|---|---|
| `survey_scatter.py` | `35` | `outputs/survey/survey_scatter_th35.png` |
| `shop_scatter.py` | `28` | `outputs/shop/shop_scatter_th28.png` |
| `fishing_scatter.py` | `20` | `outputs/fishing/fishing_scatter_th20.png` |
| `study_scatter.py` | `4` | `outputs/study/study_scatter_th4.png` |

2. `python analyses/...` を4回実行し、`ls outputs/*` で、以前のPNGと新しいPNGが両方残っていることを確認する。

3. 次の数値が出ていれば、正しく変えられています。

<details>
<summary>確認用の数値（クリックして確認）</summary>

```text
data/survey_responses.csv high 9 3.889 low 11 2.273
data/shop_sales.csv high 6 92.167 low 14 48.571
data/fishing_catch.csv high 7 222.143 low 13 126.923
data/study_scores.csv high 9 78.0 low 11 50.0
```

</details>

4. 振り返り：今回、基準の値を変えるために直した行は、全部で何行でしたか。また、直し忘れると、どんな困りごとが起きそうですか。Part 5 で、この設定をまとめる方法を学びます。

---

## 発展：Linux のシェルコマンドを学ぶ

今回のターミナル操作は、Linux のシェルコマンドの基本です。習熟のため、paiza の Linux シェルコマンド入門編を受講してください。今回は全チャプターを受講します。

https://paiza.jp/works/shellcommand/primer
