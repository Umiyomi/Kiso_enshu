# Part 5 結果の記録

# Pythonプログラミング入門：設定・メタデータ・考察をファイルに残す

**設定、実行条件、計算結果、考察を、決まったファイルに残す**流れを学びます。

Part 4では、`survey_project` のフォルダを作り、`analyses/P1_age_analysis.py` を実行して、表示を `outputs/P1/result.txt` に残しました。このテキストには件数と平均は入っていますが、何歳で分けたか、どのCSVを読んだかは入っていません。今回は、その情報を結果と一緒に残し、別の解析も同じ形で追加します。

## 今回の目標

- パスと境界条件を **`src/config.py` にまとめ**、解析スクリプトから読み込める。
- グラフとCSVに加え、実行条件と結果を書いた **`summary.json`** を `outputs/P1/` に保存できる。
- **`cp` / `mv`** でスクリプトの控えを `archive/` に置き、解析ID **`P2`** を追加できる。
- 実行したスクリプトと `config.py` を、出力フォルダへコピーして残せる。
- 仕様（`docs/specification.md`）、考察（`docs/P1_report.md`）、次に調べること（`docs/issues.md`）を書ける。

### 進め方

Part 4と同じノートブックで、Colabのターミナルを開きます。`cd /content/survey_project` を実行し、`pwd` の表示が `/content/survey_project` になることを確認してから始めてください。ランタイムを再接続してフォルダが消えている場合は、Part 4の手順でもう一度作ってください。

ファイルの作成と編集は、Part 4と同じく、左のフォルダ一覧から行います。`matplotlib` は Colab に入っているため、インストールは不要です。

---

## 1. 設定を `src/config.py` にまとめる

### なぜ必要か

Part 4のスクリプトには、`"data/survey_responses.csv"` と `30` が直接書いてあります。基準を変えるたびに、スクリプトの中の数値を探す必要があります。Part 1で、数値を変数にまとめたのと同じ理由です。

入出力のパスと境界条件は `src/config.py` に書き、解析スクリプトから読み込みます。

### 使い方

`src/config.py` を作成し、次を保存します。

```python
RAW_SURVEY_CSV = "data/survey_responses.csv"
OUTPUT_ROOT = "outputs"

AGE_THRESHOLD = 30
```

`AGE_THRESHOLD` は、30歳以上と30歳未満を分ける基準です。変えるときは、この1か所を書き換えます。

```bash
cat src/config.py
```

`analyses/P1_age_analysis.py` を、設定を読む次の内容に書き換えます。集計そのものは Part 4 と同じです。

```python
import os
import sys
import pandas as pd

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

df = pd.read_csv(config.RAW_SURVEY_CSV)
df_older = df[df["age"] >= config.AGE_THRESHOLD]
df_younger = df[df["age"] < config.AGE_THRESHOLD]

print("threshold", config.AGE_THRESHOLD)
print("count_all", len(df))
print("mean_all", df["satisfaction"].mean())
print("count_older", len(df_older))
print("mean_older", df_older["satisfaction"].mean())
print("count_younger", len(df_younger))
print("mean_younger", df_younger["satisfaction"].mean())
```

スクリプトは `analyses/` の中にあります。`sys.path.append(...)` は、1つ上の `survey_project` を読み込み先に加え、`src/config.py` を `import` できるようにします。CSVのパスは、Part 4 と同じく、コマンドを実行した場所から見た位置です。

```bash
pwd
python analyses/P1_age_analysis.py
```

実行結果：

```text
/content/survey_project
threshold 30
count_all 20
mean_all 3.0
count_older 10
mean_older 3.8
count_younger 10
mean_younger 2.2
```

先頭の `threshold 30` が、`config.py` から読んだ値です。

---

## 2. 実行条件と結果を `summary.json` に残す

### なぜ必要か

`result.txt` や、`print` の表示だけでは、あとから「どのファイルを、何歳で分けたか」が分かりません。グラフだけ保存した場合も同じです。

`outputs/P1/` には、集計表、グラフ、実行条件と結果を書いた `P1_summary.json` を一緒に保存します。JSONは、Part 1の辞書と同じく、キーと値の組で書けるテキストです。

### 使い方

`analyses/P1_age_analysis.py` を、次の内容に書き換えます。データの読み込みと、年齢での分割は前の節と同じです。

