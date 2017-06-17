Ahora que tenemos nuestros datos en una matriz de términos, podemos
empezar a hacer nuestros análisis.

    library(topicmodels)
    library(tm)

    ## Loading required package: NLP

    dtm <- readRDS("./dta/dtm.RDS")

El paquete `topicmodels` ofrece una función `LDA` para la estimación de
Latent Dirichlet Allocation models. Esta función toma tres argumentos
principales: 1. una matriz de términos como la que acabamos de
construir, 2. el número de temas que creemos que existen en nuestra base
de datos, y 3. el método de estimación. Con ello, nuestro modelo tendría
el siguiente aspecto.

    lda_model <- LDA(dtm,
                  5,
                  method="Gibbs")
    lda_model

    ## A LDA_Gibbs topic model with 5 topics.

La estimación mediante *Gibbs sampler* es una forma común de estimar
este tipo de modelos, aunque los fundamentos de estos métodos de
simulación nos llevaría por derroteros muy alejados de los contenidos
del curso. Nos conformaremos por ahora con fijar valores que son
*razonables* pero que seguramente requerirán ajuste para situaciones
reales. Lo importante ahora es entender intuitivamente qué hace cada uno
de los parámetros y cómo manipularlos si nuestra simulación no funciona
correctamente.

Una versión más completa que incorpora detalles de cómo queremos que se
ejecute la simulación es la siguiente:

    k <- 5
    burnin <- 4000
    iter <- 2000
    thin <- 500
    seed <- 2003
    nstart <- 1
    best <- TRUE
    ctrl <- list(nstart=nstart,
                 seed=seed,
                 best=best,
                 burnin=burnin,
                 iter=iter,
                 thin=thin)

    lda_model <- LDA(dtm,
                     k,
                     method="Gibbs",
                     control=ctrl)

Aquí estamos fijando el número de iteraciones que queremos descartar de
las estimación final porque forman parte de la transición a la
distribución estacionaria (`burnin`), el número de iteraciones que
queremos escoger después de que el modelo haya terminado el *burn-in
period* (`iter`) y el *offset* entre iteraciones (`thin`). El parámetro
`seed` es la semilla del generador de números pseudo-aleatorios.

Ahora que hemos ajustado el modelo, podemos ver la distribución de
términos en cada tema

    terms(lda_model, 5)

    ##      Topic 1    Topic 2   Topic 3   Topic 4    Topic 5 
    ## [1,] "presid"   "fund"    "senat"   "senat"    "health"
    ## [2,] "nation"   "program" "bill"    "state"    "care"  
    ## [3,] "american" "depart"  "million" "law"      "famili"
    ## [4,] "peopl"    "grant"   "includ"  "committe" "year"  
    ## [5,] "iraq"     "servic"  "develop" "requir"   "provid"

O podemos ver también qué tema se asocia con cada uno de los documentos,
si asumimos que el tema de mayor probabilidad es al mismo tiempo el tema
común de un documento.

    head(topics(lda_model))

    ## 24Sep2007Domenici221     7Feb2007Levin196    31Mar2006Obama561 
    ##                    1                    2                    4 
    ## 18Sep2007Voinovich71 17Oct2007Bingaman137     17Mar2006Reid156 
    ##                    3                    3                    3

