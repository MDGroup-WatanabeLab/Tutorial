# 渡邉研究室　計算班チュートリアル
<div style="text-align: right;">
文責：内藤　　最終更新日： 2024/03/08
</div>
<br>  

&emsp;渡邉研究室計算班のチュートリアルです。すでに、自分のPCに、SSH接続が可能な環境（ __CISCO AnyConnect, Teraterm, WinSCP__ ）と結晶構造の可視化ツール（ __VESTA, OVITO__ ）が準備されていることが前提です。まだの場合は先述の5つを用意してください。  
&emsp;また、渡邉研究室が所有しているPCのうち、LAMMPSとVASPが動作するのは、以下の表のとおりです。（※ 2024/03/08 現在）

| | LAMMPS |   VASP 6.4.0   |  
| :----: | :----: | :----: |
|PC名<br>（末尾）|4.4, 4.5, 6.3, 6.34, 6.35|6.3, 4.4, 4.5, 6.35|

この表に対応したPCでチュートリアルを行いましょう。詳しい性能差は、Boxの「計算班PCリスト.xlsx」を参照してください。  
&emsp;ネットワーク接続手順のおさらいですが、Teratermで __6.2PC__ にログインしましょう。ログイン出来たら、次のコマンドを実行し、他のPCに移動しましょう。[ ]は不要です。

     ssh -X -Y [ユーザー名]@[IPアドレス]

パスワードの入力をしたら（画面には入力した文字は表示されません）、Enterキーを押し、チュートリアルを行いたいPCへ移動します。
<br>  
  
&emsp;まずは、自分専用のディレクトリを作成しましょう。__Teraterm__ にて、次のコマンドで作成できます。[ ] は不要です。  

     mkdir [苗字とか（ローマ字）]
     cd [作成したディレクトリ名]


次のコマンドを打つことで、チュートリアル用のディレクトリが作成されます。

     git clone https://github.com/MDGroup-WatanabeLab/Tutorial.git

また、構造作成プログラムである、mdpythonもダウンロードしましょう。

     git clone https://github.com/MDGroup-WatanabeLab/mdpython.git

では、ディレクトリを移動しましょう。

     cd Tutorial

準備ができたら、早速、下のチュートリアルに進みましょう。

## 1. LAMMPSでMD計算  
LAMMPSでMD計算を行います。MD計算とは、古典力学に基づき、運動方程式から原子の移動をシミュレーションする方法です。実際にやってみましょう。  
まず、入力となる構造を用意します。次のコマンドで、ディレクトリを移動します。

     cd 1_lammps_run

構造を作成するには、mdpythonのStructureから、所定のpythonプログラムが必要です。今回のチュートリアルでは、NaClの計算を行いたいので、岩塩型構造を作成できるプログラムを持ってきましょう。  
次のコマンドを実行しましょう。

     cp ../../mdpython/Structure/rocksalt.py rocksalt.py

コピー出来たら、早速実行しましょう。

     python rocksalt.py

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

と打てば（>>は不要）、NaClの構造がPOSCAR形式で作成できます。  
　作成した構造がどのようになっているか確認するため、POSCARファイルを __自分のPCへコピー__ し、VESTAかOVITOで確認しましょう。
次のようになっているはずです。  
<img width="713" alt="スクリーンショット 2023-08-02 100648" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/a2f7a26c-115d-45ed-81f3-3f1161c5a076">  

しかし、これから行うLAMMPSの計算では、mdl形式やlmp形式が必要です。ですので、ファイル形式を変換する必要があります。次のコマンドで、ファイルコンバーターをコピーしましょう。

     cp ../../mdpython/Converter/convert_file.py convert_file.py

コンバーターを次のコマンドで実行しましょう。

     python convert_file.py

すると、POSCARが選択肢として出てくるはずです。0をおして、選択しましょう。どのファイル形式にするか聞かれるので、

    0 : mdl
    1 : xyz
    2 : lmp
    3 : POSCAR
    Which format do you want to convert to ? :

2を押せば、lmp形式に変換できます。変換後の名前は好きにしてください。ここで、電荷について聞かれるのですが、 __NaとClの電荷はそれぞれ1と―1にしてください。__   

