Tokenización
------------

En esta sección veremos tareas sencillas de reconocimiento de entidades.
Para ello usaremos tres librerías nuevas, una de las cuales (`openNLP`)
es una interfaz a una librería en Java. Usaremos además modelos
pre-entrenados que no están disponibles en CRAN (depbido debido a la
política del repositorio):

    library(NLP)
    library(openNLP)

    install.packages("openNLPmodels.en",
                     repos="http://datacube.wu.ac.at/",
                     type="source")

    ## Installing package into '/usr/local/lib/R/site-library'
    ## (as 'lib' is unspecified)

Vamos a hacer una tarea sencilla de separar un texto en oraciones y en
palabras. En concreto empezaremos con uno de los textos que estudiaremos
en la siguiente sección

    text <- readLines("./dta/senate-releases/Biden/01Mar2006Biden9.txt")

    ## Warning in readLines("./dta/senate-releases/Biden/01Mar2006Biden9.txt"):
    ## incomplete final line found on './dta/senate-releases/Biden/
    ## 01Mar2006Biden9.txt'

y lo transformaremos en un formato más adecuado para trabajar con `NLP`:

    text <- as.String(text)

Las funciones en `openNLP` funcionan siempre del mismo modo. En primer
lugar, inicializamos un *anotador* y después aplicamos ese anotador a un
texto. Por ejemplo, podemos empezar por anotar palabras y oraciones
utilizando las funciones:

    word_ann <- Maxent_Word_Token_Annotator()
    sent_ann <- Maxent_Sent_Token_Annotator()

y a continuación anotamos el texto aplicando estos dos objetos al texto
que queremos analizar:

    annotated_text <- annotate(text, list(sent_ann, word_ann))

Lo que obtenemos es un objeto de clase

    class(annotated_text)

    ## [1] "Annotation" "Span"

que en realidad se parece mucho a un `data.frame` en el que tenemos la
lista de oraciones y palabras identificadas por la posición en la que
empiezan y en la que acaban. Podemos, por ejemplo, recuperar el total de
oraciones:

    subset(annotated_text, type=="sentence")

    ##  id type     start end  features
    ##   1 sentence    15  215 constituents=<<integer,29>>
    ##   2 sentence   217  451 constituents=<<integer,40>>
    ##   3 sentence   453  589 constituents=<<integer,23>>
    ##   4 sentence   591  635 constituents=<<integer,10>>
    ##   5 sentence   637  820 constituents=<<integer,30>>
    ##   6 sentence   822  858 constituents=<<integer,8>>
    ##   7 sentence   860 1024 constituents=<<integer,30>>
    ##   8 sentence  1026 1137 constituents=<<integer,21>>
    ##   9 sentence  1139 1710 constituents=<<integer,94>>
    ##  10 sentence  1712 1797 constituents=<<integer,15>>
    ##  11 sentence  1799 2044 constituents=<<integer,36>>
    ##  12 sentence  2046 2276 constituents=<<integer,41>>
    ##  13 sentence  2278 2336 constituents=<<integer,13>>
    ##  14 sentence  2338 2529 constituents=<<integer,29>>
    ##  15 sentence  2531 2629 constituents=<<integer,16>>
    ##  16 sentence  2631 2831 constituents=<<integer,31>>
    ##  17 sentence  2833 3022 constituents=<<integer,36>>
    ##  18 sentence  3024 3101 constituents=<<integer,14>>
    ##  19 sentence  3103 3392 constituents=<<integer,48>>
    ##  20 sentence  3394 3493 constituents=<<integer,17>>
    ##  21 sentence  3495 3683 constituents=<<integer,35>>
    ##  22 sentence  3685 3772 constituents=<<integer,19>>
    ##  23 sentence  3774 3868 constituents=<<integer,19>>
    ##  24 sentence  3870 3915 constituents=<<integer,11>>

y podemos comprobar el contenido de esta oración

    subset(annotated_text, type=='sentence')[[4]]$features

    ## [[1]]
    ## [[1]]$constituents
    ##  [1] 117 118 119 120 121 122 123 124 125 126

    text[subset(annotated_text, type=='sentence')[[4]]]

    ## The White House has come around to this view.

