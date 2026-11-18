# Part 5 結果の記録

# Pythonプログラミング入門：設定・メタデータ・考察をファイルに残す

**設定、実行条件、計算結果、考察を、決まったファイルに残す**流れを学びます。

Part 4では、`survey_project` のフォルダを作り、`analyses/age_analysis.py` を実行して、散布図を `outputs/age_analysis/age_analysis.png` に保存しました。この画像には分析結果が視覚的に表示されていますが、どの条件で生成されたか、どのCSVを読んだかといったメタデータは含まれていません。今回は、その情報を結果と一緒にJSON形式で記録し、複数の条件での分析結果を管理します。

## 今回の目標

- パスと境界条件を **`src/config.py` にまとめ**、解析スクリプトから読み込める。
- 散布図とともに、実行条件と結果を書いた **複数の `summary.json`** を `outputs/age_analysis/` に保存できる。
- **`cp` / `mv`** でスクリプトの控えを `archive/` に置き、複数の解析スクリプトを管理できる。
- 実行したスクリプトと `config.py` を、出力フォルダへコピーして残せる。
- 仕様（`docs/specification.md`）、考察（`docs/age_analysis_report.md` など）、次に調べること（`docs/issues.md`）を書ける。

### 進め方

Part 4と同じノートブックで、Colabのターミナルを開きます。`cd /content/survey_project` を実行し、`pwd` の表示が `/content/survey_project` になることを確認してから始めてください。ランタイムを再接続してフォルダが消えている場合は、Part 4の手順でもう一度作ってください。

ファイルの作成と編集は、Part 4と同じく、左のフォルダ一覧から行います。`matplotlib` は Colab に入っているため、インストールは不要です。

---

## 1. 設定を `src/config.py` にまとめる

### なぜ必要か

Part 4のスクリプトには、`"data/survey_responses.csv"` と `30` が直接書いてあります。複数の基準で試すたびに、スクリプトの中の数値を探す必要があります。Part 1で、数値を変数にまとめたのと同じ理由です。

入出力のパスと複数の境界条件は `src/config.py` に書き、解析スクリプトから読み込みます。

### 使い方

ターミナルでファイルを作成します。まず、`src/` ディレクトリにファイルを作ります。

```bash
touch src/config.py
```

ファイルが作られたことを確認します。

```bash
ls src/
```

次に、以下のコードをコピーして、テキストエディタで `src/config.py` に貼り付けます。左のフォルダ一覧から `config.py` をダブルクリックして開き、下のコード全体をコピーして貼り付けてください。複数のthresholdを**リスト**で管理します。

```python
RAW_SURVEY_CSV = "data/survey_responses.csv"
OUTPUT_ROOT = "outputs"

AGE_THRESHOLDS = [30, 35, 40]
```

`AGE_THRESHOLDS` は、試したい年齢の基準をリストで書きます。スクリプトはこのリストをループして、複数の条件で自動的に分析を行います。

ファイルを保存したことを確認します。

```bash
cat src/config.py
```

次に、`analyses/age_analysis.py` を、設定を読んで複数のthresholdで実行する次の内容に書き換えます。ファイルを作成してからコードを貼り付けます。

```bash
touch analyses/age_analysis.py
```

テキストエディタで `analyses/age_analysis.py` を開き、以下のコード全体をコピーして貼り付けてください。

```python
import json
import os
import sys
import pandas as pd
import matplotlib.pyplot as plt

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

out_dir = f"{config.OUTPUT_ROOT}/age_analysis"
os.makedirs(out_dir, exist_ok=True)

df = pd.read_csv(config.RAW_SURVEY_CSV)

for threshold in config.AGE_THRESHOLDS:
    df_older = df[df["age"] >= threshold]
    df_younger = df[df["age"] < threshold]
    
    print(f"threshold {threshold}")
    print("count_all", len(df))
    print("mean_all", df["satisfaction"].mean())
    print("count_older", len(df_older))
    print("mean_older", df_older["satisfaction"].mean())
    print("count_younger", len(df_younger))
    print("mean_younger", df_younger["satisfaction"].mean())
    print()
    
    # 散布図を描き、PNG名にthresholdをsuffixとして付ける
    plt.figure(figsize=(8, 6))
    plt.scatter(df_older["age"], df_older["satisfaction"], color="red", label=f"age >= {threshold}", alpha=0.7)
    plt.scatter(df_younger["age"], df_younger["satisfaction"], color="blue", label=f"age < {threshold}", alpha=0.7)
    plt.xlabel("Age")
    plt.ylabel("Satisfaction")
    plt.title(f"Age and Satisfaction (threshold: {threshold})")
    plt.legend()
    plt.grid(True, alpha=0.3)
    plt.savefig(f"{out_dir}/age_analysis_threshold_{threshold}.png", dpi=100, bbox_inches="tight")
    plt.close()
```

