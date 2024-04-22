# 渡邉研究室　計算班チュートリアル
<div style="text-align: right;">
文責：内藤　　最終更新日： 2024/04/22
</div>
<br>  

&emsp;渡邉研究室計算班のチュートリアルです。すでに、自分のPCに、SSH接続が可能な環境（ __CISCO AnyConnect, Teraterm, WinSCP__ ）と結晶構造の可視化ツール（ __VESTA, OVITO__ ）が準備されていることが前提です。まだの場合は先述の5つを用意してください（Box内のネットワーク接続マニュアル、計算班マニュアル群を要参照）。  
&emsp;本チュートリアルは __4.5PC__ で行いましょう。詳しい性能差は、Boxの「計算班PCリスト.xlsx」を参照してください。  
&emsp;ネットワーク接続手順のおさらいですが、Teratermで __6.2PC__ にログインしましょう。ログイン出来たら、次のコマンドを実行し、他のPCに移動しましょう。[ ]は不要です。

     ssh -X -Y owner@192.168.4.5

パスワードの入力をしたら（画面には入力した文字が表示されませんが仕様です）、Enterキーを押し、チュートリアルを行いたいPCへ移動します。
<br>  
  
&emsp;まずは、自分専用のディレクトリを作成しましょう。__Teraterm__ にて、次のコマンドで作成できます。このドキュメントではフォルダ名 __surname__ という名前で説明します。自分の苗字等をフォルダ名にするといいでしょう。  

     mkdir surname
     cd surname


次のコマンドを打つことで、チュートリアル用のディレクトリが作成されます。

     git clone https://github.com/MDGroup-WatanabeLab/Tutorial.git

また、構造作成プログラムである、mdpythonもダウンロードしましょう。

     git clone https://github.com/MDGroup-WatanabeLab/mdpython.git

では、ディレクトリを移動しましょう。

     cd Tutorial

準備ができたら、早速、下のチュートリアルに進みましょう。  


## 1. LAMMPSでMD計算  
&emsp;LAMMPSでMD計算を行います。MD計算とは、古典力学に基づき、運動方程式から原子の移動をシミュレーションする方法です。実際にやってみましょう。  
&emsp;まず、入力となる構造を用意します。次のコマンドで、ディレクトリを移動します。

     cd 1_lammps_run

構造を作成するには、mdpythonのStructureから、所定のpythonプログラムが必要です。今回のチュートリアルでは、NaClの計算を行いたいので、岩塩型構造を作成できるプログラムを持ってきましょう。  
&emsp;次のコマンドを実行しましょう。

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
　作成した構造がどのようになっているか確認するため、POSCARファイルをVESTAかOVITOで開き、確認しましょう。
次のようになっているはずです。  
<img width="713" alt="スクリーンショット 2023-08-02 100648" src="https://github.com/MDGroup-WatanabeLab/image_for_mdpython/assets/138444525/a2f7a26c-115d-45ed-81f3-3f1161c5a076">  

しかし、これから行うLAMMPSの計算では、ファイル形式を変更する必要があります。次のコマンドで、ファイルコンバーターをコピーしましょう。

     cp ../../mdpython/Converter/convert_file.py convert_file.py

コンバーターを次のコマンドで実行しましょう。

     python convert_file.py

すると、POSCARが選択肢として出てくるはずです。0をおして、選択しましょう。どのファイル形式にするか聞かれるので、

    0 : mdl
    1 : xyz
    2 : lmp
    3 : POSCAR
    Which format do you want to convert to ? :

2を押せば、lmp形式に変換できます。変換後の名前は好きにしてください。ここで、電荷について聞かれるのですが、 __NaとClの電荷はそれぞれ 1 と -1 （半角入力）にしてください。__   

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
     pair_style   born/coul/long 10.0
     kspace_style ewald 1e-5
     pair_coeff   1 1 0.013747 0.3170 3.300 1.036 0 10.0
     pair_coeff   1 2 0.013747 0.3170 3.610 3.109 0 10.0
     pair_coeff   2 2 0.013747 0.3170 3.920 9.328 0 10.0
     neighbor     4.0 bin
     neigh_modify every 1 delay 0 check yes
     timestep     0.001
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

     dump         1 all atom 100 nacl_amorphous.xyz
     dump_modify  1 sort id element Na Cl append yes
     fix          1 all nvt temp 300 300 0.05
     run          10000
     unfix        1
     velocity     all create 1500 379278227 mom yes rot yes dist gaussian
     fix          1 all nvt temp 1500 1500 0.05
     run          100000
     unfix        1
     velocity     all create 1500 379278227 mom yes rot yes dist gaussian
     fix          1 all npt temp 1500 300 0.05 x 0 0 0.05 y 0 0 0.05  z 0 0 0.05
     run          50000
     unfix        1
     dump         2 all custom 1 amorphous.final id type xs ys zs
     dump_modify  2 sort id
     run          0
     undump       2
     undump       1