También podríamos recuperar las palabras que constituyen el texto de
modo similar

    subset(annotated_text, type=="word")

    ##  id  type start end  features
    ##   25 word    15   20 
    ##   26 word    22   25 
    ##   27 word    27   33 
    ##   28 word    35   42 
    ##   29 word    57   61 
    ##   30 word    63   63 
    ##   31 word    64   64 
    ##   32 word    66   69 
    ##   33 word    73   76 
    ##   34 word    78   82 
    ##   35 word    84   93 
    ##   36 word    95  102 
    ##   37 word   104  105 
    ##   38 word   107  109 
    ##   39 word   111  114 
    ##   40 word   116  122 
    ##   41 word   124  125 
    ##   42 word   127  131 
    ##   43 word   133  133 
    ##   44 word   134  134 
    ##   45 word   136  146 
    ##   46 word   148  151 
    ##   47 word   153  159 
    ##   48 word   161  173 
    ##   49 word   175  179 
    ##   50 word   181  188 
    ##   51 word   190  192 
    ##   52 word   194  210 
    ##   53 word   212  215 
    ##   54 word   217  219 
    ##   55 word   221  228 
    ##   56 word   230  239 
    ##   57 word   241  246 
    ##   58 word   248  251 
    ##   59 word   253  255 
    ##   60 word   257  264 
    ##   61 word   266  273 
    ##   62 word   275  276 
    ##   63 word   278  280 
    ##   64 word   282  289 
    ##   65 word   291  295 
    ##   66 word   297  298 
    ##   67 word   300  304 
    ##   68 word   306  310 
    ##   69 word   312  316 
    ##   70 word   317  317 
    ##   71 word   319  319 
    ##   72 word   321  327 
    ##   73 word   329  338 
    ##   74 word   340  341 
    ##   75 word   343  345 
    ##   76 word   347  352 
    ##   77 word   354  357 
    ##   78 word   359  366 
    ##   79 word   367  367 
    ##   80 word   369  371 
    ##   81 word   373  381 
    ##   82 word   383  384 
    ##   83 word   386  386 
    ##   84 word   388  391 
    ##   85 word   393  399 
    ##   86 word   401  409 
    ##   87 word   411  412 
    ##   88 word   414  424 
    ##   89 word   426  426 
    ##   90 word   427  440 
    ##   91 word   442  449 
    ##   92 word   450  450 
    ##   93 word   451  451 
    ##   94 word   453  453 
    ##   95 word   455  462 
    ##   96 word   463  470 
    ##   97 word   472  473 
    ##   98 word   475  482 
    ##   99 word   484  487 
    ##  100 word   489  492 
    ##  101 word   494  500 
    ##  102 word   502  508 
    ##  103 word   510  516 
    ##  104 word   518  521 
    ##  105 word   523  526 
    ##  106 word   527  527 
    ##  107 word   529  536 
    ##  108 word   538  547 
    ##  109 word   549  552 
    ##  110 word   553  553 
    ##  111 word   555  560 
    ##  112 word   562  565 
    ##  113 word   567  570 
    ##  114 word   572  579 
    ##  115 word   581  588 
    ##  116 word   589  589 
    ##  117 word   591  593 
    ##  118 word   595  599 
    ##  119 word   601  605 
    ##  120 word   607  609 
    ##  121 word   611  614 
    ##  122 word   616  621 
    ##  123 word   623  624 
    ##  124 word   626  629 
    ##  125 word   631  634 
    ##  126 word   635  635 
    ##  127 word   637  638 
    ##  128 word   640  648 
    ##  129 word   650  654 
    ##  130 word   656  660 
    ##  131 word   662  666 
    ##  132 word   668  669 
    ##  133 word   671  676 
    ##  134 word   678  679 
    ##  135 word   681  681 
    ##  136 word   683  686 
    ##  137 word   688  697 
    ##  138 word   699  700 
    ##  139 word   702  704 
    ##  140 word   706  713 
    ##  141 word   715  726 
    ##  142 word   728  729 
    ##  143 word   731  733 
    ##  144 word   735  742 
    ##  145 word   744  754 
    ##  146 word   756  761 
    ##  147 word   763  767 
    ##  148 word   769  772 
    ##  149 word   774  784 
    ##  150 word   786  789 
    ##  151 word   791  794 
    ##  152 word   796  797 
    ##  153 word   799  805 
    ##  154 word   807  813 
    ##  155 word   815  819 
    ##  156 word   820  820 
    ##  157 word   822  824 
    ##  158 word   826  828 
    ##  159 word   830  832 
    ##  160 word   834  839 
    ##  161 word   841  843 
    ##  162 word   845  851 
    ##  163 word   853  857 
    ##  164 word   858  858 
    ##  165 word   860  862 
    ##  166 word   864  870 
    ##  167 word   871  871 
    ##  168 word   873  875 
    ##  169 word   877  882 
    ##  170 word   884  889 
    ##  171 word   891  893 
    ##  172 word   895  898 
    ##  173 word   900  903 
    ##  174 word   905  911 
    ##  175 word   913  921 
    ##  176 word   923  928 
    ##  177 word   930  931 
    ##  178 word   933  940 
    ##  179 word   942  943 
    ##  180 word   945  952 
    ##  181 word   954  958 
    ##  182 word   960  964 
    ##  183 word   966  967 
    ##  184 word   969  971 
    ##  185 word   973  979 
    ##  186 word   981  983 
    ##  187 word   985  990 
    ##  188 word   992  997 
    ##  189 word   999 1000 
    ##  190 word  1002 1008 
    ##  191 word  1010 1013 
    ##  192 word  1015 1015 
    ##  193 word  1017 1023 
    ##  194 word  1024 1024 
    ##  195 word  1026 1029 
    ##  196 word  1031 1036 
    ##  197 word  1038 1041 
    ##  198 word  1043 1047 
    ##  199 word  1049 1055 
    ##  200 word  1057 1059 
    ##  201 word  1061 1066 
    ##  202 word  1068 1071 
    ##  203 word  1073 1073 
    ##  204 word  1075 1079 
    ##  205 word  1081 1086 
    ##  206 word  1088 1089 
    ##  207 word  1091 1094 
    ##  208 word  1096 1099 
    ##  209 word  1101 1106 
    ##  210 word  1108 1111 
    ##  211 word  1113 1117 
    ##  212 word  1119 1124 
    ##  213 word  1126 1127 
    ##  214 word  1129 1136 
    ##  215 word  1137 1137 
    ##  216 word  1139 1140 
    ##  217 word  1142 1143 
    ##  218 word  1145 1154 
    ##  219 word  1156 1157 
    ##  220 word  1159 1161 
    ##  221 word  1163 1166 
    ##  222 word  1168 1169 
    ##  223 word  1171 1176 
    ##  224 word  1178 1182 
    ##  225 word  1184 1186 
    ##  226 word  1188 1193 
    ##  227 word  1195 1198 
    ##  228 word  1200 1207 
    ##  229 word  1209 1211 
    ##  230 word  1213 1217 
    ##  231 word  1219 1225 
    ##  232 word  1227 1233 
    ##  233 word  1235 1237 
    ##  234 word  1239 1242 
    ##  235 word  1244 1246 
    ##  236 word  1248 1251 
    ##  237 word  1253 1259 
    ##  238 word  1261 1264 
    ##  239 word  1266 1268 
    ##  240 word  1270 1276 
    ##  241 word  1277 1278 
    ##  242 word  1280 1282 
    ##  243 word  1284 1292 
    ##  244 word  1294 1298 
    ##  245 word  1299 1299 
    ##  246 word  1301 1303 
    ##  247 word  1305 1314 
    ##  248 word  1316 1324 
    ##  249 word  1326 1334 
    ##  250 word  1336 1338 
    ##  251 word  1340 1343 
    ##  252 word  1345 1350 
    ##  253 word  1352 1355 
    ##  254 word  1357 1366 
    ##  255 word  1368 1368 
    ##  256 word  1370 1375 
    ##  257 word  1377 1389 
    ##  258 word  1390 1390 
    ##  259 word  1392 1398 
    ##  260 word  1400 1403 
    ##  261 word  1405 1407 
    ##  262 word  1409 1417 
    ##  263 word  1419 1422 
    ##  264 word  1424 1426 
    ##  265 word  1428 1432 
    ##  266 word  1434 1441 
    ##  267 word  1442 1442 
    ##  268 word  1444 1446 
    ##  269 word  1448 1451 
    ##  270 word  1453 1460 
    ##  271 word  1462 1464 
    ##  272 word  1466 1473 
    ##  273 word  1475 1480 
    ##  274 word  1482 1488 
    ##  275 word  1490 1490 
    ##  276 word  1492 1498 
    ##  277 word  1500 1509 
    ##  278 word  1511 1512 
    ##  279 word  1514 1514 
    ##  280 word  1516 1522 
    ##  281 word  1524 1533 
    ##  282 word  1535 1540 
    ##  283 word  1542 1545 
    ##  284 word  1547 1556 
    ##  285 word  1558 1559 
    ##  286 word  1561 1563 
    ##  287 word  1565 1568 
    ##  288 word  1569 1569 
    ##  289 word  1571 1575 
    ##  290 word  1577 1578 
    ##  291 word  1580 1600 
    ##  292 word  1602 1610 
    ##  293 word  1612 1614 
    ##  294 word  1616 1621 
    ##  295 word  1623 1624 
    ##  296 word  1626 1630 
    ##  297 word  1632 1634 
    ##  298 word  1636 1638 
    ##  299 word  1640 1651 
    ##  300 word  1653 1656 
    ##  301 word  1658 1664 
    ##  302 word  1666 1668 
    ##  303 word  1670 1674 
    ##  304 word  1676 1680 
    ##  305 word  1682 1684 
    ##  306 word  1686 1692 
    ##  307 word  1694 1700 
    ##  308 word  1702 1709 
    ##  309 word  1710 1710 
    ##  310 word  1712 1714 
    ##  311 word  1716 1719 
    ##  312 word  1721 1722 
    ##  313 word  1724 1732 
    ##  314 word  1734 1737 
    ##  315 word  1739 1745 
    ##  316 word  1747 1749 
    ##  317 word  1751 1755 
    ##  318 word  1757 1759 
    ##  319 word  1761 1771 
    ##  320 word  1773 1775 
    ##  321 word  1777 1784 
    ##  322 word  1786 1791 
    ##  323 word  1793 1796 
    ##  324 word  1797 1797 
    ##  325 word  1799 1801 
    ##  326 word  1803 1810 
    ##  327 word  1812 1825 
    ##  328 word  1827 1834 
    ##  329 word  1836 1838 
    ##  330 word  1840 1847 
    ##  331 word  1849 1857 
    ##  332 word  1859 1860 
    ##  333 word  1862 1867 
    ##  334 word  1869 1883 
    ##  335 word  1885 1886 
    ##  336 word  1888 1895 
    ##  337 word  1897 1899 
    ##  338 word  1901 1907 
    ##  339 word  1909 1913 
    ##  340 word  1915 1916 
    ##  341 word  1918 1924 
    ##  342 word  1926 1930 
    ##  343 word  1932 1935 
    ##  344 word  1937 1938 
    ##  345 word  1940 1941 
    ##  346 word  1943 1948 
    ##  347 word  1950 1952 
    ##  348 word  1954 1961 
    ##  349 word  1963 1966 
    ##  350 word  1968 1977 
    ##  351 word  1979 1987 
    ##  352 word  1989 1992 
    ##  353 word  1994 2001 
    ##  354 word  2003 2008 
    ##  355 word  2010 2012 
    ##  356 word  2014 2018 
    ##  357 word  2020 2030 
    ##  358 word  2031 2031 
    ##  359 word  2033 2043 
    ##  360 word  2044 2044 
    ##  361 word  2046 2049 
    ##  362 word  2051 2059 
    ##  363 word  2061 2063 
    ##  364 word  2065 2068 
    ##  365 word  2070 2075 
    ##  366 word  2077 2078 
    ##  367 word  2080 2085 
    ##  368 word  2087 2091 
    ##  369 word  2093 2097 
    ##  370 word  2099 2106 
    ##  371 word  2108 2112 
    ##  372 word  2114 2117 
    ##  373 word  2119 2124 
    ##  374 word  2126 2128 
    ##  375 word  2130 2138 
    ##  376 word  2140 2143 
    ##  377 word  2145 2151 
    ##  378 word  2153 2154 
    ##  379 word  2156 2156 
    ##  380 word  2158 2166 
    ##  381 word  2168 2173 
    ##  382 word  2174 2176 
    ##  383 word  2178 2179 
    ##  384 word  2181 2190 
    ##  385 word  2192 2193 
    ##  386 word  2195 2197 
    ##  387 word  2199 2205 
    ##  388 word  2207 2207 
    ##  389 word  2209 2218 
    ##  390 word  2220 2224 
    ##  391 word  2226 2230 
    ##  392 word  2232 2239 
    ##  393 word  2241 2244 
    ##  394 word  2246 2249 
    ##  395 word  2251 2252 
    ##  396 word  2254 2255 
    ##  397 word  2257 2257 
    ##  398 word  2259 2266 
    ##  399 word  2268 2269 
    ##  400 word  2271 2275 
    ##  401 word  2276 2276 
    ##  402 word  2278 2279 
    ##  403 word  2281 2282 
    ##  404 word  2284 2287 
    ##  405 word  2289 2290 
    ##  406 word  2292 2294 
    ##  407 word  2296 2298 
    ##  408 word  2300 2303 
    ##  409 word  2305 2308 
    ##  410 word  2310 2315 
    ##  411 word  2317 2318 
    ##  412 word  2320 2327 
    ##  413 word  2329 2335 
    ##  414 word  2336 2336 
    ##  415 word  2338 2346 
    ##  416 word  2348 2352 
    ##  417 word  2354 2362 
    ##  418 word  2364 2367 
    ##  419 word  2369 2370 
    ##  420 word  2372 2381 
    ##  421 word  2383 2385 
    ##  422 word  2387 2393 
    ##  423 word  2395 2402 
    ##  424 word  2404 2405 
    ##  425 word  2407 2410 
    ##  426 word  2412 2416 
    ##  427 word  2418 2427 
    ##  428 word  2429 2432 
    ##  429 word  2434 2440 
    ##  430 word  2442 2454 
    ##  431 word  2456 2457 
    ##  432 word  2459 2469 
    ##  433 word  2471 2472 
    ##  434 word  2474 2476 
    ##  435 word  2478 2484 
    ##  436 word  2486 2490 
    ##  437 word  2492 2496 
    ##  438 word  2498 2502 
    ##  439 word  2504 2507 
    ##  440 word  2509 2514 
    ##  441 word  2516 2518 
    ##  442 word  2520 2528 
    ##  443 word  2529 2529 
    ##  444 word  2531 2533 
    ##  445 word  2535 2540 
    ##  446 word  2542 2547 
    ##  447 word  2549 2552 
    ##  448 word  2554 2556 
    ##  449 word  2558 2561 
    ##  450 word  2563 2563 
    ##  451 word  2565 2576 
    ##  452 word  2578 2591 
    ##  453 word  2593 2594 
    ##  454 word  2596 2604 
    ##  455 word  2606 2611 
    ##  456 word  2613 2615 
    ##  457 word  2617 2620 
    ##  458 word  2622 2628 
    ##  459 word  2629 2629 
    ##  460 word  2631 2642 
    ##  461 word  2644 2645 
    ##  462 word  2647 2647 
    ##  463 word  2649 2652 
    ##  464 word  2654 2658 
    ##  465 word  2660 2664 
    ##  466 word  2666 2669 
    ##  467 word  2671 2681 
    ##  468 word  2683 2691 
    ##  469 word  2693 2697 
    ##  470 word  2699 2704 
    ##  471 word  2706 2715 
    ##  472 word  2717 2718 
    ##  473 word  2720 2727 
    ##  474 word  2729 2734 
    ##  475 word  2736 2738 
    ##  476 word  2740 2748 
    ##  477 word  2750 2751 
    ##  478 word  2753 2765 
    ##  479 word  2767 2771 
    ##  480 word  2773 2776 
    ##  481 word  2778 2779 
    ##  482 word  2781 2784 
    ##  483 word  2786 2790 
    ##  484 word  2792 2796 
    ##  485 word  2798 2802 
    ##  486 word  2804 2807 
    ##  487 word  2809 2812 
    ##  488 word  2814 2816 
    ##  489 word  2818 2830 
    ##  490 word  2831 2831 
    ##  491 word  2833 2835 
    ##  492 word  2837 2839 
    ##  493 word  2841 2850 
    ##  494 word  2852 2858 
    ##  495 word  2860 2868 
    ##  496 word  2870 2873 
    ##  497 word  2875 2876 
    ##  498 word  2878 2880 
    ##  499 word  2882 2889 
    ##  500 word  2891 2896 
    ##  501 word  2898 2903 
    ##  502 word  2905 2907 
    ##  503 word  2909 2913 
    ##  504 word  2915 2918 
    ##  505 word  2920 2935 
    ##  506 word  2937 2940 
    ##  507 word  2942 2943 
    ##  508 word  2945 2950 
    ##  509 word  2952 2953 
    ##  510 word  2955 2956 
    ##  511 word  2958 2962 
    ##  512 word  2964 2966 
    ##  513 word  2967 2967 
    ##  514 word  2969 2971 
    ##  515 word  2973 2974 
    ##  516 word  2976 2978 
    ##  517 word  2980 2982 
    ##  518 word  2984 2988 
    ##  519 word  2990 2991 
    ##  520 word  2993 2993 
    ##  521 word  2995 2998 
    ##  522 word  3000 3003 
    ##  523 word  3005 3011 
    ##  524 word  3013 3015 
    ##  525 word  3017 3021 
    ##  526 word  3022 3022 
    ##  527 word  3024 3027 
    ##  528 word  3029 3032 
    ##  529 word  3033 3033 
    ##  530 word  3035 3039 
    ##  531 word  3041 3041 
    ##  532 word  3043 3049 
    ##  533 word  3051 3058 
    ##  534 word  3060 3069 
    ##  535 word  3071 3073 
    ##  536 word  3075 3082 
    ##  537 word  3084 3085 
    ##  538 word  3087 3094 
    ##  539 word  3096 3100 
    ##  540 word  3101 3101 
    ##  541 word  3103 3106 
    ##  542 word  3108 3112 
    ##  543 word  3114 3114 
    ##  544 word  3116 3122 
    ##  545 word  3124 3126 
    ##  546 word  3128 3136 
    ##  547 word  3138 3139 
    ##  548 word  3141 3147 
    ##  549 word  3149 3157 
    ##  550 word  3158 3165 
    ##  551 word  3167 3168 
    ##  552 word  3170 3178 
    ##  553 word  3180 3181 
    ##  554 word  3183 3183 
    ##  555 word  3185 3192 
    ##  556 word  3194 3201 
    ##  557 word  3203 3208 
    ##  558 word  3210 3213 
    ##  559 word  3215 3218 
    ##  560 word  3220 3225 
    ##  561 word  3227 3233 
    ##  562 word  3235 3238 
    ##  563 word  3240 3247 
    ##  564 word  3249 3255 
    ##  565 word  3257 3264 
    ##  566 word  3265 3265 
    ##  567 word  3267 3270 
    ##  568 word  3272 3285 
    ##  569 word  3287 3289 
    ##  570 word  3291 3293 
    ##  571 word  3295 3301 
    ##  572 word  3303 3311 
    ##  573 word  3313 3314 
    ##  574 word  3316 3319 
    ##  575 word  3321 3328 
    ##  576 word  3329 3332 
    ##  577 word  3334 3337 
    ##  578 word  3339 3342 
    ##  579 word  3344 3345 
    ##  580 word  3347 3350 
    ##  581 word  3352 3353 
    ##  582 word  3355 3357 
    ##  583 word  3359 3365 
    ##  584 word  3367 3374 
    ##  585 word  3376 3381 
    ##  586 word  3383 3385 
    ##  587 word  3387 3391 
    ##  588 word  3392 3392 
    ##  589 word  3394 3397 
    ##  590 word  3399 3402 
    ##  591 word  3404 3406 
    ##  592 word  3408 3408 
    ##  593 word  3410 3418 
    ##  594 word  3420 3425 
    ##  595 word  3427 3430 
    ##  596 word  3432 3436 
    ##  597 word  3438 3440 
    ##  598 word  3442 3451 
    ##  599 word  3453 3455 
    ##  600 word  3457 3461 
    ##  601 word  3463 3467 
    ##  602 word  3469 3471 
    ##  603 word  3473 3483 
    ##  604 word  3485 3492 
    ##  605 word  3493 3493 
    ##  606 word  3495 3497 
    ##  607 word  3499 3500 
    ##  608 word  3501 3503 
    ##  609 word  3505 3506 
    ##  610 word  3508 3509 
    ##  611 word  3511 3513 
    ##  612 word  3515 3519 
    ##  613 word  3520 3522 
    ##  614 word  3524 3529 
    ##  615 word  3531 3534 
    ##  616 word  3536 3540 
    ##  617 word  3542 3546 
    ##  618 word  3548 3553 
    ##  619 word  3555 3561 
    ##  620 word  3563 3567 
    ##  621 word  3569 3572 
    ##  622 word  3574 3575 
    ##  623 word  3577 3580 
    ##  624 word  3582 3589 
    ##  625 word  3590 3590 
    ##  626 word  3592 3594 
    ##  627 word  3596 3600 
    ##  628 word  3602 3608 
    ##  629 word  3610 3617 
    ##  630 word  3619 3620 
    ##  631 word  3622 3624 
    ##  632 word  3626 3639 
    ##  633 word  3641 3644 
    ##  634 word  3646 3651 
    ##  635 word  3653 3655 
    ##  636 word  3657 3660 
    ##  637 word  3662 3666 
    ##  638 word  3668 3674 
    ##  639 word  3676 3682 
    ##  640 word  3683 3683 
    ##  641 word  3685 3686 
    ##  642 word  3688 3689 
    ##  643 word  3691 3696 
    ##  644 word  3698 3707 
    ##  645 word  3709 3716 
    ##  646 word  3717 3717 
    ##  647 word  3718 3718 
    ##  648 word  3720 3721 
    ##  649 word  3723 3725 
    ##  650 word  3727 3735 
    ##  651 word  3737 3739 
    ##  652 word  3741 3742 
    ##  653 word  3743 3743 
    ##  654 word  3745 3746 
    ##  655 word  3748 3750 
    ##  656 word  3752 3754 
    ##  657 word  3756 3759 
    ##  658 word  3761 3771 
    ##  659 word  3772 3772 
    ##  660 word  3774 3775 
    ##  661 word  3777 3778 
    ##  662 word  3780 3782 
    ##  663 word  3784 3787 
    ##  664 word  3789 3799 
    ##  665 word  3801 3806 
    ##  666 word  3807 3807 
    ##  667 word  3809 3818 
    ##  668 word  3820 3821 
    ##  669 word  3823 3827 
    ##  670 word  3829 3830 
    ##  671 word  3832 3834 
    ##  672 word  3836 3841 
    ##  673 word  3843 3850 
    ##  674 word  3852 3853 
    ##  675 word  3855 3858 
    ##  676 word  3860 3863 
    ##  677 word  3864 3864 
    ##  678 word  3865 3868 
    ##  679 word  3870 3873 
    ##  680 word  3875 3880 
    ##  681 word  3882 3886 
    ##  682 word  3887 3887 
    ##  683 word  3889 3890 
    ##  684 word  3892 3899 
    ##  685 word  3900 3900 
    ##  686 word  3902 3903 
    ##  687 word  3905 3905 
    ##  688 word  3907 3914 
    ##  689 word  3915 3915