Una visión más completa la podemos ver en la matriz `gamma` que muestra
la probabilidad de cada tema en cada documento.

    as.data.frame(lda_model@gamma)

    ##          V1     V2     V3     V4     V5
    ## 1    0.2331 0.2180 0.1579 0.2105 0.1805
    ## 2    0.0599 0.7512 0.0599 0.0553 0.0737
    ## 3    0.3010 0.1684 0.0816 0.3776 0.0714
    ## 4    0.1287 0.2178 0.4109 0.0743 0.1683
    ## 5    0.1825 0.1587 0.2937 0.1667 0.1984
    ## 6    0.2926 0.1004 0.3013 0.1135 0.1921
    ## 7    0.1200 0.2200 0.1200 0.0900 0.4500
    ## 8    0.2733 0.1200 0.2200 0.3000 0.0867
    ## 9    0.2632 0.1263 0.2526 0.2421 0.1158
    ## 10   0.1423 0.0988 0.0632 0.1621 0.5336
    ## 11   0.1131 0.1905 0.1488 0.2857 0.2619
    ## 12   0.2958 0.1831 0.0986 0.2113 0.2113
    ## 13   0.1071 0.2321 0.1726 0.1786 0.3095
    ## 14   0.1220 0.0576 0.1898 0.1356 0.4949
    ## 15   0.0647 0.6294 0.0941 0.1294 0.0824
    ## 16   0.2029 0.3116 0.1667 0.1594 0.1594
    ## 17   0.1691 0.1063 0.4300 0.1256 0.1691
    ## 18   0.1831 0.2254 0.1408 0.3521 0.0986
    ## 19   0.1016 0.4922 0.1406 0.1719 0.0938
    ## 20   0.1714 0.1048 0.1190 0.2333 0.3714
    ## 21   0.2323 0.2929 0.2222 0.1313 0.1212
    ## 22   0.1008 0.1261 0.2773 0.1218 0.3739
    ## 23   0.2201 0.1006 0.3962 0.0943 0.1887
    ## 24   0.1415 0.2358 0.2972 0.1462 0.1792
    ## 25   0.0660 0.3924 0.2743 0.1875 0.0799
    ## 26   0.1842 0.3421 0.1842 0.1140 0.1754
    ## 27   0.4158 0.1368 0.1263 0.1737 0.1474
    ## 28   0.1669 0.0609 0.0517 0.3854 0.3351
    ## 29   0.1150 0.2800 0.2150 0.1150 0.2750
    ## 30   0.0986 0.3099 0.3451 0.1056 0.1408
    ## 31   0.1099 0.0884 0.1336 0.3168 0.3513
    ## 32   0.3315 0.1304 0.1304 0.3098 0.0978
    ## 33   0.1538 0.1978 0.1758 0.1648 0.3077
    ## 34   0.2913 0.1732 0.1654 0.1575 0.2126
    ## 35   0.0656 0.6718 0.0695 0.0927 0.1004
    ## 36   0.3272 0.0897 0.2507 0.2375 0.0950
    ## 37   0.2250 0.1500 0.2062 0.2750 0.1437
    ## 38   0.2622 0.0854 0.3415 0.1890 0.1220
    ## 39   0.1016 0.2114 0.3659 0.2317 0.0894
    ## 40   0.2209 0.1105 0.1977 0.3488 0.1221
    ## 41   0.2977 0.0465 0.1349 0.0930 0.4279
    ## 42   0.2199 0.1205 0.1325 0.1355 0.3916
    ## 43   0.1659 0.1220 0.1927 0.2220 0.2976
    ## 44   0.1624 0.1880 0.1197 0.2222 0.3077
    ## 45   0.1048 0.4516 0.1694 0.1371 0.1371
    ## 46   0.1574 0.0671 0.1633 0.3236 0.2886
    ## 47   0.1099 0.0509 0.2011 0.2922 0.3458
    ## 48   0.0650 0.6626 0.0732 0.0894 0.1098
    ## 49   0.1343 0.1157 0.2127 0.2239 0.3134
    ## 50   0.3414 0.0985 0.0547 0.4420 0.0635
    ## 51   0.2297 0.1284 0.1892 0.1014 0.3514
    ## 52   0.0959 0.3506 0.3173 0.1107 0.1255
    ## 53   0.2333 0.1000 0.1800 0.2200 0.2667
    ## 54   0.1942 0.1007 0.1942 0.1079 0.4029
    ## 55   0.1837 0.1293 0.1020 0.3061 0.2789
    ## 56   0.2469 0.1276 0.2305 0.1276 0.2675
    ## 57   0.1895 0.1368 0.2105 0.3474 0.1158
    ## 58   0.1932 0.2159 0.1591 0.2500 0.1818
    ## 59   0.2037 0.1296 0.1667 0.3333 0.1667
    ## 60   0.0496 0.6322 0.0826 0.0826 0.1529
    ## 61   0.1717 0.1717 0.1313 0.2121 0.3131
    ## 62   0.1579 0.1118 0.1184 0.5132 0.0987
    ## 63   0.0929 0.2389 0.1726 0.1460 0.3496
    ## 64   0.3143 0.2286 0.1238 0.1810 0.1524
    ## 65   0.1446 0.4217 0.1767 0.1847 0.0723
    ## 66   0.1138 0.1976 0.1856 0.1617 0.3413
    ## 67   0.2548 0.1083 0.1210 0.1592 0.3567
    ## 68   0.1511 0.1801 0.1672 0.1383 0.3633
    ## 69   0.1830 0.1362 0.2936 0.1362 0.2511
    ## 70   0.1265 0.1265 0.1566 0.2470 0.3434
    ## 71   0.1568 0.1525 0.2881 0.0805 0.3220
    ## 72   0.0591 0.1682 0.3500 0.1818 0.2409
    ## 73   0.1316 0.1474 0.4263 0.1211 0.1737
    ## 74   0.1197 0.1268 0.3592 0.1620 0.2324
    ## 75   0.0973 0.1479 0.1051 0.4008 0.2490
    ## 76   0.4497 0.0888 0.2012 0.1095 0.1509
    ## 77   0.2182 0.2273 0.1818 0.2545 0.1182
    ## 78   0.3245 0.2604 0.1358 0.1660 0.1132
    ## 79   0.2500 0.1098 0.4268 0.0793 0.1341
    ## 80   0.1284 0.2905 0.3176 0.0878 0.1757
    ## 81   0.1620 0.2676 0.1690 0.2958 0.1056
    ## 82   0.2337 0.2174 0.1087 0.2663 0.1739
    ## 83   0.1693 0.1323 0.1799 0.2910 0.2275
    ## 84   0.2172 0.1515 0.1818 0.3182 0.1313
    ## 85   0.2528 0.1264 0.3271 0.1673 0.1264
    ## 86   0.1057 0.2602 0.2602 0.1138 0.2602
    ## 87   0.1294 0.2706 0.3412 0.1118 0.1471
    ## 88   0.2917 0.1944 0.1389 0.1667 0.2083
    ## 89   0.1743 0.1577 0.1577 0.1577 0.3527
    ## 90   0.1606 0.1365 0.3494 0.1888 0.1647
    ## 91   0.2316 0.2421 0.2211 0.2000 0.1053
    ## 92   0.0463 0.6795 0.0772 0.0618 0.1351
    ## 93   0.1792 0.2075 0.2358 0.2264 0.1509
    ## 94   0.1012 0.2918 0.3191 0.1128 0.1751
    ## 95   0.3621 0.1638 0.1897 0.1552 0.1293
    ## 96   0.1257 0.4371 0.1317 0.1497 0.1557
    ## 97   0.1413 0.1848 0.1848 0.1413 0.3478
    ## 98   0.1803 0.2459 0.1803 0.2131 0.1803
    ## 99   0.3986 0.0653 0.0968 0.2995 0.1396
    ## 100  0.0880 0.1157 0.2037 0.1343 0.4583
    ## 101  0.3096 0.1066 0.2234 0.2690 0.0914
    ## 102  0.1637 0.1504 0.2522 0.1858 0.2478
    ## 103  0.2805 0.0679 0.1403 0.4253 0.0860
    ## 104  0.1582 0.1695 0.2034 0.1977 0.2712
    ## 105  0.1260 0.2913 0.1260 0.2756 0.1811
    ## 106  0.2356 0.2719 0.1118 0.2598 0.1208
    ## 107  0.1973 0.0852 0.1839 0.3722 0.1614
    ## 108  0.2975 0.1322 0.2231 0.2066 0.1405
    ## 109  0.3030 0.0909 0.1039 0.2381 0.2641
    ## 110  0.1387 0.2370 0.1734 0.1734 0.2775
    ## 111  0.1066 0.3033 0.1639 0.2459 0.1803
    ## 112  0.1976 0.1617 0.1557 0.2695 0.2156
    ## 113  0.2522 0.1565 0.2348 0.2174 0.1391
    ## 114  0.4083 0.1167 0.1333 0.2250 0.1167
    ## 115  0.2459 0.1639 0.1803 0.2459 0.1639
    ## 116  0.1975 0.2739 0.1529 0.2866 0.0892
    ## 117  0.1347 0.1295 0.1036 0.5285 0.1036
    ## 118  0.4426 0.1803 0.0902 0.1803 0.1066
    ## 119  0.1854 0.0899 0.3371 0.1629 0.2247
    ## 120  0.1781 0.2055 0.2192 0.2192 0.1781
    ## 121  0.0825 0.1649 0.3162 0.2371 0.1993
    ## 122  0.0985 0.2069 0.1133 0.4286 0.1527
    ## 123  0.2637 0.2088 0.1538 0.2308 0.1429
    ## 124  0.3289 0.0361 0.2928 0.1217 0.2205
    ## 125  0.0884 0.5782 0.0952 0.1429 0.0952
    ## 126  0.1257 0.1257 0.4731 0.1677 0.1078
    ## 127  0.0909 0.1962 0.2584 0.1148 0.3397
    ## 128  0.2414 0.3218 0.1379 0.1494 0.1494
    ## 129  0.4519 0.1538 0.1154 0.1250 0.1538
    ## 130  0.3009 0.1239 0.1504 0.2566 0.1681
    ## 131  0.1017 0.3644 0.2034 0.2119 0.1186
    ## 132  0.1826 0.1478 0.2000 0.2957 0.1739
    ## 133  0.2400 0.2533 0.1600 0.1867 0.1600
    ## 134  0.1795 0.3436 0.1385 0.1949 0.1436
    ## 135  0.2514 0.1045 0.1469 0.2119 0.2853
    ## 136  0.2232 0.3214 0.1429 0.2143 0.0982
    ## 137  0.3019 0.1302 0.2094 0.1075 0.2509
    ## 138  0.2617 0.1495 0.1589 0.3271 0.1028
    ## 139  0.1583 0.1889 0.5000 0.0889 0.0639
    ## 140  0.2073 0.1341 0.1585 0.3537 0.1463
    ## 141  0.2000 0.1407 0.1259 0.3852 0.1481
    ## 142  0.1321 0.1384 0.3711 0.2013 0.1572
    ## 143  0.1983 0.1293 0.3448 0.1897 0.1379
    ## 144  0.0536 0.6830 0.0670 0.0670 0.1295
    ## 145  0.1484 0.1806 0.2968 0.2129 0.1613
    ## 146  0.1064 0.1319 0.4468 0.1957 0.1191
    ## 147  0.1139 0.1076 0.1709 0.0886 0.5190
    ## 148  0.2622 0.1159 0.1463 0.2500 0.2256
    ## 149  0.1370 0.2041 0.2653 0.1720 0.2216
    ## 150  0.1382 0.1579 0.1250 0.2105 0.3684
    ## 151  0.0892 0.2611 0.1146 0.4586 0.0764
    ## 152  0.2647 0.1373 0.1765 0.2157 0.2059
    ## 153  0.0927 0.2748 0.1661 0.1118 0.3546
    ## 154  0.0382 0.3506 0.3720 0.1653 0.0739
    ## 155  0.4123 0.0702 0.1228 0.2588 0.1360
    ## 156  0.4158 0.1188 0.1337 0.1287 0.2030
    ## 157  0.1971 0.1314 0.1387 0.2336 0.2993
    ## 158  0.1667 0.1377 0.2101 0.3841 0.1014
    ## 159  0.0827 0.1492 0.0867 0.2097 0.4718
    ## 160  0.2057 0.1314 0.4571 0.0857 0.1200
    ## 161  0.3224 0.1243 0.2802 0.1981 0.0750
    ## 162  0.1111 0.2407 0.2407 0.2685 0.1389
    ## 163  0.1610 0.2034 0.2373 0.2119 0.1864
    ## 164  0.3186 0.1373 0.2402 0.1520 0.1520
    ## 165  0.1075 0.1582 0.1284 0.4328 0.1731
    ## 166  0.1750 0.1375 0.1500 0.3500 0.1875
    ## 167  0.2200 0.2222 0.2111 0.0800 0.2667
    ## 168  0.1399 0.1111 0.1934 0.1111 0.4444
    ## 169  0.1714 0.1286 0.3643 0.2286 0.1071
    ## 170  0.2522 0.1565 0.3130 0.1130 0.1652
    ## 171  0.1909 0.1784 0.3154 0.1784 0.1369
    ## 172  0.1634 0.0784 0.3824 0.1111 0.2647
    ## 173  0.0864 0.3951 0.1173 0.1728 0.2284
    ## 174  0.2050 0.1801 0.3540 0.1491 0.1118
    ## 175  0.4218 0.0816 0.1565 0.1905 0.1497
    ## 176  0.1274 0.5924 0.0892 0.1019 0.0892
    ## 177  0.1988 0.1696 0.0936 0.2456 0.2924
    ## 178  0.1974 0.2500 0.0987 0.3092 0.1447
    ## 179  0.2602 0.1224 0.1633 0.1020 0.3520
    ## 180  0.3529 0.1765 0.0909 0.1497 0.2299
    ## 181  0.2194 0.0903 0.1290 0.2258 0.3355
    ## 182  0.3628 0.1062 0.1504 0.2743 0.1062
    ## 183  0.4710 0.1742 0.1355 0.0903 0.1290
    ## 184  0.3258 0.2576 0.1212 0.1515 0.1439
    ## 185  0.1262 0.2136 0.3010 0.2136 0.1456
    ## 186  0.0854 0.1960 0.4623 0.1256 0.1307
    ## 187  0.2087 0.1304 0.2000 0.2957 0.1652
    ## 188  0.1379 0.1494 0.1322 0.4540 0.1264
    ## 189  0.3462 0.1346 0.2115 0.1827 0.1250
    ## 190  0.3907 0.0789 0.1828 0.2581 0.0896
    ## 191  0.1887 0.0802 0.1132 0.1179 0.5000
    ## 192  0.1048 0.1976 0.1653 0.2500 0.2823
    ## 193  0.3392 0.1111 0.1345 0.2339 0.1813
    ## 194  0.2087 0.1699 0.2718 0.2087 0.1408
    ## 195  0.2951 0.0830 0.1025 0.1113 0.4081
    ## 196  0.1371 0.2742 0.1129 0.3952 0.0806
    ## 197  0.0620 0.6364 0.1074 0.0744 0.1198
    ## 198  0.4636 0.1182 0.1545 0.1727 0.0909
    ## 199  0.4983 0.0475 0.1322 0.1627 0.1593
    ## 200  0.1059 0.2118 0.4863 0.1059 0.0902
    ##  [ reached getOption("max.print") -- omitted 800 rows ]