すでに必要なファイルはすべて用意できているはずなので、

     ls

で、ディレクトリ内のファイルを確認しましょう。  
「in.amorphous.NaCl」というファイルがあると思います。  
中身は・・・

    units        metal
    boundary     p p p
    atom_style   charge
    atom_modify  sort 10000 1.0
    read_data    NaCl444.lmp
    pair_style   born/coul/long/omp 10.0
    kspace_style ewald 1e-5
    pair_coeff   1 1 0.013747 0.3170 3.300 1.036 0 10.0
    pair_coeff   1 2 0.013747 0.3170 3.610 3.109 0 10.0
    pair_coeff   2 2 0.013747 0.3170 3.920 9.328 0 10.0
    neighbor     4.0 bin
    neigh_modify every 1 delay 0 check yes
    timestep     0.0001
    velocity     all create 300 318796474 mom yes rot yes dist gaussian
    thermo_style custom step temp ke pe etotal press vol density
    thermo       1000
    fix          1 all nve
    fix          2 all box/relax aniso 0.0 fixedpoint 0.0 0.0 0.0
    min_style    cg
    minimize     1e-25 1e-25 50000 100000
    dump         1 all custom 1 stable.final id type xs ys zs
    dump_modify  1 sort id
    run          0
    undump       1
    unfix        1
    unfix        2

    dump         1 all xyz 100 nacl_amorphous.xyz
    dump_modify  1 sort id element Na Cl append yes
    fix          1 all nvt temp 300 300 0.05
    run          10000
    unfix        1
    fix          1 all nvt temp 2000 2000 0.05
    run          100000
    unfix        1
    fix          1 all npt temp 2000 300 0.05 x 0 0 0.05 y 0 0 0.05 z 0 0 0.05
    run          50000
    unfix        1
    dump         2 all custom 1 amorphous.final id type xs ys zs
    dump_modify  2 sort id
    run          0
    undump       2
    undump       1

となっています。非常に長いですが、頑張って読みましょう。  
インターネットで「LAMMPS Manual」で検索し、どのパラメータが何を表しているか調べて、今から行う計算の意味を考えましょう。

さて、このファイルでは、一行だけ自分で変えなければならない部分があります。わかりますか？  

わかったら、その部分を変え、計算を始めましょう。  
まずは、他の人が計算していないか、次のコマンドで確認しましょう。

     top

q キーで元の画面に戻ります。次のコマンドを実行します。

     nohup mpirun -np 16 lmp_mpi < in.amorphous.NaCl &

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_lammps_run
               └── ***.lmp
               └── amorphous.final
               └── convert_file.py
               └── in.amorphous.NaCl
               └── log.lammps
               └── nacl_amorphous.xyz
               └── nohup.out
               └── POSCAR
               └── rocksalt.py
               └── stable.final
        └── 2_lammps_run0
        └── 3_VASP_DFT
        └── 4_VASP_AIMD

1分半ほどで終わります。amorphous.final が生成されたらオッケーです。しかし、このままでは結晶が本当にアモルファス化しているかわかりません。ファイル形式を変更し、VESTAで見られるようにしましょう。再び、

     python convert_file.py

で、__xyz形式__ に変更しましょう。なお、id1とid2の原子はそれぞれna, clと答えてください。  
変換出来たら、そのxyzファイルをVESTAで開いてみましょう。
次のようになっているはずです。  
<img width="662" alt="スクリーンショット 2023-08-02 100915" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/bf6dd118-a49e-4db2-82e0-29577567f912">

次に、原子間結合距離などの情報が得られる __動径分布関数__ を出しましょう。先ほどのxyzファイルをOVITOで開きましょう。
右上の「Add modification」から「Coordination analysis」で動径分関数が表示できます。「Cutoff radius」を10などに設定すれば、以下のような結果が得られるはずです。
<img width="1127" alt="スクリーンショット 2023-08-02 103320" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/98d86d36-2a6e-470f-b31c-1b7403d92a4b">

POSCARの動径分関数（アモルファス化させる前）も出してみましょう。結晶とアモルファスの動径分布関数にどｄのような違いがあるか考えましょう。

