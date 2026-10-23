# 実際のデータを読み込み、グラフを書いてみる
ここからは実際の観測データを触りながら、プログラミングでデータ処理を行うための視座を導入します

---

## プログラムにデータを渡そう
今回はGoogle Colabでプログラミングを行うため、Google Drive上のフォルダにデータをアップロードしました。  
プログラミングではフォルダのことを**ディレクトリ**と呼びます。
データがどこにあるかは**ディレクトリパス**で表現します。
```Python
PROJECT_ROOT = "/content/drive/MyDrive/kankyogaku2026"

wakasa_st1 = f"{PROJECT_ROOT}/data/wakasa/20260624/202606241429_ASTD152-ALC-R02_0184_142943.Csv"
```
プログラムにデータの在処を伝えるためには、ディレクトリパスを使います。
ディレクトリパスを`wakasa_st1`変数に代入しておきましょう。

---

## Pythonの表データ処理機能"Pandas"
Pythonで表データを処理するために**Pandas**という機能があります。    
**Pandas**はコード内で`import`して使用します。

```Python
# Pandasのテストコード

# pandasをimport
import pandas as pd

# df は DataFrameの略
test_df = pd.DataFrame({
    "depth": [0, 1, 2],
    "temp": [26, 25, 24],
    "sal": [32, 33, 34],
    "chl": [1, 2, 3]
})

print(test_data)

```
Pandasは`DataFrame`という表データを扱うことができます。  
また、 **読み込んだCSVファイルをDataFrameに変換**することができます。  
ここで、先ほど`wakasa_st1`変数に代入したデータを読み込んでみます。  


```Python
# Pandasをimport
import pandas as pd

# データのパスを定義
PROJECT_ROOT = "/content/drive/MyDrive/kankyogaku2026"
wakasa_st1 = f"{PROJECT_ROOT}/data/wakasa/20260624/202606241429_ASTD152-ALC-R02_0184_142943.Csv"

# データ読み込みのメイン処理
def load_csv_data():
    
    skiprows = get_skip_rows(wakasa_st1) # <-ここでサブ処理を呼び出している
    # Pandasのread_csv関数にデータパスを渡している
    df = pd.read_csv(wakasa_st1, skiprows=skiprows, encoding='shift-jis')
    print(df.head(10))


# csvファイルの不要な行をskipするためのサブ処理
def get_skip_rows(csv_file_path):
    skiprows = None
    with open(csv_file_path, 'r', encoding='shift-jis') as f:
        for num, line in enumerate(f):
            if "[Item]" in line:
                skiprows = num

    if skiprows is None:
        raise ValueError(f"[Item] 行が見つかりません: {csv_file_path}")
    
    return skiprows + 1

# この行でメイン処理が実行されている
load_csv_data()
```
Pandasの`read_csv`関数は**行と列の数が揃ったCSVデータを与えないとエラーを起こしてしまいます。**  
今回のデータはファイルの上部に非CSVデータ行が存在するため、`get_skip_rows`関数を組み合わせてエラーを回避しました。  
このように、**観測データを読み込む際には不要行のスキップなどの前処理が必要な場合があります。**  
どのような前処理が必要かは実際のデータの性質や目的によって異なるので、一般化しにくいです。  
そのため、AI時代においても人間のデータ観察力・目的意識が試されるところです。  

---

## Pythonの作図機能"Matplotlib"
ここからは先ほど読み込んだデータをグラフとして可視化していきます。
Pythonでグラフを描画するために**Matplotlib**という機能があります。
**Matplotlib**もPandasと同様に`import`して使います。
まずは**x軸, y軸を持つ散布図(scatter graph)にテストデータをプロット**してみましょう。

```Python
# 機能をimport
import pandas as pd
import matplotlib.pyplot as plt

# pandasのDataFrameで作図用のテストデータを定義
test_df = pd.DataFrame({
    "depth": [0, 1, 2],
    "temp": [26, 25, 24],
    "sal": [32, 33, 34],
    "chl": [1, 2, 3]
})

# x軸を温度(temp)とする
x = test_df["temp"]
# y軸を水深(depth)とする
y = test_df["depth"]

# matplotlibのscatter関数にx,yを与える
plt.scatter(x, y)
# show関数で表示
plt.show()

```
**x軸に観測項目、y軸に水深を取った散布図**、これは今回の実習で作る**鉛直プロファイル図**ですね。

では、`wakasa_st1`データを使って実際に鉛直プロファイル図を描いてみましょう。
まずは、`Pandas`の節で作った**データ読み込み関数**を再利用してデータを読み込みます。
```Python
# Pandasを用いたデータ読み込み関数
import pandas as pd

# データ読み込みのメイン処理
def load_csv_data(csv_file_path):
    
    skiprows = get_skip_rows(csv_file_path)
    df = pd.read_csv(
        csv_file_path, skiprows=skiprows + 2, encoding="shift-jis",
        usecols=[0, 1, 2, 8], names=["depth(m)", "temp(℃)", "sal", "chl-a(μg/l)"],
        header=None
    )
    print(df.head(10))
    return df

# csvファイルの不要な行をskipするためのサブ処理
def get_skip_rows(csv_file_path:str):
    skiprows = None
    with open(csv_file_path, 'r', encoding='shift-jis') as f:
        for num, line in enumerate(f):
            if "[Item]" in line:
                skiprows = num
    if skiprows is None:
        raise ValueError(f"[Item] 行が見つかりません: {csv_file_path}")
    return skiprows
```

次に、**作図関数**を用意しましょう。

```Python
# matplotlibを用いた作図関数
import pandas as pd
import matplotlib.pyplot as plt

def draw_vertical_profile(df:pd.DataFrame, x_col_name:str):
    x = df[x_col_name]
    y = df["depth(m)"]

    plt.scatter(x, y)
    plt.xlabel(x_col_name)
    plt.ylabel("depth(m)")
    plt.show()

```
最後に、**読み込み関数と作図関数をつなげたメイン処理プロセスにデータパスを渡します**

```Python
# メイン処理プロセス

# データパス
PROJECT_ROOT = "/content/drive/MyDrive/kankyogaku2026"
wakasa_st1 = f"{PROJECT_ROOT}/data/wakasa/20260624/202606241429_ASTD152-ALC-R02_0184_142943.Csv"

# データを読み込む
df = load_csv_data(wakasa_st1)
# 作図する
draw_vertical_profile(df, x_col_name="temp(℃)")
```
他の観測項目も描いてみましょう。  
一度作った関数の**引数(関数に渡すことのできる値)** を変えることで再利用できます。  
**関数を再利用するために、共通部分と変更部分を考え、変更部分は引数に設定するのが基本です。**

```Python
# 観測項目は "temp(℃)", "sal", "chl-a(μg/l)" の三種類

draw_vertical_profile(df, x_col_name="sal")

draw_vertical_profile(df, x_col_name="chl-a(μg/l)")

```

---

## まとめ
観測データを可視化するためには
- **データパスを定義する**
- **データ読み込み処理を作る**
- **データ可視化/統計などの処理を作る**

という流れが基本です。  
**それぞれの過程を関数で分割しておくと、修正や変更、再利用がしやすくなります。**  
AIにプログラムを書いてもらう際も、**一気に作らせるより、関数単位で作らせた方が、意図した実装かどうかを検証しやすい**です。