Por supuesto, el problema de la estrategia que hemos utilizado es que
hemos fijado un número de temas que puede no ser el correcto ¿Cómo
escoger cuál es el número de temas si no tenemos información *ex ante*?
La mejor estrategia, al igual que vimos en el caso de los modelos de
agrupamiento, es estimar el modelo para un gran número de temas y
comprobar qué tan bien cada supuesto ajusta los datos.

El límite en este caso es que cada uno de los modelos consume mucho
tiempo. Para esquivar ese problema, lo que haremos será estimar en
paralelo un modelo con un número diferente de temas. Dependiendo del
número de núcleos que tenga nuestra máquina, esto puede rebajar muy
considerablemente el tiempo que tenemos que esperar.

Empezaremos por cargar en nuestra sesión los paquetes de `R` para
realizar computaciones en paralelo.

    library(parallel)
    library(doMC); library(doParallel)

    ## Loading required package: foreach

    ## Loading required package: iterators

    cl <- makeCluster(detectCores() - 1)
    registerDoParallel(cl)

En este caso, hemos definido un cluster de tantos núcleos como tenga la
máquina en la que estamos trabajando, reservando uno para el sistema
operativo.

Ahora podemos estimar nuestro modelo para una serie de modelos que
asumen un número de temas entre 2 y 29.

    mlist <- foreach(i=seq(2, 29, 3),
                     .packages="topicmodels",
                     .export=c("ctrl", "dtm")) %dopar% {
        out <- LDA(dtm,
                   i,
                   method="Gibbs",
                   control=ctrl)
        return(out)
    }

    ## Warning in e$fun(obj, substitute(ex), parent.frame(), e$data): already
    ## exporting variable(s): ctrl, dtm