## 2. LAMMPSで一点計算
今度は、LAMMPSの一点計算でエネルギーを計算してみましょう。まず、ディレクトリを移動しましょう。  

     cd ../2_lammps_run0

今回は、すでに構造を用意してありますので、すぐに計算が始められます。
入力条件を示す「in.Ge」の中身は、  

     units           metal
     atom_style      atomic
     boundary        p p p

     read_data       Ge222.lmp

     pair_style      tersoff
     pair_coeff      * * Ge.tersoff Ge 

     neighbor        4.0 bin
     neigh_modify    every 1 delay 0 check yes

     timestep        0.0001

     velocity        all create 300 318796474 mom yes rot yes dist gaussian
     thermo_style    custom step temp ke pe etotal press vol density
     thermo 1000
     fix             1 all nve
     fix             2 all box/relax aniso 0.0 fixedpoint 0.0 0.0 0.0

     min_style       cg
     minimize        1e-25 1e-25 50000 100000

     dump            1 all custom 1 stable.final id type xs ys zs
     dump_modify     1 sort id

     run 0

     undump          1
     unfix           1
     unfix           2

となっています。原子を固定し、エネルギーを算出できます。計算してみましょう。  
まずは、他の人が計算していないか、次のコマンドで確認しましょう。

     top

q キーで元の画面に戻ります。次のコマンドを実行します。

     nohup mpirun -np 16 lmp_mpi < in.Ge &

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_lammps_run
        └── 2_lammps_run0
               └── stable.final
               └── nohup.out
               └── log.lammps
               └── in.Ge
               └── Ge222.lmp
               └── Ge.tersoff
        └── 3_VASP_DFT
        └── 4_VASP_AIMD

計算が終了すると、「nohup.out」の下の方に、エネルギーが出力されます。

     Step          Temp          KinEng         PotEng         TotEng         Press          Volume        Density    
       407   300            2.4430167     -270.91251     -268.46949      1905.0693      1369.7294      5.6359886    

この行で、各物理量がいくつになっているかわかります。このあと行うDFT計算の結果と比較してみましょう。

## 3. VASPでDFT計算  
VASPでDFT計算を行います。量子力学に基づいた、正確だが時間がかかる計算である第一原理計算のうち、密度汎関数法（Density functional theory）を扱います。   
ディレクトリを移動しましょう。

     cd ../3_VASP_DFT
 
すでに必要なファイルはすべて用意されています。各ファイルの中身は、以下の通りです。

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
    LREAL = Auto

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


POSCARをVESTAかOVITOで開き、どのような構造かあらかじめ確認しましょう。次のような構造がみられるはずです。  
<img width="701" alt="スクリーンショット 2023-08-02 101109" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/289278ab-1744-4d8b-b14d-511bc68866fe">

確認出来たら、 VASPの計算を開始しましょう。まずは、他の人が計算していないか、次のコマンドで確認します。

     top

q キーで元の画面に戻ります。確認出来たら、次のコマンドを打ちましょう。[ ] は不要です。また、vasp_std のパスは、以下の表の通りです。
|PC名|vasp_std のパス|
|:-----:|:-----:|
|4.4| /home/thermal/VASP.6/vasp.6.4.0/bin/vasp_std |
|4.5| /home/owner/VASP/vasp.6.4.0/bin/vasp_std |

    # PC 4.4と4.5の場合
     nohup mpirun -np [並列コア数] [vasp_stdのパス] &
     disown
     top

    # PC 6.35の場合
     run_vasp.sh
     top

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_lammps_run
        └── 2_lammps_run0
        └── 3_VASP_DFT
            └── CHG
            └── CHGCAR
                  :
            └── nohup.out
                  :
            └── WAVECAR
            └── XDATCAR
        └── 4_VASP_AIMD

時間経過で nohup.out に結果が記録されていくので、適宜確認しましょう。  
また、計算が終わるまでは、入力ファイルの意味をvaspwikiで確認しておきましょう。  
エネルギーの計算が完了すれば終了です。せっかくなので、エネルギーバンド図を作成しましょう。そのためのプログラムをコピーしましょう。

     cp ../../mdpython/Tool4VASP/EIGENVAL.py EIGENVAL.py

実行しましょう。

     python EIGENVAL.py