```python
import json
import os
import sys
import pandas as pd
import matplotlib.pyplot as plt

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

analysis_id = "P1"
out_dir = f"{config.OUTPUT_ROOT}/{analysis_id}"
os.makedirs(out_dir, exist_ok=True)

df = pd.read_csv(config.RAW_SURVEY_CSV)
df_older = df[df["age"] >= config.AGE_THRESHOLD]
df_younger = df[df["age"] < config.AGE_THRESHOLD]

mean_all = float(df["satisfaction"].mean())
mean_older = float(df_older["satisfaction"].mean())
mean_younger = float(df_younger["satisfaction"].mean())

df_older.to_csv(f"{out_dir}/filtered_age_{config.AGE_THRESHOLD}.csv", index=False)

plt.scatter(df["age"], df["satisfaction"])
plt.axvline(x=config.AGE_THRESHOLD, color="red", linestyle="--")
plt.xlabel("Age")
plt.ylabel("Satisfaction")
plt.title(f"{analysis_id}: Age and Satisfaction")
plt.savefig(f"{out_dir}/age_satisfaction.png")
plt.close()

summary = {
    "analysis_id": analysis_id,
    "input_file": config.RAW_SURVEY_CSV,
    "parameters": {
        "age_threshold": config.AGE_THRESHOLD,
    },
    "results": {
        "total_count": len(df),
        "older_count": len(df_older),
        "younger_count": len(df_younger),
        "mean_satisfaction_all": round(mean_all, 3),
        "mean_satisfaction_older": round(mean_older, 3),
        "mean_satisfaction_younger": round(mean_younger, 3),
    },
}

with open(f"{out_dir}/{analysis_id}_summary.json", "w", encoding="utf-8") as f:
    json.dump(summary, f, indent=2, ensure_ascii=False)

print(out_dir)
```

`os.makedirs(..., exist_ok=True)` は、出力フォルダがなければ作ります。`to_csv` の `index=False` は、行番号をCSVに書きません。`axvline` は、境界の年齢に縦の破線を引きます。Part 3の `plt.show()` と違い、`savefig` は画像をファイルに保存します。

`with open(...) as f` はファイルを開いて書き、終わったら閉じます。`json.dump` は辞書をJSONとして書き出します。`indent=2` は読みやすいように字下げし、`ensure_ascii=False` は日本語を `\u...` にせずそのまま保存します。

```bash
python analyses/P1_age_analysis.py
ls outputs/P1
cat outputs/P1/P1_summary.json
```

実行結果：

```text
outputs/P1
P1_summary.json  age_satisfaction.png  filtered_age_30.csv  result.txt
{
  "analysis_id": "P1",
  "input_file": "data/survey_responses.csv",
  "parameters": {
    "age_threshold": 30
  },
  "results": {
    "total_count": 20,
    "older_count": 10,
    "younger_count": 10,
    "mean_satisfaction_all": 3.0,
    "mean_satisfaction_older": 3.8,
    "mean_satisfaction_younger": 2.2
  }
}
```

`ls` に並ぶ `result.txt` は、Part 4 で残したファイルです。課題で `result_age_30.txt` などを作っていれば、それも並びます。今回の確認には使いません。`parameters` が実行時の条件、`results` が計算結果です。

同じ `outputs/P1/` に再実行すると、JSONと画像は上書きされます。CSVのファイル名には閾値が入るため、`filtered_age_40.csv` のような別名は残ります。

### 演習1：境界条件を変えて記録を確認する

1. `src/config.py` の `AGE_THRESHOLD` を `40` に変える。
2. `python analyses/P1_age_analysis.py` を再実行する。
3. `cat outputs/P1/P1_summary.json` で、`age_threshold` と件数が変わっていることを確認する。
4. `AGE_THRESHOLD` を `30` に戻し、もう一度実行する。

<details>
<summary>確認用の数値（40歳で分けた場合）</summary>

```text
"age_threshold": 40
"older_count": 7
"younger_count": 13
"mean_satisfaction_older": 4.0
"mean_satisfaction_younger": 2.462
```

</details>

---

## 3. スクリプトの控えを残す

### なぜ必要か

スクリプトを書き直す前に、今の版を残しておきたいことがあります。`P1_old.py` や `P1_final.py` を `analyses/` に増やしていくと、どれを実行すればよいか分かりにくくなります。

今のスクリプトは `analyses/` に残し、控えは `analyses/archive/` へ移します。

### 使い方

| コマンド | 役割 |
|---|---|
| `cp コピー元 コピー先` | ファイルを複製する。フォルダごと複製するときは `cp -r` |
| `mv 移動元 移動先` | ファイルを移動する。名前の変更にも使う |

```bash
pwd
mkdir -p analyses/archive
cp analyses/P1_age_analysis.py analyses/archive/P1_age_analysis_backup.py
ls analyses
ls analyses/archive
```

実行結果：

```text
/content/survey_project
P1_age_analysis.py  archive
P1_age_analysis_backup.py
```

`cp` のあと、元の `analyses/P1_age_analysis.py` はそのまま残ります。控えのファイル名を変えるときは `mv` を使います。

```bash
mv analyses/archive/P1_age_analysis_backup.py analyses/archive/P1_v1.py
ls analyses/archive
```

実行結果：