Quizás sea más fácil explorar los resultados utilizando la función
`AnnotatedPlainTextDocument`:

    adoc <- AnnotatedPlainTextDocument(text, annotated_text)
    head(sents(adoc))

    ## [[1]]
    ##  [1] "Rushed"            "deal"              "demands"          
    ##  [4] "scrutiny"          "March"             "1"                
    ##  [7] ","                 "2006"              "This"             
    ## [10] "op-ed"             "originally"        "appeared"         
    ## [13] "in"                "THE"               "NEWS"             
    ## [16] "JOURNAL"           "on"                "March"            
    ## [19] "1"                 ","                 "2006.Rushed"      
    ## [22] "deal"              "demands"           "scrutinyDubai"    
    ## [25] "Ports"             "takeover"          "has"              
    ## [28] "vulnerabilitiesBy" "SEN."             
    ## 
    ## [[2]]
    ##  [1] "JOE"            "BIDENThe"       "legitimate"     "outcry"        
    ##  [5] "over"           "the"            "proposed"       "takeover"      
    ##  [9] "of"             "six"            "American"       "ports"         
    ## [13] "by"             "Dubai"          "Ports"          "World"         
    ## [17] ","              "a"              "company"        "controlled"    
    ## [21] "by"             "the"            "United"         "Arab"          
    ## [25] "Emirates"       ","              "was"            "dismissed"     
    ## [29] "in"             "a"              "News"           "Journal"       
    ## [33] "editorial"      "as"             "unjustified"    "\""            
    ## [37] "chest-thumping" "jingoism"       "."              "\""            
    ## 
    ## [[3]]
    ##  [1] "I"          "disagree"   ".Members"   "of"         "Congress"  
    ##  [6] "from"       "both"       "parties"    "reacted"    "because"   
    ## [11] "this"       "deal"       ","          "approved"   "alarmingly"
    ## [16] "fast"       ","          "raises"     "some"       "real"      
    ## [21] "security"   "concerns"   "."         
    ## 
    ## [[4]]
    ##  [1] "The"    "White"  "House"  "has"    "come"   "around" "to"    
    ##  [8] "this"   "view"   "."     
    ## 
    ## [[5]]
    ##  [1] "It"           "convinced"    "Dubai"        "Ports"       
    ##  [5] "World"        "to"           "submit"       "to"          
    ##  [9] "a"            "full"         "assessment"   "of"          
    ## [13] "the"          "security"     "implications" "of"          
    ## [17] "the"          "proposed"     "buyout.Many"  "people"      
    ## [21] "argue"        "that"         "questioning"  "this"        
    ## [25] "deal"         "is"           "bigotry"      "against"     
    ## [29] "Arabs"        "."           
    ## 
    ## [[6]]
    ## [1] "But"     "not"     "all"     "allies"  "are"     "created" "equal"  
    ## [8] "."

    head(words(adoc))

    ## [1] "Rushed"   "deal"     "demands"  "scrutiny" "March"    "1"

