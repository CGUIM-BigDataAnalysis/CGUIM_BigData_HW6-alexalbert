台灣Pokémon GO地圖&汽機車違規紀錄相關性
================

李士閎、林宇辰分析議題背景
--------------------------

寶可夢GO是一款基於位置服務的擴增實境類手機遊戲，由任天堂公司、精靈寶可夢公司授權，Niantic, Inc.負責開發和營運。於2016年7月起在iOS和Android平台上發布。該遊戲允許玩家以現實世界為平台，捕捉、戰鬥、訓練和交易虛擬怪獸「寶可夢」。 台灣去年也盛行一時，因而產生一些問題，像是有些虛擬怪物會出現在特別的地方(例如：海邊、山區等等偏僻處)，使玩家會讓自己深入危險處，也有一些玩家會一邊開(騎)車一邊抓寶，以上問題皆可能造成自身安全受害。

分析動機
--------

自始Pokemon Go盛行，政府一再呼籲民眾玩樂之餘也要注意交通安全，卻仍在新聞媒體層出不窮出現因為玩寶可夢而致使交通打結的景況，欲藉由此期末專題了解交通是否因為寶可夢的盛行而有所影響，受傷人數多的區域是否皆為相同的寶可夢。

使用資料
--------

A2\_record是事故類別：A2類的交通資料紀錄，A2的意思是交通事故當事人一人以上受傷(不管事輕傷或重傷)或超過24小時後死亡。 pokemon是全台各縣市寶可夢巢穴名稱及地點。

``` r
library(readxl)
```

    ## Warning: package 'readxl' was built under R version 3.3.3

``` r
A2_record2 <- read_excel("C:/Users/user/Desktop/A2 record2.xlsx", 
    skip = 2)
```

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E145012 / R145012C5: got '頝舫�餈�(��)'

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E177678 / R177678C5: got '��)(��)'

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E201565 / R201565C5: got '��)'

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E260891 / R260891C5: got '摰�銵�139撌�(��)'

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E267664 / R267664C5: got '�楝��(��)'

    ## Warning in read_fun(path = path, sheet = sheet, limits = limits, shim =
    ## shim, : Expecting numeric in E282330 / R282330C5: got '銝銵(��)'

``` r
View(A2_record2)

library(readxl)
pokemon2 <- read_excel("C:/Users/user/Desktop/pokemon2.xlsx")
View(pokemon2)
```

資料處理與清洗
--------------

我們將上面兩個匯進來的資料A2\_record2和pokemon2用inner\_join依照「發生地點=巢穴地址」合併在一塊兒，接著由於發生地點的最低層級包含了鄉、鎮、市、區等，我們只針對"區"進行分析，因此搜尋字串grepl有「區」的發生地點。

``` r
library(dplyr)
```

    ## Warning: package 'dplyr' was built under R version 3.3.3

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
A2popkemon2_join <- inner_join(A2_record2,pokemon2,by=c("發生地點"="巢穴地址"))
A2popkemon2_join2 <- A2popkemon2_join[grepl("區",A2popkemon2_join$發生地點),]
```

探索式資料分析
--------------

我們將上面得出來的A2popkemon2\_join2用發生地點分組，並算出各發生地點的「死亡人數」和「受傷人數」，依照多寡順序以大排至小做遞減，最後以圖表呈現最多人數的前十名。

``` r
library(dplyr)
SumofHurt <- group_by(A2popkemon2_join2, place=`發生地點`)%>%
    summarise(ndeath=sum(`死亡人數`),
              ninjuries=sum(`受傷人數`))%>%
      arrange(desc(ninjuries))
head(SumofHurt,10)
```

    ## # A tibble: 10 × 3
    ##           place ndeath ninjuries
    ##           <chr>  <dbl>     <dbl>
    ## 1  高雄市三民區      0     70525
    ## 2  高雄市鳳山區      0     61369
    ## 3  桃園市桃園區      0     60032
    ## 4  新北市板橋區      0     58698
    ## 5  高雄市前鎮區      0     39571
    ## 6  新北市三重區      0     29085
    ## 7  高雄市左營區      0     26975
    ## 8  高雄市苓雅區      0     18816
    ## 9  新北市新店區      0     14940
    ## 10 新北市新莊區      0     14212

期末專題分析規劃
----------------

期末專題要做寶可夢地圖及交通違規事故交叉分析，了解發生地點之寶可夢散布情形。
