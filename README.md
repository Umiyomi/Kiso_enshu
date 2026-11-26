# 基礎演習・データサイエンス入門

## このページについて

このページは、基礎演習・データサイエンス入門で使用する教材集です。

Python の基本（変数・リスト・辞書、条件分岐・繰り返し・関数）から、CSV の読み込みと可視化（`pandas`・`matplotlib`）、ターミナルでのフォルダ分けと結果の記録までを学びます。いずれも [Google Colab](https://colab.research.google.com/) 上で進めます。Part 1〜3 はコードセル、Part 4〜5 は Colab のターミナルを使います。テキストは本ページの[docs](./docs/)を見ながら進めてください。

## 流れ

- Python入門 (1~2週間) — Part 1・2
- データ処理・可視化入門 (1~2週間) — Part 3
- CLI入門 (1週間) — Part 4
- 結果の記録 (1週間) — Part 5

## 教材

- 本リポジトリ（`docs/`、`data/`）
- [paiza Python新入門編](https://paiza.jp/works/python/new-primer)
- [paiza Pythonデータ分析入門編](https://paiza.jp/works/python-data-analysis/primer)
- [paiza Linux シェルコマンド入門編](https://paiza.jp/works/shellcommand/primer)（Part 4 の発展、全チャプター）
- Google Colab

---

## 資料

### Google Colab

プログラムを作成・実行するサイトです。  
https://colab.research.google.com/

### テキスト（基礎演習）

GitHub で開きます。Part 1〜3 は表示されているコードを Colab のセルにコピーして実行し、Part 4〜5 は Colab のターミナルでコマンドを実行します。

| ファイル | 内容 |
| --- | --- |
| [`part_1.md`](./docs/part_1.md) | **Part 1** — 変数・`list`・`dict`。Colab で動かしながら、アンケート調査のデータの扱い方の基本を学びます。 |
| [`part_2.md`](./docs/part_2.md) | **Part 2** — `if`・`for`・関数（`def`）。条件分岐・繰り返し・関数の定義と呼び出しを学び、Part 1 の内容と組み合わせて使います。 |
| [`part_3.md`](./docs/part_3.md) | **Part 3** — `pandas`・`matplotlib`。CSV を DataFrame として読み、絞り込み・並べ替えのあと、散布図で2つの数値の関係を確認します。 |
| [`part_4.md`](./docs/part_4.md) | **Part 4** — CLI。Colab のターミナルで `survey_project` のフォルダを作り、4つのデータそれぞれの散布図を、データごとのフォルダに保存します。 |
| [`part_5.md`](./docs/part_5.md) | **Part 5** — 設定を `src/config.json` にまとめ、結果を `outputs/runs.csv` に記録し、出力の数を点検します。仕様と課題を `docs/` に書きます。 |
| [`introduction.md`](./docs/introduction.md) | **導入** — 研究のためにプログラミングを学ぶ意義と、「プログラミング」と「コーディング」の違い。 |
| [`example.md`](./docs/example.md) | **例** — 研究データ解析パイプラインの全体像（現場の記録から解析・成果物までの流れ）。 |
| [`survey_responses.csv`](./data/survey_responses.csv) ほか3つのCSV | Part 3〜5 で使うモックデータ。アンケート、店舗の売上、漁獲、学習時間と得点の4種類です。 |
| [`data.zip`](./data/data.zip) | Part 4〜5 用に、4つのCSVをまとめたzip。Colab の `/content` にアップロードして `unzip` します。 |

---

## Google Colab について

Google Colab はWebブラウザ上で動作するPython実行環境です(以下、Colab)。   
**Colab においてプログラムを記述、実行、保存するページを`ノートブック` と呼びます。**  
ノートブックは作成すると自動でGoogleドライブに保存されます。  
ノートブックは`*.ipynb`という拡張子で保存されます。  
(*部分にはファイル名、デフォルトではUntitled0.ipynb)

### データの置き方

**Colab はパソコン内に保存されたローカルデータに直接アクセスすることはできません。**  
使うファイルは、ノートブックから見える場所へ置く必要があります。

Part 3 では、まず次の方法で [`data/survey_responses.csv`](./data/survey_responses.csv) を置きます。

1. GitHub から `survey_responses.csv` をダウンロードする
2. Colab 左のフォルダアイコンから、そのファイルをアップロードする
3. ファイル名は `survey_responses.csv` のままにする

ランタイムを再接続すると、この方法で置いたファイルは消えます。その場合は再度アップロードしてください。

Google Drive 上のファイルを使う場合は、次の「ドライブへの接続」を行います。

### ドライブへの接続

ColabとDriveの紐付けは`ノートブック`毎に行われます。  
ノートブックを開くと、画面左側に`フォルダ`のアイコン🗂️があります。  
フォルダアイコンをクリックすると、`ドライブをマウント`というボタンがあります。  
ボタンをクリックすると`Googleドライブをマウントするには、このセルを実行してください`という指示が出るので実行する。  
実行すると、ノートブックにGoogleドライブが紐付けられます。  
**この作業はノートブックを作成したり開き直したりする度に実行する必要があります。**

### 手順まとめ

- ログインする
- `ノートブックを新規作成` ボタンをクリック
- ノートブック画面上部の`Untitled0.ipynb`と書かれた所でファイル名を変更
- Part 3 では、左のフォルダから `survey_responses.csv` をアップロードする（Drive 上のファイルを使う場合は`ドライブをマウント`する）
- Part 4以降は、画面左下の`ターミナル`を開き、コマンドを入力する（ランタイムに接続してから開く）
- 二回目以降のログイン時は`ノートブックを開く`画面で過去に作成した`ノートブック.ipynb`を開く
  ("最近"に無いときは"Googleドライブ"の方を見てみる)

---

## リポジトリ構成

```text
.
├── .gitignore
├── README.md
├── data
│   ├── data.zip               # Part 4〜5 用に4つのCSVをまとめたzip
│   ├── survey_responses.csv   # アンケート（Part 3〜5）
│   ├── shop_sales.csv         # 店舗の日次売上（Part 4〜5）
│   ├── fishing_catch.csv      # 出漁ごとの漁獲（Part 4〜5）
│   └── study_scores.csv       # 学習時間と得点（Part 4〜5）
└── docs                       # 基礎演習テキスト
    ├── introduction.md
    ├── example.md
    ├── part_1.md
    ├── part_2.md
    ├── part_3.md
    ├── part_4.md
    └── part_5.md
```
