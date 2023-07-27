# 渡邉研究室　計算班チュートリアル
渡邉研究室計算班のチュートリアルです。まずは、チュートリアルのために、次のようなディレクトリ群を作成しましょう。Linuxコマンドの練習も

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_LAMMPS_run0
        └── 3_VASP_AIMD
        └── 4_VASP_DFT

Linuxコマンドの練習もかねて、ターミナルで次のコマンドを打つことでディレクトリを作ってみてもいいかもしれません。  

    $ mkdir Tutorial
    $ cd Tutorial
    $ mkdir 1_LAMMPS_run
    $ mkdir 2_LAMMPS_run0
    $ mkdir 3_VASP_AIMD
    $ mkdir 4_VASP_DFT



準備ができたら、早速、下のチュートリアルに進みましょう。

## 1. LAMMPSでMD計算

## 2. LAMMPSで一点計算

## 3. VASPで第一原理MD
　VASPで第一原理MDを行います。第一原理MDとは、簡単に言うと、正確だが遅い第一原理計算と、精度は悪いが速いMD計算を組み合わせた計算方法です。実際にやってみましょう。  
　まず、次の５つのファイルを用意します。「3_VASP_AIMD」フォルダから、ダウンロードしてください。各ファイルの中身は、以下の通りです。

ICONSTは、

    LR 1 7  
    LR 2 7  
    LR 3 7  
    LA 2 3 7  
    LA 1 3 7  
    LA 1 2 7  
    LV 7

INCARは、

    # Basic parameters
    ISMEAR = 0
    SIGMA = 0.05
    LREAL = Auto
    ISYM = 0
    NELMIN = 4
    NELM = 100
    EDIFF = 1E-6
    ALGO = VeryFast
    PREC = Accurate

    # MD
    IBRION = 0
    MDALGO = 3
    ISIF = 3
    TEBEG = 300
    TEEND = 300
    NSW = 50
    POTIM = 1.0
    Langevin_GAMMA = 10.0
    Langevin_GAMMA_L = 10.0

KPOINTSは、

    K-Points
    0
    Gamma
    2  2  2
    0  0  0

POSCARは、

    Diamond_Ge_2x2x2  
    1.0  
    11.3508 0.0 0.0  
    0.0 11.3508 0.0   
    0.0 0.0 11.3508   
    Ge  
    64  
    Cartesian  
    0.0 0.0 0.0  
    0.0 2.8377 2.8377  
    2.8377 0.0 2.8377  
    2.8377 2.8377 0.0  
            :
            :
            :
    8.5131 8.5131 5.6754
    7.09425 7.09425 7.09425
    9.93195 9.93195 7.09425
    9.93195 7.09425 9.93195
    7.09425 9.93195 9.93195

POTCARは、

    PAW_PBE Ge 05Jan2001                   
    4.00000000000000     
    parameters from PSCTR are:  
             :  
             :  
             :  
    0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00
    0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00
    0.000000000000E+00  0.000000000000E+00
    End of Dataset

それでは、WinSCPで、次のようなディレクトリを作成しましょう。

## 4. VASPでDFT計算