「EIGENVAL.xlsx」が生成されるはずです。開くと、  
<img width="586" alt="スクリーンショット 2023-08-02 105455" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/3cf5b9c6-8ad1-4977-9399-dabd5288dda3">

このようにバンド図が作成されます。横軸は波数 [/angs]、縦軸はエネルギー [eV]を表しています。それぞれの線は各電子軌道ごとのエネルギーを表しています。    
最後に、 __先ほどのLAMMPSの一点計算の結果と比較しましょう。__ 「nohup.out」の下の方にエネルギーの計算結果が出力されています。  
両者とも、内部エネルギーは、-260～-280eV ほどになっているはずです。  
お疲れさまでした。

## 4. VASPで第一原理MD
　VASPで第一原理MDを行います。第一原理MDとは、簡単に言うと、正確だが遅い第一原理計算と、精度は悪いが速いMD計算を組み合わせた計算方法です。実際にやってみましょう。これまでと同様、ディレクトリを移動しましょう。

     cd ../4_VASP_AIMD

すでに必要なファイルはすべて用意されています。各ファイルの中身は、以下の通りです。

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
    LREAL = Auto

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

 
VASPの計算を開始しましょう。まずは、他の人が計算していないか、次のコマンドで確認しましょう。

     top

q キーで元の画面に戻ります。
確認出来たら、次のコマンドを打ちましょう。  
vasp_std のパスは、以下の表の通りです。
|PC名|vasp_std のパス|
|:-----:|:-----:|
|4.4| /home/thermal/VASP.6/vasp.6.4.0/bin/vasp_std |
|4.5| /home/owner/VASP/vasp.6.4.0/bin/vasp_std |

    # PC 4.4と4.5の場合
     nohup mpirun -np [並列コア数] [vasp_stdのパス] &
     disown
     top

    # PC 6.35の場合
     run_vasp.sh
     top

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_LAMMPS_run0
        └── 3_VASP_DFT
        └── 4_VASP_AIMD
            └── CHG
            └── CHGCAR
                  :
            └── nohup.out
                  :
            └── WAVECAR
            └── XDATCAR

時間経過で nohup.out に結果が記録されていくので、適宜確認しましょう。  
また、計算が終わるまでは、入力ファイルの意味をvaspwikiで確認しておきましょう。  
50タイムステップ分が実行されたら完了です。10分もかからずに終わると思います。  
CONTCARをVESTAで開いてみてください。下の写真では、左が計算前（POSCAR）、右が計算後（CONTCAR）の構造を表しています。わずかに原子が移動していることがわかると思います。原子数が減っているようにも見えますが、これは周期的境界条件により、POSCARでは境界線上の原子が表示されているためです。  
![AIMD_change](https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/71b6139c-69c1-497b-bcf2-61e3eba1e8fa)

続いて、圧力と体積、温度、エネルギー変化のグラフ化を行います。そのためのプログラムを次のコマンドでコピーします。

     cp ../../mdpython/Tool4VASP/PVTE_graph.py PVTE_graph.py

プログラムを実行しましょう。

     python PVTE_graph.py

「PVTE_graph.xlsx」というエクセルファイルが生成されます。開いてみると、下の図のような圧力、体積、温度、エネルギー変化のグラフが作成されているはずです。  
<img width="604" alt="スクリーンショット 2023-08-02 133526" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/164f693b-860c-4d7d-9336-c43b6a3788a1">  

お疲れさまでした。


## 5. mdpythonを編集する  
先ほど、「rocksalt.py」を使ってもらいましたが、このプログラムは計算班が作成した「mdpython」というプログラム群の一つです。特級呪物にならないよう、追記や作成の仕方を確認しましょう。

     cd ../5_mdpython

で移動しましょう。「bcc.py」と「fcc.py」という、二つのファイルがあると思います。この二つのファイルを開き、どのような処理を行っているか見てみましょう。  
それでは、「bcc.py」に __リチウム（Li, a = 3.491 angs）__ 、「fcc.py」に __カルシウム（Ca, a = 5.81 angs）__ の構造を作成できるように、プログラムを追記しましょう。  
コードが完成したら、実行し、エラー処理しつつ完成させましょう。