Anotador de entidades y POS
---------------------------

Podemos utilizar el mismo proceso para anotar entidades dentro de un
texto. Por ejemplo, podemos anotar personas, localizaciones,
organizaciones y fechas dentro del documento que ya hemos anotado
anteriormente.

    person_ann <- Maxent_Entity_Annotator(kind="person")
    loc_ann <- Maxent_Entity_Annotator(kind="location")
    org_ann <- Maxent_Entity_Annotator(kind="organization")
    date_ann <- Maxent_Entity_Annotator(kind="date")

y ahora aplicamos estos nuevos anotadores al texto que ya tokenizamos
previamente:

    annotated_text <- annotate(text,
                              list(person_ann, loc_ann, org_ann, date_ann),
                              annotated_text)

podemos ver que, a la lista de tokens, se han añadido las entidades que
el clasificador ha logrado identificar.

    subset(annotated_text, type=="entity")$features

    ## [[1]]
    ## [[1]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[2]]
    ## [[2]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[3]]
    ## [[3]]$kind
    ## [1] "person"
    ## 
    ## 
    ## [[4]]
    ## [[4]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[5]]
    ## [[5]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[6]]
    ## [[6]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[7]]
    ## [[7]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[8]]
    ## [[8]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[9]]
    ## [[9]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[10]]
    ## [[10]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[11]]
    ## [[11]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[12]]
    ## [[12]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[13]]
    ## [[13]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[14]]
    ## [[14]]$kind
    ## [1] "location"
    ## 
    ## 
    ## [[15]]
    ## [[15]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[16]]
    ## [[16]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[17]]
    ## [[17]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[18]]
    ## [[18]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[19]]
    ## [[19]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[20]]
    ## [[20]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[21]]
    ## [[21]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[22]]
    ## [[22]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[23]]
    ## [[23]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[24]]
    ## [[24]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[25]]
    ## [[25]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[26]]
    ## [[26]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[27]]
    ## [[27]]$kind
    ## [1] "organization"
    ## 
    ## 
    ## [[28]]
    ## [[28]]$kind
    ## [1] "date"
    ## 
    ## 
    ## [[29]]
    ## [[29]]$kind
    ## [1] "date"

