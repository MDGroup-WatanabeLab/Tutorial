# 渡邉研究室　チュートリアル解答  
チュートリアルの解答です。計算の意味や、プログラムの変更すべき点などを記しています。答え合わせや、確認、ミーティング資料の作成に役立ててください。

## 1. LAMMPSでMD計算

## 2. LAMMPSで一点計算

## 3. VASPでDFT計算
計算の内容を確認します。

    # Basic parameters
    ISMEAR = 0        :
    SIGMA = 0.05      :
    LREAL = Auto      :
    ISYM = 0          :
    NELMIN = 4        :
    NELM = 100        :
    EDIFF = 1E-6      :
    ALGO = VeryFast   :
    PREC = Accurate   :

    # Not change position
    IBRION = -1

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