```text
P1_v1.py
```

使わなくなった版を片づけるときも、削除せず `archive/` へ `mv` します。`data/` のCSVと、今実行しているスクリプトは移動しません。

---

## 4. 解析ID `P2` を追加する

### なぜ必要か

`P1` は「年齢と満足度」です。次に「商品ごとの満足度」を調べるとき、`P1_age_analysis.py` に処理を足すと、1つのファイルが2つの問いを担います。

問いごとに解析IDを分け、スクリプト、出力フォルダ、`summary.json` の名前を揃えます。

```text
analyses/P1_age_analysis.py      outputs/P1/P1_summary.json
analyses/P2_product_analysis.py  outputs/P2/P2_summary.json
```

さらに、実行の瞬間のスクリプトと `config.py` を `outputs/P2/` へコピーします。あとから設定を書き換えても、そのフォルダを見れば、どのプログラムでその結果が出たかが分かります。

### 使い方

`analyses/P2_product_analysis.py` を作成します。商品ごとの件数と平均は、Part 3の絞り込みを商品名の数だけ繰り返して求めます。

```python
import json
import os
import shutil
import sys
import pandas as pd
import matplotlib.pyplot as plt

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

analysis_id = "P2"
out_dir = f"{config.OUTPUT_ROOT}/{analysis_id}"
os.makedirs(out_dir, exist_ok=True)

shutil.copy(__file__, f"{out_dir}/used_script.py")
shutil.copy("src/config.py", f"{out_dir}/used_config.py")

df = pd.read_csv(config.RAW_SURVEY_CSV)

rows = []
names = []
means = []
for product in ["商品A", "商品B", "商品C"]:
    part = df[df["product"] == product]
    mean = round(float(part["satisfaction"].mean()), 3)
    rows.append({
        "product": product,
        "count": len(part),
        "mean": mean,
    })
    names.append(product)
    means.append(mean)

pd.DataFrame(rows).to_csv(f"{out_dir}/product_summary.csv", index=False)

plt.bar(names, means)
plt.xlabel("Product")
plt.ylabel("Mean Satisfaction")
plt.title(f"{analysis_id}: Mean Satisfaction by Product")
plt.savefig(f"{out_dir}/product_satisfaction_bar.png")
plt.close()

summary = {
    "analysis_id": analysis_id,
    "input_file": config.RAW_SURVEY_CSV,
    "results_by_product": rows,
}

with open(f"{out_dir}/{analysis_id}_summary.json", "w", encoding="utf-8") as f:
    json.dump(summary, f, indent=2, ensure_ascii=False)

print(out_dir)
```

`__file__` は、今実行しているスクリプト自身のパスです。`shutil.copy` は、`cp` と同じ複製を、プログラムの中から行います。`plt.bar` は棒グラフです。軸の日本語が環境によって文字化けすることがあります。数値はCSVとJSONで確認できます。

```bash
python analyses/P2_product_analysis.py
ls outputs/P2
cat outputs/P2/P2_summary.json
```

実行結果：

```text
outputs/P2
P2_summary.json  product_satisfaction_bar.png  product_summary.csv  used_config.py  used_script.py
{
  "analysis_id": "P2",
  "input_file": "data/survey_responses.csv",
  "results_by_product": [
    {
      "product": "商品A",
      "count": 7,
      "mean": 3.286
    },
    {
      "product": "商品B",
      "count": 7,
      "mean": 3.0
    },
    {
      "product": "商品C",
      "count": 6,
      "mean": 2.667
    }
  ]
}
```

`outputs/P1/` と `outputs/P2/` は別フォルダです。`P2` を実行しても、`P1_summary.json` は上書きされません。

`used_config.py` は、実行した時点の `src/config.py` のコピーです。その後 `src/config.py` を書き換えても、こちらは変わりません。`P2` を再実行すると、その時点の内容で上書きされます。

```bash
cat outputs/P2/used_config.py
```

---

## 5. 仕様・考察・次の課題を書く

### なぜ必要か

`satisfaction` の `1` と `5` のどちらが高評価かは、JSONを見ても分かりません。列の意味、数値から言えること、次に調べることは、`docs/` に文章で残します。数値の根拠は `summary.json` に置いたままにします。

### 使い方

`docs/specification.md` を作成します。

```markdown
# データ仕様

## 対象ファイル

- パス: `data/survey_responses.csv`
- 文字コード: UTF-8
- 件数: 20行（ヘッダーを除く）

## 列の定義

| 列名 | 意味 | このファイルでの値 |
|---|---|---|
| `respondent_id` | 回答者番号 | 1〜20 |
| `product` | 商品名 | `商品A`、`商品B`、`商品C` |
| `age` | 満年齢 | 18〜46 |
| `satisfaction` | 満足度 | 1（不満）〜 5（満足） |
```