y ahora podemos ver las entidades que hemos recuperado

    sel <- subset(annotated_text, type=="entity")
    cbind(unlist(sel$features), text[sel])

    ##      [,1]           [,2]                              
    ## kind "person"       "Bush"                            
    ## kind "person"       "U.S."                            
    ## kind "person"       "Joseph Biden"                    
    ## kind "location"     "United States"                   
    ## kind "location"     "United States"                   
    ## kind "location"     "Poland"                          
    ## kind "location"     "Saudi Arabia"                    
    ## kind "location"     "Pakistan"                        
    ## kind "location"     "United Arab"                     
    ## kind "location"     "New York"                        
    ## kind "location"     "Miami"                           
    ## kind "location"     "United States"                   
    ## kind "location"     "Hong Kong"                       
    ## kind "location"     "Delaware"                        
    ## kind "organization" "Congress"                        
    ## kind "organization" "White House"                     
    ## kind "organization" "NATO"                            
    ## kind "organization" "Great Britain"                   
    ## kind "organization" "United Arab Emirates"            
    ## kind "organization" "Great Britain"                   
    ## kind "organization" "Congress"                        
    ## kind "organization" "Coast Guard"                     
    ## kind "organization" "Maritime Transportation Security"
    ## kind "organization" "Coast"                           
    ## kind "organization" "Guard"                           
    ## kind "organization" "Coast Guard"                     
    ## kind "organization" "Customs"                         
    ## kind "date"         "March 1"                         
    ## kind "date"         "March 1"

