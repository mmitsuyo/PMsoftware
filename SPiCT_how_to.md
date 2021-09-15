-   [1．準備](https://github.com/mmitsuyo/PMsoftware/blob/master/SPiCT_how_to.md#1%E6%BA%96%E5%82%99)
-   [2. test\_data(ここではHake)の解析](#test_dataここではhakeの解析)
 -   [以下は季節性データを使用しない場合の例です　](#以下は季節性データを使用しない場合の例です)   
    -   [2-1.シナリオ1：Schaefer型(n=2),BiomassとFの過程誤差あり・IndexとCatchのの観測誤差あり（自由に推定）](#シナリオ1schaefer型n2biomassとfの過程誤差ありindexとcatchのの観測誤差あり自由に推定)   
    -   [2-2. シナリオ2：nを推定させる,alpha=1,
        beta=1と制約を置く](#シナリオ2nを推定させるalpha1-beta1と制約を置く)   
    -   [2-3.
        シナリオ3：Schaefer型(n=2),BiomassとFの過程誤差あり，Indexの観測誤差あり，Cの観測誤差なし](#シナリオ3schaefer型n2biomassとfの過程誤差ありindexの観測誤差ありcの観測誤差なし)
-   [3. Residuls and diagnostics
        残差診断のやり方例](#residuls-and-diagnostics-残差診断のやり方例)
-   [4. Retrospective plots
        レトロ解析のやり方例](#retrospective-plots-レトロ解析のやり方例)
-   [5. Robust estimation
        外れ値の影響の緩和の例](#robust-estimation-外れ値の影響の緩和の例)
-   [6. Forecasting
        将来予測のやり方例](#forecasting-将来予測のやり方例)
-   [7. Management 管理 　　](#management-管理)  
-   
### 詳しい説明はspict_guidelines.pdfやspict_handbook.pdfを参照してください
# 1．準備
-------

``` r


devtools::install_github("DTUAqua/spict/spict") #パッケージのインストール
```

    Error in get(genname, envir = envir) : 
       オブジェクト 'testthat_print' がありません 

``` r
library(spict)#ライブラリーの読み込み  

#1-1　データの読み込み  
test_data<-read.csv("example.csv") #example.csvの形式に従い，データを作成してください
test_data<-as.list(test_data) #リスト形式に変換　
```

# 2. test\_data(ここではHake)の解析
---------------------------------

``` r
#2-1.　Catchと資源量指標値データのプロット  
plotspict.data(test_data)
```

<img src="SPiCT_how_to_files/figure-markdown_github/plot_C_I-1.png" width="80%" />

``` r
# 2-2.　time intervalの指定/確認  
inp<-check.inp(test_data)
inp$dtc #time-intervalの指定．特に指定しない場合は，データの最初の観察値を1とし，その次の観察値は1年後とみなされるのでtime-intervalは1となる.もし四半期のデータなら，inp$dtc<-0.25と指定する
```

     [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1

以下は季節性データを使用しない場合の例です　
============================================

## 2-1. シナリオ1：Schaefer型(n=2),BiomassとFの過程誤差あり・IndexとCatchのの観測誤差あり（自由に推定）
----------------------------------------------------------------------------------------------------

``` r
 inp <- test_data

 #shape parameterを固定したい場合は下記で定める
 inp$priors$logn<-c(log(2),1e-3) #shape parameter=2 Schaefer型にshape parameterを固定
 
 #その他の過程誤差，観測誤差に関してはとりあえずデフォルトのゆるいpriorをあてて推定させてみる
 #(どんなゆるいpriorをあてているかはsummaryを実行したときのPriorsというところに出てくる)
 
 #実行するためのコマンド
 res_hake2<-fit.spict(inp)

 #結果を要約する
 summary(res_hake2)
```

    Convergence: 0  MSG: both X-convergence and relative convergence (5)
    Objective function at optimum: -2.8079635
    Euler time step (years):  1/16 or 0.0625
    Nobs C: 24,  Nobs I1: 24

    Priors
         logn  ~  dnorm[log(2), 0.001^2] (fixed)
     logalpha  ~  dnorm[log(1), 2^2]
      logbeta  ~  dnorm[log(1), 2^2]

    Model parameter estimates w 95% CI 
                estimate        cilow        ciupp    log.est  
     alpha     0.6057577    0.0777672    4.7184727 -0.5012753  
     beta      0.4787822    0.1907514    1.2017341 -0.7365094  
     r         0.4643762    0.3096574    0.6963995 -0.7670603  
     rc        0.4643755    0.3096563    0.6963999 -0.7670618  
     rold      0.4643748    0.3096522    0.6964069 -0.7670634  
     m       279.5169464  224.3535080  348.2438231  5.6330629  
     K      2407.6790324 1601.0116610 3620.7845730  7.7864185  
     q         0.0003888    0.0002318    0.0006522 -7.8524600  
     n         2.0000031    1.9960870    2.0039269  0.6931487  
     sdb       0.0774072    0.0334233    0.1792723 -2.5586751  
     sdf       0.3987663    0.2541566    0.6256557 -0.9193799  
     sdi       0.0468900    0.0125740    0.1748591 -3.0599504  
     sdc       0.1909222    0.0997700    0.3653530 -1.6558893  
     
    Deterministic reference points (Drp)
               estimate       cilow        ciupp   log.est  
     Bmsyd 1203.8402386 800.5052404 1810.3957938  7.093272  
     Fmsyd    0.2321877   0.1548281    0.3481999 -1.460209  
     MSYd   279.5169464 224.3535080  348.2438231  5.633063  
    Stochastic reference points (Srp)
               estimate      cilow        ciupp   log.est rel.diff.Drp  
     Bmsys 1193.8994590 793.482204 1796.3804496  7.084980 -0.008326312  
     Fmsys    0.2307156   0.153918    0.3458314 -1.466570 -0.006380764  
     MSYs   275.4366060 221.838588  341.9843443  5.618358 -0.014814082  

    States w 95% CI (inp$msytype: s)
                        estimate        cilow        ciupp    log.est  
     B_1988.94      1678.5857253 1001.8110774 2812.5562801  7.4257069  
     F_1988.94         0.1249674    0.0579310    0.2695763 -2.0797027  
     B_1988.94/Bmsy    1.4059691    1.1449356    1.7265155  0.3407268  
     F_1988.94/Fmsy    0.5416511    0.2804645    1.0460712 -0.6131333  

    Predictions w 95% CI (inp$msytype: s)
                      prediction        cilow        ciupp    log.est  
     B_1990.00      1697.9521532 1007.3274155 2862.0699390  7.4371782  
     F_1990.00         0.1249675    0.0410367    0.3805588 -2.0797013  
     B_1990.00/Bmsy    1.4221902    1.1217736    1.8030599  0.3521981  
     F_1990.00/Fmsy    0.5416518    0.1913925    1.5329058 -0.6131318  
     Catch_1989.00   211.0506958  104.3218557  426.9708962  5.3520984  
     E(B_inf)       1724.3530870           NA           NA  7.4526072  

``` r
 #ちなみに結果に表示されるr,rold,rcの中身は r = m⁄K nn⁄(n-1), rold = | m⁄K nn⁄(n-1)1⁄(n-1) |,rc = | m⁄K n1⁄(n-1) 2 |.必要なのはr
```

``` r
# plot results
plot(res_hake2) #全体的な結果のプロット
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_fit2-1.png" width="80%" />

``` r
plotspict.priors(res_hake2)#shape parameter(n)とalpha,betaの事前分布と事後分布
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_fit2-2.png" width="80%" />

## 2-2. シナリオ2：nを推定させる,alpha=1, beta=1と制約を置く
---------------------------------------------------------

``` r
 inp <- test_data

 inp$priors$logbeta<-c(log(1),0.001^2) #betaに関するパラメータをfix. beta=1に固定
 inp$priors$logalpha<-c(log(1),0.001^2) #alphaに関するパラメータをfix. beta=1に固定
 
 #inp$priors$logalpha<-c(1,1,0) #alphaに関するpriorをなくす場合の設定
 #inp$priors$logbeta<-c(1,1,0)#betaに関するpriorをなくす場合の設定
 
 res_hake3<-fit.spict(inp)

# summary of result
 summary(res_hake3)
```

    Convergence: 0  MSG: relative convergence (4)
    Objective function at optimum: -23.0742227
    Euler time step (years):  1/16 or 0.0625
    Nobs C: 24,  Nobs I1: 24

    Priors
      logbeta  ~  dnorm[log(1), 0^2] (fixed)
     logalpha  ~  dnorm[log(1), 0^2] (fixed)
         logn  ~  dnorm[log(2), 2^2]

    Model parameter estimates w 95% CI 
                estimate        cilow        ciupp   log.est  
     alpha     1.0000000    0.9999980    1.0000020  0.000000  
     beta      1.0000000    0.9999980    1.0000020  0.000000  
     r         0.5352673    0.3251328    0.8812125 -0.624989  
     rc        0.3887278    0.2286035    0.6610104 -0.944876  
     rold      0.3051792    0.1366284    0.6816615 -1.186856  
     m       262.9569462  215.6692091  320.6130160  5.571990  
     K      2410.4972919 1556.6278156 3732.7466051  7.787588  
     q         0.0004055    0.0002400    0.0006854 -7.810286  
     n         2.7539443    1.4803034    5.1234152  1.013034  
     sdb       0.0576021    0.0410078    0.0809113 -2.854197  
     sdf       0.2894893    0.2161515    0.3877097 -1.239637  
     sdi       0.0576021    0.0410078    0.0809113 -2.854197  
     sdc       0.2894893    0.2161515    0.3877097 -1.239637  
     
    Deterministic reference points (Drp)
               estimate       cilow        ciupp   log.est  
     Bmsyd 1352.9104850 794.8574529 2302.7610470  7.210013  
     Fmsyd    0.1943639   0.1143017    0.3305052 -1.638023  
     MSYd   262.9569462 215.6692091  320.6130160  5.571990  
    Stochastic reference points (Srp)
               estimate       cilow        ciupp   log.est rel.diff.Drp  
     Bmsys 1345.3076080 792.3712070 2284.0968276  7.204378 -0.005651404  
     Fmsys    0.1929259   0.1124838    0.3308955 -1.645449 -0.007453593  
     MSYs   259.5336850 212.4730966  317.0177059  5.558886 -0.013190046  

    States w 95% CI (inp$msytype: s)
                        estimate       cilow        ciupp    log.est  
     B_1988.94      1627.8971261 961.0274422 2757.5165253  7.3950444  
     F_1988.94         0.1246277   0.0561440    0.2766471 -2.0824241  
     B_1988.94/Bmsy    1.2100557   0.8921826    1.6411829  0.1906664  
     F_1988.94/Fmsy    0.6459878   0.3194270    1.3064024 -0.4369747  

    Predictions w 95% CI (inp$msytype: s)
                      prediction       cilow        ciupp    log.est  
     B_1990.00      1667.0749850 976.6070042 2845.7086563  7.4188259  
     F_1990.00         0.1246279   0.0463600    0.3350325 -2.0824226  
     B_1990.00/Bmsy    1.2391775   0.9063412    1.6942417  0.2144479  
     F_1990.00/Fmsy    0.6459887   0.2586169    1.6135888 -0.4369733  
     Catch_1989.00   205.4389275 110.8759029  380.6521693  5.3251488  
     E(B_inf)       1755.7829461          NA           NA  7.4706702  

``` r
# plot results
plot(res_hake3) #全体的な結果のプロット
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_plot3-1.png" width="80%" />

``` r
plotspict.priors(res_hake3)#shape parameter(n)とalpha,betaの事前分布と事後分布
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_plot3-2.png" width="80%" />

## 2-3. シナリオ3：Schaefer型(n=2),BiomassとFの過程誤差あり，Indexの観測誤差あり，Cの観測誤差なし
----------------------------------------------------------------------------------------------

``` r
 inp <- test_data
 inp$priors$logn<-c(log(2),1e-3) #shape parameter=2 Schaefer型にshape parameterを固定
 

 inp$priors$logsdc<-c(log(0.001),1e-3)#漁獲量に関する観察誤差をほぼなしと仮定するための設定
 inp$priors$logbeta<-c(1,1,0)#betaに関するpriorはなくしておく
 
 res_hake<-fit.spict(inp)

# summary of result
summary(res_hake)
```

    Convergence: 0  MSG: relative convergence (4)
    Objective function at optimum: -5.2953627
    Euler time step (years):  1/16 or 0.0625
    Nobs C: 24,  Nobs I1: 24

    Priors
         logn  ~  dnorm[log(2), 0.001^2] (fixed)
       logsdc  ~  dnorm[log(0.001), 0.001^2] (fixed)
     logalpha  ~  dnorm[log(1), 2^2]

    Model parameter estimates w 95% CI 
                estimate        cilow        ciupp    log.est  
     alpha     0.5743521    0.0634165    5.2018063 -0.5545127  
     beta      0.0017992    0.0013421    0.0024121 -6.3203948  
     r         0.4470386    0.2960115    0.6751208 -0.8051103  
     rc        0.4470381    0.2960106    0.6751211 -0.8051115  
     rold      0.4470375    0.2960070    0.6751277 -0.8051128  
     m       283.3584775  225.2763663  356.4156689  5.6467128  
     K      2535.4292251 1695.0218973 3792.5181768  7.8381182  
     q         0.0003641    0.0002152    0.0006161 -7.9179770  
     n         2.0000025    1.9960864    2.0039263  0.6931484  
     sdb       0.0847776    0.0375251    0.1915316 -2.4677235  
     sdf       0.5557924    0.4145854    0.7450942 -0.5873604  
     sdi       0.0486922    0.0111643    0.2123672 -3.0222362  
     sdc       0.0010000    0.0009980    0.0010020 -6.9077552  
     
    Deterministic reference points (Drp)
              estimate       cilow        ciupp   log.est  
     Bmsyd 1267.715227 847.5103705 1896.2622211  7.144971  
     Fmsyd    0.223519   0.1480053    0.3375605 -1.498259  
     MSYd   283.358477 225.2763663  356.4156689  5.646713  
    Stochastic reference points (Srp)
               estimate       cilow        ciupp   log.est rel.diff.Drp  
     Bmsys 1254.7986098 840.3765744 1873.5881023  7.134730 -0.010293777  
     Fmsys    0.2217507   0.1467487    0.3350856 -1.506202 -0.007974404  
     MSYs   278.2295769 222.7296042  347.5590851  5.628447 -0.018434060  

    States w 95% CI (inp$msytype: s)
                        estimate        cilow        ciupp    log.est  
     B_1988.94      1783.5953912 1060.0172060 3001.0951723  7.4863865  
     F_1988.94         0.1384329    0.0664955    0.2881946 -1.9773698  
     B_1988.94/Bmsy    1.4214196    1.1384578    1.7747112  0.3516561  
     F_1988.94/Fmsy    0.6242726    0.3350390    1.1631970 -0.4711681  

    Predictions w 95% CI (inp$msytype: s)
                      prediction        cilow        ciupp    log.est  
     B_1990.00      1768.2779820 1032.9418961 3027.0889712  7.4777615  
     F_1990.00         0.1384331    0.0362094    0.5292472 -1.9773684  
     B_1990.00/Bmsy    1.4092126    1.0817635    1.8357803  0.3430311  
     F_1990.00/Fmsy    0.6242735    0.1729147    2.2538128 -0.4711667  
     Catch_1989.00   245.7838154  110.0711137  548.8241364  5.5044524  
     E(B_inf)       1702.9408294           NA           NA  7.4401119  

``` r
# plot results
plot(res_hake) #全体的な結果のプロット
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_fit1-1.png" width="80%" />

``` r
plotspict.priors(res_hake)#shape parameter(n)とalpha,betaの事前分布と事後分布
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_fit1-2.png" width="80%" />

 

# 3. Residuls and diagnostics  残差診断のやり方例
----------------------------------------------

``` r
#シナリオ3の結果を例に
res<-calc.osa.resid(res_hake)
plotspict.diagnostic(res)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_residual-1.png" width="80%" />
  

# 4. Retrospective plots レトロ解析のやり方例
-------------------------------------------

``` r
#シナリオ3の結果を例に
res<-retro(res_hake,nretroyear=5)
plotspict.retro(res)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_retro-1.png" width="80%" />

          FFmsy       BBmsy 
     0.01771116 -0.01836890 

``` r
plotspict.retro.fixed(res)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_retro-2.png" width="80%" />

``` r
mohns_rho(res,what=c("FFmsy","BBmsy")) #モーンズローの値
```

          FFmsy       BBmsy 
     0.01771116 -0.01836890 
  

# 5. Robust estimation 外れ値の影響の緩和の例
-------------------------------------------

``` r
inp <- test_data
 inp$obsC[10] <-3*inp$obsC[10] #わざと外れ値をつくってみる
 res1 <-fit.spict(inp) #外れ値の影響を特に気にせず通常の推定をした場合
 
 inp$robflagc <-1 #robust estimationをCatchデータに適用する場合
 #inp$robflagi <-1 #robust estimationをindexデータに適用する場合
 #inp$robflage <-1 #robust estimationをeffortデータに適用する場合
 
 res2 <-fit.spict(inp) #Cの外れ値を考慮した場合の推定
 sumspict.parest(res2)
```

               estimate        cilow        ciupp    log.est
    alpha  3.025826e+00 1.533404e-01 5.970781e+01  1.1071840
    beta   7.098228e-01 3.500466e-01 1.439375e+00 -0.3427399
    r      4.241222e-01 2.853478e-01 6.303872e-01 -0.8577337
    rc     3.137342e-01 1.996025e-01 4.931260e-01 -1.1592090
    rold   2.489413e-01 1.200241e-01 5.163277e-01 -1.3905380
    m      2.929635e+02 2.412458e+02 3.557684e+02  5.6800482
    K      3.348288e+03 2.341094e+03 4.788803e+03  8.1162046
    q      2.720300e-04 1.772300e-04 4.175200e-04 -8.2096041
    n      2.703703e+00 1.533024e+00 4.768360e+00  0.9946224
    sdb    2.485346e-02 1.665880e-03 3.707916e-01 -3.6947584
    sdf    4.035196e-01 2.519999e-01 6.461435e-01 -0.9075302
    sdi    7.520223e-02 4.697344e-02 1.203952e-01 -2.5875744
    sdc    2.864274e-01 1.814061e-01 4.522488e-01 -1.2502701
    pp     1.000000e+00 0.000000e+00 1.000000e+00 18.8219729
    robfac 1.147013e+00 1.000000e+00          Inf -1.9172340

``` r
 #外れ値を考慮に入れた推定(観察誤差の誤差分布として混合分布を仮定）では新たに，ppとrobfacという裾の分布の広がり度を示すパラが推定されるようになるが，必ずしも上手く推定できるとは限らない．上記の例でも推定は難しいので下記のようにして，裾の広がり度をgivenにする場合も多い.manualでは下記のようにすればよい，と書いてあるが走らせると，ppやrobfacにはphaseは指定できないと言われてしまう．
 
 #inp$phases$pp <--1
 #inp$phases$robfac <--1
 
 #inp$ini$pp <-0.95
 #inp$ini$robfac <-15 
```

# 6. Forecasting 将来予測のやり方例  
---------------------------------

``` r
inp <- test_data
 inp$maninterval <-c(1990,1991) #もし1990年から1年間の将来予測をしたいときのスタート年の終わりの年
 inp$ffac <-0.75 #Fishing mortality multiplier at the start of the forecastを指定．デフォルトは潜在的なseasonal patternをmaintainして将来予測される
 inp$maneval<-1991 #評価したい年を指定
 rep<-fit.spict(inp)
 
 sumspict.predictions(rep) #将来予測だけの結果を特にみたい場合
```

                     prediction        cilow        ciupp    log.est
    B_1991.00      1.950517e+03 1.046106e+03 3636.8385633  7.5758499
    F_1991.00      8.541414e-02 2.064521e-02    0.3533785 -2.4602436
    B_1991.00/Bmsy 1.293723e+00 9.959454e-01    1.6805325  0.2575239
    F_1991.00/Fmsy 4.858849e-01 1.330830e-01    1.7739620 -0.7217835
    Catch_1990.00  1.634251e+02 6.104824e+01  437.4861264  5.0963547
    E(B_inf)       2.152319e+03           NA           NA  7.6743011

``` r
 summary(rep) #いつも通りの結果をみたい場合
```

    Convergence: 0  MSG: relative convergence (4)
    Objective function at optimum: 4.4440893
    Euler time step (years):  1/16 or 0.0625
    Nobs C: 24,  Nobs I1: 24

    Priors
         logn  ~  dnorm[log(2), 2^2]
     logalpha  ~  dnorm[log(1), 2^2]
      logbeta  ~  dnorm[log(1), 2^2]

    Model parameter estimates w 95% CI 
                estimate        cilow        ciupp    log.est  
     alpha     1.7959743    0.0006129 5262.7763737  0.5855477  
     beta      0.4848746    0.1980997    1.1867933 -0.7238649  
     r         0.5090408    0.2963677    0.8743279 -0.6752272  
     rc        0.3529879    0.1760163    0.7078913 -1.0413216  
     rold      0.2701653    0.1057079    0.6904810 -1.3087214  
     m       266.8344900  223.2802584  318.8846411  5.5866286  
     K      2652.5400114 1615.5641813 4355.1154412  7.8832730  
     q         0.0003666    0.0002217    0.0006062 -7.9113255  
     n         2.8841828    1.5794289    5.2667834  1.0592416  
     sdb       0.0388151    0.0000694   21.7119809 -3.2489449  
     sdf       0.3980508    0.2543937    0.6228316 -0.9211758  
     sdi       0.0697110    0.0127255    0.3818818 -2.6633973  
     sdc       0.1930047    0.1022833    0.3641925 -1.6450406  
     
    Deterministic reference points (Drp)
               estimate       cilow        ciupp   log.est  
     Bmsyd 1511.8620969 821.5946811 2782.0615845  7.321097  
     Fmsyd    0.1764939   0.0880082    0.3539457 -1.734469  
     MSYd   266.8344900 223.2802584  318.8846411  5.586629  
    Stochastic reference points (Srp)
               estimate       cilow       ciupp   log.est rel.diff.Drp  
     Bmsys 1507.6780199 798.2694731 2847.525915  7.318326 -0.002775179  
     Fmsys    0.1757909   0.0908695    0.340075 -1.738460 -0.003999266  
     MSYs   265.0331320 228.5426991  307.349836  5.579855 -0.006796728  

    States w 95% CI (inp$msytype: s)
                        estimate       cilow        ciupp    log.est  
     B_1988.94      1844.1703430 953.7479560 3565.8941469  7.5197848  
     F_1988.94         0.1138851   0.0475966    0.2724948 -2.1725652  
     B_1988.94/Bmsy    1.2231858   0.9550639    1.5665795  0.2014588  
     F_1988.94/Fmsy    0.6478442   0.3384197    1.2401823 -0.4341051  

    Predictions w 95% CI (inp$msytype: s)
                      prediction        cilow        ciupp    log.est  
     B_1991.00      1950.5173470 1046.1058017 3636.8385633  7.5758499  
     F_1991.00         0.0854141    0.0206452    0.3533785 -2.4602436  
     B_1991.00/Bmsy    1.2937227    0.9959454    1.6805325  0.2575239  
     F_1991.00/Fmsy    0.4858849    0.1330830    1.7739620 -0.7217835  
     Catch_1990.00   163.4250853   61.0482410  437.4861264  5.0963547  
     E(B_inf)       2152.3190221           NA           NA  7.6743012  

``` r
 plot(rep) #plotみたいとき
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_forecast-1.png" width="80%" />

# 7. Management 管理 　　
-----------------------

　上記の方法でも将来予測出来るが，SPiCTにはデフォルトで8種類の管理方策が備わっている．  
1. currentCatch: 最終年の漁獲量を続ける 　  
2. currentF:最終年のFを用いる  
3. Fmsy: Fmsyで漁獲    
4. noF No fishing: 現在のFの1％まで減らす    
5. reduceF25: Fを25%減らす    
6. increaseF25: Fを25%増やす   
7. msyHockeyStick: ICESのMSYのH-S ruleを適用  
8. ices: ICES MSY35番目のH-S advice rule使う    
 
 - ?manageと検索すると色々と詳しい説明が出てくる

``` r
 inp <- test_data
 rep <- fit.spict(inp)
 rep <- manage(rep)
```

    Selected scenario(s):  currentCatch, currentF, Fmsy, noF, reduceF25, increaseF25, msyHockeyStick, ices  

``` r
 sumspict.manage(rep) #管理方策を適用した場合の結果のまとめの出力
```

    SPiCT timeline:
                                                      
          Observations              Management        
        1965.00 - 1989.00        1989.00 - 1990.00    
     |-----------------------| ----------------------|

    Management evaluation: 1990.00

    Predicted catch for management period and states at management evaluation time:

                                 C B/Bmsy F/Fmsy
    1. Keep current catch    197.4   1.25   0.60
    2. Keep current F        212.0   1.25   0.65
    3. Fish at Fmsy          318.6   1.18   1.00
    4. No fishing              0.2   1.38   0.00
    5. Reduce F by 25%       161.0   1.28   0.49
    6. Increase F by 25%     261.8   1.21   0.81
    7. MSY hockey-stick rule 318.6   1.18   1.00
    8. ICES advice rule      278.7   1.20   0.87

``` r
 plot2(rep) #様々な管理方策の結果を図示
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_management-1.png" width="80%" />

``` r
 plotspict.hcr(rep) #様々な管理方策そのもののルールを図示
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_management-2.png" width="80%" />
  
    

## 7-1. 評価と管理の間の期間の指定

``` r
 man.timeline(inp) #観察事象の年代と将来予測（管理）の年代の表示
```

    SPiCT timeline:
                                                      
          Observations              Management        
        1965.00 - 1989.00        1989.00 - 1990.00    
     |-----------------------| ----------------------|

    Management evaluation: 1990.00

``` r
 man.timeline(rep) #観察事象の年代と将来予測（管理）の年代の表示(上記でもこれでもどっちでもOK）
```

    SPiCT timeline:
                                                      
          Observations              Management        
        1965.00 - 1989.00        1989.00 - 1990.00    
     |-----------------------| ----------------------|

    Management evaluation: 1990.00

``` r
 inp$maninterval <-c(1990,1991) #もし1990年から1年間の将来予測をしたいときのスタート年の終わりの年
 man.timeline(inp) #intermediate periodが表示されるようになる
```

    SPiCT timeline:
                                                                              
          Observations             Intermediate             Management        
        1965.00 - 1989.00        1989.00 - 1990.00       1990.00 - 1991.00    
     |-----------------------| ----------------------| ----------------------|

    Management evaluation: 1991.00

``` r
 repIntPer <-manage(rep, scenarios=c(1,2), maninterval=c(1990,1991),maneval=1991) #1991年から管理方策１，２の管理を実施するように指定
```

    Selected scenario(s):  currentCatch, currentF  

``` r
 plotspict.catch(repIntPer)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_inter-1.png" width="80%" />

``` r
 #intermediate periodの間のCatchレベルを指定してたい場合（↓）．デフォルトでは，最終年と同じFを使う．
  repIntPer <-manage(rep, scenarios=c(8), maninterval=c(1990,1991), intermediatePeriodCatch=5)
```

    Selected scenario(s):  ices  

``` r
  par(mfrow=c(1,2))
  plotspict.biomass(repIntPer)
  plotspict.catch(repIntPer)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_inter-2.png" width="80%" />

## 7-2. 自分でつくった管理方策を適用する場合
add.man.scenarioの引数で大事なもの：  
 - ffac: 現在のFの何倍にするのかを指定  
 - cfac: 現在のCの何倍にするのかを指定　 
 - breakpointBBmsy: H-S
HCRの場合のB/Bmsyの折れ点の位置．デフォルトでは折れ点なし
などなど（詳細はhandbook p51参照）

``` r
 repIntPer <- add.man.scenario(repIntPer,ffac=1.5) #例えば，漁獲係数を50%増加のようなシナリオをrepIntPerに加えたい場合
 sumspict.manage(repIntPer)
```

    SPiCT timeline:
                                                                              
          Observations             Intermediate             Management        
        1965.00 - 1989.00        1989.00 - 1990.00       1990.00 - 1991.00    
     |-----------------------| ----------------------| ----------------------|

    Management evaluation: 1990.00

    Predicted catch for management period and states at management evaluation time:

                                C B/Bmsy F/Fmsy
    1. ICES advice rule (@) 328.2   1.40   0.91
    2. customScenario_1     310.3   1.18   0.97
    (@) This scenario assumes another management period. Thus, the estimates might not be comparable to the other scenarios.

``` r
 plotspict.f(repIntPer)
```

<img src="SPiCT_how_to_files/figure-markdown_github/hake_management2-1.png" width="80%" />

``` r
 #例えば，最終年に観察されたCatchの64%減での将来予測をしたい場合,そしてそのシナリオ名を"reduced_catch"と名付けたい場合
 repIntPer <-add.man.scenario(repIntPer, cfac=0.64, scenarioTitle="reduced_catch")

 names(repIntPer$man)
```

    [1] "ices"             "customScenario_1" "reduced_catch"
