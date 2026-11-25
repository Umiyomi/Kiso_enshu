# Part 5 結果の記録

# Pythonプログラミング入門：設定を1か所にまとめ、大量の結果を管理する

**散らばった設定を1か所にまとめ、結果を1つの表に記録し、全体を点検する**流れを学びます。

Part 4では、4つのデータに、4つのスクリプトと、4つのPNGを作りました。基準の値は、スクリプトの中に4か所書かれています。基準を変えるたびに4つのファイルを開き直す必要があります。結果も、どのPNGがどの条件か、ファイル名を見ないと分かりません。

今回は、基準の値を `config.json` にまとめ、結果を `runs.csv` に1行ずつ記録します。そのうえで、全体が正しく揃っているかを確かめます。

## 今回の目標

- 基準の値とデータのパスを **`src/config.json` にまとめ**、スクリプトから読み込める。
- 結果を **`outputs/runs.csv` に1行ずつ追記**し、`cat` や `wc -l` で確認できる。
- **`>` と `>>`** の違いを使い分けられる。
- 出力の数が期待どおりかを、`find` と `wc -l` で点検できる。
- 仕様（`docs/specification.md`）と次の課題（`docs/issues.md`）を書ける。

### 進め方

Part 4と同じノートブックで、Colabのターミナルを開きます。`cd /content/survey_project` を実行し、`pwd` の表示が `/content/survey_project` になることを確認してから始めてください。

ランタイムを再接続してフォルダが消えている場合は、Part 4の手順でもう一度作ってください。

ファイルの作成と編集は、Part 4と同じく、左のフォルダ一覧から行います。

---

## 1. 設定を `src/config.json` にまとめる

### なぜ必要か

Part 4の4つのスクリプトには、`DATA_PATH`、`X_COL`、`Y_COL`、`THRESHOLD` がそれぞれ書かれています。基準を3つ試したいときは、4つのファイルを開き、同じ作業を12回繰り返すことになります。

設定を1か所にまとめれば、直すべき場所は1つです。

### 使い方

ターミナルで、`src/` フォルダと設定ファイルを作ります。

```bash
mkdir src
touch src/config.json
ls src
```

実行結果：

```text
config.json
```

左のフォルダ一覧から `src/config.json` をダブルクリックして開き、次の内容を貼り付けます。

```json
{
  "survey": {
    "data_path": "data/survey_responses.csv",
    "x_col": "age",
    "y_col": "satisfaction",
    "thresholds": [30, 35, 40]
  },
  "shop": {
    "data_path": "data/shop_sales.csv",
    "x_col": "temperature",
    "y_col": "sales",
    "thresholds": [20, 25, 30]
  },
  "fishing": {
    "data_path": "data/fishing_catch.csv",
    "x_col": "water_temp",
    "y_col": "catch_kg",
    "thresholds": [16, 18, 20]
  },
  "study": {
    "data_path": "data/study_scores.csv",
    "x_col": "study_hours",
    "y_col": "score",
    "thresholds": [2, 3, 4]
  }
}
```

JSONは、Part 1の辞書と同じく、`"キー": 値` の組で書きます。`{ }` の中に、データの名前（`survey` など）ごとの設定が並びます。

`"thresholds"` は**リスト**です。`[30, 35, 40]` のように、基準を複数書けます。スクリプトはこのリストを1つずつ取り出して、基準ごとに結果を作ります。

設定が正しく書けたか確認します。

```bash
cat src/config.json
```

---

## 2. スクリプトを設定ファイルから動かす

### なぜ必要か

スクリプトの中には、もう数字もパスも書きません。`config.json` を読み、その項目を使います。スクリプトに残すのは、**どの項目を読むか**を決める1行だけです。

### 使い方

`analyses/survey_scatter.py` を、次のコードで置き換えます。左のフォルダ一覧から開き、中身をすべて消してから貼り付けてください。