スクリプトは `analyses/` の中にあります。`sys.path.append(...)` は、1つ上の `survey_project` を読み込み先に加え、`src/config.py` を `import` できるようにします。`for` ループで、`config.AGE_THRESHOLDS` の各値に対して散布図を生成します。PNG名は `age_analysis_threshold_30.png`、`age_analysis_threshold_35.png`、`age_analysis_threshold_40.png` のように、thresholdをsuffixとして自動的に付けます。

```bash
pwd
python analyses/age_analysis.py
ls outputs/age_analysis
```

実行結果：

```text
/content/survey_project
age_analysis_threshold_30.png  age_analysis_threshold_35.png  age_analysis_threshold_40.png
threshold 30
count_all 20
mean_all 3.0
count_older 10
mean_older 3.8
count_younger 10
mean_younger 2.2

threshold 35
count_all 20
mean_all 3.0
count_older 7
mean_older 3.857
count_younger 13
mean_younger 2.462

threshold 40
count_all 20
mean_all 3.0
count_older 7
mean_older 4.0
count_younger 13
mean_younger 2.462
```

3つのPNG画像が、それぞれ異なるthresholdで生成されました。ファイル名にthresholdが含まれているため、後から見直すときに、どの条件での結果か判断できます。

---

## 2. 実行条件と結果を JSON に残す

### なぜ必要か

複数のthresholdで実行したとき、画像ファイルだけでは、「どの画像がどのthresholdで生成されたか」という条件が分かりにくくなります。

各thresholdの実行条件と結果を、JSON形式でファイルとして保存します。JSONは、Part 1の辞書と同じく、キーと値の組で書けるテキストです。

### 使い方

セクション1のスクリプトを拡張し、複数のthresholdで実行されるたびにJSON結果を記録するようにします。`analyses/age_analysis.py` を更新して、以下のコードで置き換えます。テキストエディタで開いて、下のコード全体をコピーして貼り付けてください。

```python
import json
import os
import sys
import pandas as pd
import matplotlib.pyplot as plt

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

out_dir = f"{config.OUTPUT_ROOT}/age_analysis"
os.makedirs(out_dir, exist_ok=True)

df = pd.read_csv(config.RAW_SURVEY_CSV)

for threshold in config.AGE_THRESHOLDS:
    df_older = df[df["age"] >= threshold]
    df_younger = df[df["age"] < threshold]
    
    mean_all = float(df["satisfaction"].mean())
    mean_older = float(df_older["satisfaction"].mean())
    mean_younger = float(df_younger["satisfaction"].mean())
    
    print(f"threshold {threshold}")
    print("count_all", len(df))
    print("mean_all", mean_all)
    print("count_older", len(df_older))
    print("mean_older", round(mean_older, 3))
    print("count_younger", len(df_younger))
    print("mean_younger", round(mean_younger, 3))
    print()
    
    # 散布図を描き、PNG名にthresholdをsuffixとして付ける
    plt.figure(figsize=(8, 6))
    plt.scatter(df_older["age"], df_older["satisfaction"], color="red", label=f"age >= {threshold}", alpha=0.7)
    plt.scatter(df_younger["age"], df_younger["satisfaction"], color="blue", label=f"age < {threshold}", alpha=0.7)
    plt.xlabel("Age")
    plt.ylabel("Satisfaction")
    plt.title(f"Age and Satisfaction (threshold: {threshold})")
    plt.legend()
    plt.grid(True, alpha=0.3)
    plt.savefig(f"{out_dir}/age_analysis_threshold_{threshold}.png", dpi=100, bbox_inches="tight")
    plt.close()
    
    # JSON形式で実行条件と結果を記録
    summary = {
        "analysis": "age_analysis",
        "input_file": config.RAW_SURVEY_CSV,
        "parameters": {
            "age_threshold": threshold,
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
    
    # JSONファイル名にもthresholdをsuffixとして付ける
    json_path = f"{out_dir}/age_analysis_threshold_{threshold}.json"
    with open(json_path, "w", encoding="utf-8") as f:
        json.dump(summary, f, indent=2, ensure_ascii=False)
```

ループの中で、各thresholdについて以下が自動的に行われます。

