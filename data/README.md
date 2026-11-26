# 教材データ

基礎演習で使う CSV データです。すべて架空のモックデータで、各 20 行です。  

## ファイル一覧

| ファイル | 内容 | 使う Part |
| --- | --- | --- |
| [`survey_responses.csv`](./survey_responses.csv) | 商品に関するアンケート | Part 3〜5 |
| [`shop_sales.csv`](./shop_sales.csv) | 店舗の日次売上 | Part 4〜5 |
| [`fishing_catch.csv`](./fishing_catch.csv) | 出漁ごとの漁獲 | Part 4〜5 |
| [`study_scores.csv`](./study_scores.csv) | 学習時間と得点 | Part 4〜5 |
| [`data.zip`](./data.zip) | 上の 4 つの CSV をまとめた zip | Part 4〜5 |

`data.zip` の中身は、上の 4 つの CSV と同じです。Colab の `/content` にアップロードして `unzip` すると、`data/` フォルダに 4 つの CSV が並びます。

## 各ファイルの列

### survey_responses.csv（アンケート）

| 列名 | 意味 | 値の例・範囲 |
| --- | --- | --- |
| `respondent_id` | 回答者の番号 | 1〜20 |
| `product` | 回答した商品 | 商品A・商品B・商品C |
| `age` | 年齢（満年齢） | 18〜46 |
| `satisfaction` | 満足度 | 1（不満）〜 5（満足） |

### shop_sales.csv（店舗の売上）

| 列名 | 意味 | 値の範囲 |
| --- | --- | --- |
| `id` | 行の番号 | 1〜20 |
| `store` | 店舗 | 店舗A・店舗B・店舗C |
| `temperature` | 気温（℃） | 6〜35 |
| `sales` | 売上（千円） | 27〜101 |

### fishing_catch.csv（漁獲）

| 列名 | 意味 | 値の範囲 |
| --- | --- | --- |
| `id` | 行の番号 | 1〜20 |
| `vessel` | 船名 | 第一丸・第二丸・第三丸 |
| `water_temp` | 水温（℃） | 11〜25 |
| `catch_kg` | 漁獲量（kg） | 60〜260 |

### study_scores.csv（学習時間と得点）

| 列名 | 意味 | 値の範囲 |
| --- | --- | --- |
| `id` | 行の番号 | 1〜20 |
| `class` | クラス | A・B・C |
| `study_hours` | 学習時間（時間） | 0〜7 |
| `score` | 得点 | 35〜92 |

## 関連

- [教材の一覧](../docs/README.md)：各テキストがどのデータを使うか
- [リポジトリの README](../README.md)：データの置き方（Colab へのアップロード）