となっています。非常に長いですが、頑張って読みましょう。  
インターネットで「LAMMPS Manual」で検索し、どのパラメータが何を表しているか調べて、今から行う計算の意味を考えましょう。

&emsp;__さて、このファイルでは、一行だけ自分で変えなければならない部分があります。わかりますか？__  

&emsp;わかったら、その部分を変え、計算を始めましょう。まずは、他の人が計算していないか、次のコマンドで確認しましょう。

     top

__q キー__ で元の画面に戻ります。次のコマンドを実行します。

     nohup mpirun -np 16 lmp_mpi -in in.amorphous.NaCl &

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
        └── 2_VASP_DFT
        └── 3_VASP_AIMD
        └── 4_python
        └── 100_answer

1分半ほどで終わります。amorphous.final が生成されたらオッケーです。しかし、このままでは結晶が本当にアモルファス化しているかわかりません。  

&emsp;原子間結合距離などの情報が得られる __動径分布関数__ を出しましょう。先ほどの.finalファイルをOVITOで開きましょう。右上の「Add modification」から「Coordination analysis」で動径分布関数が表示できます。「Cutoff radius」を10などに設定すれば、以下のような結果が得られるはずです。  

![スクリーンショット 2024-04-18 132851](https://github.com/MDGroup-WatanabeLab/Tutorial/assets/138444525/3a65803f-45a4-431b-b5f6-1ea828eae85c)

アモルファスの動径分布関数は第一ピークより右側の領域の値が１になっていなければいけません。もし自分の計算後の構造の動径分布関数が上の図のようになっていれば、アモルファス化していると言えます。  

&emsp;他のチュートリアルが終わったら、計算条件を変えて、動径分布関数がどのように変化するかいろいろ計算してみてください。


## 2. VASPでDFT計算  
&emsp;VASPでDFT計算を行います。量子力学に基づいた、正確だが時間がかかる計算である第一原理計算のうち、密度汎関数法（Density functional theory）を扱います。計算に必要なファイルは次の４ファイルです。

|ファイル名|格納している情報|  
|:--:|:--|
|INCAR|計算条件など|
|KPOINTS|メッシュを切る数|
|POSCAR|原子の構造|
|POTCAR|擬ポテンシャル|

ディレクトリを移動しましょう。

     cd ../2_VASP_DFT
 
DFT計算を２回行ってもらいます。まずは、１回目の計算用のディレクトリに移動しましょう。

     cd 1_SCF

すでに必要なファイルはすべて用意されています。各ファイルの中身は、以下の通りです。

INCARは、

     ISMEAR = 0
     SIGMA = 0.1

KPOINTSは、

    K-Points
    0
    Gamma
    4  4  4
    0  0  0

POSCARは、

     diamond_Ge_2
     1.0
     0.0 2.8377 2.8377
     2.8377 0.0 2.8377
     2.8377 2.8377 0.0
     Ge
     2
     direct
     0.75 0.75 0.75
     0.50 0.50 0.50

POTCARは、

    PAW_PBE Ge 05Jan2001                   
    4.00000000000000     
    parameters from PSCTR are:  
             :  
          （省略）  
             :  
    0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00
    0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00  0.000000000000E+00
    0.000000000000E+00  0.000000000000E+00
    End of Dataset


POSCARをVESTAかOVITOで開き、どのような構造かあらかじめ確認しましょう。次のような構造がみられるはずです。どんな構造か、Ge原子であることを念頭に考えてみましょう。  

![スクリーンショット 2024-04-18 134220](https://github.com/MDGroup-WatanabeLab/Tutorial/assets/138444525/ae6cd0d7-ad77-4fbc-9a07-d4f8275fd736)

確認出来たら、 VASPの計算を開始しましょう。まずは、他の人が計算していないか、次のコマンドで確認します。

     top

q キーで元の画面に戻ります。確認出来たら、次のコマンドを打ちましょう。

     nohup mpirun -np 16 vasp_std &


正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_lammps_run
        └── 2_VASP_DFT
                    └── 1_SCF
                    └── CHG
                    └── CHGCAR
                         :
                    └── nohup.out
                         :
                    └── WAVECAR
                    └── XDATCAR
               └── 2_nonSCF
        └── 3_VASP_AIMD
        └── 4_python
        └── 100_answer


&emsp;計算が完了次第、次の計算に移ります。次の計算用のディレクトリに移動します。

     cd ../2_nonSCF

今回は、Pythonプログラムを用い、KPOINTSファイルを生成します。次のコマンドを実行します。

     python generate_kpoints4VASP.py

KPOINTSファイルが生成されるはずです。中身は・・・

     Line_mode KPOINTS file
     40
     Line_mode
     Reciprocal
     0.0 0.0 0.0 ! \Gamma
     0.5 0.0 0.5 ! X

     0.5 0.0 0.5 ! X
     0.5 0.25 0.75 ! W

     0.5 0.25 0.75 ! W
     0.375 0.375 0.75 ! K

     0.375 0.375 0.75 ! K
     0.0 0.0 0.0 ! \Gamma

     0.0 0.0 0.0 ! \Gamma
     0.5 0.5 0.5 ! L

     0.5 0.5 0.5 ! L
     0.625 0.25 0.625 ! U

     0.625 0.25 0.625 ! U
     0.5 0.25 0.75 ! W

     0.5 0.25 0.75 ! W
     0.5 0.5 0.5 ! L

     0.5 0.5 0.5 ! L
     0.375 0.375 0.75 ! K

     0.625 0.25 0.625 ! U
     0.5 0.0 0.5 ! X

GammaやKなどの文字がありますね。固体物理の内容を思い出してください。
&emsp;また、INCARの中身も少し違います。

     System = fcc Si 
     ICHARG = 11 
     ISMEAR = 0
     SIGMA = 0.1
     LORBIT = 11


&emsp;今回の計算では先ほどの計算結果の一部を使います。次のコマンドを実行し、コピーしましょう。

     cp ../1_SCF/CHGCAR CHGCAR

これで準備完了です。先ほどと同様の手順で、計算を開始しましょう。

     top

他の人が計算していないか忘れずに確認しましょう。

     nohup mpirun -np 16 vasp_std &


__計算が終了したら__ 、エネルギーバンド図を出しましょう。次のコマンドを実行してください。

     python plot_band.py

「band.png」という画像が出力されるはずです。開いてみてください。  

![band](https://github.com/MDGroup-WatanabeLab/Tutorial/assets/138444525/9111f2ab-436f-4f94-bb67-587f35fc351e)

このようにエネルギーバンド図が作成されます。文献のGeのバンド図と比較してみてください。
<br/>  


## 3. VASPで第一原理MD
　VASPで第一原理MDを行います。第一原理MDとは、簡単に言うと、正確だが遅い第一原理計算と、精度は悪いが速いMD計算を組み合わせた計算方法です。実際にやってみましょう。これまでと同様、ディレクトリを移動しましょう。

     cd ../../3_VASP_AIMD

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
     LWAVE = .FALSE.
     LCHARG = .FALSE.
     ALGO = Normal
     PREC = Normal

     # MD
     IBRION = 0
     MDALGO = 3
     ISIF = 3
     SMASS = -1
     TEBEG = 300
     TEEND = 300
     NSW = 100
     POTIM = 1.0
     LANGEVIN_GAMMA = 10.0
     LANGEVIN_GAMMA_L = 10.0

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

 
VASPの計算を開始しましょう。他の人が計算していないか、次のコマンドで確認しましょう。

     top

q キーで元の画面に戻ります。

     nohup mpirun -np 16 vasp_std &

正しく計算が開始できていれば、次のようにファイルが生成されます。

    Tutorial       
        └── 1_LAMMPS_run
        └── 2_VASP_DFT
        └── 3_VASP_AIMD
            └── CHG
            └── CHGCAR
                  :
            └── nohup.out
                  :
            └── WAVECAR
            └── XDATCAR
        └── 4_python
        └── 100_answer

時間経過で nohup.out に結果が記録されていくので、適宜確認しましょう。100タイムステップ分が実行されたら完了です。時間がかかるので、今までのINCARファイルに記載してあったタグの意味を調べましょう。
<br>  

&emsp;LAMMPSで行った計算よりも遅いため、普段はあまり使わないかもしれません。しかし、機械学習ポテンシャルの訓練を行う際は、この第一原理MDを行うことがあります。覚えておきましょう。
 

## 4. 最後に
&emsp;これで以上です。わからないことはまず、マニュアルやvaspwikiなどで確認する癖をつけましょう。英語を読む練習にもなります。しかし、どうしようもないときや、深刻なエラーが発生したときは先輩に頼りましょう。

&emsp;なお、このチュートリアルの正しい計算結果は「100_answer」フォルダにすべて格納されています。自分の計算結果と比較してください。