La anotación de POS sigue la misma lógica. Ahora generaremos un nuevo
objeto en el que inicializamos el anotador de POS y aplicamos este nuevo
anotador a nuestro texto:

    pos_ann <- Maxent_POS_Tag_Annotator()
    pos_annotated_text <- annotate(text, pos_ann, annotated_text)

Ahora lo que tenemos en la columna de `features` es una indiación de la
POS de cada una de las palabras

    head(subset(annotated_text, type=="word"))

    ##  id type start end features
    ##  25 word    15  20 
    ##  26 word    22  25 
    ##  27 word    27  33 
    ##  28 word    35  42 
    ##  29 word    57  61 
    ##  30 word    63  63

En este caso vemos que, por ejemplo,

    text[subset(annotated_text, type=="word")[[1]]]

    ## Rushed

está etiquetado como `JJ` que se corresponde como adjetivo y

    text[subset(annotated_text, type=="word")[[2]]]

    ## deal

está etiquetado como `NN` que es un sustantivo en singular. La lista
completa de abreviaturas es:

<table>
<tr>
<th>
Símbolo
</th>
<th>
Significado
</th>
</tr>
<tr>
<td>
CC
</td>
<td>
Coordinating conjunction
</td>
</tr>
<tr>
<td>
CD
</td>
<td>
Cardinal number
</td>
</tr>
<tr>
<td>
DT
</td>
<td>
Determiner
</td>
</tr>
<tr>
<td>
EX
</td>
<td>
Existential there
</td>
</tr>
<tr>
<td>
FW
</td>
<td>
Foreign word
</td>
</tr>
<tr>
<td>
IN
</td>
<td>
Preposition or subordinating conjunction
</td>
</tr>
<tr>
<td>
JJ
</td>
<td>
Adjective
</td>
</tr>
<tr>
<td>
JJR
</td>
<td>
Adjective, comparative
</td>
</tr>
<tr>
<td>
JJS
</td>
<td>
Adjective, superlative
</td>
</tr>
<tr>
<td>
LS
</td>
<td>
List item marker
</td>
</tr>
<tr>
<td>
MD
</td>
<td>
Modal
</td>
</tr>
<tr>
<td>
NN
</td>
<td>
Noun, singular or mass
</td>
</tr>
<tr>
<td>
NNS
</td>
<td>
Noun, plural
</td>
</tr>
<tr>
<td>
NNP
</td>
<td>
Proper noun, singular
</td>
</tr>
<tr>
<td>
NNPS
</td>
<td>
Proper noun, plural
</td>
</tr>
<tr>
<td>
PDT
</td>
<td>
Predeterminer
</td>
</tr>
<tr>
<td>
POS
</td>
<td>
Possessive ending
</td>
</tr>
<tr>
<td>
PRP
</td>
<td>
Personal pronoun
</td>
</tr>
<tr>
<td>
PRP&lt;/*t**d* &gt; &lt;*t**d* &gt; *P**o**s**s**e**s**s**i**v**e**p**r**o**n**o**u**n* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *R**B* &lt; /*t**d* &gt; &lt;*t**d* &gt; *A**d**v**e**r**b* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *R**B**R* &lt; /*t**d* &gt; &lt;*t**d* &gt; *A**d**v**e**r**b*, *c**o**m**p**a**r**a**t**i**v**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *R**B**S* &lt; /*t**d* &gt; &lt;*t**d* &gt; *A**d**v**e**r**b*, *s**u**p**e**r**l**a**t**i**v**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *R**P* &lt; /*t**d* &gt; &lt;*t**d* &gt; *P**a**r**t**i**c**l**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *S**Y**M* &lt; /*t**d* &gt; &lt;*t**d* &gt; *S**y**m**b**o**l* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *T**O* &lt; /*t**d* &gt; &lt;*t**d* &gt; *t**o* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *U**H* &lt; /*t**d* &gt; &lt;*t**d* &gt; *I**n**t**e**r**j**e**c**t**i**o**n* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, *b**a**s**e**f**o**r**m* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B**D* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, *p**a**s**t**t**e**n**s**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B**G* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, *g**e**r**u**n**d**o**r**p**r**e**s**e**n**t**p**a**r**t**i**c**i**p**l**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B**N* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, *p**a**s**t**p**a**r**t**i**c**i**p**l**e* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B**P* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, *n**o**n*­3*r**d**p**e**r**s**o**n**s**i**n**g**u**l**a**r**p**r**e**s**e**n**t* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *V**B**Z* &lt; /*t**d* &gt; &lt;*t**d* &gt; *V**e**r**b*, 3*r**d**p**e**r**s**o**n**s**i**n**g**u**l**a**r**p**r**e**s**e**n**t* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *W**D**T* &lt; /*t**d* &gt; &lt;*t**d* &gt; *W**h*­*d**e**t**e**r**m**i**n**e**r* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *W**P* &lt; /*t**d* &gt; &lt;*t**d* &gt; *W**h*­*p**r**o**n**o**u**n* &lt; /*t**d* &gt; &lt;/*t**r* &gt; &lt;*t**r* &gt; &lt;*t**d* &gt; *W**P*
</td>
<td>
Possessive wh­pronoun
</td>
</tr>
<tr>
<td>
WRB
</td>
<td>
Wh­adverb
</td>
</tr>
</table>
Hemos visto que las etiquetaciones son en realidad el resultado de un
modelo estadístico. Podemos pedirle al anotador que nos devuelva las
probabilidades asociadas a la etiqueta escogida con el fin de tener
información acerca de la incertidumbre.

    pos_ann <- Maxent_POS_Tag_Annotator(probs=TRUE)
    pos_annotated_text <- annotate(text, pos_ann, annotated_text)
    head(subset(pos_annotated_text, type=="word"))

    ##  id type start end features
    ##  25 word    15  20 POS=JJ, POS_prob=0.228
    ##  26 word    22  25 POS=NN, POS_prob=0.881
    ##  27 word    27  33 POS=NNS, POS_prob=0.836
    ##  28 word    35  42 POS=NN, POS_prob=0.563
    ##  29 word    57  61 POS=NNP, POS_prob=0.941
    ##  30 word    63  63 POS=CD, POS_prob=0.989

y así comprobamos que la asignación de *rushed* como adjetivo en
realidad tiene una enorme incertidumbre.