```python
import json
import pandas as pd
import matplotlib.pyplot as plt

NAME = "survey"  # config.json の項目名（データごとに変える）
with open("src/config.json", encoding="utf-8") as f:
    config = json.load(f)[NAME]

x_col = config["x_col"]
y_col = config["y_col"]
df = pd.read_csv(config["data_path"])

for threshold in config["thresholds"]:
    high = df[df[x_col] >= threshold]
    low = df[df[x_col] < threshold]

    plt.figure(figsize=(8, 6))
    plt.scatter(high[x_col], high[y_col], color="red", label=f"{x_col} >= {threshold}", alpha=0.7)
    plt.scatter(low[x_col], low[y_col], color="blue", label=f"{x_col} < {threshold}", alpha=0.7)
    plt.xlabel(x_col)
    plt.ylabel(y_col)
    plt.legend()
    plt.savefig(f"outputs/{NAME}/{NAME}_scatter_th{threshold}.png", dpi=100, bbox_inches="tight")
    plt.close()

    # 結果を runs.csv に1行追記する
    with open("outputs/runs.csv", "a", encoding="utf-8") as f:
        f.write(f"{NAME},{threshold},{len(high)},{round(high[y_col].mean(), 3)},{len(low)},{round(low[y_col].mean(), 3)}\n")
```

書き換えた部分は次のとおりです。

| 行 | 役割 |
|---|---|
| `NAME = "survey"` | `config.json` のどの項目を読むか。データごとに変える唯一の行 |
| `json.load(f)[NAME]` | `config.json` を読み、`NAME` の項目を取り出す |
| `for threshold in config["thresholds"]:` | 基準のリストを1つずつ取り出して、繰り返す |
| `open("outputs/runs.csv", "a")` | 結果の表に1行を追記する |

`"a"` は「追記（append）」の指定です。ファイルの末尾に、新しい行を足します。

### 残りの3つを、コピーして作る

Part 4と同じように、`cp` で3つ複製します。

```bash
cp analyses/survey_scatter.py analyses/shop_scatter.py
cp analyses/survey_scatter.py analyses/fishing_scatter.py
cp analyses/survey_scatter.py analyses/study_scatter.py
```

コピーした3つを開き、**`NAME` の行だけを**書き換えます。

| ファイル | 書き換える行 |
|---|---|
| `shop_scatter.py` | `NAME = "shop"` |
| `fishing_scatter.py` | `NAME = "fishing"` |
| `study_scatter.py` | `NAME = "study"` |

---

## 3. 全部を1行で回し、結果を `runs.csv` に記録する

### 使い方

`runs.csv` は、結果を書きためる表です。最初に、列名の行を1行だけ作ります。

```bash
echo "dataset,threshold,n_high,mean_high,n_low,mean_low" > outputs/runs.csv
```

`>` は、ファイルを**新しく作って、中身を置き換える**指定です。`>>` は、末尾に**追記する**指定です。今の場合、表を作るのは1回だけなので `>` を使います。

続けて、4つのスクリプトを1行のコマンドで順に実行します。

```bash
pwd
for f in analyses/*_scatter.py; do python $f; done
ls outputs/*
```

`for f in analyses/*_scatter.py` は、「`analyses/` の中で `_scatter.py` で終わるファイルを、1つずつ `f` に入れて」という意味です。`*` は、任意の文字列に一致する記号です。`do` と `done` の間を、1つずつ実行します。

`ls outputs/*` の結果は次のとおりです（一部を省略）。

```text
outputs/fishing:
fishing_scatter_th16.png  fishing_scatter_th18.png  fishing_scatter_th20.png
outputs/shop:
shop_scatter_th20.png  shop_scatter_th25.png  shop_scatter_th30.png
outputs/study:
study_scatter_th2.png  study_scatter_th3.png  study_scatter_th4.png
outputs/survey:
survey_scatter_th30.png  survey_scatter_th35.png  survey_scatter_th40.png
```

Part 4の4枚が、12枚に増えました。

`runs.csv` の中身を見ます。

```bash
cat outputs/runs.csv
```

実行結果：