| 処理 | ファイル名のパターン |
|---|---|
| 散布図を描いてPNGで保存 | `age_analysis_threshold_30.png` |
| JSON形式で条件と結果を記録 | `age_analysis_threshold_30.json` |

`json.dump` は辞書をJSONとして書き出します。`indent=2` は読みやすいように字下げし、`ensure_ascii=False` は日本語を `\u...` にせずそのまま保存します。

```bash
python analyses/age_analysis.py
ls outputs/age_analysis
cat outputs/age_analysis/age_analysis_threshold_30.json
```

実行結果（ls の部分）：

```text
outputs/age_analysis
age_analysis_threshold_30.png   age_analysis_threshold_30.json
age_analysis_threshold_35.png   age_analysis_threshold_35.json
age_analysis_threshold_40.png   age_analysis_threshold_40.json
```

`age_analysis_threshold_30.json` の内容：

```json
{
  "analysis": "age_analysis",
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

複数のthresholdで実行するたびに、各JSONには対応するthresholdの条件と結果が記録されます。`parameters` が実行時の条件、`results` が計算結果です。PNG と JSON の名前が対応しているため、画像を見るときに、対応する JSON で条件を確認できます。

### 演習1：複数の条件で結果を比較する

1. `python analyses/age_analysis.py` を実行する。
2. `ls outputs/age_analysis` で、複数のPNG と JSON ファイルが生成されたことを確認する。
3. `cat outputs/age_analysis/age_analysis_threshold_40.json` で、threshold=40の結果を確認する。

確認用の数値（threshold=40の場合）：

```json
"age_threshold": 40
"older_count": 7
"younger_count": 13
"mean_satisfaction_older": 4.0
"mean_satisfaction_younger": 2.462
```

---

## 3. スクリプトの控えを残す

### なぜ必要か

スクリプトを書き直す前に、今の版を残しておきたいことがあります。`age_analysis_old.py` や `age_analysis_final.py` を `analyses/` に増やしていくと、どれを実行すればよいか分かりにくくなります。

今のスクリプトは `analyses/` に残し、控えは `analyses/archive/` へ移します。

### 使い方

| コマンド | 役割 |
|---|---|
| `cp コピー元 コピー先` | ファイルを複製する。フォルダごと複製するときは `cp -r` |
| `mv 移動元 移動先` | ファイルを移動する。名前の変更にも使う |

```bash
pwd
mkdir -p analyses/archive
cp analyses/age_analysis.py analyses/archive/age_analysis_v1.py
ls analyses
ls analyses/archive
```

実行結果：

```text
/content/survey_project
age_analysis.py  archive
age_analysis_v1.py
```

`cp` のあと、元の `analyses/age_analysis.py` はそのまま残ります。

使わなくなった版を片づけるときも、削除せず `archive/` へ `mv` します。`data/` のCSVと、今実行しているスクリプトは移動しません。

---

## 4. `product_analysis` を追加する

### なぜ必要か

`age_analysis` は「年齢と満足度」です。次に「商品ごとの満足度」を調べるとき、`age_analysis.py` に処理を足すと、1つのファイルが2つの異なる問いを担うことになり、メンテナンスが困難になります。

問いごとに解析スクリプトを分け、解析内容を反映したファイル・フォルダ名を使用します。

```text
analyses/age_analysis.py         outputs/age_analysis/age_analysis_threshold_*.json
analyses/product_analysis.py     outputs/product_analysis/product_analysis_*.json
```

さらに、実行の瞬間のスクリプトと `config.py` を出力フォルダへコピーします。あとから設定を書き換えても、そのフォルダを見れば、どのプログラムでその結果が出たかが分かります。

### 使い方

ターミナルで `analyses/product_analysis.py` を作成します。

```bash
touch analyses/product_analysis.py
```

テキストエディタで開き、以下のコードをコピーして貼り付けます。商品ごとの件数と平均は、Part 3の絞り込みを商品名の数だけ繰り返して求めます。

```python
import json
import os
import shutil
import sys
import pandas as pd
import matplotlib.pyplot as plt

sys.path.append(os.path.join(os.path.dirname(__file__), ".."))
from src import config

out_dir = f"{config.OUTPUT_ROOT}/product_analysis"
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

pd.DataFrame(rows).to_csv(f"{out_dir}/product_analysis.csv", index=False)

plt.bar(names, means)
plt.xlabel("Product")
plt.ylabel("Mean Satisfaction")
plt.title("Product Analysis: Mean Satisfaction by Product")
plt.savefig(f"{out_dir}/product_analysis_bar.png")
plt.close()

