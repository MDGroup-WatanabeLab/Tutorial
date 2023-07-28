# 渡邉研究室　計算班チュートリアル
渡邉研究室計算班のチュートリアルです。まずは、チュートリアルのために、次のようなディレクトリ群を作成しましょう。

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_LAMMPS_run0
        └── 3_VASP_AIMD
        └── 4_VASP_DFT

Linuxコマンドの練習もかねて、ターミナルで次のコマンドを打つことでディレクトリを作ってみてもいいかもしれません。  

    $ mkdir Tutorial
    $ cd Tutorial
    $ git clone https://github.com/MDGroup-WatanabeLab/Tutorial/1_LAMMPS_run
    $ git clone https://github.com/MDGroup-WatanabeLab/Tutorial/2_LAMMPS_run0
    $ git clone https://github.com/MDGroup-WatanabeLab/Tutorial/3_VASP_AIMD
    $ git clone https://github.com/MDGroup-WatanabeLab/Tutorial/4_VASP_DFT



準備ができたら、早速、下のチュートリアルに進みましょう。

## 1. LAMMPSでMD計算
　LAMMPSでMD計算を行います。MD計算とは、古典力学に基づき、運動方程式から原子の移動をシミュレーションする方法です。実際にやってみましょう。  
　まず、入力ファイルを用意します。構造を作成するには、mdpythonのStructureから、所定のpythonプログラムが必要です。今回のチュートリアルでは、NaClの計算を行いたいので、「rocksalt.py」を次のコマンドでダウンロードしましょう。

    $ git clone https://github.com/MDGroup-WatanabeLab/mdpython/blob/main/Structure/rocksalt.py

ダウンロード出来たら、早速実行しましょう。

    $ python rocksalt.py

選択肢が出てくるので、

    Select atoms from following:
    0 : GST
    1 : NaCl
    2 : MgO
    3 : CaO
    4 : GeTe with vacancy (1/10)
    5 : SbTe with vacancy for Sb:Te=2:3
    6 : GeSb with vacancy (1/10)
    7 : GeTe
    8 : SbTe
    9 : GeSb
    Input number :

1を入力してEnterを押したら、「NaCl」が選択出来ます。すると、

    Please input the lattice size [ x, y, z ] :

とでるので、

    >> 4 4 4

と打てば（>>は不要）、NaClの構造がPOSCAR形式で作成できます。しかし、これから行うLAMMPSの計算では、mdl形式やlmp形式が必要です。ですので、ファイル形式を変換する必要があります。次のコマンドで、ファイルコンバーターをダウンロードしましょう。

    $ git clone https://github.com/MDGroup-WatanabeLab/mdpython/blob/main/Converter/convert_file.py 

コンバーターを次のコマンドで実行しましょう。

    $ python convert_file.py

すると、POSCARが選択肢として出てくるはずです。0をおして、選択しましょう。どのファイル形式にするか聞かれるので、

    0 : mdl
    1 : xyz
    2 : lmp
    3 : POSCAR
    Which format do you want to convert to ? :

2を押せば、lmp形式に変換できます。名前は好きにしてください。


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

それでは、Teratermで、次のコマンドを入れましょう。

    $ cd 3_VASP_AIMD

これで 3_VASP_AIMD ディレクトリに移動できたはずです。  
次に、VASPの計算を開始しましょう。まずは、他の人が計算していないか、次のコマンドで確認しましょう。

    $ top

q キーで元の画面に戻ります。
確認出来たら、次のコマンドを打ちましょう。  
vasp_std のパスは、WinSCPからコピーできます。

    $ nohup mpirun -np [並列コア数] [vasp_stdのパス] &

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_LAMMPS_run0
        └── 3_VASP_AIMD
            └── CHG
            └── CHGCAR
                  :
            └── nohup.out
                  :
            └── WAVECAR
            └── XDATCAR
        └── 4_VASP_DFT

時間経過で nohup.outに結果が記録されていくので、適宜確認しましょう。  
また、計算が終わるまでは、入力ファイルの意味をvaspwikiで確認しておきましょう。  
50タイムステップ分が実行されたら完了です。CONTCARをVESTAなどで見たり、XDATCARをOVITOでgif動画にしてみましょう。お疲れさまでした。

## 4. VASPでDFT計算  
VASPでDFT計算を行います。簡単にいえば、第一原理計算です。手順は先ほどの３とかなり似ています。復習もかねて進めましょう。  
まず、次の５つのファイルを用意します。「3_VASP_AIMD」フォルダから、ダウンロードしてください。各ファイルの中身は、以下の通りです。

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

    # Not change position
    IBRION = -1

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

それでは、Teratermで、次のコマンドを入れましょう。

    $ cd 4_VASP_DFT

これで 4_VASP_AIMD ディレクトリに移動できたはずです。  
次に、VASPの計算を開始しましょう。まずは、他の人が計算していないか、次のコマンドで確認しましょう。

    $ top

q キーで元の画面に戻ります。
確認出来たら、次のコマンドを打ちましょう。  
vasp_std のパスは、WinSCPからコピーできます。

    $ nohup mpirun -np [並列コア数] [vasp_stdのパス] &

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_LAMMPS_run0
        └── 3_VASP_AIMD
        └── 4_VASP_DFT
            └── CHG
            └── CHGCAR
                  :
            └── nohup.out
                  :
            └── WAVECAR
            └── XDATCAR

時間経過で nohup.outに結果が記録されていくので、適宜確認しましょう。  
また、計算が終わるまでは、入力ファイルの意味をvaspwikiで確認しておきましょう。  
エネルギーの計算が完了すれば終了です。お疲れさまでした。