```text
dataset,threshold,n_high,mean_high,n_low,mean_low
survey,30,10,3.8,10,2.2
survey,35,9,3.889,11,2.273
survey,40,7,4.0,13,2.462
shop,20,12,77.75,8,37.5
shop,25,9,85.889,11,41.818
shop,30,4,94.5,16,53.438
fishing,16,14,188.571,6,94.167
fishing,18,11,210.0,9,99.444
fishing,20,7,222.143,13,126.923
study,2,15,69.467,5,42.0
study,3,12,73.75,8,45.875
study,4,9,78.0,11,50.0
```

1行が、1回の実行に対応します。データ名、基準、基準以上の件数と平均、基準未満の件数と平均が並んでいます。PNGの名前と、この表の行は、データ名と基準で対応しています。

---

## 4. 全体が正しく揃っているか点検する

### なぜ必要か

複数のデータを扱うと、**「作られるはずのものが揃っているか」**を確認しないと、欠けたまま気づかないことがあります。Part 2の「偽の成功」と同じで、エラーが出なくても、件数が足りないことはあります。

### 使い方

点検は、期待する数と、実際の数を比べることから始めます。

| 確認すること | コマンド | 期待する結果 |
|---|---|---|
| PNGの枚数 | `find outputs -name "*.png" \| wc -l` | `12` |
| 表の行数（見出しを含む） | `wc -l outputs/runs.csv` | `13` |
| 特定のデータの結果 | `grep shop outputs/runs.csv` | `shop` の3行 |
| 特定の基準の画像 | `ls outputs/*/ \| grep th30` | `survey_scatter_th30.png` など |

`find outputs -name "*.png" | wc -l` の `|` は、左のコマンドの結果を右のコマンドに渡す指定です。`wc -l` は、行数を数えます。

```bash
find outputs -name "*.png" | wc -l
wc -l outputs/runs.csv
grep shop outputs/runs.csv
```

実行結果：

```text
12
13 outputs/runs.csv
shop,20,12,77.75,8,37.5
shop,25,9,85.889,11,41.818
shop,30,4,94.5,16,53.438
```

Part 4の課題で、基準を35・28・20・4にして作った画像が残っている場合は、PNGの枚数が12より多くなります。その差が、残っているファイルの数です。**数が合わないときは、どのファイルが余分かを探します。**

### 演習1：同じ実行を2回すると、どうなるか

結果が二重に残ると、どうなるかを確かめます。

1. `cp -r outputs outputs_backup` を実行し、今の `outputs` の控えを作る。
2. `python analyses/survey_scatter.py` を1回だけ実行する。
3. `wc -l outputs/runs.csv` を実行する。

`16` になっていれば、アンケートの3行が、もう1回追記されています。`cat outputs/runs.csv` で、`survey` の行が2組あることを確認してください。

4. 表を作り直してから、4つのスクリプトを順に実行し直す。

```bash
echo "dataset,threshold,n_high,mean_high,n_low,mean_low" > outputs/runs.csv
for f in analyses/*_scatter.py; do python $f; done
wc -l outputs/runs.csv
```

`13` に戻れば、表は揃っています。

`>>` で追記するたびに、同じ実行が重なります。結果を残すときは、**どの実行の行かを確かめ、必要なら表を作り直す**習慣が大切です。

---

## 5. 控えを残し、文章を書く

### なぜ必要か

数字だけでは、データの列の意味や、次に何を調べるかは分かりません。一方で、数字を文章に書き写すと、直したときに食い違います。数字は `runs.csv` を正とし、文章は仕様と課題だけに絞ります。

### 使い方

`cp` と `mv` は、Part 4 の `cp` と同じ要領で使えます。

| コマンド | 役割 |
|---|---|
| `cp コピー元 コピー先` | ファイルを複製する。フォルダごとは `cp -r` |
| `mv 移動元 移動先` | ファイルを移動する。名前の変更にも使う |

今の `outputs_backup` のような控えは、消さずに残します。不要になったら、`mv` で別の場所へ移します。

```bash
mkdir docs
touch docs/specification.md
touch docs/issues.md
touch README.md
```

左のフォルダ一覧から各ファイルを開き、次のとおり貼り付けます。