summary = {
    "analysis": "product_analysis",
    "input_file": config.RAW_SURVEY_CSV,
    "results_by_product": rows,
}

with open(f"{out_dir}/product_analysis_summary.json", "w", encoding="utf-8") as f:
    json.dump(summary, f, indent=2, ensure_ascii=False)

print(out_dir)
```

`__file__` は、今実行しているスクリプト自身のパスです。`shutil.copy` は、`cp` と同じ複製を、プログラムの中から行います。`plt.bar` は棒グラフです。軸の日本語が環境によって文字化けすることがあります。数値はCSVとJSONで確認できます。

```bash
python analyses/product_analysis.py
ls outputs/product_analysis
cat outputs/product_analysis/product_analysis_summary.json
```

実行結果：

```text
outputs/product_analysis
product_analysis_bar.png  product_analysis.csv  product_analysis_summary.json  used_config.py  used_script.py
{
  "analysis": "product_analysis",
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

`outputs/age_analysis/` と `outputs/product_analysis/` は別フォルダです。異なる解析を実行しても、互いのJSONファイルは上書きされません。

`used_config.py` は、実行した時点の `src/config.py` のコピーです。その後 `src/config.py` を書き換えても、こちらは変わりません。同じ解析を再実行すると、その時点の内容で上書きされます。

```bash
cat outputs/product_analysis/used_config.py
```

---

## 5. 仕様・考察・次の課題を書く

### なぜ必要か

`satisfaction` の `1` と `5` のどちらが高評価かは、JSONを見ても分かりません。列の意味、数値から言えること、次に調べることは、`docs/` に文章で残します。数値の根拠は `summary.json` に置いたままにします。

### 使い方

ターミナルでファイルを作成します。

```bash
touch docs/specification.md
touch docs/age_analysis_report.md
touch docs/product_analysis_report.md
touch docs/issues.md
```

テキストエディタで各ファイルを開き、以下のコードをコピーして貼り付けます。

#### docs/specification.md

左のフォルダ一覧から `specification.md` をダブルクリックして開き、以下をコピーして貼り付けてください。

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

#### docs/age_analysis_report.md

左のフォルダ一覧から `age_analysis_report.md` をダブルクリックして開き、以下をコピーして貼り付けてください。複数のthresholdで実行した結果について、各JSONファイルから数値を取得して書きます。

```markdown
# 解析レポート：年齢と満足度（age_analysis）

## 条件

- 目的: 異なる年齢基準で満足度に差があるかを見る
- スクリプト: `analyses/age_analysis.py`
- 試した基準: `AGE_THRESHOLDS = [30, 35, 40]`（`src/config.py` から）
- 出力: `outputs/age_analysis/age_analysis_threshold_*.png`、`outputs/age_analysis/age_analysis_threshold_*.json`

## 結果

全体（20件）の平均満足度: 3.0

| 年齢基準 | 基準以上の件数・平均 | 基準未満の件数・平均 |
|---|---|---|
| 30歳 | 10件（3.8） | 10件（2.2） |
| 35歳 | 7件（3.857） | 13件（2.462） |
| 40歳 | 7件（4.0） | 13件（2.462） |

## 考察

いずれの基準でも、年齢が高い側の平均満足度が低い側より高い傾向がある。基準を上げるほど、高い側の平均が上がる（30歳基準:3.8 → 40歳基準:4.0）。

この差が商品の内訳によるものかは、age_analysisだけでは分からない。商品ごとの平均は、product_analysisの結果と合わせて見る。
```

#### docs/product_analysis_report.md

左のフォルダ一覧から `product_analysis_report.md` をダブルクリックして開き、以下をコピーして貼り付けてください。

```markdown
# 解析レポート：商品ごとの満足度（product_analysis）

## 条件

- 目的: 商品ごとの満足度の平均を比べる
- スクリプト: `analyses/product_analysis.py`
- 出力: `outputs/product_analysis/product_analysis_bar.png`、`outputs/product_analysis/product_analysis_summary.json`

## 結果

全体（20件）の平均満足度: 3.0

| 商品 | 件数 | 平均満足度 |
|---|---|---|
| 商品A | 7 | 3.286 |
| 商品B | 7 | 3.0 |
| 商品C | 6 | 2.667 |

## 考察

商品Aの平均満足度が最も高く、商品Cが最も低い。ただし、各商品の回答者の年齢分布が異なる可能性があるため、age_analysisの結果と合わせて解釈する必要がある。
```

#### docs/issues.md

左のフォルダ一覧から `issues.md` をダブルクリックして開き、以下をコピーして貼り付けてください。調べることの一覧にします。終わったものと、まだのものを分けて書きます。

```markdown
# 調べること

- [Issue-1] 完了: 年齢基準を変えて満足度を比べる（age_analysis）
- [Issue-2] 完了: 商品ごとの平均満足度を比べる（product_analysis）
- [Issue-3] 未着手: 商品ごとに、回答者の年齢分布を確認する（age_product_analysis）
```

#### README.md

ターミナルで `README.md` を作成します。

```bash
touch README.md
```

左のフォルダ一覧から `README.md` をダブルクリックして開き、以下をコピーして貼り付けてください。フォルダの役割と実行コマンドを書きます。

````markdown
# アンケート満足度の集計（survey_project）

## フォルダ

- `data/`: 生データ（編集しない）
- `src/`: パスと境界条件（`config.py`）
- `analyses/`: 解析スクリプト
- `outputs/`: 解析結果（PNG、JSON、CSV）
- `docs/`: データ仕様、考察、次に調べること

## 実行方法

`survey_project` で実行する。

```bash
python analyses/age_analysis.py
python analyses/product_analysis.py
```
````

各レポートは `docs/age_analysis_report.md` や `docs/product_analysis_report.md` で確認できます。すべてのレポートは同じ見出し（条件・結果・考察）で書かれ、一貫性があります。

---

## 6. まとめ

今回のゼミでは、Part 4のフォルダの中に、設定と結果と考察を残しました。複数の条件で自動的に分析を行い、各条件の結果を対応するファイルに記録することで、比較と検証が容易になります。

| 学んだこと | 役割 | 今回作ったもの |
|---|---|---|
| 設定の分離 | パスと複数の境界条件を1か所で管理 | `src/config.py`（`AGE_THRESHOLDS = [30, 35, 40]`） |
| ループによる自動処理 | 複数条件で自動的に分析を繰り返す | `for threshold in config.AGE_THRESHOLDS:` |
| ファイル名の自動生成 | 条件を含めてファイル名を生成 | `age_analysis_threshold_{threshold}.png`、`age_analysis_threshold_{threshold}.json` |
| 実行記録 | 各条件での結果をJSON形式で記録 | `outputs/age_analysis/age_analysis_threshold_*.json` |
| 控え | 今のスクリプトを残して、古い版を移す | `cp`、`mv`、`analyses/archive/` |
| 解析の分離 | 問いごとにコードと出力を分ける | `age_analysis/`、`product_analysis/` |
| 実行時のコピー | 結果を出したプログラムと設定を残す | `used_script.py`、`used_config.py` |
| 文章 | 列の意味、考察、次に調べることを残す | `docs/specification.md`、`docs/age_analysis_report.md`、`docs/product_analysis_report.md`、`docs/issues.md` |

結果を見返すときは、次の順でファイルを開きます。

- 元データは `data/`
- 試した条件は `src/config.py` の `AGE_THRESHOLDS`
- 各thresholdでの条件と数値は `outputs/age_analysis/age_analysis_threshold_*.json` の `parameters` と `results`
- 複数条件の比較結果は `docs/` のレポート

---

## ゼミ後の課題

`age_product_analysis` を追加し、商品ごとに「平均年齢」と「満足度が基準以上の人数」を、`age_product_analysis_summary.json` に残してください。

1. `src/config.py` に `PASS_SATISFACTION = 4` を追加する。4以上を、人数を数える基準にします。
2. `survey_project` で次を実行し、product_analysisのスクリプトを複製する。

```bash
cp analyses/product_analysis.py analyses/age_product_analysis.py
```

3. テキストエディタで `age_product_analysis.py` を開き、商品ごとの繰り返しの中で、平均年齢と、満足度が `config.PASS_SATISFACTION` 以上の人数を求める。CSV、棒グラフ、`age_product_analysis_summary.json`、実行時のコピーが `outputs/age_product_analysis/` に出るようにする。
4. ターミナルで `docs/age_product_analysis_report.md` を作成する。
   
   ```bash
   touch docs/age_product_analysis_report.md
   ```
   
   テキストエディタで開き、`age_product_analysis_summary.json` の数値を使ってレポートを書く。見出しは他のレポートと同じく、条件・結果・考察とする。
5. ターミナルで `docs/issues.md` を開き、`[Issue-3]` を「完了」に変え、まだ分からなかったことを `[Issue-4]` として1つ足す。

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
