# CTDデータから鉛直プロファイルとコンターを作図し海洋観測結果を可視化する
---
## データの概要
- 1日目、2日目、マガキ調査の3種類のサンプリングを実施
- 1日目、2日目は各3ファイルのcsv
- マガキ調査は6ファイルのcsv
- 1ファイルが1サンプリングステーションに対応

```python
PROJECT_ROOT = "/content/drive/MyDrive/kankyogaku2026"

WAKASA_DATA_DIR = f"{PROJECT_ROOT}/data/wakasa"
MAGAKI_DATA_DIR = f"{PROJECT_ROOT}/data/magaki"
OUTPUT_DIR = f"{PROJECT_ROOT}/output"

# 20260624
ST1_LATITUDE = 35 + 30.496 / 60
ST2_LATITUDE = 35 + 31.161 / 60
ST3_LATITUDE = 35 + 31.848 / 60
ST4_LATITUDE = 35 + 32.535 / 60

# wakasa20260624調査データ
WAKASA_0624_ST1_PATH = f"{WAKASA_DATA_DIR}/20260624/202606241429_ASTD152-ALC-R02_0184_142943.Csv"
WAKASA_0624_ST2_PATH = f"{WAKASA_DATA_DIR}/20260624/202606241417_ASTD152-ALC-R02_0184_141756.Csv"
WAKASA_0624_ST3_PATH = f"{WAKASA_DATA_DIR}/20260624/202606241356_ASTD152-ALC-R02_0184_135631.Csv"
WAKASA_0624_ST4_PATH = f"{WAKASA_DATA_DIR}/20260624/202606241340_ASTD152-ALC-R02_0184_134017.Csv"

# magaki20260621調査データ
MAGAKI_0621_ST1_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621090317_AAQ177_SNo0636.csv"
MAGAKI_0621_ST2_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621090953_AAQ177_SNo0636.csv"
MAGAKI_0621_ST3_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621092021_AAQ177_SNo0636.csv"
MAGAKI_0621_ST4_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621092708_AAQ177_SNo0636.csv"
MAGAKI_0621_ST5_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621094533_AAQ177_SNo0636.csv"
MAGAKI_0621_ST6_PATH = f"{MAGAKI_DATA_DIR}/20260621/0621095111_AAQ177_SNo0636.csv"

# magaki20260627調査データ
MAGAKI_0627_ST1_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627085508_AAQ177_SNo0636.csv"
MAGAKI_0627_ST2_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627090223_AAQ177_SNo0636.csv"
MAGAKI_0627_ST3_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627091435_AAQ177_SNo0636.csv"
MAGAKI_0627_ST4_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627092047_AAQ177_SNo0636.csv"
MAGAKI_0627_ST5_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627093537_AAQ177_SNo0636.csv"
MAGAKI_0627_ST6_PATH = f"{MAGAKI_DATA_DIR}/20260627/0627094142_AAQ177_SNo0636.csv"

WAKASA_0624_DATA_DICT = {
    "St1": (WAKASA_0624_ST1_PATH, ST1_LATITUDE),
    "St2": (WAKASA_0624_ST2_PATH, ST2_LATITUDE),
    "St3": (WAKASA_0624_ST3_PATH, ST3_LATITUDE),
    "St4": (WAKASA_0624_ST4_PATH, ST4_LATITUDE)
}

MAGAKI_0621_DATA_DICT = {
    "St1": (MAGAKI_0621_ST1_PATH, 6),
    "St2": (MAGAKI_0621_ST2_PATH, 5),
    "St3": (MAGAKI_0621_ST3_PATH, 4),
    "St4": (MAGAKI_0621_ST4_PATH, 3),
    "St5": (MAGAKI_0621_ST5_PATH, 2),
    "St6": (MAGAKI_0621_ST6_PATH, 1)
}

MAGAKI_0627_DATA_DICT = {
    "St1": (MAGAKI_0627_ST1_PATH, 6),
    "St2": (MAGAKI_0627_ST2_PATH, 5),
    "St3": (MAGAKI_0627_ST3_PATH, 4),
    "St4": (MAGAKI_0627_ST4_PATH, 3),
    "St5": (MAGAKI_0627_ST5_PATH, 2),
    "St6": (MAGAKI_0627_ST6_PATH, 1)
}
```

## データ様式
- csvファイル
- encoding='shift-jis'
- "[Item]"と書かれた行の1行下にヘッダ行
- カラム構成は

```
深度 [m],水温 [℃],塩分,電導度 [mS/cm],EC25 [μS/cm],密度 [kg/m3],シグマＴ,Chl-Flu. [ppb],Chl-a [μg/l],濁度 [FTU],pH,ORP [mV],DO [%],Weiss-DO [mg/l],光量子 [μmol/(m2*s)],G&G-DO [mg/l],B&K-DO [mg/l],
```

---

## スクリプト要件定義
### 概要
csvファイルを読み込み、鉛直プロファイルとコンターの作図を行う。スクリプトは別々に作成する。データの前処理部分は共通化する。csvファイルは複数存在し、その対応関係は手動で定義する。

### データの読み込み
- 使うカラムは[0, 1, 2, 8]
- ヘッダ行までスキップしてカラム名は新たに設定
```
["depth(m)", "temp(℃)", "sal", "chl-a(μg/l)"]
```


### 出力
```
/content/drive/MyDrive/kankyogaku2026/outputs
```

---

### 作図の要件
#### 共通要件
- 図は水温、塩分等、測定項目毎に分ける(temp_fig, sal_fig, chl_fig)
- depthmax, vmin, vmaxは引数化し手動で調整できるようにする(settings_dictとしてコード上部に定数としてまとめる)
- タイトル、保存先も引数化する
- figの保存名とタイトルは以下のフォーマット
```
{temp}_{day1}_{profile}
{sal}_{day2}_{contour}
```

#### 鉛直プロファイル
- y軸を水深、x軸を観測値(水温、塩分、クロロフィル)
- ステーションを色分けする(st1:red, st2:blue, st3:green, ...)
#### コンター
- y軸を水深、x軸をステーション番号


### 関数の分割
- 単一ファイルの前処理関数
- ステーションデータ結合関数
- 作図&保存の関数  
の3関数で構成、main関数にパイプラインを書く  
スクリプトは1枚の.pyで完結する

