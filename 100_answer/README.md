# 渡邉研究室　チュートリアル解答  
チュートリアルの解答です。計算の意味や、プログラムの変更すべき点などを記しています。答え合わせや、確認、ミーティング資料の作成に役立ててください。

## 1. LAMMPSでMD計算
まず、変えるべき場所は、__「read_data」の行__ です。構造を読み取るための行なので、自分で作ったlmpファイルの名前に変更しましょう。

次に、計算の内容を確認します。要点としては、
 - 周期的境界条件あり
 - Born-Mayer-Huggins Potential を使用
 - NVEアンサンブルで構造最適化
 - NVTアンサンブルで300K、1[ps]のMD
 - NVTアンサンブルで2000K、10[ps]のMD
 - NVTアンサンブルで2000Kから300Kへ冷却、5[ps]のMD

## 2. LAMMPSで一点計算
計算の内容だけ簡単に。  
Ge64個のダイアモンド構造を、Tersoffポテンシャルを用いてエネルギーの計算をしています。要点としては、
 - タイムステップは __0.1 fs__
 - アンサンブルは __NVE__


## 3. VASPでDFT計算
計算の内容を確認します。

    # Basic parameters
    ISMEAR = 0        : Fermi smearing を使用
    SIGMA = 0.05      : Smearing 用のパラメーター
    ISYM = 0          : not use symmetry (setting for MD)
    NELMIN = 4        : SCF計算の最小回数
    NELM = 100        : SCF計算の最大回数
    EDIFF = 1E-6      : SCF計算の終了条件
    ALGO = VeryFast   : RMM-DIIS algorithm を使用
    PREC = Accurate   : egg-box effect による誤差を減らす

    # Not change position
    IBRION = -1       : 原子位置を動かさない

## 4. VASPで第一原理MD
計算の内容を確認します。MDに関するパラメーターの意味は以下の通りです。

    # MD
    IBRION = 0　　：MD計算を行う
    MDALGO = 3　　：Langevin熱浴を使用
    ISIF = 3　　  ：NpTアンサンブルを使用
    TEBEG = 300　 ：300Kで開始
    TEEND = 300　 ：300Kで終了
    NSW = 50 　　 ：50ステップ計算する
    POTIM = 1.0　 ：1ステップの間隔は1[fs]

## 5. mdpythonを編集する
変えるべきは、プログラム上部の「atom_list」「lattice_list」だけです。  
なので、答えは…

    # bcc
    import os
    import math
    import numpy as np

    # Parameters
    atom_list = ["W", "Cr", "Fe", "Li"] ← ココ !
    lattice_list = [3.16469, 2.8839, 2.8665, 3.491] ← ココ !


    print("Select atoms from following: ")
    for i in range(len(atom_list)):
        print("{} : {}".format(i, atom_list[i]))
    atomflag = int(input("Input number : "))
       :
       :
    （省略）

そして、

    # fcc
    import os
    import math
    import numpy as np

    # Parameters
    atom_list = ["Au", "Ag", "Cu", "Fe", "Al", "Co", "Ca"] ← ココ !
    lattice_list = [4.7825, 4.0862, 3.614967, 3.5910, 4.04958, 3.548, 5.81] ← ココ !

    print("Select atoms from following: ")
    for i in range(len(atom_list)):
        print("{} : {}".format(i, atom_list[i]))
    atomflag = int(input("Input number : "))
       :
       :
    （省略）

__atom_list は文字列の配列なので " " で括ることを忘れないでください！__