#### docs/specification.md

```markdown
# データ仕様

教材用のモックデータです。単位は説明のための仮定です。

| ファイル | 内容 | 横軸（X） | 縦軸（Y） | 行数 |
|---|---|---|---|---|
| `data/survey_responses.csv` | アンケート | `age`（満年齢） | `satisfaction`（1不満〜5満足） | 20 |
| `data/shop_sales.csv` | 店舗の日次売上 | `temperature`（気温、℃） | `sales`（売上、千円） | 20 |
| `data/fishing_catch.csv` | 出漁ごとの漁獲 | `water_temp`（水温、℃） | `catch_kg`（漁獲量、kg） | 20 |
| `data/study_scores.csv` | 学習時間と得点 | `study_hours`（時間） | `score`（得点） | 20 |

基準（`thresholds`）は `src/config.json` に書きます。数値の結果は `outputs/runs.csv` を参照してください。
```

#### docs/issues.md

```markdown
# 調べること

- [Issue-1] 完了: アンケートで、年齢の基準を30・35・40に変えて満足度を比べる
- [Issue-2] 完了: 店舗の売上を、気温の基準を変えて比べる
- [Issue-3] 未着手: 漁獲は、水温の基準を何度にすると差がはっきりするかを決める
```

#### README.md

````markdown
# 複数データの散布図（survey_project）

## フォルダ

- `data/`: 生データ（編集しない）
- `src/config.json`: データのパスと、基準の値
- `analyses/`: 解析スクリプト（データごとに1つ）
- `outputs/`: 結果（PNG と `runs.csv`）
- `docs/`: データ仕様と、次に調べること

## 実行方法

`survey_project` で実行する。

```bash
for f in analyses/*_scatter.py; do python $f; done
```
````

`README.md` は、フォルダの役割と実行方法を書いておくファイルです。あとから見る人が、最初に読みます。

---

## 6. まとめ

今回のゼミでは、Part 4の4つのデータに対する解析を、設定と記録と点検で管理しました。

| 学んだこと | 役割 | 今回使ったもの |
|---|---|---|
| 設定の集約 | 基準とパスを1か所で管理する | `src/config.json` |
| 設定の読み込み | スクリプトは設定を読むだけにする | `json.load`、`NAME` |
| 一括実行 | 全データを1行で回す | `for f in analyses/*_scatter.py; do python $f; done` |
| 結果の記録 | 実行ごとに1行を表に残す | `outputs/runs.csv`、`>`、`>>` |
| 点検 | 期待する数と実際の数を比べる | `find`、`wc -l`、`grep` |
| 控え | 変える前の状態を残す | `cp -r`、`mv` |
| 文章 | 数字ではなく、意味と課題を残す | `docs/specification.md`、`docs/issues.md`、`README.md` |

結果を見返すときは、次の順でファイルを開きます。

- 元データは `data/`。
- 試した条件は `src/config.json`。
- 結果の数値は `outputs/runs.csv`。
- 画像は `outputs/データ名/`。
- 列の意味と次の課題は `docs/`。

---

## ゼミ後の課題

5つ目のデータを追加してください。

1. 自分で作った、または教材で用意した CSV（列は `id` と、X、Y の2列以上）を `data/` に置く。
2. `src/config.json` に、新しい項目を1つ足す。`data_path`、`x_col`、`y_col`、`thresholds` を書く。
3. `cp analyses/survey_scatter.py analyses/新しい名前_scatter.py` で複製し、`NAME` の行だけを書き換える。
4. 全体のループを、もう1回だけ実行する。ループの1行は変えずに、新しいスクリプトも回るか確かめる。

確認項目は次のとおりです。

- 新しい設定と、新しいスクリプトを足しただけで済んだか。
- `runs.csv` に、新しいデータの行が増えたか。
- `docs/issues.md` の `[Issue-3]` を完了に変え、分からなかったことを `[Issue-4]` として1つ足したか。

5つ目を足すとき、どこを直したかを数えてください。直すべき行数が少ないほど、設定を1か所にまとめた効果が出ています。
