# 渡邉研究室　チュートリアル解答  
チュートリアルの解答です。計算の意味や、プログラムの変更すべき点などを記しています。答え合わせや、確認、ミーティング資料の作成に役立ててください。

## 1. LAMMPSでMD計算
まず、変えるべき場所は、__「read_data」の行__ です。構造を読み取るための行なので、自分で作ったlmpファイルの名前に変更しましょう。

次に、計算の内容を確認します。要点としては、
 - 周期的境界条件あり
 - Born-Mayer-Huggins Potential を使用
 - NVEアンサンブルで構造最適化
 - NVTアンサンブルで300K、10[ps]のMD
 - NVTアンサンブルで1500K、100[ps]のMD
 - NVTアンサンブルで1500Kから300Kへ冷却、50[ps]のMD


## 2. VASPでDFT計算
計算の内容を確認します。
１回目の計算の内容は、

    ISMEAR = 0              : Fermi smearing を使用
    SIGMA = 0.1             : Smearing 用のパラメーター

２回目の計算の内容は、

    System = fcc Si         : 系の名称設定
    ICHARG = 11             : CHGCARを読む
    ISMEAR = 0              : Fermi smearing を使用
    SIGMA = 0.1             : Smearing 用のパラメーター
    LORBIT = 11             : PAW法を用いてProjectionを行う


## 3. VASPで第一原理MD
計算の内容を確認します。MDに関するパラメーターの意味は以下の通りです。

    # Basic parameters
    ISMEAR = 0              : Fermi Smearingを使用
    SIGMA = 0.05            : Smearingの幅
    LREAL = Auto            : ROPT=-5E-4 に設定
    ISYM = 0                : 対称性を用いない
    NELMIN = 4              : SCループの最小回数
    NELM = 100              : SCループの最大回数
    EDIFF = 1E-6            : SCループの終了条件
    LWAVE = .FALSE.         : WAVECARの出力OFF
    LCHARG = .FALSE.        : CHGCARの出力OFF
    ALGO = Normal           : blocked-Davidson-iteration scheme
    PREC = Normal           : 下の表を参照

    # MD
    IBRION = 0              : MD実行
    MDALGO = 3              : Langevin熱浴を使用
    ISIF = 3                : 原子座標や格子の体積の変化を許可
    SMASS = -1              : 毎ステップごとに速度をスケーリング
    TEBEG = 300             : 計算開始時の温度
    TEEND = 300             : 計算終了時の温度
    NSW = 100               : タイムステップ数
    POTIM = 1.0             : 1タイムステップの長さを 1.0 fsに
    LANGEVIN_GAMMA = 10.0   : 原子のLangevin方程式の弾性係数
    LANGEVIN_GAMMA_L = 10.0 : 格子のLangevin方程式の弾性係数


なお、PRECの詳細は次の通り
|パラメータ|設定値|
|:--:|:--:|
|ENCUT|max(ENMAX)|
|NGX,Y,Z|3/2×$G_{cut}$|
|NGXF,YF,ZF|2×NGX|