Aquí estamos usando la función `foreach` que es similar a `for` pero
intenta enviar cada iteración a un proceso diferente. El número de temas
es el índice de la iteración y solo tenemos que tener en cuenta que
tenemos que exportar explícitamente los objetos que el cuerpo de la
iteración necesita para poder trabajar. Pensad en ello como lo que una
nueva sesión de `R` necesitaría para poder hacer el análisis que hemos
solicitado.

Lo que tenemos es una lista `mlist` que contiene, en cada elemento, el
resultado de estimar un modelo LDA con un número diferente de temas
¿Cómo podeoms escoger entre estas alternativas? Las formas más comunes
son fijándose en la verosimilitud de cada modelo o en su perplejidad,
que es una medida de la capacidad del modelo para capturar la estructura
de nuevos datos. Idealmente, haríamos esta estimación usando validación
cruzada, que veremos en la segunda mitad del curso, pero por ahora nos
limitaremos a ver cómo recuperar estas dos cantidades.

    mlogLik <- as.data.frame(as.matrix(lapply(mlist, logLik)))
    ## mperp <- as.data.frame(as.matrix(lapply(mlist, perplexity)))

Y podemos crear gráficos para ver con más claridad qué tan bien funciona
cada modelo:

    plot(seq(2, 29, 3),
         unlist(mlogLik),
         xlab="Número de temas",
         ylab="Log-Verosimilitud")

![](./assets/unnamed-chunk-10-1.png)
