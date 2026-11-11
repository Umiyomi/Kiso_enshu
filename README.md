# 基礎演習・データサイエンス入門

## このページについて

このページは、基礎演習・データサイエンス入門で使用する教材集です。

Python の基本（変数・リスト・辞書、条件分岐・繰り返し・関数）から、CSV の読み込みと可視化（`pandas`・`matplotlib`）までを、[Google Colab](https://colab.research.google.com/) 上でコードをコピー・実行しながら学びます。テキストは GitHub 上の Markdown を見ながら進めてください。

## 流れ

- Python入門 (1~2週間) — Part 1・2
- CLI入門 (1週間)
- データ処理・可視化入門 (1~2週間) — Part 3

## 教材

- 本リポジトリ（`docs/kiso_enshu/`、`data/`）
- [paiza Python新入門編](https://paiza.jp/works/python/new-primer)
- [paiza Pythonデータ分析入門編](https://paiza.jp/works/python-data-analysis/primer)
- Google Colab

---

## 資料

### Google Colab

プログラムを作成・実行するサイトです。  
https://colab.research.google.com/

### テキスト（基礎演習）

GitHub で開き、表示されているコードを Colab のセルにコピーして実行します。

| ファイル | 内容 |
| --- | --- |
| [`part_1.md`](./docs/kiso_enshu/part_1.md) | **Part 1** — 変数・`list`・`dict`。Colab で動かしながら、アンケート調査のデータの扱い方の基本を学びます。 |
| [`part_2.md`](./docs/kiso_enshu/part_2.md) | **Part 2** — `if`・`for`・関数（`def`）。条件分岐・繰り返し・関数の定義と呼び出しを学び、Part 1 の内容と組み合わせて使います。 |
| [`part_3.md`](./docs/kiso_enshu/part_3.md) | **Part 3** — `pandas`・`matplotlib`。CSV を DataFrame として読み、絞り込み・並べ替えのあと、散布図で2つの数値の関係を確認します。 |
| [`survey_responses.csv`](./data/survey_responses.csv) | Part 3 で使うアンケートCSV（年齢と満足度）。Colab へアップロードしてから `pd.read_csv()` で読み込みます。 |

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
- 二回目以降のログイン時は`ノートブックを開く`画面で過去に作成した`ノートブック.ipynb`を開く
  ("最近"に無いときは"Googleドライブ"の方を見てみる)

---

## リポジトリ構成

```text
.
├── README.md
├── data
│   └── survey_responses.csv   # Part 3 用のアンケートCSV
└── docs
    └── kiso_enshu             # 基礎演習テキスト
        ├── introduction.md
        ├── example.md
        ├── part_1.md
        ├── part_2.md
        └── part_3.md
```