`docs/P1_report.md` を作成します。数値は `outputs/P1/P1_summary.json` と揃えます。

```markdown
# 解析レポート P1：年齢と満足度

## 条件

- 目的: 年齢で満足度に差があるかを見る
- スクリプト: `analyses/P1_age_analysis.py`
- 境界: `AGE_THRESHOLD = 30`（`P1_summary.json` の `parameters`）
- 出力: `outputs/P1/P1_summary.json`、`outputs/P1/age_satisfaction.png`

## 結果

- 全体（20件）の平均満足度: 3.0
- 30歳以上（10件）: 3.8
- 30歳未満（10件）: 2.2

## 考察

30歳以上の平均は、30歳未満より1.6高い。散布図（`age_satisfaction.png`）でも、年齢が高い側に満足度4〜5の点が寄りやすい。

この差が商品の内訳によるものかは、P1だけでは分からない。商品ごとの平均は、P2の `P2_summary.json` と合わせて見る。
```

`docs/issues.md` を作成し、調べることの一覧にします。終わったものと、まだのものを分けて書きます。

```markdown
# 調べること

- [Issue-1] 完了: 年齢30歳で満足度を比べる（P1）
- [Issue-2] 完了: 商品ごとの平均満足度を比べる（P2）
- [Issue-3] 未着手: 商品ごとに、回答者の年齢が偏っていないか確認する（P3）
```

`survey_project` 直下に `README.md` を作成し、フォルダの役割と実行コマンドを書きます。

````markdown
# アンケート満足度の集計（survey_project）

## フォルダ

- `data/`: 生データ（編集しない）
- `src/`: パスと境界条件（`config.py`）
- `analyses/`: 解析IDごとのスクリプト
- `outputs/`: 解析IDごとのCSV、PNG、`summary.json`
- `docs/`: データ仕様、考察、次に調べること

## 実行方法

`survey_project` で実行する。

```bash
python analyses/P1_age_analysis.py
python analyses/P2_product_analysis.py
```
````

`cat docs/P1_report.md` で、保存した文章を確認できます。P2の考察も、P1と同じ見出し（条件・結果・考察）で書けます。

---

## 6. まとめ

今回のゼミでは、Part 4のフォルダの中に、設定と結果と考察を残しました。

| 学んだこと | 役割 | 今回作ったもの |
|---|---|---|
| 設定の分離 | パスと境界条件を1か所で変える | `src/config.py` |
| 実行記録 | 条件と結果を結果ファイルと一緒に残す | `outputs/P1/P1_summary.json` |
| 控え | 今のスクリプトを残して、古い版を移す | `cp`、`mv`、`analyses/archive/` |
| 解析ID | 問いごとにコードと出力を分ける | `P2`、`outputs/P2/` |
| 実行時のコピー | 結果を出したプログラムと設定を残す | `used_script.py`、`used_config.py` |
| 文章 | 列の意味、考察、次に調べることを残す | `docs/specification.md`、`docs/P1_report.md`、`docs/issues.md` |

結果を見返すときは、次の順でファイルを開きます。

- 元データは `data/`
- 今の境界条件は `src/config.py`
- その実行での条件と数値は `outputs/P1/P1_summary.json` の `parameters` と `results`
- 数値から読んだことは `docs/P1_report.md`

---

## ゼミ後の課題

解析ID `P3` を追加し、商品ごとに「平均年齢」と「満足度が基準以上の人数」を、`P3_summary.json` に残してください。

1. `src/config.py` に `PASS_SATISFACTION = 4` を追加する。4以上を、人数を数える基準にします。
2. `survey_project` で次を実行し、P2のスクリプトを複製する。

```bash
cp analyses/P2_product_analysis.py analyses/P3_product_age.py
```

3. `P3_product_age.py` の `analysis_id` を `"P3"` に変える。商品ごとの繰り返しの中で、平均年齢と、満足度が `config.PASS_SATISFACTION` 以上の人数を求める。CSV、棒グラフ、`P3_summary.json`、実行時のコピーが `outputs/P3/` に出るようにする。
4. `P3_summary.json` の数値を使って `docs/P3_report.md` を書く。見出しは P1 のレポートと同じく、条件・結果・考察とする。
5. `docs/issues.md` の `[Issue-3]` を「完了」に変え、P3でまだ分からなかったことを `[Issue-4]` として1つ足す。

人数は、Part 3と同じ絞り込みで数えられます。

```python
high = part[part["satisfaction"] >= config.PASS_SATISFACTION]
```

<details>
<summary>確認用の数値（クリックして確認）</summary>

| 商品 | 平均年齢 | 満足度4以上の人数 |
|---|---|---|
| 商品A | 34.571 | 2 |
| 商品B | 32.429 | 2 |
| 商品C | 28.833 | 2 |

</details>
