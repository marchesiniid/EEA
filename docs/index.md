-   [Sobre los datos:](#sobre-los-datos)
    -   [Cargamos Datos:](#cargamos-datos)
    -   [Sobre el tratamiento de los
        datos:](#sobre-el-tratamiento-de-los-datos)
    -   [Duracion](#duracion)
-   [EDA - Analisis Exploratorio](#eda---analisis-exploratorio)
    -   [Correlaciones](#correlaciones)
-   [Modelo CART](#modelo-cart)
    -   [Preparación adicional de
        datos](#preparación-adicional-de-datos)
    -   [Preparamos nuestro train y
        test.](#preparamos-nuestro-train-y-test.)
    -   [Modelo para Depresion](#modelo-para-depresion)
        -   [Variando complejidad](#variando-complejidad)
        -   [Modelo con más variables](#modelo-con-más-variables)
        -   [Poda del árbol](#poda-del-árbol)
    -   [Métricas y comparación con otros
        modelos](#métricas-y-comparación-con-otros-modelos)

    library(rpart)
    library(rpart.plot)
    library(tidyverse)

    ## Warning: package 'tidyverse' was built under R version 4.3.2

    ## Warning: package 'tidyr' was built under R version 4.3.2

    ## Warning: package 'readr' was built under R version 4.3.2

    ## Warning: package 'purrr' was built under R version 4.3.2

    ## Warning: package 'dplyr' was built under R version 4.3.2

    ## Warning: package 'forcats' was built under R version 4.3.2

    ## Warning: package 'lubridate' was built under R version 4.3.2

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## Sobre los datos:

El Inventario de Depresión Ansiedad y Estrés (Depression Anxiety and
Stress Scale - DASS) es un set the 3 escalas auto-administrables
diseñadas para medir los estados emocionales negativos de depresión,
ansiedad y estrés. Cada escala individual consta de 14 items, siendo un
total de 42 para todo el cuestionario. A su vez, cada escala está
dividida en sub-escalas de 2 a 5 items de contenido similar. (…
continuar texto <https://www2.psy.unsw.edu.au/dass/over.htm>)

### Cargamos Datos:

    ##   Q1A Q1I  Q1E Q2A Q2I  Q2E Q3A Q3I   Q3E Q4A Q4I  Q4E Q5A Q5I  Q5E Q6A Q6I
    ## 1   4  28 3890   4  25 2122   2  16  1944   4   8 2044   4  34 2153   4  33
    ## 2   4   2 8118   1  36 2890   2  35  4777   3  28 3090   4  10 5078   4  40
    ## 3   3   7 5784   1  33 4373   4  41  3242   1  13 6470   4  11 3927   3   9
    ## 4   2  23 5081   3  11 6837   2  37  5521   1  27 4556   3  28 3269   3  26
    ## 5   2  36 3215   2  13 7731   3   5  4156   4  10 2802   4   2 5628   2   9
    ## 6   1  18 6116   1  28 3193   2   2 12542   1   8 6150   3  40 6428   1   4
    ##     Q6E Q7A Q7I  Q7E Q8A Q8I  Q8E Q9A Q9I  Q9E Q10A Q10I Q10E Q11A Q11I Q11E
    ## 1  2416   4  10 2818   4  13 2259   2  21 5541    1   38 4441    4   31 2451
    ## 2  2790   3  18 3408   4   1 8342   3  37  916    2   32 1537    2   21 3926
    ## 3  3704   1  17 4550   3   5 3021   2  32 5864    4   21 3722    2   10 3424
    ## 4  3231   4   2 7138   2  19 3079   3  31 9650    3   17 4179    2    5 5928
    ## 5  6522   4  34 2374   4  11 3054   4   7 2975    3   14 3524    2   33 3033
    ## 6 17001   1  33 2944   3   7 8626   3  14 9639    2   20 6175    1   34 6008
    ##   Q12A Q12I Q12E Q13A Q13I Q13E Q14A Q14I  Q14E Q15A Q15I Q15E Q16A Q16I Q16E
    ## 1    4   24 3325    4   14 1416    4   37  5021    4   27 2342    4   39 2480
    ## 2    2   25 3691    4   26 2004    4    4  8888    3   27 4109    3   19 4058
    ## 3    1   36 3236    4   23 2489    1   34  7290    4   12 6587    4   22 3627
    ## 4    1   21 2838    1   20 2560    4   29  5139    2   22 3597    2   35 3336
    ## 5    4   23 2132    4   17 1314    4   16  3181    4   26 2249    3   19 2623
    ## 6    2   21 9267    1   41 5290    3    1 25694    2    9 7634    4   37 8513
    ##   Q17A Q17I Q17E Q18A Q18I Q18E Q19A Q19I  Q19E Q20A Q20I Q20E Q21A Q21I Q21E
    ## 1    3    6 2476    4   35 1627    3   17  9050    3   30 7001    1   11 4719
    ## 2    4   12 3692    2    6 3373    1   23  6015    1   16 3023    2   22 2670
    ## 3    4   38 2905    2   18 2998    2    8 10233    1   16 4258    4   28 2888
    ## 4    3   10 4506    1   14 2695    1   25  8128    2   15 3125    1    6 4061
    ## 5    4   35 3093    4   38 7098    4   37  1938    4   15 3502    3   32 4776
    ## 6    2   25 9078    1   15 4381    1   23  6647    2   36 6250    1   39 3842
    ##   Q22A Q22I  Q22E Q23A Q23I  Q23E Q24A Q24I Q24E Q25A Q25I  Q25E Q26A Q26I Q26E
    ## 1    4   20  2984    4   36  1313    4   42 2444    4    1  9880    4    2 4695
    ## 2    3    3  5727    1   39  3641    2   33 2670    2    7  7649    3   11 2537
    ## 3    3    4 59592    2    3 11732    4    2 8834    2   29  7358    1   30 4928
    ## 4    1   40  4272    1   12  4029    1    9 5630    1   18 30631    2   24 9870
    ## 5    3   18  4463    4    4  2436    2   40 4047    4   31  3787    4   42 2102
    ## 6    1   16  7876    1   27  3124    2   12 6836    1   31 12063    1    3 9264
    ##   Q27A Q27I  Q27E Q28A Q28I Q28E Q29A Q29I  Q29E Q30A Q30I  Q30E Q31A Q31I Q31E
    ## 1    4    5  1677    3    4 6723    4    3  5953    2   26  8062    4   12 5560
    ## 2    3    5  2907    4    9 1685    3   41  4726    3   17  6063    2   20 3307
    ## 3    2   15  3036    1   19 4127    2   37  3934    2   26 10782    4    1 8273
    ## 4    4    4  2411    1   16 9478    3    1  7618    3   32 12639    3   34 5378
    ## 5    2    1 12351    4    3 2410    2   22  5056    4   39  3343    3   27 3012
    ## 6    1   35  3957    1   42 2537    3   17 10880    2    5  8462    2   32 5615
    ##   Q32A Q32I  Q32E Q33A Q33I Q33E Q34A Q34I Q34E Q35A Q35I  Q35E Q36A Q36I Q36E
    ## 1    4    7  3032    2   29 3316    3   40 3563    4   23  5594    4   41 1477
    ## 2    3   14  4995    3   38 2505    2   34 2540    2   31  4359    3   15 3925
    ## 3    3   39  3501    1   27 3824    4   25 2141    3    6 17461    4   24 1557
    ## 4    1   41  8923    2   38 2977    4    3 5620    1    7 16760    1    8 6427
    ## 5    4   20  3520    4    8 1868    4   25 2536    3   24  3725    4   30 2130
    ## 6    1   30 11412    4    6 5112    1   29 3070    3   10 13377    2   38 4506
    ##   Q37A Q37I  Q37E Q38A Q38I  Q38E Q39A Q39I  Q39E Q40A Q40I Q40E Q41A Q41I Q41E
    ## 1    1   18  3885    2    9  5265    4   19  1892    3   22 4228    4   32 1574
    ## 2    4   13  4609    2   30  3755    2   42  2323    1   24 5713    2    8 1334
    ## 3    4   40  4446    4   42  1883    2   35  5790    2   14 4432    1   20 2203
    ## 4    2   39  3760    1   13  4112    3   42  2769    4   33 4432    4   30 3643
    ## 5    3   29  3952    3   21 10694    3   41  3231    4   12 3604    4   28 1950
    ## 6    2   24 17227    2   13  7844    1   26 20253    1   22 8528    1   11 4370
    ##   Q42A Q42I  Q42E country source introelapse testelapse surveyelapse TIPI1
    ## 1    4   15  2969      IN      2          19        167          166     1
    ## 2    2   29  5562      US      2           1        193          186     6
    ## 3    4   31  5768      PL      2           5        271          122     2
    ## 4    2   36  3698      US      2           3        261          336     1
    ## 5    3    6  6265      MY      2        1766        164          157     2
    ## 6    2   19 10310      US      2           4        349          213     2
    ##   TIPI2 TIPI3 TIPI4 TIPI5 TIPI6 TIPI7 TIPI8 TIPI9 TIPI10 VCL1 VCL2 VCL3 VCL4
    ## 1     5     7     7     7     7     7     5     1      1    1    0    0    1
    ## 2     5     4     7     5     4     7     7     1      5    1    1    0    1
    ## 3     5     2     2     5     6     5     5     3      2    1    0    0    1
    ## 4     1     7     4     6     4     6     1     6      1    1    0    0    1
    ## 5     5     3     6     5     5     5     6     3      3    1    1    0    1
    ## 6     1     6     1     7     7     7     2     6      7    1    1    0    1
    ##   VCL5 VCL6 VCL7 VCL8 VCL9 VCL10 VCL11 VCL12 VCL13 VCL14 VCL15 VCL16 education
    ## 1    1    0    1    0    0     1     0     0     0     1     1     1         2
    ## 2    1    0    0    0    0     1     0     0     0     1     1     1         2
    ## 3    1    0    0    0    0     0     1     0     0     1     1     1         2
    ## 4    1    0    0    0    0     1     0     0     0     1     1     1         1
    ## 5    1    0    0    1    0     1     0     0     1     1     1     1         3
    ## 6    1    0    0    0    0     1     0     0     0     0     1     1         2
    ##   urban gender engnat age screensize uniquenetworklocation hand religion
    ## 1     3      2      2  16          1                     1    1       12
    ## 2     3      2      1  16          2                     1    2        7
    ## 3     3      2      2  17          2                     1    1        4
    ## 4     3      2      1  13          2                     1    2        4
    ## 5     2      2      2  19          2                     2    3       10
    ## 6     3      2      2  20          2                     1    1        4
    ##   orientation race voted married familysize      major
    ## 1           1   10     2       1          2           
    ## 2           0   70     2       1          4           
    ## 3           3   60     1       1          3           
    ## 4           5   70     2       1          5    biology
    ## 5           1   10     2       1          4 Psychology
    ## 6           1   70     2       1          4

### Sobre el tratamiento de los datos:

Para nuestro caso de estudio, nos interesa el scoring total en cada
escala (Depresión, Ansiedad y Estrés respectivamente). Si bien los
autores plantean que no son independientes, sino que están hasta cierto
punto correlacionadas, vamos a evaluar el grado de severidad para cada
caso. Dado que nuestro dataset contiene el score individual marcado para
cada pregunta, calcularemos tres columnas adicionales con el score total
de cada estado respectivamente. Asimismo, como en este caso los valores
van de 1 a 4, y en el DASS original se puntuan de 0 a 3, vamos a restar
1 a todos los valores.

    #Creamos las columnas que nos interesan

    columnas_respuesta <- c("Q1A", "Q2A", "Q3A", "Q4A", "Q5A", "Q6A", "Q7A", "Q8A", "Q9A", "Q10A", "Q11A", "Q12A", "Q13A", "Q14A", "Q15A", "Q16A", "Q17A", "Q18A", "Q19A", "Q20A", "Q21A", "Q22A", "Q23A", "Q24A", "Q25A", "Q26A", "Q27A", "Q28A", "Q29A", "Q30A", "Q31A", "Q32A", "Q33A", "Q34A", "Q35A", "Q36A", "Q37A", "Q38A", "Q39A", "Q40A", "Q41A", "Q42A")

    # Restar 1 a las 42 columnas al mismo tiempo
    df[, columnas_respuesta] <- df[, columnas_respuesta] - 1

    df$Ansiedad <- rowSums(df[, c("Q2A", "Q4A", "Q7A", "Q9A", "Q15A", "Q19A", "Q20A", "Q23A", "Q25A", "Q28A", "Q30A", "Q36A", "Q40A", "Q41A")])
    df$Depresion <- rowSums(df[, c("Q3A", "Q5A", "Q10A", "Q13A", "Q16A", "Q17A", "Q21A", "Q24A", "Q26A", "Q31A", "Q34A", "Q37A", "Q38A", "Q42A")])
    df$Stress <- rowSums(df[, c("Q1A", "Q6A", "Q8A", "Q11A", "Q12A", "Q14A", "Q18A", "Q22A", "Q27A", "Q29A", "Q32A", "Q33A", "Q35A", "Q39A")])


    #Sabemos ademas que el valor máximo de cada escala es 3 para cada pregunta, y son 14 preguntas, en total 42 puntos es el valor maximo en cada escala. 

    #Calculamos entonces la proporcion sobre el total maximo

    df$Ansiedad_perc <- df$Ansiedad / 42
    df$Depresion_perc <- df$Depresion / 42
    df$Stress_perc <- df$Stress / 42

    head(df[, c("Ansiedad", "Ansiedad_perc", "Depresion", "Depresion_perc", "Stress", "Stress_perc")])

    ##   Ansiedad Ansiedad_perc Depresion Depresion_perc Stress Stress_perc
    ## 1       34     0.8095238        27      0.6428571     40   0.9523810
    ## 2       17     0.4047619        24      0.5714286     27   0.6428571
    ## 3       12     0.2857143        39      0.9285714     17   0.4047619
    ## 4       17     0.4047619        16      0.3809524     16   0.3809524
    ## 5       40     0.9523810        32      0.7619048     29   0.6904762
    ## 6        6     0.1428571        13      0.3095238     12   0.2857143

También vamos a construir 4 categorias de Severidad para cada Target.
Depresion: 0-9 normal, 10-13 leve, 14-20 moderado, 21-42 severo
Ansiedad: 0-7 normal, 8-9 leve, 10-14 moderado, 15-42 severo Stress:
0-14 normal, 15-18 leve, 19-25 moderado, 26-42 severo

Información tomada de la hoja de información de DASS:
<https://static1.squarespace.com/static/5934abbb1b631ba4718d4c2d/t/604d2386f36aae7a6f01fb67/1615668113669/DASS+23+Information+Sheet.2.25.21.docx.pdf>

    df <- df %>%
      mutate(
        Depresion_cat = case_when(
          between(Depresion, 0, 9) ~ "Normal",
          between(Depresion, 10, 13) ~ "Leve",
          between(Depresion, 14, 20) ~ "Moderado",
          between(Depresion, 21, 42) ~ "Severo",
          TRUE ~ NA_character_
        ),
        Ansiedad_cat = case_when(
          between(Ansiedad, 0, 7) ~ "Normal",
          between(Ansiedad, 8, 9) ~ "Leve",
          between(Ansiedad, 10, 14) ~ "Moderado",
          between(Ansiedad, 15, 42) ~ "Severo",
          TRUE ~ NA_character_
        ),
        Stress_cat = case_when(
          between(Stress, 0, 14) ~ "Normal",
          between(Stress, 15, 18) ~ "Leve",
          between(Stress, 19, 25) ~ "Moderado",
          between(Stress, 26, 42) ~ "Severo",
          TRUE ~ NA_character_
        )
    )

Además de esto, contamos con la escala de Ten Item Personality
Inventory, que contiene 10 preguntas que se responden con números del 1
al 7. En base al manual de dicho inventario, debemos recodificar algunas
respuestas de la siguiente manera:

Para los items 2, 4, 6, 8 y 10, codificar a la inversa: 7 como 1, 6 como
2, 5 como 3, etc.

    # Columnas a codificar
    columnas_a_codificar <- c("TIPI2", "TIPI4", "TIPI6", "TIPI8", "TIPI10")

    # Codificación inversa
    df <- df %>%
      mutate(across(all_of(columnas_a_codificar), ~if_else(. %in% c(1, 2, 3, 4, 5, 6, 7), recode(., `7` = 1, `6` = 2, `5` = 3, `4` = 4, `3` = 5, `2` = 6, `1` = 7), .)))

    ## Warning: There were 5 warnings in `mutate()`.
    ## The first warning was:
    ## ℹ In argument: `across(...)`.
    ## Caused by warning:
    ## ! Unreplaced values treated as NA as `.x` is not compatible.
    ## Please specify replacements exhaustively or supply `.default`.
    ## ℹ Run `dplyr::last_dplyr_warnings()` to see the 4 remaining warnings.

Ahora vamos a calcular las sumas de cada rasgo de Personalidad:
Extroversion, Agreeableness, Conscientiousness, Emotional Stability,
Openness, y sus respectivos valores porcentuales (máximo es 7)

    df$Extraversion <- (df$TIPI1 + df$TIPI6) / 2
    df$Extraversion_perc <- df$Extraversion / 7
    df$Agreeableness <- (df$TIPI2 + df$TIPI7) / 2
    df$Agreeableness_perc <- df$Agreeableness / 7
    df$Conscientiousness <- (df$TIPI3 + df$TIPI8) / 2
    df$Conscientiousness_perc <- df$Conscientiousness / 7
    df$EmotionalStability <- (df$TIPI4 + df$TIPI9) / 2
    df$EmotionalStability_perc <- df$EmotionalStability / 7
    df$Openness <- (df$TIPI5 + df$TIPI10) / 2
    df$Openness_perc <- df$Openness / 7
    head(df[, c("Extraversion", "Extraversion_perc", "Agreeableness", "Agreeableness_perc", "Conscientiousness", "Conscientiousness_perc", "EmotionalStability", "EmotionalStability_perc", "Openness", "Openness_perc")])

    ##   Extraversion Extraversion_perc Agreeableness Agreeableness_perc
    ## 1          1.0         0.1428571           5.0          0.7142857
    ## 2          5.0         0.7142857           5.0          0.7142857
    ## 3          2.0         0.2857143           4.0          0.5714286
    ## 4          2.5         0.3571429           6.5          0.9285714
    ## 5          2.5         0.3571429           4.0          0.5714286
    ## 6          1.5         0.2142857           7.0          1.0000000
    ##   Conscientiousness Conscientiousness_perc EmotionalStability
    ## 1               5.0              0.7142857                1.0
    ## 2               2.5              0.3571429                1.0
    ## 3               2.5              0.3571429                4.5
    ## 4               7.0              1.0000000                5.0
    ## 5               2.5              0.3571429                2.5
    ## 6               6.0              0.8571429                6.5
    ##   EmotionalStability_perc Openness Openness_perc
    ## 1               0.1428571      7.0     1.0000000
    ## 2               0.1428571      4.0     0.5714286
    ## 3               0.6428571      5.5     0.7857143
    ## 4               0.7142857      6.5     0.9285714
    ## 5               0.3571429      5.0     0.7142857
    ## 6               0.9285714      4.0     0.5714286

Ademas vamos a filtrar aquellos valores que en VCL6, VCL9 y VCL12 tengan
como respuesta un “1” ya que son preguntas-señuelo que nos indican que
la persona no estaba concentrada, o contestó al azar, o no podemos
asegurar que haya comprendido correctamente las consignas. Lo mismo
haremos en los casos donde todas las preguntas VCL sean 0, ya que
también indicaría que esos resultados no son confiables.

    library(ggplot2)

    # Calculamos la proporción de datos que vamos a eliminar
    prop_condicion <- df %>%
      filter(VCL6 == 1 | VCL9 == 1 | VCL12 == 1 | (VCL1 == 0 & VCL2 == 0 & VCL3 == 0 & VCL4 == 0 & VCL5 == 0 & VCL6 == 0 & VCL7 == 0 & VCL8 == 0 & VCL9 == 0 & VCL10 == 0 & VCL11 == 0 & VCL12 == 0)) %>%
      nrow() / nrow(df)




    # Filtrar las filas que cumplen la condición y actualizar el dataframe
    df_filtrado <- df %>%
      filter(!(VCL6 == 1 | VCL9 == 1 | VCL12 == 1 | (VCL1 == 0 & VCL2 == 0 & VCL3 == 0 & VCL4 == 0 & VCL5 == 0 & VCL6 == 0 & VCL7 == 0 & VCL8 == 0 & VCL9 == 0 & VCL10 == 0 & VCL11 == 0 & VCL12 == 0)))

    # Muestra los primeros registros del dataframe filtrado
    head(df_filtrado)

    ##   Q1A Q1I  Q1E Q2A Q2I  Q2E Q3A Q3I   Q3E Q4A Q4I  Q4E Q5A Q5I  Q5E Q6A Q6I
    ## 1   3  28 3890   3  25 2122   1  16  1944   3   8 2044   3  34 2153   3  33
    ## 2   3   2 8118   0  36 2890   1  35  4777   2  28 3090   3  10 5078   3  40
    ## 3   2   7 5784   0  33 4373   3  41  3242   0  13 6470   3  11 3927   2   9
    ## 4   1  23 5081   2  11 6837   1  37  5521   0  27 4556   2  28 3269   2  26
    ## 5   1  36 3215   1  13 7731   2   5  4156   3  10 2802   3   2 5628   1   9
    ## 6   0  18 6116   0  28 3193   1   2 12542   0   8 6150   2  40 6428   0   4
    ##     Q6E Q7A Q7I  Q7E Q8A Q8I  Q8E Q9A Q9I  Q9E Q10A Q10I Q10E Q11A Q11I Q11E
    ## 1  2416   3  10 2818   3  13 2259   1  21 5541    0   38 4441    3   31 2451
    ## 2  2790   2  18 3408   3   1 8342   2  37  916    1   32 1537    1   21 3926
    ## 3  3704   0  17 4550   2   5 3021   1  32 5864    3   21 3722    1   10 3424
    ## 4  3231   3   2 7138   1  19 3079   2  31 9650    2   17 4179    1    5 5928
    ## 5  6522   3  34 2374   3  11 3054   3   7 2975    2   14 3524    1   33 3033
    ## 6 17001   0  33 2944   2   7 8626   2  14 9639    1   20 6175    0   34 6008
    ##   Q12A Q12I Q12E Q13A Q13I Q13E Q14A Q14I  Q14E Q15A Q15I Q15E Q16A Q16I Q16E
    ## 1    3   24 3325    3   14 1416    3   37  5021    3   27 2342    3   39 2480
    ## 2    1   25 3691    3   26 2004    3    4  8888    2   27 4109    2   19 4058
    ## 3    0   36 3236    3   23 2489    0   34  7290    3   12 6587    3   22 3627
    ## 4    0   21 2838    0   20 2560    3   29  5139    1   22 3597    1   35 3336
    ## 5    3   23 2132    3   17 1314    3   16  3181    3   26 2249    2   19 2623
    ## 6    1   21 9267    0   41 5290    2    1 25694    1    9 7634    3   37 8513
    ##   Q17A Q17I Q17E Q18A Q18I Q18E Q19A Q19I  Q19E Q20A Q20I Q20E Q21A Q21I Q21E
    ## 1    2    6 2476    3   35 1627    2   17  9050    2   30 7001    0   11 4719
    ## 2    3   12 3692    1    6 3373    0   23  6015    0   16 3023    1   22 2670
    ## 3    3   38 2905    1   18 2998    1    8 10233    0   16 4258    3   28 2888
    ## 4    2   10 4506    0   14 2695    0   25  8128    1   15 3125    0    6 4061
    ## 5    3   35 3093    3   38 7098    3   37  1938    3   15 3502    2   32 4776
    ## 6    1   25 9078    0   15 4381    0   23  6647    1   36 6250    0   39 3842
    ##   Q22A Q22I  Q22E Q23A Q23I  Q23E Q24A Q24I Q24E Q25A Q25I  Q25E Q26A Q26I Q26E
    ## 1    3   20  2984    3   36  1313    3   42 2444    3    1  9880    3    2 4695
    ## 2    2    3  5727    0   39  3641    1   33 2670    1    7  7649    2   11 2537
    ## 3    2    4 59592    1    3 11732    3    2 8834    1   29  7358    0   30 4928
    ## 4    0   40  4272    0   12  4029    0    9 5630    0   18 30631    1   24 9870
    ## 5    2   18  4463    3    4  2436    1   40 4047    3   31  3787    3   42 2102
    ## 6    0   16  7876    0   27  3124    1   12 6836    0   31 12063    0    3 9264
    ##   Q27A Q27I  Q27E Q28A Q28I Q28E Q29A Q29I  Q29E Q30A Q30I  Q30E Q31A Q31I Q31E
    ## 1    3    5  1677    2    4 6723    3    3  5953    1   26  8062    3   12 5560
    ## 2    2    5  2907    3    9 1685    2   41  4726    2   17  6063    1   20 3307
    ## 3    1   15  3036    0   19 4127    1   37  3934    1   26 10782    3    1 8273
    ## 4    3    4  2411    0   16 9478    2    1  7618    2   32 12639    2   34 5378
    ## 5    1    1 12351    3    3 2410    1   22  5056    3   39  3343    2   27 3012
    ## 6    0   35  3957    0   42 2537    2   17 10880    1    5  8462    1   32 5615
    ##   Q32A Q32I  Q32E Q33A Q33I Q33E Q34A Q34I Q34E Q35A Q35I  Q35E Q36A Q36I Q36E
    ## 1    3    7  3032    1   29 3316    2   40 3563    3   23  5594    3   41 1477
    ## 2    2   14  4995    2   38 2505    1   34 2540    1   31  4359    2   15 3925
    ## 3    2   39  3501    0   27 3824    3   25 2141    2    6 17461    3   24 1557
    ## 4    0   41  8923    1   38 2977    3    3 5620    0    7 16760    0    8 6427
    ## 5    3   20  3520    3    8 1868    3   25 2536    2   24  3725    3   30 2130
    ## 6    0   30 11412    3    6 5112    0   29 3070    2   10 13377    1   38 4506
    ##   Q37A Q37I  Q37E Q38A Q38I  Q38E Q39A Q39I  Q39E Q40A Q40I Q40E Q41A Q41I Q41E
    ## 1    0   18  3885    1    9  5265    3   19  1892    2   22 4228    3   32 1574
    ## 2    3   13  4609    1   30  3755    1   42  2323    0   24 5713    1    8 1334
    ## 3    3   40  4446    3   42  1883    1   35  5790    1   14 4432    0   20 2203
    ## 4    1   39  3760    0   13  4112    2   42  2769    3   33 4432    3   30 3643
    ## 5    2   29  3952    2   21 10694    2   41  3231    3   12 3604    3   28 1950
    ## 6    1   24 17227    1   13  7844    0   26 20253    0   22 8528    0   11 4370
    ##   Q42A Q42I  Q42E country source introelapse testelapse surveyelapse TIPI1
    ## 1    3   15  2969      IN      2          19        167          166     1
    ## 2    1   29  5562      US      2           1        193          186     6
    ## 3    3   31  5768      PL      2           5        271          122     2
    ## 4    1   36  3698      US      2           3        261          336     1
    ## 5    2    6  6265      MY      2        1766        164          157     2
    ## 6    1   19 10310      US      2           4        349          213     2
    ##   TIPI2 TIPI3 TIPI4 TIPI5 TIPI6 TIPI7 TIPI8 TIPI9 TIPI10 VCL1 VCL2 VCL3 VCL4
    ## 1     3     7     1     7     1     7     3     1      7    1    0    0    1
    ## 2     3     4     1     5     4     7     1     1      3    1    1    0    1
    ## 3     3     2     6     5     2     5     3     3      6    1    0    0    1
    ## 4     7     7     4     6     4     6     7     6      7    1    0    0    1
    ## 5     3     3     2     5     3     5     2     3      5    1    1    0    1
    ## 6     7     6     7     7     1     7     6     6      1    1    1    0    1
    ##   VCL5 VCL6 VCL7 VCL8 VCL9 VCL10 VCL11 VCL12 VCL13 VCL14 VCL15 VCL16 education
    ## 1    1    0    1    0    0     1     0     0     0     1     1     1         2
    ## 2    1    0    0    0    0     1     0     0     0     1     1     1         2
    ## 3    1    0    0    0    0     0     1     0     0     1     1     1         2
    ## 4    1    0    0    0    0     1     0     0     0     1     1     1         1
    ## 5    1    0    0    1    0     1     0     0     1     1     1     1         3
    ## 6    1    0    0    0    0     1     0     0     0     0     1     1         2
    ##   urban gender engnat age screensize uniquenetworklocation hand religion
    ## 1     3      2      2  16          1                     1    1       12
    ## 2     3      2      1  16          2                     1    2        7
    ## 3     3      2      2  17          2                     1    1        4
    ## 4     3      2      1  13          2                     1    2        4
    ## 5     2      2      2  19          2                     2    3       10
    ## 6     3      2      2  20          2                     1    1        4
    ##   orientation race voted married familysize      major Ansiedad Depresion
    ## 1           1   10     2       1          2                  34        27
    ## 2           0   70     2       1          4                  17        24
    ## 3           3   60     1       1          3                  12        39
    ## 4           5   70     2       1          5    biology       17        16
    ## 5           1   10     2       1          4 Psychology       40        32
    ## 6           1   70     2       1          4                   6        13
    ##   Stress Ansiedad_perc Depresion_perc Stress_perc Depresion_cat Ansiedad_cat
    ## 1     40     0.8095238      0.6428571   0.9523810        Severo       Severo
    ## 2     27     0.4047619      0.5714286   0.6428571        Severo       Severo
    ## 3     17     0.2857143      0.9285714   0.4047619        Severo     Moderado
    ## 4     16     0.4047619      0.3809524   0.3809524      Moderado       Severo
    ## 5     29     0.9523810      0.7619048   0.6904762        Severo       Severo
    ## 6     12     0.1428571      0.3095238   0.2857143          Leve       Normal
    ##   Stress_cat Extraversion Extraversion_perc Agreeableness Agreeableness_perc
    ## 1     Severo          1.0         0.1428571           5.0          0.7142857
    ## 2     Severo          5.0         0.7142857           5.0          0.7142857
    ## 3       Leve          2.0         0.2857143           4.0          0.5714286
    ## 4       Leve          2.5         0.3571429           6.5          0.9285714
    ## 5     Severo          2.5         0.3571429           4.0          0.5714286
    ## 6     Normal          1.5         0.2142857           7.0          1.0000000
    ##   Conscientiousness Conscientiousness_perc EmotionalStability
    ## 1               5.0              0.7142857                1.0
    ## 2               2.5              0.3571429                1.0
    ## 3               2.5              0.3571429                4.5
    ## 4               7.0              1.0000000                5.0
    ## 5               2.5              0.3571429                2.5
    ## 6               6.0              0.8571429                6.5
    ##   EmotionalStability_perc Openness Openness_perc
    ## 1               0.1428571      7.0     1.0000000
    ## 2               0.1428571      4.0     0.5714286
    ## 3               0.6428571      5.5     0.7857143
    ## 4               0.7142857      6.5     0.9285714
    ## 5               0.3571429      5.0     0.7142857
    ## 6               0.9285714      4.0     0.5714286

Asimismo, quitaremos todos aquellos registros donde todas las respuestas
tengan el mismo valor.

    filas_con_mismo_valor <- apply(df_filtrado[, grep("^Q\\d+A$", colnames(df_filtrado))], 1, function(x) length(unique(x)) == 1)

    # Filtrar el dataframe
    df_filtrado <- df_filtrado[!filas_con_mismo_valor, ]

    cat("Número de filas después de limpiar:", nrow(df_filtrado), "\n")

    ## Número de filas después de limpiar: 32505

### Duracion

Estas medidas nos permiten observar que hay datos máximos que llaman la
atención. Para asegurar que los datos sean confiables, vamos a filtrar
aquellos que sean outliers en tiempo (testelapse, surveyelapse), y
también aquellos donde todas las respuestas hayan sido con el mismo
puntaje (todas 4, o todas 1, o todas 2, o todas 3, etc)

    columnas_duracion <- c("testelapse", "sum_T_elapsed", "surveyelapse")

    df_filtrado$sum_T_elapsed <- rowSums(df_filtrado[, grep("^Q\\d+E$", colnames(df_filtrado))])
    df_filtrado$sum_T_elapsed <- df_filtrado$sum_T_elapsed / 1000
    head(df_filtrado[,columnas_duracion])

    ##   testelapse sum_T_elapsed surveyelapse
    ## 1        167       157.622          166
    ## 2        193       168.927          186
    ## 3        271       270.194          122
    ## 4        261       253.531          336
    ## 5        164       163.402          157
    ## 6        349       348.041          213

    summary(df_filtrado[,columnas_duracion])

    ##    testelapse       sum_T_elapsed        surveyelapse     
    ##  Min.   :      14   Min.   :    11.19   Min.   :       1  
    ##  1st Qu.:     167   1st Qu.:   159.69   1st Qu.:     147  
    ##  Median :     216   Median :   204.99   Median :     187  
    ##  Mean   :    3012   Mean   :   317.00   Mean   :    4995  
    ##  3rd Qu.:     299   3rd Qu.:   277.66   3rd Qu.:     249  
    ##  Max.   :20829721   Max.   :151401.02   Max.   :20828454

Dado que testelapse y surveyelapse corresponden a el tiempo en segundos
que se tardó en responde todo el test, tenemos que considerar que siendo
42 preguntas del test no podemos confiar demasiado en aquellos registros
que hayan tardado menos de 1 minuto y más de 40 minutos

    df_filtrado <- df_filtrado %>%
      filter(testelapse >= 60, testelapse <= 2400, surveyelapse <= 2400, surveyelapse >=60)

Habiendo limpiado los absurdos, vamos a verificar los outliers.

    iqr_testelapse <- IQR(df_filtrado$testelapse)
    iqr_surveyelapse <- IQR(df_filtrado$surveyelapse)

    # Definir los límites para identificar outliers
    limite_superior_testelapse <- quantile(df_filtrado$testelapse)[4] + 1.5 * iqr_testelapse
    limite_inferior_testelapse <- quantile(df_filtrado$testelapse)[2] - 1.5 * iqr_testelapse

    limite_superior_surveyelapse <- quantile(df_filtrado$surveyelapse)[4] + 1.5 * iqr_surveyelapse
    limite_inferior_surveyelapse <- quantile(df_filtrado$surveyelapse)[2] - 1.5 * iqr_surveyelapse

    # Identificar outliers en testelapse y surveyelapse
    outliers_testelapse <- df_filtrado$testelapse > limite_superior_testelapse | df_filtrado$testelapse < limite_inferior_testelapse
    outliers_surveyelapse <- df_filtrado$surveyelapse > limite_superior_surveyelapse | df_filtrado$surveyelapse < limite_inferior_surveyelapse

    # Contar y mostrar los outliers
    cat("Número de outliers en testelapse:", sum(outliers_testelapse), "\n")

    ## Número de outliers en testelapse: 2580

    cat("Número de outliers en surveyelapse:", sum(outliers_surveyelapse), "\n")

    ## Número de outliers en surveyelapse: 2191

    # Crear un boxplot para testelapse con outliers marcados
    ggplot(df_filtrado, aes(x = factor(1), y = testelapse)) +
      geom_boxplot(outlier.shape = 16, outlier.colour = "red") +
      labs(title = "Boxplot de testelapse con outliers marcados",
           x = "",
           y = "testelapse") +
      theme_minimal()

![](index_files/figure-markdown_strict/Outliers%20de%20tiempo-1.png)

    # Crear un boxplot para surveyelapse con outliers marcados
    ggplot(df_filtrado, aes(x = factor(1), y = surveyelapse)) +
      geom_boxplot(outlier.shape = 16, outlier.colour = "red") +
      labs(title = "Boxplot de surveyelapse con outliers marcados",
           x = "",
           y = "surveyelapse") +
      theme_minimal()

![](index_files/figure-markdown_strict/Outliers%20de%20tiempo-2.png)

Ahora procedemos a filtrar los outliers y observar los boxplots luego de
quitar outliers.

    df_filtrado <- df_filtrado %>%
      filter(testelapse >= limite_inferior_testelapse, testelapse <= limite_superior_testelapse,
             surveyelapse >= limite_inferior_surveyelapse, surveyelapse <= limite_superior_surveyelapse)

    # Verificar el nuevo tamaño del dataframe después de la eliminación de outliers
    cat("Número de filas después de eliminar outliers:", nrow(df_filtrado), "\n")

    ## Número de filas después de eliminar outliers: 27539

    # Crear un boxplot para testelapse con outliers marcados
    ggplot(df_filtrado, aes(x = factor(1), y = testelapse)) +
      geom_boxplot(outlier.shape = 16, outlier.colour = "red") +
      labs(title = "Boxplot de testelapse con outliers marcados",
           x = "",
           y = "testelapse") +
      theme_minimal()

![](index_files/figure-markdown_strict/Eliminamos%20outliers%20de%20tiempo-1.png)

    # Crear un boxplot para surveyelapse con outliers marcados
    ggplot(df_filtrado, aes(x = factor(1), y = surveyelapse)) +
      geom_boxplot(outlier.shape = 16, outlier.colour = "red") +
      labs(title = "Boxplot de surveyelapse con outliers marcados",
           x = "",
           y = "surveyelapse") +
      theme_minimal()

![](index_files/figure-markdown_strict/Eliminamos%20outliers%20de%20tiempo-2.png)

    ggplot(df_filtrado, aes(x = testelapse)) +
      geom_density(fill = "blue", alpha = 0.5) +
      labs(title = "Densidad de testelapse sin outliers",
           x = "testelapse",
           y = "Densidad") +
      theme_minimal()

![](index_files/figure-markdown_strict/Eliminamos%20outliers%20de%20tiempo-3.png)

    ggplot(df_filtrado, aes(x = surveyelapse)) +
      geom_density(fill = "orange", alpha = 0.5) +
      labs(title = "Densidad de surveyelapse sin outliers",
           x = "testelapse",
           y = "Densidad") +
      theme_minimal()

![](index_files/figure-markdown_strict/Eliminamos%20outliers%20de%20tiempo-4.png)

## EDA - Analisis Exploratorio

    columnas_personales <- c("education", "urban", "gender", "engnat", "age", "hand", "religion", "orientation", "race", "voted", "married", "familysize")

    columnas_duracion <- c("testelapse", "surveyelapse")

    columnas_interesantes <- c("Depresion","Ansiedad","Stress",  "Extraversion", "Agreeableness",  "Conscientiousness", "EmotionalStability", "Openness") 

    cat("Summary interesantes: ")

    ## Summary interesantes:

    summary(df_filtrado[, columnas_interesantes])

    ##    Depresion        Ansiedad         Stress       Extraversion  
    ##  Min.   : 0.00   Min.   : 0.00   Min.   : 0.00   Min.   :0.000  
    ##  1st Qu.:11.00   1st Qu.: 8.00   1st Qu.:13.00   1st Qu.:2.000  
    ##  Median :21.00   Median :15.00   Median :21.00   Median :3.500  
    ##  Mean   :21.07   Mean   :15.81   Mean   :21.05   Mean   :3.401  
    ##  3rd Qu.:31.00   3rd Qu.:23.00   3rd Qu.:29.00   3rd Qu.:4.500  
    ##  Max.   :42.00   Max.   :42.00   Max.   :42.00   Max.   :7.000  
    ##  Agreeableness   Conscientiousness EmotionalStability    Openness    
    ##  Min.   :0.000   Min.   :0.000     Min.   :0.000      Min.   :0.000  
    ##  1st Qu.:4.000   1st Qu.:3.000     1st Qu.:2.000      1st Qu.:3.500  
    ##  Median :4.500   Median :4.000     Median :3.000      Median :4.500  
    ##  Mean   :4.501   Mean   :4.168     Mean   :3.178      Mean   :4.531  
    ##  3rd Qu.:5.500   3rd Qu.:5.500     3rd Qu.:4.000      3rd Qu.:5.500  
    ##  Max.   :7.000   Max.   :7.000     Max.   :7.000      Max.   :7.000

    cat("Summary duracion: ")

    ## Summary duracion:

    summary(df_filtrado[,columnas_duracion])

    ##    testelapse     surveyelapse  
    ##  Min.   : 61.0   Min.   : 60.0  
    ##  1st Qu.:162.0   1st Qu.:143.0  
    ##  Median :203.0   Median :178.0  
    ##  Mean   :219.3   Mean   :187.8  
    ##  3rd Qu.:260.0   3rd Qu.:223.0  
    ##  Max.   :482.0   Max.   :389.0

    cat("Summary personales: ")

    ## Summary personales:

    summary(df_filtrado[,columnas_personales])

    ##    education         urban           gender          engnat     
    ##  Min.   :0.000   Min.   :0.000   Min.   :0.000   Min.   :0.000  
    ##  1st Qu.:2.000   1st Qu.:2.000   1st Qu.:2.000   1st Qu.:1.000  
    ##  Median :3.000   Median :2.000   Median :2.000   Median :2.000  
    ##  Mean   :2.517   Mean   :2.227   Mean   :1.789   Mean   :1.642  
    ##  3rd Qu.:3.000   3rd Qu.:3.000   3rd Qu.:2.000   3rd Qu.:2.000  
    ##  Max.   :4.000   Max.   :3.000   Max.   :3.000   Max.   :2.000  
    ##       age               hand          religion       orientation   
    ##  Min.   :  13.00   Min.   :0.000   Min.   : 0.000   Min.   :0.000  
    ##  1st Qu.:  18.00   1st Qu.:1.000   1st Qu.: 4.000   1st Qu.:1.000  
    ##  Median :  21.00   Median :1.000   Median :10.000   Median :1.000  
    ##  Mean   :  23.62   Mean   :1.129   Mean   : 7.533   Mean   :1.646  
    ##  3rd Qu.:  25.00   3rd Qu.:1.000   3rd Qu.:10.000   3rd Qu.:2.000  
    ##  Max.   :1998.00   Max.   :3.000   Max.   :12.000   Max.   :5.000  
    ##       race           voted          married        familysize    
    ##  Min.   :10.00   Min.   :0.000   Min.   :0.000   Min.   : 0.000  
    ##  1st Qu.:10.00   1st Qu.:1.000   1st Qu.:1.000   1st Qu.: 2.000  
    ##  Median :10.00   Median :2.000   Median :1.000   Median : 3.000  
    ##  Mean   :31.21   Mean   :1.711   Mean   :1.153   Mean   : 3.482  
    ##  3rd Qu.:60.00   3rd Qu.:2.000   3rd Qu.:1.000   3rd Qu.: 4.000  
    ##  Max.   :70.00   Max.   :2.000   Max.   :3.000   Max.   :99.000

Verificaremos las variables que seran las target: Depresion, Ansiedad y
Stress

    # Definir colores para cada variable
    color_depresion <- "green"
    color_ansiedad <- "orange"
    color_stress <- "purple"

    # Crear histograma y boxplot para la variable de Depresion
    ggplot(df_filtrado, aes(x = Depresion)) +
      geom_histogram(fill = color_depresion, color = "black", bins = 21) +
      labs(title = "Histograma de Depresion",
           x = "Depresion",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-1.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Depresion)) +
      geom_boxplot(fill = color_depresion, color = "black") +
      labs(title = "Boxplot de Depresion",
           x = "",
           y = "Depresion") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-2.png)

    # Crear histograma y boxplot para la variable de Ansiedad
    ggplot(df_filtrado, aes(x = Ansiedad)) +
      geom_histogram(fill = color_ansiedad, color = "black", bins = 21) +
      labs(title = "Histograma de Ansiedad",
           x = "Ansiedad",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-3.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Ansiedad)) +
      geom_boxplot(fill = color_ansiedad, color = "black") +
      labs(title = "Boxplot de Ansiedad",
           x = "",
           y = "Ansiedad") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-4.png)

    # Crear histograma y boxplot para la variable de Stress
    ggplot(df_filtrado, aes(x = Stress)) +
      geom_histogram(fill = color_stress, color = "black", bins = 21) +
      labs(title = "Histograma de Stress",
           x = "Stress",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-5.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Stress)) +
      geom_boxplot(fill = color_stress, color = "black") +
      labs(title = "Boxplot de Stress",
           x = "",
           y = "Stress") +
      theme_minimal()

![](index_files/figure-markdown_strict/EDA%20Variables%20target-6.png)

Se puede observar que el Stress tiene una distribución que se asemeja un
poco a una normal a primera vista, mientras que la Depresión tiene una
distribución considerablemente más uniforme, y la Ansiedad se asemeja un
poco a una Chi-cuadrado.

Veamos ahora nuestras variables de Personalidad:

    # Definir colores para cada variable de personalidad
    color_extraversion <- "skyblue"
    color_agreeableness <- "lightcoral"
    color_conscientiousness <- "lightgreen"
    color_emotional_stability <- "gold"
    color_openness <- "mediumpurple"

    # Crear histograma y boxplot para la variable de Extraversion
    ggplot(df_filtrado, aes(x = Extraversion)) +
      geom_histogram(fill = color_extraversion, color = "black", bins = 14) +
      labs(title = "Histograma de Extraversion",
           x = "Extraversion",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-1.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Extraversion)) +
      geom_boxplot(fill = color_extraversion, color = "black") +
      labs(title = "Boxplot de Extraversion",
           x = "",
           y = "Extraversion") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-2.png)

    # Crear histograma y boxplot para la variable de Agreeableness
    ggplot(df_filtrado, aes(x = Agreeableness)) +
      geom_histogram(fill = color_agreeableness, color = "black", bins = 14) +
      labs(title = "Histograma de Agreeableness",
           x = "Agreeableness",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-3.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Agreeableness)) +
      geom_boxplot(fill = color_agreeableness, color = "black") +
      labs(title = "Boxplot de Agreeableness",
           x = "",
           y = "Agreeableness") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-4.png)

    # Crear histograma y boxplot para la variable de Conscientiousness
    ggplot(df_filtrado, aes(x = Conscientiousness)) +
      geom_histogram(fill = color_conscientiousness, color = "black", bins = 14) +
      labs(title = "Histograma de Conscientiousness",
           x = "Conscientiousness",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-5.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Conscientiousness)) +
      geom_boxplot(fill = color_conscientiousness, color = "black") +
      labs(title = "Boxplot de Conscientiousness",
           x = "",
           y = "Conscientiousness") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-6.png)

    # Crear histograma y boxplot para la variable de Emotional Stability
    ggplot(df_filtrado, aes(x = EmotionalStability)) +
      geom_histogram(fill = color_emotional_stability, color = "black", bins = 14) +
      labs(title = "Histograma de Emotional Stability",
           x = "Emotional Stability",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-7.png)

    ggplot(df_filtrado, aes(x = factor(1), y = EmotionalStability)) +
      geom_boxplot(fill = color_emotional_stability, color = "black") +
      labs(title = "Boxplot de Emotional Stability",
           x = "",
           y = "Emotional Stability") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-8.png)

    # Crear histograma y boxplot para la variable de Openness
    ggplot(df_filtrado, aes(x = Openness)) +
      geom_histogram(fill = color_openness, color = "black", bins = 14) +
      labs(title = "Histograma de Openness",
           x = "Openness",
           y = "Frecuencia") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-9.png)

    ggplot(df_filtrado, aes(x = factor(1), y = Openness)) +
      geom_boxplot(fill = color_openness, color = "black") +
      labs(title = "Boxplot de Openness",
           x = "",
           y = "Openness") +
      theme_minimal()

![](index_files/figure-markdown_strict/Big%20Five-10.png)

### Correlaciones

    library(corrplot)

    ## Warning: package 'corrplot' was built under R version 4.3.2

    ## corrplot 0.92 loaded

    # Seleccionar las variables de interés
    variables_interes <- c("Depresion", "Ansiedad", "Stress", "Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness")

    # Crear una submatriz con las variables seleccionadas
    matriz_correlacion <- df_filtrado[, variables_interes]

    # Eliminar filas y columnas con valores NA
    matriz_correlacion <- matriz_correlacion[complete.cases(matriz_correlacion), ]

    # Calcular la matriz de correlación
    matriz_correlacion <- cor(matriz_correlacion)

    # Visualizar la matriz de correlación usando corrplot
    corrplot(matriz_correlacion, method = "number", type = "upper", order = "hclust", tl.col = "black", tl.srt = 45, addCoef.col = "black", number.cex = 0.7)

![](index_files/figure-markdown_strict/Correlaciones-1.png)

Se observa que la estabilidad emocional tiene una correlacion negativa
bastante alta con nuestras tres variables target, mayormente con Estrés.
El resto de los rasgos de personalidad tienen todos correlacion negativa
pero relativamente baja con nuestras variables target. Analicemos ahora
en función de datos personales.

## Modelo CART

### Preparación adicional de datos

Vamos a factorizar columnas que usaremos más adelante, que corresponden
a las preguntas de índole personal y demográfica.

    columnas_personales <- c("education", "urban", "gender", "engnat", "hand", "religion", "orientation", "race", "voted", "married", "familysize")

    # Filtrar filas con valores NA en las columnas de interés
    df_filtrado <- df_filtrado[complete.cases(df_filtrado[columnas_personales]), ]

    # Identificar y factorizar solo las columnas de tipo "character" o "factor"
    df_filtrado[columnas_personales] <- sapply(df_filtrado[columnas_personales], function(x) {
      if(is.character(x) || is.factor(x)) {
        return(as.factor(x))
      } else {
        return(x)
      }
    })

    # Verificar el resultado
    str(df_filtrado[columnas_personales])

    ## 'data.frame':    27539 obs. of  11 variables:
    ##  $ education  : int  2 2 2 1 3 2 1 1 3 4 ...
    ##  $ urban      : int  3 3 3 3 2 3 1 2 0 2 ...
    ##  $ gender     : int  2 2 2 2 2 2 2 1 2 1 ...
    ##  $ engnat     : int  2 1 2 1 2 2 2 1 1 2 ...
    ##  $ hand       : int  1 2 1 2 3 1 1 1 1 1 ...
    ##  $ religion   : int  12 7 4 4 10 4 2 6 1 12 ...
    ##  $ orientation: int  1 0 3 5 1 1 2 1 1 1 ...
    ##  $ race       : int  10 70 60 70 10 70 60 60 60 60 ...
    ##  $ voted      : int  2 2 1 2 2 2 2 2 2 2 ...
    ##  $ married    : int  1 1 1 1 1 1 1 1 1 1 ...
    ##  $ familysize : int  2 4 3 5 4 4 3 1 2 5 ...

### Preparamos nuestro train y test.

Vamos a comenzar factorizando nuestras variables categóricas
Depresion\_cat, Ansiedad\_cat, y Stress\_cat

    library(dplyr)
    library(forcats)

    # Columnas a codificar
    columnas_a_codificar <- c("Depresion_cat", "Ansiedad_cat", "Stress_cat")

    # Función para codificar las categorías
    codificar_categoria <- function(x) {
      recode(x,
        "Normal" = 0,
        "Leve" = 1,
        "Moderado" = 2,
        "Severo" = 3,
        .default = as.numeric(as.character(x))
      )
    }

    # Aplica la codificación a cada columna y crea nuevas columnas
    df_filtrado <- df_filtrado %>%
      mutate(
        Depresion_cat_f = codificar_categoria(Depresion_cat),
        Ansiedad_cat_f = codificar_categoria(Ansiedad_cat),
        Stress_cat_f = codificar_categoria(Stress_cat)
      )

    ## Warning: There were 3 warnings in `mutate()`.
    ## The first warning was:
    ## ℹ In argument: `Depresion_cat_f = codificar_categoria(Depresion_cat)`.
    ## Caused by warning in `lapply()`:
    ## ! NAs introduced by coercion
    ## ℹ Run `dplyr::last_dplyr_warnings()` to see the 2 remaining warnings.

Verificamos las proporciones de la severidad para Depresión.

    prop_target <- prop.table(table(df_filtrado$Depresion_cat))
    prop_target

    ## 
    ##      Leve  Moderado    Normal    Severo 
    ## 0.0939395 0.1792730 0.2213225 0.5054650

    library(caret)

    ## Warning: package 'caret' was built under R version 4.3.2

    ## Loading required package: lattice

    ## 
    ## Attaching package: 'caret'

    ## The following object is masked from 'package:purrr':
    ## 
    ##     lift

    set.seed(17)
    # Agregar una columna de ID
    df_filtrado$ID <- seq_len(nrow(df_filtrado))

    # Definir las variables predictoras
    variables_predictoras <- c("Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness", "education", "urban", "gender", "engnat", "hand", "religion", "orientation", "race", "voted", "married", "familysize")

    # Definir las variables objetivo
    variables_objetivo <- c("Depresion_cat", "Ansiedad_cat", "Stress_cat")
    df_modelo <- df_filtrado[, c(variables_predictoras, variables_objetivo, "ID")]

    # Dividir en train y test
    indice_entrenamiento <- createDataPartition(df_modelo$Depresion, p = 0.7, list = FALSE)

    #Train
    df_entrenamiento <- df_modelo[indice_entrenamiento, ]

    #Test
    df_prueba <- df_modelo[-indice_entrenamiento, ]

### Modelo para Depresion

    library(rpart.plot)
    # Crear una fórmula para el modelo


    variables_predictoras_depresion <- c("Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness",  "Depresion_cat")

    variables_predictoras_ansiedad <- c("Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness", "Ansiedad_cat")

    variables_predictoras_stress <- c("Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness", "Stress_cat")


    df_entrenamiento_depresion <- df_entrenamiento[variables_predictoras_depresion]

    df_entrenamiento_stress <- df_entrenamiento[variables_predictoras_stress]

    df_entrenamiento_ansiedad <- df_entrenamiento[variables_predictoras_ansiedad]

    #formula <- as.formula(paste('Depresion', " ~ ."))

    # Entrenar el modelo en el conjunto de entrenamiento
    modelo_cart_depresion <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = 'class')

    # Resumen del modelo
    print(paste("Resumen del modelo para", 'Depresion'))

    ## [1] "Resumen del modelo para Depresion"

    print(summary(modelo_cart_depresion))

    ## Call:
    ## rpart(formula = Depresion_cat ~ ., data = df_entrenamiento_depresion, 
    ##     method = "class")
    ##   n= 19278 
    ## 
    ##           CP nsplit rel error    xerror        xstd
    ## 1 0.08023914      0 1.0000000 1.0000000 0.007281152
    ## 2 0.05821271      1 0.9197609 0.9197609 0.007251863
    ## 3 0.01000000      2 0.8615481 0.8663730 0.007206681
    ## 
    ## Variable importance
    ## EmotionalStability  Conscientiousness      Agreeableness           Openness 
    ##                 79                  8                  5                  5 
    ##       Extraversion 
    ##                  3 
    ## 
    ## Node number 1: 19278 observations,    complexity param=0.08023914
    ##   predicted class=Severo  expected loss=0.4945534  P(node) =1
    ##     class counts:  1811  3456  4267  9744
    ##    probabilities: 0.094 0.179 0.221 0.505 
    ##   left son=2 (8566 obs) right son=3 (10712 obs)
    ##   Primary splits:
    ##       EmotionalStability < 3.25 to the right, improve=1101.90400, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve= 343.14590, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve= 292.54760, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 228.50220, (0 missing)
    ##       Agreeableness      < 4.25 to the right, improve=  64.67917, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.25 to the right, agree=0.600, adj=0.101, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.582, adj=0.060, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.577, adj=0.048, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.568, adj=0.027, (0 split)
    ## 
    ## Node number 2: 8566 observations,    complexity param=0.05821271
    ##   predicted class=Normal  expected loss=0.6096194  P(node) =0.4443407
    ##     class counts:  1093  1550  3344  2579
    ##    probabilities: 0.128 0.181 0.390 0.301 
    ##   left son=4 (4353 obs) right son=5 (4213 obs)
    ##   Primary splits:
    ##       EmotionalStability < 4.25 to the right, improve=221.81220, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve=148.19480, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve=127.33130, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 87.23226, (0 missing)
    ##       Agreeableness      < 3.75 to the right, improve= 16.80514, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.75 to the right, agree=0.570, adj=0.125, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.568, adj=0.122, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.547, adj=0.079, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.544, adj=0.073, (0 split)
    ## 
    ## Node number 3: 10712 observations
    ##   predicted class=Severo  expected loss=0.331124  P(node) =0.5556593
    ##     class counts:   718  1906   923  7165
    ##    probabilities: 0.067 0.178 0.086 0.669 
    ## 
    ## Node number 4: 4353 observations
    ##   predicted class=Normal  expected loss=0.4812773  P(node) =0.2258014
    ##     class counts:   520   637  2258   938
    ##    probabilities: 0.119 0.146 0.519 0.215 
    ## 
    ## Node number 5: 4213 observations
    ##   predicted class=Severo  expected loss=0.6104913  P(node) =0.2185393
    ##     class counts:   573   913  1086  1641
    ##    probabilities: 0.136 0.217 0.258 0.390 
    ## 
    ## n= 19278 
    ## 
    ## node), split, n, loss, yval, (yprob)
    ##       * denotes terminal node
    ## 
    ## 1) root 19278 9534 Severo (0.09394128 0.17927171 0.22134039 0.50544662)  
    ##   2) EmotionalStability>=3.25 8566 5222 Normal (0.12759748 0.18094793 0.39038057 0.30107401)  
    ##     4) EmotionalStability>=4.25 4353 2095 Normal (0.11945785 0.14633586 0.51872272 0.21548357) *
    ##     5) EmotionalStability< 4.25 4213 2572 Severo (0.13600760 0.21671018 0.25777356 0.38950866) *
    ##   3) EmotionalStability< 3.25 10712 3547 Severo (0.06702763 0.17793129 0.08616505 0.66887603) *

    # Visualizar el árbol de decisión 
    rpart.plot(modelo_cart_depresion)

![](index_files/figure-markdown_strict/Depresion-1.png)

Vemos que, sin pasarle ningún hiperparámetro, el árbol decide únicamente
en base al valor que tenga una persona en Estabilidad Emocional, y no
logra clasificar Leve ni Moderado

Probemos ahora un segundo modelo agregando algunos hiperparámetros:

    # Fit
    modelo_cart_depresion_md <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = "class", control = rpart.control(maxdepth = 10))

    #Resumen del modelo
    print(paste("Resumen del modelo para", 'Depresion'))

    ## [1] "Resumen del modelo para Depresion"

    print(head(summary(modelo_cart_depresion_md)))

    ## Call:
    ## rpart(formula = Depresion_cat ~ ., data = df_entrenamiento_depresion, 
    ##     method = "class", control = rpart.control(maxdepth = 10))
    ##   n= 19278 
    ## 
    ##           CP nsplit rel error    xerror        xstd
    ## 1 0.08023914      0 1.0000000 1.0000000 0.007281152
    ## 2 0.05821271      1 0.9197609 0.9167191 0.007249844
    ## 3 0.01000000      2 0.8615481 0.8633312 0.007203480
    ## 
    ## Variable importance
    ## EmotionalStability  Conscientiousness      Agreeableness           Openness 
    ##                 79                  8                  5                  5 
    ##       Extraversion 
    ##                  3 
    ## 
    ## Node number 1: 19278 observations,    complexity param=0.08023914
    ##   predicted class=Severo  expected loss=0.4945534  P(node) =1
    ##     class counts:  1811  3456  4267  9744
    ##    probabilities: 0.094 0.179 0.221 0.505 
    ##   left son=2 (8566 obs) right son=3 (10712 obs)
    ##   Primary splits:
    ##       EmotionalStability < 3.25 to the right, improve=1101.90400, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve= 343.14590, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve= 292.54760, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 228.50220, (0 missing)
    ##       Agreeableness      < 4.25 to the right, improve=  64.67917, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.25 to the right, agree=0.600, adj=0.101, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.582, adj=0.060, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.577, adj=0.048, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.568, adj=0.027, (0 split)
    ## 
    ## Node number 2: 8566 observations,    complexity param=0.05821271
    ##   predicted class=Normal  expected loss=0.6096194  P(node) =0.4443407
    ##     class counts:  1093  1550  3344  2579
    ##    probabilities: 0.128 0.181 0.390 0.301 
    ##   left son=4 (4353 obs) right son=5 (4213 obs)
    ##   Primary splits:
    ##       EmotionalStability < 4.25 to the right, improve=221.81220, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve=148.19480, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve=127.33130, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 87.23226, (0 missing)
    ##       Agreeableness      < 3.75 to the right, improve= 16.80514, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.75 to the right, agree=0.570, adj=0.125, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.568, adj=0.122, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.547, adj=0.079, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.544, adj=0.073, (0 split)
    ## 
    ## Node number 3: 10712 observations
    ##   predicted class=Severo  expected loss=0.331124  P(node) =0.5556593
    ##     class counts:   718  1906   923  7165
    ##    probabilities: 0.067 0.178 0.086 0.669 
    ## 
    ## Node number 4: 4353 observations
    ##   predicted class=Normal  expected loss=0.4812773  P(node) =0.2258014
    ##     class counts:   520   637  2258   938
    ##    probabilities: 0.119 0.146 0.519 0.215 
    ## 
    ## Node number 5: 4213 observations
    ##   predicted class=Severo  expected loss=0.6104913  P(node) =0.2185393
    ##     class counts:   573   913  1086  1641
    ##    probabilities: 0.136 0.217 0.258 0.390 
    ## 
    ## $frame
    ##                  var     n    wt  dev yval  complexity ncompete nsurrogate
    ## 1 EmotionalStability 19278 19278 9534    4 0.080239144        4          4
    ## 2 EmotionalStability  8566  8566 5222    3 0.058212712        4          4
    ## 4             <leaf>  4353  4353 2095    3 0.005506608        0          0
    ## 5             <leaf>  4213  4213 2572    4 0.006660373        0          0
    ## 3             <leaf> 10712 10712 3547    4 0.000000000        0          0
    ##       yval2.V1     yval2.V2     yval2.V3     yval2.V4     yval2.V5     yval2.V6
    ## 1 4.000000e+00 1.811000e+03 3.456000e+03 4.267000e+03 9.744000e+03 9.394128e-02
    ## 2 3.000000e+00 1.093000e+03 1.550000e+03 3.344000e+03 2.579000e+03 1.275975e-01
    ## 4 3.000000e+00 5.200000e+02 6.370000e+02 2.258000e+03 9.380000e+02 1.194578e-01
    ## 5 4.000000e+00 5.730000e+02 9.130000e+02 1.086000e+03 1.641000e+03 1.360076e-01
    ## 3 4.000000e+00 7.180000e+02 1.906000e+03 9.230000e+02 7.165000e+03 6.702763e-02
    ##       yval2.V7     yval2.V8     yval2.V9 yval2.nodeprob
    ## 1 1.792717e-01 2.213404e-01 5.054466e-01   1.000000e+00
    ## 2 1.809479e-01 3.903806e-01 3.010740e-01   4.443407e-01
    ## 4 1.463359e-01 5.187227e-01 2.154836e-01   2.258014e-01
    ## 5 2.167102e-01 2.577736e-01 3.895087e-01   2.185393e-01
    ## 3 1.779313e-01 8.616505e-02 6.688760e-01   5.556593e-01
    ## 
    ## $where
    ##     3     6     7     8    10    11    12    13    15    16    17    18    20 
    ##     3     3     5     4     3     5     5     3     5     5     5     5     5 
    ##    21    22    23    26    27    28    29    31    32    33    34    35    36 
    ##     3     5     5     5     3     3     5     3     4     5     5     5     5 
    ##    38    39    41    44    45    47    48    50    52    53    55    56    57 
    ##     5     3     3     3     5     3     4     4     3     3     5     5     5 
    ##    58    59    60    62    63    64    65    66    67    70    71    73    74 
    ##     4     5     3     3     5     5     3     5     5     4     5     5     5 
    ##    75    80    81    83    84    85    86    87    88    90    91    92    93 
    ##     5     5     5     5     4     5     5     5     3     3     5     4     5 
    ##    96    97    98    99   101   102   103   104   105   106   107   109   112 
    ##     3     5     5     4     3     5     3     4     5     4     3     5     3 
    ##   113   114   115   116   118   119   120   121   122   123   124   126   127 
    ##     5     5     4     5     5     3     5     3     3     5     5     5     5 
    ##   128   129   130   131   134   135   136   138   139   140   143   145   146 
    ##     5     5     4     4     5     3     4     3     5     5     4     5     5 
    ##   147   149   150   151   153   154   155   156   158   159   161   162   163 
    ##     4     5     5     5     5     3     5     5     4     3     5     5     5 
    ##   164   165   167   168   169   170   171   173   174   175   176   177   178 
    ##     4     5     3     5     3     5     5     4     5     5     5     3     5 
    ##   179   180   182   184   185   186   187   189   191   193   194   195   196 
    ##     3     5     4     5     3     3     3     3     5     3     5     3     5 
    ##   197   199   200   202   203   204   205   206   207   208   211   212   213 
    ##     3     5     5     4     5     5     3     5     5     3     4     5     3 
    ##   214   215   219   220   221   222   223   226   227   228   230   232   233 
    ##     3     4     4     5     4     5     5     5     5     3     3     4     5 
    ##   235   236   237   239   241   243   244   245   246   247   248   249   250 
    ##     4     4     5     5     3     3     5     5     3     4     5     5     4 
    ##   251   252   253   254   256   258   259   260   262   263   264   265   267 
    ##     3     3     5     3     4     5     5     4     5     4     5     5     5 
    ##   269   270   272   273   277   278   280   281   282   283   284   285   286 
    ##     3     5     5     5     5     5     5     5     5     4     4     3     3 
    ##   287   288   290   291   292   293   294   297   299   301   302   304   305 
    ##     5     5     5     5     3     5     4     3     5     4     5     4     4 
    ##   307   308   310   311   312   313   314   315   316   317   318   321   322 
    ##     5     3     5     3     5     4     5     4     4     5     5     5     5 
    ##   326   327   328   329   330   333   334   335   337   338   340   342   343 
    ##     5     5     4     5     5     3     5     5     5     3     3     4     5 
    ##   344   345   348   350   351   352   353   354   355   356   357   358   359 
    ##     5     5     3     3     5     5     5     3     3     5     5     5     5 
    ##   360   361   362   364   367   369   371   372   373   374   375   376   377 
    ##     5     5     5     5     5     3     5     5     5     5     3     5     4 
    ##   378   379   381   382   383   384   387   388   389   390   391   397   398 
    ##     5     4     3     3     3     3     5     4     4     5     5     5     5 
    ##   401   402   403   404   405   406   410   411   414   415   416   417   418 
    ##     5     4     5     4     3     5     5     5     4     4     5     5     3 
    ##   420   422   423   424   425   426   427   428   429   431   435   436   437 
    ##     4     5     5     4     5     4     5     3     3     5     3     5     3 
    ##   440   442   443   445   446   447   449   450   454   455   457   459   460 
    ##     5     3     5     3     3     5     3     5     4     5     5     4     5 
    ##   462   463   465   466   467   469   470   471   475   476   478   479   480 
    ##     4     4     3     4     4     5     5     3     5     5     5     5     5 
    ##   482   483   484   485   486   487   489   491   492   493   494   495   496 
    ##     4     5     3     3     4     5     5     5     3     5     5     5     5 
    ##   498   499   501   502   505   506   507   508   509   510   514   515   516 
    ##     4     5     3     5     4     3     3     5     5     5     3     5     5 
    ##   517   518   519   522   526   527   528   530   531   532   534   535   536 
    ##     5     5     3     5     3     5     3     5     5     5     5     5     4 
    ##   537   538   540   541   543   544   545   546   547   550   551   554   555 
    ##     5     5     5     5     5     5     3     4     5     5     4     3     4 
    ##   556   557   558   559   560   562   564   566   567   568   569   570   571 
    ##     5     3     5     5     5     5     5     5     3     5     5     5     4 
    ##   572   573   574   575   576   577   578   580   581   583   584   587   588 
    ##     5     3     5     3     5     5     3     3     5     3     5     4     5 
    ##   594   596   597   598   600   601   603   605   606   607   608   609   611 
    ##     5     4     3     3     5     5     5     5     3     5     5     3     5 
    ##   612   613   615   616   618   620   621   622   623   624   625   627   628 
    ##     3     4     4     5     5     5     4     5     3     4     4     5     3 
    ##   630   631   632   633   634   635   636   640   642   645   646   647   648 
    ##     3     3     5     4     5     5     5     5     3     4     3     5     5 
    ##   649   653   654   657   659   661   665   668   669   670   671   674   676 
    ##     3     5     5     5     5     5     5     3     5     5     5     5     3 
    ##   678   680   681   682   684   686   687   690   691   692   693   694   695 
    ##     3     5     5     3     3     5     4     3     5     5     5     3     3 
    ##   698   699   700   701   703   704   705   708   709   710   711   712   715 
    ##     5     5     5     5     3     5     4     5     5     3     3     5     5 
    ##   716   717   718   719   721   722   723   726   727   728   729   730   731 
    ##     5     3     5     5     3     3     4     3     3     5     4     5     3 
    ##   733   734   736   740   742   743   744   745   746   747   749   751   753 
    ##     3     4     5     3     5     4     4     5     5     4     5     3     4 
    ##   754   755   756   757   758   762   764   765   766   767   768   770   771 
    ##     5     5     5     4     5     5     5     5     5     5     5     3     5 
    ##   772   773   774   775   776   777   778   779   780   782   783   784   785 
    ##     5     5     5     4     3     4     5     4     3     3     5     4     5 
    ##   786   787   788   789   790   791   793   794   795   796   797   798   799 
    ##     5     5     5     3     5     4     4     4     5     5     5     5     5 
    ##   802   803   805   807   809   810   814   817   818   819   823   824   825 
    ##     3     5     5     5     4     3     3     5     3     5     5     5     5 
    ##   827   828   829   830   831   834   836   838   839   841   842   843   844 
    ##     5     5     5     5     5     3     5     3     5     3     3     5     3 
    ##   845   849   850   851   852   854   855   858   859   860   862   863   864 
    ##     5     5     3     5     3     5     4     4     5     5     4     4     3 
    ##   865   866   867   869   870   872   873   877   879   881   882   884   886 
    ##     5     4     3     3     5     3     5     5     4     5     5     5     5 
    ##   888   889   890   891   892   893   894   895   896   898   899   900   901 
    ##     5     5     3     5     5     5     5     4     3     5     5     4     5 
    ##   902   903   904   905   909   910   911   912   913   914   915   916   917 
    ##     5     5     5     5     5     5     4     5     4     5     4     5     4 
    ##   919   920   923   924   926   928   930   931   932   933   934   936   938 
    ##     4     5     3     3     5     3     5     4     5     5     5     3     5 
    ##   939   940   941   942   943   944   945   946   947   948   949   950   953 
    ##     3     3     5     5     4     5     4     5     5     5     3     5     5 
    ##   954   955   956   957   959   961   962   963   964   965   966   967   968 
    ##     5     5     5     5     5     5     5     5     3     5     4     5     5 
    ##   969   970   971   972   974   975   976   980   981   982   983   985   986 
    ##     4     4     5     5     3     5     5     5     5     5     5     4     5 
    ##   987   989   990   991   992   993   994   995   996   997   999  1000  1003 
    ##     5     5     5     5     5     5     4     5     5     4     5     5     5 
    ##  1004  1005  1007  1008  1009  1010  1011  1012  1013  1014  1015  1017  1018 
    ##     5     5     5     4     5     5     3     5     5     4     3     3     5 
    ##  1027  1029  1030  1031  1032  1033  1034  1037  1039  1040  1041  1043  1044 
    ##     3     3     4     5     5     5     3     3     3     5     3     5     3 
    ##  1045  1046  1054  1055  1056  1057  1058  1059  1061  1062  1063  1064  1065 
    ##     4     3     3     5     5     5     5     5     4     5     5     3     5 
    ##  1066  1067  1068  1069  1070  1071  1072  1073  1074  1075  1076  1077  1078 
    ##     4     5     4     3     5     3     4     3     5     5     3     5     5 
    ##  1081  1082  1083  1085  1086  1087  1088  1090  1091  1092  1093  1095  1096 
    ##     5     3     3     5     4     4     3     3     4     5     5     5     5 
    ##  1097  1098  1099  1100  1101  1102  1104  1107  1108  1109  1110  1111  1113 
    ##     5     5     3     5     3     5     5     3     5     5     5     5     4 
    ##  1114  1116  1117  1118  1120  1122  1124  1126  1128  1129  1131  1132  1134 
    ##     5     4     5     5     5     5     5     3     4     5     5     4     4 
    ##  1138  1139  1140  1141  1142  1143  1144  1145  1146  1148  1149  1152  1154 
    ##     3     5     5     5     5     5     5     4     5     4     3     4     3 
    ##  1156  1157  1160  1161  1164  1165  1166  1167  1169  1170  1171  1172  1174 
    ##     4     5     5     5     3     5     5     5     5     3     4     5     4 
    ##  1175  1176  1179  1181  1183  1184  1185  1186  1187  1188  1189  1190  1191 
    ##     4     5     5     5     4     5     3     5     5     5     5     4     3 
    ##  1193  1194  1196  1197  1198  1201  1202  1203  1204  1205  1206  1208  1209 
    ##     5     5     3     4     5     5     5     4     5     5     5     4     3 
    ##  1211  1212  1213  1214  1215  1216  1218  1219  1220  1222  1225  1226  1229 
    ##     5     5     3     4     5     4     3     5     5     4     4     5     5 
    ##  1230  1232  1233  1234  1236  1237  1239  1242  1243  1244  1245  1246  1248 
    ##     5     3     5     4     5     5     3     3     5     5     5     5     5 
    ##  1249  1251  1252  1256  1257  1258  1260  1261  1262  1264  1266  1267  1268 
    ##     3     3     4     5     5     4     5     5     5     5     4     3     5 
    ##  1269  1270  1271  1272  1274  1277  1278  1279  1280  1281  1282  1285  1286 
    ##     4     4     5     5     5     4     5     4     4     3     4     3     3 
    ##  1287  1289  1291  1294  1295  1296  1297  1299  1302  1303  1305  1308  1311 
    ##     4     3     3     4     3     5     5     4     3     3     5     5     5 
    ##  1312  1314  1315  1318  1320  1321  1322  1323  1326  1327  1330  1331  1333 
    ##     5     3     3     5     4     5     4     4     4     4     5     3     5 
    ##  1335  1336  1337  1338  1340  1341  1342  1344  1345  1347  1350  1351  1353 
    ##     5     3     5     5     5     3     5     5     4     4     3     3     3 
    ##  1354  1355  1357  1358  1359  1360  1361  1362  1364  1366  1367  1370  1371 
    ##     5     5     5     3     5     5     5     3     4     5     5     4     5 
    ##  1372  1373  1374  1375  1376  1377  1378  1379  1381  1382  1383  1385  1387 
    ##     5     5     4     5     4     4     5     5     5     5     5     3     5 
    ##  1388  1389  1390  1391  1394  1395  1396  1398  1399  1400  1402  1404  1405 
    ##     5     3     5     5     5     4     5     5     5     5     3     5     5 
    ##  1406  1408  1409  1410  1411  1412  1413  1414  1415  1416  1417  1418  1421 
    ##     3     3     5     3     5     3     3     5     5     5     5     3     4 
    ##  1423  1426  1428  1429  1430  1431  1432  1434  1435  1438  1439  1440  1442 
    ##     3     5     5     3     4     5     4     5     5     5     5     5     5 
    ##  1443  1444  1445  1446  1447  1449  1451  1453  1454  1455  1456  1458  1459 
    ##     5     4     5     5     5     5     5     3     3     5     5     5     4 
    ##  1460  1462  1463  1465  1467  1468  1469  1470  1473  1475  1476  1481  1486 
    ##     4     5     5     5     5     3     5     5     4     5     5     5     4 
    ##  1487  1488  1491  1493  1494  1496  1497  1498  1499  1500  1501  1502  1503 
    ##     5     5     5     5     5     3     3     5     5     3     5     5     5 
    ##  1505  1508  1509  1512  1513  1514  1515  1516  1517  1518  1519  1520  1521 
    ##     5     4     4     5     4     5     5     5     5     4     5     5     5 
    ##  1522  1524  1525  1526  1529  1531  1532  1533  1534  1535  1536  1538  1540 
    ##     3     5     4     5     4     5     5     5     5     4     3     4     5 
    ##  1541  1543  1544  1545  1546  1547  1548  1549  1550  1551  1552  1553  1554 
    ##     5     5     5     5     5     5     5     5     5     5     4     5     5 
    ##  1555  1556  1558  1559  1560  1562  1566  1567  1569  1571  1573  1574  1575 
    ##     5     5     5     3     5     5     5     3     4     5     4     5     4 
    ##  1576  1579  1580  1581  1584  1585  1588  1589  1590  1591  1592  1593  1594 
    ##     3     4     5     5     4     5     5     5     4     4     4     3     5 
    ##  1595  1596  1598  1599  1601  1602  1604  1605  1607  1609  1610  1611  1612 
    ##     5     5     5     3     3     5     5     4     5     4     3     5     5 
    ##  1614  1615  1616  1618  1619  1620  1621  1622  1624  1625  1626  1629  1631 
    ##     4     4     3     5     5     5     5     5     5     5     5     4     3 
    ##  1633  1636  1637  1638  1640  1641  1643  1646  1647  1648  1649  1650  1652 
    ##     4     5     5     5     5     5     5     4     5     5     5     5     5 
    ##  1655  1657  1658  1659  1660  1661  1662  1663  1664  1665  1666  1668  1669 
    ##     5     5     5     5     4     5     3     5     5     3     5     5     3 
    ##  1670  1671  1672  1674  1675  1677  1678  1679  1680  1681  1682  1683  1685 
    ##     4     5     5     5     4     5     5     3     5     5     5     4     5 
    ##  1686  1687  1688  1689  1693  1694  1696  1697  1700  1702  1703  1705  1708 
    ##     5     3     3     3     5     5     3     3     4     4     3     4     3 
    ##  1709  1711  1713  1714  1715  1716  1720  1721  1722  1724  1725  1726  1727 
    ##     4     4     4     5     5     5     5     5     5     3     4     5     4 
    ##  1728  1729  1730  1731  1732  1733  1734  1735  1736  1737  1739  1740  1741 
    ##     5     5     4     3     5     5     5     3     5     3     5     4     4 
    ##  1744  1745  1746  1747  1748  1749  1750  1752  1753  1755  1756  1757  1758 
    ##     5     5     5     5     3     5     5     5     5     5     3     5     5 
    ##  1759  1761  1762  1763  1764  1765  1766  1767  1769  1770  1771  1772  1773 
    ##     3     5     5     5     5     3     5     5     5     5     5     3     5 
    ##  1774  1776  1779  1780  1782  1783  1785  1786  1788  1789  1790  1791  1792 
    ##     4     5     5     4     5     5     5     3     5     4     3     3     3 
    ##  1793  1794  1795  1797  1798  1799  1800  1801  1802  1803  1804  1806  1807 
    ##     5     5     4     4     5     5     5     3     5     3     5     5     5 
    ##  1808  1809  1810  1811  1812  1813  1815  1816  1817  1819  1820  1821  1822 
    ##     5     5     5     5     4     5     3     3     3     4     5     3     5 
    ##  1823  1824  1828  1830  1831  1834  1836  1837  1838  1839  1840  1842  1843 
    ##     4     5     3     5     4     3     5     3     4     5     5     5     4 
    ##  1844  1845  1847  1848  1849  1851  1852  1853  1854  1855  1858  1859  1860 
    ##     4     3     5     5     5     5     5     5     5     4     5     5     5 
    ##  1862  1864  1866  1868  1869  1870  1871  1872  1873  1874  1875  1877  1878 
    ##     5     3     4     5     3     5     4     5     4     4     3     3     4 
    ##  1880  1881  1882  1884  1885  1887  1889  1890  1891  1892  1896  1897  1900 
    ##     5     5     3     3     5     3     3     3     5     4     5     4     5 
    ##  1904  1905  1906  1908  1910  1911  1912  1914  1916  1917  1921  1922  1923 
    ##     5     4     5     5     3     5     5     3     3     3     4     5     4 
    ##  1924  1926  1927  1928  1929  1931  1932  1936  1937  1938  1939  1940  1941 
    ##     3     4     5     3     4     5     5     5     5     5     3     5     5 
    ##  1942  1943  1944  1945  1946  1947  1949  1950  1953  1955  1957  1958  1959 
    ##     5     3     5     3     5     5     5     5     4     5     3     5     5 
    ##  1960  1961  1962  1963  1964  1966  1967  1970  1972  1973  1974  1976  1977 
    ##     3     5     5     3     5     5     5     5     5     5     3     5     5 
    ##  1978  1979  1980  1982  1983  1984  1985  1987  1989  1990  1991  1992  1994 
    ##     5     5     5     5     5     4     5     3     5     5     5     3     5 
    ##  1996  1997  1998  1999  2001  2002  2003  2004  2005  2006  2007  2009  2010 
    ##     5     5     5     5     5     5     5     4     5     3     5     3     3 
    ##  2011  2012  2014  2016  2017  2018  2021  2022  2025  2026  2027  2029  2030 
    ##     3     5     4     4     4     5     4     5     3     5     5     3     5 
    ##  2031  2032  2036  2037  2038  2040  2041  2044  2045  2046  2047  2050  2051 
    ##     3     5     5     5     5     3     5     5     5     4     5     4     3 
    ##  2054  2057  2058  2059  2060  2061  2062  2063  2064  2065  2068  2069  2070 
    ##     4     5     5     5     5     5     5     5     4     5     4     4     5 
    ##  2071  2072  2073  2074  2075  2076  2077  2078  2079  2080  2081  2082  2083 
    ##     4     5     5     5     5     5     4     5     5     5     5     5     4 
    ##  2084  2085  2087  2091  2092  2093  2094  2095  2098  2099  2100  2101  2102 
    ##     5     3     5     5     5     3     3     3     5     5     5     4     5 
    ##  2104  2105  2106  2107  2108  2109  2110  2111  2112  2113  2114  2115  2117 
    ##     5     4     5     5     5     4     5     4     3     5     3     5     5 
    ##  2119  2121  2122  2124  2125  2127  2128  2129  2130  2131  2132  2133  2135 
    ##     5     5     5     3     4     3     5     4     5     5     4     3     5 
    ##  2136  2137  2140  2141  2142  2144  2145  2147  2149  2150  2151  2152  2153 
    ##     5     5     3     3     3     4     4     5     5     3     5     5     5 
    ##  2154  2155  2157  2158  2159  2160  2164  2165  2166  2167  2168  2169  2172 
    ##     5     5     5     5     3     5     4     4     3     5     5     5     3 
    ##  2173  2176  2178  2180  2182  2183  2184  2185  2187  2189  2190  2193  2194 
    ##     3     3     5     5     3     5     4     3     4     4     5     4     4 
    ##  2195  2196  2197  2198  2200  2201  2202  2203  2204  2205  2206  2207  2209 
    ##     5     4     4     5     4     5     3     3     5     5     5     5     4 
    ##  2210  2211  2212  2214  2215  2217  2218  2220  2221  2222  2224  2225  2226 
    ##     5     5     5     5     5     3     5     3     5     5     3     5     4 
    ##  2227  2228  2231  2232  2234  2235  2236  2237  2238  2239  2240  2241  2242 
    ##     5     3     5     4     4     3     5     5     4     5     5     5     3 
    ##  2243  2244  2245  2246  2247  2248  2249  2250  2251  2254  2255  2257  2259 
    ##     5     3     5     5     5     4     3     3     5     5     3     5     5 
    ##  2260  2261  2262  2263  2264  2265  2267  2268  2269  2272  2273  2274  2275 
    ##     5     5     3     3     3     5     4     5     5     5     5     5     5 
    ##  2276  2277  2278  2279  2280  2281  2282  2283  2284  2285  2286  2287  2288 
    ##     5     5     5     4     4     3     3     5     5     5     3     5     5 
    ##  2290  2291  2292  2294  2295  2297  2298  2300  2301  2304  2305  2307  2308 
    ##     3     5     4     5     4     5     4     4     5     3     5     5     5 
    ##  2309  2311  2312  2313  2314  2316  2317  2318  2320  2321  2322  2325  2326 
    ##     3     5     5     5     5     5     5     5     5     5     3     5     3 
    ##  2327  2328  2329  2330  2331  2332  2333  2335  2336  2337  2338  2339  2340 
    ##     3     4     5     5     5     5     3     5     5     3     5     3     4 
    ##  2341  2343  2345  2346  2350  2351  2352  2353  2354  2355  2357  2359  2360 
    ##     5     4     5     4     5     5     4     4     3     3     5     5     5 
    ##  2361  2362  2364  2366  2367  2369  2370  2372  2373  2374  2375  2376  2377 
    ##     3     5     3     4     5     5     3     3     3     4     4     5     3 
    ##  2378  2379  2380  2382  2383  2384  2385  2388  2389  2391  2393  2395  2398 
    ##     4     5     3     5     5     5     5     3     3     3     3     3     4 
    ##  2399  2400  2401  2402  2403  2404  2406  2407  2408  2409  2410  2412  2414 
    ##     5     5     5     3     3     3     3     5     4     5     4     5     5 
    ##  2415  2416  2417  2419  2420  2421  2423  2424  2425  2426  2427  2428  2431 
    ##     5     5     3     5     5     5     3     5     4     5     3     4     3 
    ##  2432  2433  2434  2435  2436  2438  2439  2440  2441  2442  2444  2445  2448 
    ##     5     5     4     5     4     5     5     4     5     5     4     5     5 
    ##  2449  2450  2451  2452  2453  2454  2456  2457  2459  2460  2462  2465  2466 
    ##     3     3     5     4     5     3     5     4     5     3     3     5     5 
    ##  2467  2471  2473  2474  2475  2476  2477  2478  2479  2480  2481  2482  2483 
    ##     3     5     5     4     5     5     3     5     3     3     5     5     5 
    ##  2484  2485  2487  2488  2489  2490  2493  2494  2496  2498  2500  2501  2502 
    ##     5     5     3     5     3     5     5     4     5     5     5     5     5 
    ##  2503  2505  2506  2510  2512  2514  2516  2518  2520  2522  2523  2525  2526 
    ##     4     5     5     3     5     5     5     5     4     5     5     5     3 
    ##  2527  2529  2531  2532  2533  2535  2536  2541  2542  2543  2544  2545  2546 
    ##     3     5     5     5     4     3     5     5     4     4     5     5     5 
    ##  2547  2549  2550  2551  2552  2554  2555  2556  2558  2559  2562  2563  2564 
    ##     5     4     3     5     4     4     5     5     5     3     5     3     5 
    ##  2566  2567  2568  2569  2572  2573  2575  2576  2578  2579  2580  2581  2583 
    ##     5     3     3     3     5     5     5     4     5     5     5     5     5 
    ##  2584  2585  2586  2587  2588  2589  2590  2591  2592  2594  2595  2596  2597 
    ##     5     5     5     3     4     5     5     5     4     5     5     5     3 
    ##  2598  2599  2600  2601  2602  2604  2605  2608  2609  2610  2611  2613  2616 
    ##     5     5     3     3     4     3     4     5     4     3     3     3     5 
    ##  2617  2618  2620  2621  2622  2623  2625  2626  2627  2629  2634  2637  2638 
    ##     4     5     5     4     5     5     5     5     5     5     3     4     5 
    ##  2640  2641  2642  2644  2645  2646  2647  2649  2650  2651  2652  2653  2657 
    ##     5     3     5     4     5     5     5     5     3     4     5     4     4 
    ##  2658  2661  2662  2663  2665  2668  2669  2671  2672  2674  2675  2676  2679 
    ##     5     5     3     5     5     5     5     5     4     5     3     3     4 
    ##  2681  2682  2683  2685  2687  2688  2689  2690  2691  2693  2694  2700  2702 
    ##     5     5     3     3     5     5     5     5     4     4     5     5     5 
    ##  2703  2704  2705  2706  2707  2708  2709  2710  2711  2712  2714  2715  2716 
    ##     4     5     5     5     3     5     4     3     5     3     5     5     5 
    ##  2717  2718  2720  2721  2723  2724  2726  2729  2731  2733  2734  2736  2737 
    ##     5     5     5     4     5     5     5     5     5     5     5     4     5 
    ##  2740  2741  2742  2743  2744  2747  2748  2750  2752  2753  2754  2756  2759 
    ##     4     5     5     5     5     5     5     5     4     5     5     5     5 
    ##  2761  2762  2765  2766  2768  2769  2770  2771  2772  2775  2776  2777  2779 
    ##     5     3     3     4     4     5     5     5     3     3     3     5     5 
    ##  2782  2783  2784  2785  2787  2788  2789  2790  2791  2794  2795  2796  2797 
    ##     5     4     3     5     5     5     5     3     5     5     4     5     4 
    ##  2798  2801  2802  2808  2811  2813  2815  2818  2820  2823  2828  2829  2830 
    ##     4     3     5     3     5     5     4     5     5     5     3     4     3 
    ##  2831  2832  2833  2834  2835  2836  2837  2838  2839  2840  2841  2842  2843 
    ##     5     5     5     5     4     3     5     5     5     5     5     5     5 
    ##  2844  2845  2846  2847  2848  2850  2855  2856  2857  2858  2859  2860  2863 
    ##     4     5     4     5     5     4     4     5     4     5     3     5     3 
    ##  2864  2867  2868  2869  2870  2871  2872  2873  2875  2877  2878  2879  2880 
    ##     5     5     5     5     5     5     5     4     5     5     5     5     4 
    ##  2881  2882  2884  2885  2886  2887  2888  2889  2890  2891  2892  2894  2896 
    ##     5     5     4     5     5     3     5     5     5     5     4     5     5 
    ##  2897  2898  2899  2900  2901  2902  2903  2904  2906  2907  2908  2909  2910 
    ##     5     3     3     5     4     5     4     5     3     5     4     3     5 
    ##  2912  2913  2914  2916  2917  2918  2919  2920  2922  2923  2924  2925  2926 
    ##     5     5     5     5     3     5     5     5     5     5     5     5     5 
    ##  2927  2929  2930  2932  2934  2935  2936  2937  2938  2940  2941  2945  2947 
    ##     3     4     5     5     3     5     5     3     4     3     5     4     5 
    ##  2948  2949  2950  2951  2955  2956  2958  2959  2960  2962  2964  2965  2966 
    ##     4     5     5     5     5     5     5     4     4     3     4     3     5 
    ##  2967  2970  2971  2972  2973  2975  2977  2978  2979  2980  2981  2983  2984 
    ##     5     5     4     3     3     5     5     5     5     3     5     5     5 
    ##  2985  2986  2987  2988  2989  2990  2992  2993  2994  2996  2997  2998  3000 
    ##     4     5     3     5     5     5     5     5     4     5     5     3     5 
    ##  3002  3003  3004  3005  3006  3007  3008  3009  3011  3012  3013  3014  3015 
    ##     4     4     5     3     5     5     3     3     4     5     5     4     5 
    ##  3016  3018  3019  3020  3021  3022  3026  3027  3028  3029  3032  3033  3034 
    ##     5     4     4     3     5     3     5     3     5     4     3     5     5 
    ##  3035  3036  3037  3040  3041  3042  3043  3044  3046  3048  3051  3054  3056 
    ##     5     5     4     5     3     4     5     4     5     5     5     5     5 
    ##  3057  3058  3060  3061  3062  3063  3065  3066  3068  3069  3071  3072  3073 
    ##     5     4     5     3     5     4     5     3     5     3     3     3     3 
    ##  3074  3075  3078  3079  3081  3082  3083  3084  3085  3086  3087  3088  3091 
    ##     5     5     5     3     5     5     5     5     5     4     5     3     4 
    ##  3092  3095  3096  3097  3099  3101  3102  3103  3106  3107  3108  3109  3111 
    ##     5     5     5     5     3     5     5     3     5     5     5     4     5 
    ##  3113  3114  3115  3116  3119  3121  3125  3126  3127  3128  3129  3130  3131 
    ##     3     3     5     5     3     4     4     5     5     4     4     4     5 
    ##  3134  3137  3138  3139  3143  3144  3147  3148  3149  3152  3155  3157  3158 
    ##     4     4     5     3     5     4     5     4     5     5     4     4     3 
    ##  3159  3160  3162  3163  3165  3166  3167  3168  3169  3171  3173  3174  3175 
    ##     5     5     5     5     5     5     5     5     3     3     4     4     5 
    ##  3176  3178  3179  3180  3182  3183  3184  3188  3190  3191  3193  3195  3196 
    ##     4     5     3     5     5     3     5     3     5     5     5     5     4 
    ##  3199  3200  3201  3202  3204  3205  3206  3211  3212  3214  3215  3216  3217 
    ##     4     5     4     4     5     5     5     5     4     5     5     3     5 
    ##  3218  3219  3220  3221  3222  3223  3225  3226  3227  3229  3230  3231  3232 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     3 
    ##  3234  3235  3236  3237  3238  3239  3240  3241  3242  3243  3244  3246  3247 
    ##     5     5     5     5     5     5     5     5     5     5     3     5     5 
    ##  3248  3249  3251  3253  3254  3255  3257  3258  3262  3263  3264  3265  3266 
    ##     5     4     5     4     4     5     5     5     4     5     5     4     5 
    ##  3267  3268  3270  3271  3273  3274  3276  3277  3278  3279  3281  3282  3283 
    ##     3     5     5     5     3     5     5     5     5     3     4     5     5 
    ##  3284  3285  3287  3288  3289  3290  3295  3296  3297  3298  3299  3300  3301 
    ##     3     5     4     3     3     4     5     4     3     4     5     5     4 
    ##  3302  3303  3304  3305  3306  3307  3308  3309  3310  3311  3312  3313  3317 
    ##     5     5     5     5     4     5     5     3     5     5     5     5     3 
    ##  3318  3319  3320  3321  3322  3324  3325  3327  3328  3329  3330  3331  3332 
    ##     4     5     4     5     5     4     3     5     3     5     4     5     4 
    ##  3333  3334  3337  3339  3340  3341  3345  3348  3349  3350  3352  3353  3355 
    ##     5     5     3     4     5     5     3     5     5     3     3     5     4 
    ##  3356  3357  3358  3359  3362  3364  3365  3366  3368  3369  3370  3371  3372 
    ##     3     5     5     3     3     5     4     3     5     3     3     5     5 
    ##  3373  3375  3376  3377  3378  3379  3380  3383  3385  3386  3387  3388  3389 
    ##     5     3     3     5     5     5     4     5     3     5     5     5     4 
    ##  3390  3391  3392  3394  3396  3397  3398  3399  3400  3401  3403  3404  3405 
    ##     4     3     3     5     5     4     5     4     5     5     5     5     5 
    ##  3406  3407  3409  3410  3413  3414  3417  3418  3421  3422  3425  3427  3429 
    ##     5     3     4     4     5     5     5     3     4     5     4     3     5 
    ##  3431  3432  3434  3435  3436  3437  3439  3442  3443  3446  3447  3448  3450 
    ##     5     4     3     5     5     4     4     3     4     4     5     3     3 
    ##  3451  3455  3456  3457  3459  3460  3461  3462  3463  3465  3466  3467  3468 
    ##     5     5     5     3     5     5     3     5     5     3     5     3     3 
    ##  3469  3470  3473  3474  3475  3476  3478  3480  3481  3483  3487  3488  3489 
    ##     4     5     5     5     5     4     5     5     4     5     4     5     3 
    ##  3490  3491  3492  3495  3496  3498  3499  3500  3501  3502  3503  3507  3508 
    ##     3     4     5     4     4     5     3     3     5     4     5     3     4 
    ##  3509  3512  3515  3516  3518  3519  3521  3523  3526  3527  3529  3531  3533 
    ##     3     3     3     5     5     3     5     5     4     5     5     4     5 
    ##  3537  3538  3540  3541  3543  3544  3547  3549  3551  3552  3553  3554  3556 
    ##     3     5     5     4     4     5     5     5     3     5     5     4     5 
    ##  3559  3560  3563  3564  3566  3567  3568  3569  3571  3572  3573  3576  3577 
    ##     5     5     3     4     5     5     3     4     4     3     4     4     4 
    ##  3578  3581  3582  3586  3587  3588  3589  3590  3591  3593  3597  3598  3599 
    ##     5     4     5     3     5     5     5     5     4     5     5     3     5 
    ##  3600  3601  3603  3604  3606  3607  3608  3609  3611  3612  3613  3614  3616 
    ##     4     5     4     5     5     3     5     5     4     4     3     5     4 
    ##  3618  3619  3621  3622  3623  3627  3631  3632  3634  3635  3638  3640  3641 
    ##     4     4     3     5     5     3     5     3     3     5     4     3     3 
    ##  3642  3643  3644  3645  3646  3647  3648  3649  3650  3651  3652  3653  3654 
    ##     5     5     3     4     5     4     4     3     4     5     5     4     3 
    ##  3655  3656  3658  3659  3660  3661  3662  3663  3665  3666  3667  3668  3671 
    ##     3     5     5     4     3     5     3     5     5     3     3     5     4 
    ##  3673  3674  3676  3677  3678  3679  3681  3682  3683  3684  3686  3687  3689 
    ##     3     4     5     3     3     5     5     3     5     5     4     3     4 
    ##  3690  3691  3692  3693  3694  3695  3697  3698  3699  3701  3702  3704  3705 
    ##     4     3     4     5     4     5     4     3     3     5     5     3     5 
    ##  3706  3707  3711  3714  3715  3716  3717  3719  3720  3723  3724  3725  3726 
    ##     5     3     5     5     4     3     4     3     5     5     5     5     3 
    ##  3727  3728  3729  3730  3732  3733  3734  3735  3737  3741  3742  3743  3746 
    ##     4     4     3     5     4     5     5     5     3     3     3     3     3 
    ##  3747  3748  3749  3750  3752  3754  3755  3756  3762  3763  3764  3767  3769 
    ##     5     4     5     3     3     4     5     3     4     5     3     3     5 
    ##  3772  3774  3775  3776  3777  3778  3779  3783  3784  3785  3786  3787  3788 
    ##     5     5     5     5     3     5     5     3     4     5     4     5     4 
    ##  3789  3792  3796  3798  3799  3800  3801  3802  3804  3808  3809  3810  3811 
    ##     5     3     3     5     5     3     5     5     5     5     5     3     5 
    ##  3812  3814  3815  3816  3817  3818  3821  3822  3823  3825  3827  3828  3829 
    ##     5     4     4     4     5     3     5     4     3     5     4     4     5 
    ##  3830  3831  3833  3834  3835  3836  3837  3838  3840  3841  3842  3843  3844 
    ##     5     5     5     3     4     5     5     3     3     5     3     3     5 
    ##  3845  3846  3848  3849  3850  3852  3854  3855  3857  3858  3859  3860  3862 
    ##     5     5     4     5     3     5     5     4     5     4     5     5     3 
    ##  3863  3865  3867  3868  3869  3870  3871  3874  3875  3876  3877  3878  3879 
    ##     3     3     3     4     4     5     5     3     3     3     5     5     5 
    ##  3881  3882  3883  3887  3888  3889  3891  3892  3893  3894  3896  3897  3900 
    ##     4     5     3     5     5     5     5     4     3     5     5     3     4 
    ##  3901  3905  3906  3907  3908  3909  3910  3911  3912  3914  3915  3916  3917 
    ##     3     5     5     5     3     4     5     5     4     3     5     4     4 
    ##  3918  3919  3921  3923  3924  3925  3926  3927  3929  3930  3931  3932  3934 
    ##     3     5     5     5     5     5     5     5     4     4     5     4     5 
    ##  3935  3936  3937  3938  3939  3940  3943  3944  3945  3946  3947  3948  3951 
    ##     4     5     5     3     3     4     5     3     5     5     5     5     5 
    ##  3952  3954  3955  3956  3957  3958  3959  3961  3962  3965  3967  3968  3969 
    ##     5     5     4     3     5     3     5     5     5     4     5     3     5 
    ##  3970  3971  3972  3974  3975  3976  3978  3979  3980  3985  3986  3987  3988 
    ##     5     3     3     3     3     4     5     5     5     4     5     5     4 
    ##  3990  3993  3994  3997  4000  4001  4002  4003  4006  4007  4008  4009  4010 
    ##     5     5     5     3     5     3     3     3     5     4     5     5     4 
    ##  4012  4014  4016  4017  4018  4019  4020  4021  4022  4024  4025  4028  4029 
    ##     5     5     4     5     3     5     5     5     3     4     5     3     5 
    ##  4030  4032  4033  4035  4037  4039  4040  4042  4043  4044  4045  4046  4049 
    ##     4     3     5     4     5     3     3     3     3     3     4     5     5 
    ##  4052  4053  4054  4055  4056  4057  4058  4059  4060  4061  4062  4065  4070 
    ##     5     5     3     5     3     3     5     5     4     5     3     4     5 
    ##  4071  4072  4073  4075  4077  4078  4081  4082  4083  4086  4087  4088  4090 
    ##     3     5     5     5     5     4     3     4     3     4     3     4     5 
    ##  4091  4092  4093  4094  4096  4097  4098  4099  4100  4106  4107  4108  4109 
    ##     5     4     4     4     4     3     5     5     3     4     5     5     3 
    ##  4110  4111  4112  4114  4115  4116  4117  4118  4119  4122  4123  4124  4125 
    ##     5     5     5     5     4     5     4     4     5     3     3     3     5 
    ##  4126  4127  4128  4131  4132  4133  4134  4136  4137  4138  4139  4141  4142 
    ##     5     4     5     3     5     3     5     5     4     5     5     4     3 
    ##  4143  4144  4145  4147  4148  4149  4150  4151  4153  4154  4156  4157  4160 
    ##     5     5     4     5     5     5     3     5     5     3     3     4     5 
    ##  4161  4162  4163  4166  4167  4168  4169  4170  4174  4175  4177  4178  4179 
    ##     5     5     5     4     5     5     3     4     5     3     5     5     5 
    ##  4180  4182  4184  4185  4186  4187  4188  4190  4191  4194  4196  4197  4199 
    ##     5     3     3     5     5     5     5     5     4     3     5     4     3 
    ##  4200  4201  4202  4203  4204  4206  4209  4210  4211  4213  4214  4215  4217 
    ##     5     5     4     5     5     5     3     5     5     5     5     4     5 
    ##  4219  4220  4221  4222  4223  4224  4226  4228  4229  4230  4231  4232  4236 
    ##     3     5     4     5     5     4     3     4     4     5     5     4     4 
    ##  4237  4238  4239  4240  4241  4242  4244  4246  4247  4248  4251  4252  4253 
    ##     3     5     3     5     4     4     5     4     5     5     5     4     5 
    ##  4254  4256  4258  4259  4260  4261  4265  4266  4268  4269  4270  4271  4272 
    ##     3     3     5     5     3     3     5     3     5     5     5     5     5 
    ##  4273  4274  4275  4276  4278  4279  4283  4284  4285  4286  4287  4288  4289 
    ##     5     3     5     5     4     5     5     4     5     5     4     5     5 
    ##  4290  4293  4295  4296  4297  4298  4299  4300  4301  4302  4303  4305  4308 
    ##     3     3     3     4     4     5     3     5     5     5     5     5     5 
    ##  4309  4310  4312  4313  4314  4316  4318  4319  4320  4322  4323  4324  4325 
    ##     5     5     4     5     5     4     4     5     5     3     4     4     4 
    ##  4326  4329  4330  4332  4333  4335  4339  4340  4341  4343  4344  4345  4346 
    ##     4     5     4     5     4     4     5     5     5     3     4     5     5 
    ##  4347  4348  4350  4351  4352  4353  4354  4355  4357  4358  4360  4362  4363 
    ##     5     4     5     3     5     3     4     4     5     3     5     5     5 
    ##  4365  4368  4369  4371  4372  4375  4376  4379  4380  4381  4382  4383  4384 
    ##     5     5     3     3     5     3     5     5     5     3     5     5     4 
    ##  4385  4386  4387  4388  4389  4390  4391  4392  4393  4394  4396  4398  4400 
    ##     5     5     5     4     5     4     5     3     4     3     3     3     5 
    ##  4401  4402  4404  4406  4407  4408  4409  4410  4411  4413  4414  4416  4418 
    ##     3     4     5     3     4     5     4     3     3     5     5     5     5 
    ##  4419  4420  4421  4422  4423  4426  4427  4429  4432  4434  4435  4436  4437 
    ##     3     5     4     5     5     4     5     4     4     3     4     5     4 
    ##  4439  4440  4442  4443  4444  4445  4446  4447  4448  4450  4451  4452  4455 
    ##     4     5     5     4     3     5     3     3     3     4     4     5     5 
    ##  4456  4457  4458  4459  4462  4463  4466  4470  4472  4473  4474  4475  4477 
    ##     3     5     5     5     3     3     4     5     3     5     5     3     5 
    ##  4478  4479  4480  4482  4484  4485  4486  4487  4489  4490  4491  4492  4493 
    ##     5     5     5     4     5     5     3     3     5     4     4     5     5 
    ##  4495  4496  4497  4498  4500  4503  4504  4506  4507  4509  4510  4511  4512 
    ##     5     3     3     5     5     3     3     5     3     3     5     5     5 
    ##  4513  4514  4516  4518  4520  4521  4522  4523  4524  4525  4526  4527  4528 
    ##     5     3     4     4     5     5     5     4     5     3     3     4     4 
    ##  4529  4531  4533  4535  4536  4537  4538  4540  4542  4543  4544  4545  4546 
    ##     3     4     4     5     5     3     4     5     4     4     3     5     5 
    ##  4547  4548  4549  4550  4551  4552  4553  4554  4555  4557  4562  4563  4564 
    ##     3     5     4     4     5     5     5     5     5     3     3     5     5 
    ##  4565  4566  4567  4568  4569  4571  4572  4574  4575  4576  4578  4579  4580 
    ##     3     4     3     5     5     4     4     4     4     4     3     3     5 
    ##  4581  4582  4583  4584  4585  4586  4588  4589  4590  4591  4592  4593  4596 
    ##     4     5     5     4     5     4     4     5     5     3     5     5     3 
    ##  4597  4598  4599  4600  4602  4604  4606  4607  4608  4609  4611  4613  4614 
    ##     4     4     4     5     5     3     5     3     4     4     5     3     4 
    ##  4616  4617  4618  4619  4620  4621  4622  4623  4624  4625  4626  4627  4628 
    ##     3     4     4     5     3     5     5     4     5     5     5     3     5 
    ##  4631  4632  4633  4636  4637  4638  4639  4641  4642  4644  4646  4647  4649 
    ##     5     5     5     3     3     4     4     4     4     3     4     4     5 
    ##  4651  4654  4655  4657  4658  4661  4662  4663  4664  4665  4666  4667  4668 
    ##     3     5     4     3     4     3     5     5     5     5     5     4     3 
    ##  4670  4671  4672  4673  4674  4677  4678  4680  4681  4684  4685  4686  4687 
    ##     5     3     5     3     5     5     4     5     5     4     5     3     5 
    ##  4688  4689  4690  4691  4692  4693  4695  4698  4699  4700  4701  4703  4704 
    ##     5     3     5     3     4     4     3     4     5     5     4     4     5 
    ##  4705  4706  4708  4709  4710  4711  4713  4714  4715  4716  4717  4718  4719 
    ##     5     3     5     5     4     5     5     4     5     5     5     4     5 
    ##  4721  4722  4724  4725  4726  4728  4730  4731  4732  4734  4736  4738  4739 
    ##     5     5     5     3     5     5     3     5     5     5     5     3     5 
    ##  4741  4742  4743  4744  4746  4749  4750  4751  4753  4754  4756  4757  4758 
    ##     5     5     4     4     4     5     4     5     5     3     5     5     4 
    ##  4759  4761  4762  4764  4765  4767  4769  4771  4772  4773  4774  4776  4778 
    ##     4     4     5     5     5     5     5     3     5     5     4     5     5 
    ##  4779  4781  4782  4783  4785  4786  4787  4789  4790  4792  4795  4798  4802 
    ##     3     3     3     3     3     5     5     3     5     3     5     5     5 
    ##  4804  4805  4807  4808  4810  4811  4812  4813  4815  4818  4819  4820  4822 
    ##     4     5     4     4     5     5     5     5     5     4     5     3     5 
    ##  4823  4825  4826  4827  4828  4829  4830  4831  4832  4833  4834  4836  4837 
    ##     5     4     4     5     5     5     5     3     5     5     3     3     5 
    ##  4840  4841  4844  4845  4847  4848  4849  4850  4851  4852  4853  4854  4857 
    ##     3     5     5     5     5     5     5     4     5     3     3     5     4 
    ##  4858  4859  4860  4861  4862  4863  4866  4867  4868  4870  4874  4877  4878 
    ##     4     5     5     5     3     4     3     5     3     5     5     5     3 
    ##  4881  4882  4883  4884  4886  4887  4888  4889  4890  4891  4892  4893  4894 
    ##     5     5     5     4     5     5     5     4     5     3     3     4     5 
    ##  4895  4896  4897  4898  4899  4902  4903  4905  4906  4907  4908  4909  4910 
    ##     5     4     4     5     5     5     5     3     5     3     4     5     4 
    ##  4912  4913  4914  4915  4916  4918  4919  4920  4922  4923  4925  4926  4928 
    ##     5     3     3     5     4     4     3     5     5     5     4     4     4 
    ##  4929  4930  4931  4932  4933  4934  4935  4936  4940  4942  4943  4944  4946 
    ##     3     3     4     4     5     5     5     3     5     5     4     3     5 
    ##  4948  4949  4950  4951  4952  4953  4954  4955  4956  4957  4958  4959  4960 
    ##     5     5     4     4     5     4     3     4     5     3     5     4     5 
    ##  4961  4963  4964  4965  4968  4970  4972  4973  4974  4975  4978  4980  4981 
    ##     4     4     5     5     3     3     4     3     5     4     5     5     3 
    ##  4982  4983  4984  4985  4987  4988  4991  4992  4993  4994  4996  4999  5000 
    ##     4     4     3     3     3     3     5     4     3     3     4     3     3 
    ##  5001  5002  5004  5006  5008  5011  5014  5016  5018  5019  5020  5021  5022 
    ##     5     5     5     3     4     4     5     5     5     4     5     4     5 
    ##  5023  5024  5026  5028  5031  5032  5034  5036  5037  5039  5040  5042  5044 
    ##     5     5     5     5     5     4     5     4     5     5     4     3     5 
    ##  5046  5048  5049  5050  5051  5052  5053  5054  5055  5057  5058  5059  5060 
    ##     5     5     3     3     3     3     5     4     3     5     4     5     5 
    ##  5063  5064  5066  5067  5068  5069  5070  5071  5073  5075  5076  5077  5079 
    ##     5     5     4     5     5     5     5     4     5     5     3     5     5 
    ##  5080  5084  5085  5086  5087  5088  5090  5091  5094  5095  5097  5098  5099 
    ##     3     5     3     5     5     5     4     3     3     4     5     5     4 
    ##  5100  5101  5102  5103  5104  5106  5107  5109  5111  5113  5114  5116  5117 
    ##     3     3     5     3     5     4     3     5     5     5     4     5     5 
    ##  5118  5121  5122  5123  5127  5128  5130  5131  5132  5133  5135  5136  5137 
    ##     5     3     4     5     3     5     4     5     4     5     5     4     4 
    ##  5138  5140  5141  5144  5145  5146  5147  5149  5150  5152  5153  5154  5155 
    ##     5     5     4     5     5     5     3     3     4     4     4     3     5 
    ##  5156  5157  5158  5159  5160  5161  5162  5163  5164  5166  5168  5169  5170 
    ##     5     5     4     5     5     5     3     4     3     3     3     5     5 
    ##  5172  5173  5177  5178  5179  5180  5181  5182  5184  5186  5187  5188  5189 
    ##     5     5     3     3     5     5     5     3     5     5     3     5     5 
    ##  5190  5192  5193  5195  5197  5199  5200  5203  5206  5207  5211  5212  5214 
    ##     5     5     4     5     4     3     5     3     3     5     4     4     4 
    ##  5216  5218  5222  5223  5227  5228  5229  5230  5232  5233  5234  5235  5236 
    ##     3     3     3     5     5     5     4     3     4     5     4     5     5 
    ##  5238  5243  5244  5245  5246  5247  5250  5251  5252  5253  5256  5257  5258 
    ##     4     5     5     3     3     3     5     5     5     5     5     3     3 
    ##  5260  5261  5262  5263  5264  5265  5267  5268  5269  5270  5272  5274  5275 
    ##     4     5     5     5     5     5     4     3     4     5     4     4     4 
    ##  5276  5278  5280  5281  5282  5283  5284  5285  5287  5288  5289  5290  5292 
    ##     5     3     5     5     5     5     4     5     3     4     5     4     5 
    ##  5294  5295  5296  5297  5298  5299  5300  5301  5303  5304  5305  5306  5308 
    ##     4     5     4     5     5     5     5     3     5     5     3     5     3 
    ##  5309  5310  5311  5316  5317  5319  5322  5323  5324  5326  5327  5328  5329 
    ##     3     5     4     5     5     3     3     5     5     5     5     3     4 
    ##  5330  5331  5333  5336  5337  5338  5339  5340  5343  5344  5346  5347  5349 
    ##     4     3     4     5     5     5     3     3     4     5     5     5     5 
    ##  5351  5352  5353  5354  5355  5356  5358  5361  5363  5364  5365  5367  5369 
    ##     5     3     4     3     5     5     5     5     5     5     5     4     4 
    ##  5370  5372  5373  5374  5375  5376  5378  5379  5380  5381  5382  5384  5387 
    ##     5     3     5     3     5     5     5     5     5     5     5     3     4 
    ##  5389  5390  5391  5392  5393  5394  5395  5396  5397  5398  5399  5400  5401 
    ##     3     5     3     4     4     5     3     3     5     4     4     4     4 
    ##  5402  5403  5405  5407  5408  5409  5410  5411  5412  5413  5414  5417  5418 
    ##     3     3     3     4     4     3     5     4     4     5     5     4     4 
    ##  5419  5422  5424  5427  5430  5431  5432  5433  5434  5436  5437  5438  5439 
    ##     5     5     3     5     5     5     5     5     5     4     5     5     3 
    ##  5442  5444  5446  5449  5450  5451  5452  5453  5454  5455  5456  5458  5460 
    ##     5     5     5     5     3     5     3     3     5     5     3     4     5 
    ##  5462  5463  5465  5466  5467  5468  5469  5470  5472  5474  5475  5476  5477 
    ##     3     3     5     3     3     5     5     5     5     4     3     5     4 
    ##  5478  5480  5482  5484  5485  5486  5487  5488  5489  5491  5492  5493  5495 
    ##     3     3     5     5     5     5     3     5     5     5     5     5     4 
    ##  5496  5497  5498  5500  5501  5504  5505  5506  5507  5508  5509  5510  5511 
    ##     5     3     5     4     3     5     5     4     5     4     4     5     5 
    ##  5512  5514  5515  5516  5517  5518  5519  5520  5521  5522  5523  5524  5525 
    ##     4     3     4     5     5     5     5     3     3     3     5     5     4 
    ##  5526  5528  5532  5533  5536  5537  5539  5542  5545  5546  5549  5550  5551 
    ##     5     4     5     3     5     5     4     4     4     4     4     3     3 
    ##  5553  5554  5555  5556  5559  5560  5562  5563  5564  5565  5566  5567  5568 
    ##     5     5     4     5     4     3     4     3     4     4     3     4     3 
    ##  5570  5572  5573  5575  5578  5579  5580  5584  5586  5587  5588  5589  5591 
    ##     3     5     5     3     5     5     4     3     4     3     4     5     5 
    ##  5592  5593  5596  5598  5600  5601  5602  5603  5604  5606  5607  5609  5610 
    ##     4     5     5     3     5     3     4     3     5     5     4     5     5 
    ##  5611  5615  5617  5619  5622  5623  5624  5625  5626  5627  5628  5629  5630 
    ##     5     4     3     4     5     4     5     3     4     4     4     4     5 
    ##  5631  5632  5633  5634  5635  5636  5638  5639  5640  5642  5643  5644  5645 
    ##     3     3     5     4     4     3     4     4     5     5     5     5     5 
    ##  5646  5647  5649  5650  5651  5652  5653  5654  5655  5656  5660  5661  5665 
    ##     5     3     4     4     3     3     4     3     5     3     3     5     4 
    ##  5666  5667  5668  5669  5670  5671  5672  5673  5675  5676  5677  5678  5679 
    ##     4     5     5     4     5     3     3     3     5     3     5     5     4 
    ##  5680  5681  5682  5683  5685  5687  5688  5691  5693  5696  5698  5699  5700 
    ##     4     5     4     3     4     4     5     5     4     5     3     4     4 
    ##  5701  5702  5703  5704  5705  5706  5707  5708  5709  5710  5711  5712  5714 
    ##     5     4     5     5     4     3     3     4     5     4     3     4     3 
    ##  5715  5716  5717  5718  5719  5720  5723  5724  5725  5728  5730  5731  5732 
    ##     3     3     5     3     5     5     5     4     3     4     5     3     5 
    ##  5733  5734  5735  5737  5738  5739  5740  5741  5742  5743  5746  5747  5748 
    ##     4     3     4     5     4     3     3     3     3     5     5     3     4 
    ##  5749  5751  5752  5753  5754  5756  5757  5758  5759  5760  5761  5763  5764 
    ##     3     5     3     3     5     3     3     5     3     5     5     4     5 
    ##  5765  5766  5769  5770  5771  5772  5774  5775  5776  5781  5782  5786  5787 
    ##     5     5     4     3     5     5     4     3     5     5     5     4     3 
    ##  5788  5790  5791  5793  5794  5795  5798  5799  5800  5802  5803  5806  5807 
    ##     5     5     5     5     3     5     5     5     4     5     4     4     5 
    ##  5808  5813  5814  5815  5818  5820  5821  5822  5824  5825  5828  5829  5830 
    ##     4     5     5     3     4     3     4     3     4     4     5     5     3 
    ##  5831  5832  5833  5835  5836  5837  5840  5841  5843  5844  5846  5847  5848 
    ##     4     3     3     5     5     5     3     3     5     4     5     4     5 
    ##  5849  5850  5851  5853  5854  5856  5857  5859  5860  5863  5864  5865  5866 
    ##     3     3     5     5     4     5     5     5     5     5     5     5     5 
    ##  5867  5868  5869  5873  5874  5877  5878  5879  5881  5882  5883  5884  5885 
    ##     3     3     5     3     4     3     3     3     4     3     3     3     3 
    ##  5887  5890  5891  5892  5893  5896  5897  5898  5899  5900  5901  5902  5903 
    ##     5     3     5     3     5     5     3     5     3     4     4     3     4 
    ##  5904  5905  5906  5908  5910  5914  5915  5916  5917  5918  5920  5921  5922 
    ##     4     5     4     3     5     5     3     5     4     3     5     3     4 
    ##  5923  5926  5927  5930  5931  5932  5933  5935  5936  5938  5939  5941  5942 
    ##     5     5     3     5     4     4     4     3     5     4     5     5     5 
    ##  5944  5945  5946  5947  5948  5949  5951  5952  5953  5954  5958  5959  5960 
    ##     4     3     5     5     3     5     4     3     5     3     4     5     3 
    ##  5962  5963  5964  5965  5967  5968  5969  5972  5973  5974  5975  5976  5978 
    ##     3     3     4     3     5     5     4     4     5     3     5     5     5 
    ##  5980  5982  5983  5984  5985  5986  5988  5990  5993  5994  5996  5997  5998 
    ##     3     5     4     5     5     3     4     4     5     4     4     3     5 
    ##  5999  6000  6004  6006  6007  6008  6009  6011  6014  6018  6019  6020  6021 
    ##     3     5     5     5     4     4     5     5     5     3     4     3     4 
    ##  6023  6024  6025  6027  6029  6031  6032  6033  6035  6037  6039  6040  6041 
    ##     5     4     3     3     5     5     3     4     5     5     4     5     4 
    ##  6042  6043  6044  6045  6047  6048  6049  6052  6053  6054  6055  6058  6059 
    ##     5     5     5     5     5     5     5     4     3     4     5     5     5 
    ##  6062  6063  6064  6065  6066  6067  6069  6070  6072  6074  6076  6078  6080 
    ##     5     5     5     3     5     4     5     3     3     5     5     4     5 
    ##  6081  6083  6084  6085  6086  6087  6088  6089  6090  6092  6095  6097  6098 
    ##     4     5     5     5     5     3     5     4     3     5     3     3     5 
    ##  6099  6100  6102  6103  6104  6105  6106  6107  6108  6110  6111  6112  6113 
    ##     3     5     3     3     3     5     4     3     3     3     5     5     5 
    ##  6114  6115  6116  6117  6118  6119  6121  6122  6123  6124  6125  6126  6127 
    ##     4     5     4     4     5     3     3     5     5     5     5     4     4 
    ##  6129  6130  6132  6133  6134  6136  6139  6140  6141  6142  6143  6145  6146 
    ##     3     5     5     5     5     5     4     3     3     3     3     4     5 
    ##  6147  6148  6149  6150  6153  6154  6155  6156  6157  6158  6159  6162  6163 
    ##     3     3     3     3     3     5     5     4     3     4     4     5     3 
    ##  6165  6166  6167  6169  6173  6174  6177  6179  6180  6181  6182  6183  6184 
    ##     3     5     5     3     5     5     3     3     5     4     5     4     4 
    ##  6186  6188  6189  6191  6194  6195  6196  6197  6198  6199  6200  6201  6202 
    ##     5     5     5     5     4     4     3     5     5     5     4     5     5 
    ##  6208  6210  6211  6213  6215  6216  6217  6218  6220  6221  6222  6223  6224 
    ##     4     5     4     3     5     5     4     3     3     3     4     3     5 
    ##  6226  6227  6228  6229  6230  6234  6237  6238  6239  6240  6242  6243  6244 
    ##     4     4     3     4     5     5     5     5     4     5     4     5     4 
    ##  6246  6247  6250  6251  6252  6255  6257  6258  6260  6261  6262  6263  6265 
    ##     4     5     4     3     4     5     5     5     5     5     5     5     5 
    ##  6266  6267  6269  6270  6272  6273  6275  6276  6277  6279  6281  6282  6283 
    ##     4     5     5     5     3     4     5     5     5     4     5     3     5 
    ##  6284  6285  6288  6289  6290  6292  6294  6296  6298  6299  6301  6302  6305 
    ##     5     5     5     4     5     5     3     4     4     4     4     5     5 
    ##  6306  6307  6308  6309  6310  6312  6313  6315  6316  6318  6319  6322  6323 
    ##     5     4     4     3     5     5     3     5     5     5     4     4     5 
    ##  6324  6327  6328  6330  6331  6334  6336  6337  6338  6340  6342  6344  6345 
    ##     4     3     5     5     5     5     4     4     5     3     3     3     4 
    ##  6346  6347  6348  6349  6350  6351  6352  6353  6357  6359  6360  6361  6363 
    ##     5     5     5     3     5     3     3     4     5     5     5     4     4 
    ##  6365  6366  6367  6368  6369  6370  6372  6374  6375  6376  6377  6379  6381 
    ##     5     3     5     3     3     5     3     5     5     5     3     5     4 
    ##  6383  6384  6385  6386  6387  6388  6389  6390  6391  6392  6393  6394  6395 
    ##     3     3     4     3     4     4     4     4     3     3     3     5     4 
    ##  6396  6398  6399  6401  6402  6404  6405  6406  6407  6408  6409  6410  6411 
    ##     3     4     5     4     5     3     4     4     5     3     5     5     3 
    ##  6413  6416  6417  6419  6421  6422  6423  6424  6425  6429  6430  6432  6433 
    ##     4     3     5     3     3     4     5     5     5     5     3     4     5 
    ##  6434  6435  6436  6438  6440  6442  6443  6445  6446  6447  6448  6449  6452 
    ##     4     4     4     5     3     5     3     3     5     5     4     4     4 
    ##  6455  6456  6457  6458  6459  6462  6465  6467  6468  6469  6470  6471  6473 
    ##     5     5     5     5     5     5     5     4     5     5     5     4     4 
    ##  6474  6475  6476  6477  6478  6479  6480  6481  6482  6485  6486  6487  6489 
    ##     4     4     3     5     5     4     5     3     5     3     3     4     3 
    ##  6490  6491  6492  6493  6497  6498  6499  6500  6501  6505  6506  6507  6510 
    ##     5     4     3     4     5     4     5     5     4     4     5     3     5 
    ##  6512  6513  6515  6516  6517  6518  6519  6520  6521  6522  6523  6524  6525 
    ##     3     3     3     4     5     5     3     5     5     3     4     5     5 
    ##  6526  6528  6529  6530  6531  6532  6533  6535  6536  6538  6539  6541  6542 
    ##     5     5     5     5     5     5     4     5     5     5     3     3     3 
    ##  6543  6545  6548  6549  6550  6551  6552  6553  6554  6555  6556  6557  6558 
    ##     3     5     5     5     4     5     3     5     5     4     5     4     5 
    ##  6559  6560  6561  6563  6565  6567  6568  6569  6573  6574  6575  6576  6577 
    ##     5     3     4     5     5     4     3     3     5     5     5     5     5 
    ##  6578  6580  6581  6582  6583  6584  6585  6586  6587  6589  6592  6594  6596 
    ##     3     3     5     5     3     4     5     5     5     5     4     5     4 
    ##  6597  6598  6599  6601  6602  6604  6605  6606  6607  6610  6611  6612  6613 
    ##     5     3     4     5     4     5     5     5     5     5     4     5     5 
    ##  6616  6617  6618  6619  6620  6624  6625  6626  6627  6628  6629  6631  6633 
    ##     5     5     3     4     3     3     5     5     4     4     4     5     3 
    ##  6634  6635  6636  6641  6642  6644  6645  6646  6647  6648  6649  6650  6651 
    ##     4     5     5     5     5     3     4     5     5     4     3     5     3 
    ##  6653  6655  6656  6657  6658  6659  6660  6663  6665  6666  6669  6670  6671 
    ##     4     4     3     4     3     5     5     4     4     3     5     3     5 
    ##  6672  6673  6674  6676  6677  6679  6680  6682  6683  6687  6689  6691  6692 
    ##     4     5     5     3     4     3     5     5     5     5     4     5     5 
    ##  6693  6694  6695  6698  6699  6700  6701  6702  6703  6706  6707  6709  6710 
    ##     5     5     5     5     3     4     4     3     4     4     5     4     5 
    ##  6714  6717  6718  6720  6721  6726  6727  6729  6730  6731  6732  6733  6737 
    ##     3     5     3     4     5     5     4     5     5     5     5     4     4 
    ##  6738  6740  6741  6742  6743  6744  6745  6748  6749  6750  6752  6754  6757 
    ##     3     3     5     3     5     3     4     3     5     5     5     5     5 
    ##  6759  6762  6763  6764  6765  6766  6767  6768  6769  6770  6773  6774  6777 
    ##     5     3     5     5     5     5     5     5     3     5     3     3     4 
    ##  6782  6783  6784  6786  6787  6789  6792  6793  6794  6795  6796  6797  6798 
    ##     5     4     5     5     5     5     5     4     3     5     5     3     4 
    ##  6799  6802  6803  6804  6805  6806  6808  6809  6810  6814  6815  6818  6819 
    ##     4     5     4     5     5     5     5     3     5     3     5     4     3 
    ##  6820  6822  6824  6825  6827  6828  6832  6834  6835  6836  6837  6839  6841 
    ##     3     3     4     3     3     5     5     3     5     5     3     3     5 
    ##  6843  6844  6847  6848  6849  6851  6852  6853  6854  6855  6856  6857  6858 
    ##     5     4     4     5     5     5     3     3     5     5     5     3     5 
    ##  6859  6861  6863  6865  6866  6867  6868  6869  6870  6871  6872  6876  6880 
    ##     5     5     3     3     4     3     5     5     3     5     5     5     3 
    ##  6881  6883  6884  6885  6886  6889  6890  6891  6892  6893  6894  6895  6896 
    ##     4     4     4     5     3     4     5     4     4     5     5     3     5 
    ##  6897  6898  6899  6901  6902  6903  6904  6908  6909  6910  6911  6914  6916 
    ##     5     5     4     5     4     5     4     3     4     3     3     5     4 
    ##  6917  6918  6919  6920  6921  6923  6925  6927  6929  6930  6931  6935  6936 
    ##     3     5     3     4     5     4     5     5     5     4     4     3     5 
    ##  6938  6939  6940  6941  6942  6943  6944  6945  6946  6947  6948  6949  6950 
    ##     3     4     5     5     5     5     5     4     5     4     5     4     5 
    ##  6952  6953  6955  6956  6957  6958  6959  6960  6962  6963  6964  6965  6967 
    ##     5     4     5     3     5     5     4     5     5     5     5     4     3 
    ##  6968  6969  6970  6971  6972  6974  6975  6977  6978  6979  6980  6981  6982 
    ##     5     5     5     5     4     5     4     5     5     5     4     5     4 
    ##  6983  6984  6985  6986  6989  6990  6991  6993  6995  6996  6997  7001  7002 
    ##     4     5     5     3     5     5     5     5     4     5     4     5     5 
    ##  7005  7006  7007  7008  7010  7012  7013  7015  7016  7017  7019  7020  7023 
    ##     4     5     4     5     3     5     4     5     5     4     4     5     5 
    ##  7025  7026  7027  7028  7029  7033  7034  7035  7036  7037  7038  7040  7041 
    ##     3     5     3     3     3     5     4     4     5     3     4     5     4 
    ##  7042  7044  7045  7047  7051  7052  7053  7054  7056  7057  7059  7060  7061 
    ##     4     4     5     4     4     4     5     3     5     5     3     5     5 
    ##  7063  7066  7067  7068  7069  7070  7071  7072  7074  7075  7076  7077  7078 
    ##     5     4     4     5     5     3     5     3     5     5     5     5     3 
    ##  7079  7080  7081  7082  7083  7084  7086  7088  7089  7090  7091  7092  7094 
    ##     5     5     5     3     3     4     3     3     3     5     4     5     5 
    ##  7095  7096  7098  7099  7101  7102  7103  7104  7105  7106  7108  7109  7110 
    ##     5     4     5     4     5     3     5     5     4     5     5     5     5 
    ##  7111  7112  7113  7114  7116  7118  7120  7121  7123  7124  7125  7127  7130 
    ##     5     5     5     5     4     5     3     5     5     5     4     5     4 
    ##  7131  7132  7133  7134  7136  7137  7138  7139  7140  7141  7143  7144  7145 
    ##     3     3     5     5     5     5     4     3     5     5     5     4     5 
    ##  7146  7148  7149  7150  7152  7153  7154  7156  7157  7158  7159  7160  7161 
    ##     3     5     3     3     5     4     3     4     5     3     4     5     5 
    ##  7162  7163  7164  7167  7168  7169  7170  7171  7172  7173  7175  7176  7177 
    ##     5     5     4     5     4     5     3     4     4     4     3     4     5 
    ##  7178  7180  7181  7182  7184  7185  7187  7188  7189  7190  7191  7192  7193 
    ##     5     5     5     4     3     4     5     5     3     5     5     5     5 
    ##  7194  7195  7197  7198  7199  7200  7201  7203  7204  7206  7207  7209  7210 
    ##     5     3     5     3     3     3     5     4     4     4     4     5     5 
    ##  7211  7213  7214  7215  7216  7217  7218  7219  7221  7222  7223  7224  7226 
    ##     5     5     5     5     5     3     4     5     5     5     5     5     5 
    ##  7227  7228  7229  7231  7233  7234  7235  7236  7237  7239  7241  7242  7243 
    ##     3     5     5     5     5     5     5     5     5     5     5     3     5 
    ##  7244  7245  7252  7253  7254  7255  7256  7257  7258  7259  7262  7263  7264 
    ##     4     3     4     5     4     5     4     3     5     5     5     4     5 
    ##  7268  7272  7273  7274  7275  7276  7277  7278  7280  7281  7282  7283  7285 
    ##     5     5     3     3     5     4     5     5     5     3     5     5     4 
    ##  7286  7287  7288  7289  7290  7291  7292  7293  7294  7295  7296  7297  7300 
    ##     5     5     4     4     5     3     3     5     3     5     5     5     5 
    ##  7302  7305  7306  7307  7308  7309  7312  7313  7315  7316  7318  7319  7320 
    ##     5     5     5     5     5     4     3     5     5     4     5     4     5 
    ##  7321  7324  7325  7327  7330  7332  7333  7334  7335  7336  7337  7338  7340 
    ##     3     5     5     5     3     5     4     5     5     4     5     3     5 
    ##  7341  7342  7344  7345  7346  7347  7348  7350  7351  7353  7354  7357  7359 
    ##     3     5     4     5     5     3     5     5     4     5     5     5     5 
    ##  7360  7361  7362  7363  7366  7368  7369  7371  7372  7373  7374  7375  7377 
    ##     5     4     4     5     5     5     5     5     3     5     5     5     5 
    ##  7378  7380  7381  7382  7383  7384  7385  7386  7387  7389  7391  7395  7397 
    ##     5     4     5     4     4     5     3     5     3     3     4     4     5 
    ##  7398  7399  7400  7401  7402  7403  7406  7407  7408  7409  7410  7412  7413 
    ##     3     4     5     5     5     5     5     5     3     5     4     5     5 
    ##  7414  7416  7417  7418  7419  7420  7421  7422  7424  7425  7426  7427  7428 
    ##     5     5     4     5     4     5     5     3     4     5     5     3     5 
    ##  7430  7431  7433  7435  7436  7437  7438  7440  7442  7444  7445  7449  7450 
    ##     4     4     5     5     5     5     5     5     5     4     5     5     5 
    ##  7451  7452  7454  7455  7456  7457  7458  7460  7461  7462  7464  7465  7467 
    ##     5     4     5     3     5     4     4     5     3     5     4     3     4 
    ##  7471  7473  7474  7475  7476  7477  7478  7481  7482  7483  7484  7485  7487 
    ##     3     5     5     3     5     4     4     3     5     5     5     5     5 
    ##  7488  7490  7491  7494  7496  7498  7500  7501  7503  7504  7506  7508  7511 
    ##     4     5     5     3     4     4     4     5     4     5     4     3     5 
    ##  7512  7513  7516  7518  7519  7520  7521  7522  7524  7526  7527  7528  7533 
    ##     5     3     3     4     5     5     5     5     3     3     3     3     4 
    ##  7534  7535  7536  7537  7538  7539  7540  7541  7542  7543  7544  7546  7547 
    ##     3     5     5     5     5     5     5     4     5     5     5     5     5 
    ##  7549  7552  7553  7554  7555  7556  7557  7558  7559  7560  7561  7563  7564 
    ##     4     5     3     5     4     4     3     3     5     5     5     5     4 
    ##  7565  7567  7569  7570  7571  7574  7575  7576  7577  7578  7579  7580  7581 
    ##     5     5     5     5     5     3     5     5     5     5     4     5     5 
    ##  7582  7583  7586  7588  7590  7591  7592  7594  7595  7597  7598  7599  7600 
    ##     5     5     5     4     4     4     5     5     5     5     4     5     4 
    ##  7602  7603  7604  7605  7607  7608  7609  7610  7611  7613  7615  7617  7618 
    ##     5     5     4     5     5     5     5     5     4     5     5     3     4 
    ##  7619  7620  7621  7622  7623  7624  7626  7627  7628  7631  7633  7635  7637 
    ##     3     3     5     4     5     4     5     5     5     4     5     5     4 
    ##  7639  7640  7642  7643  7644  7645  7647  7648  7650  7651  7652  7654  7655 
    ##     5     4     5     5     5     5     4     5     4     5     5     5     5 
    ##  7656  7657  7658  7660  7661  7662  7664  7667  7669  7670  7671  7672  7675 
    ##     5     4     3     5     5     4     5     4     5     5     5     5     5 
    ##  7676  7677  7678  7680  7681  7682  7683  7684  7685  7686  7688  7689  7691 
    ##     5     3     4     5     5     4     5     4     4     3     5     5     5 
    ##  7694  7695  7696  7697  7698  7699  7700  7702  7703  7704  7705  7708  7709 
    ##     4     5     4     5     5     3     5     3     5     3     3     5     4 
    ##  7710  7711  7712  7713  7714  7716  7717  7718  7720  7721  7723  7724  7725 
    ##     5     3     4     5     5     4     5     5     4     5     3     5     5 
    ##  7726  7727  7730  7731  7732  7733  7734  7736  7737  7738  7739  7740  7741 
    ##     5     5     5     3     4     4     5     4     4     5     5     5     4 
    ##  7742  7745  7746  7748  7749  7750  7751  7752  7753  7754  7755  7756  7757 
    ##     3     5     5     3     5     5     5     5     5     3     5     3     5 
    ##  7758  7760  7762  7763  7764  7766  7767  7768  7770  7772  7773  7774  7775 
    ##     5     4     4     5     5     5     4     5     5     5     3     5     3 
    ##  7778  7779  7780  7781  7782  7784  7785  7786  7787  7788  7789  7790  7791 
    ##     5     3     5     3     5     5     4     5     5     5     5     5     5 
    ##  7792  7794  7796  7798  7799  7801  7802  7803  7804  7805  7806  7807  7808 
    ##     5     5     3     3     5     5     4     5     4     5     5     5     5 
    ##  7809  7810  7812  7813  7814  7815  7816  7817  7818  7819  7820  7822  7823 
    ##     3     5     5     5     4     5     5     5     5     5     5     4     3 
    ##  7824  7825  7826  7827  7828  7830  7831  7832  7834  7835  7836  7837  7838 
    ##     3     5     5     5     4     3     5     5     3     5     5     3     5 
    ##  7839  7841  7842  7843  7845  7846  7847  7848  7850  7851  7852  7853  7854 
    ##     4     5     5     5     5     4     5     5     4     5     5     5     3 
    ##  7858  7859  7860  7861  7862  7863  7864  7865  7866  7867  7868  7870  7871 
    ##     4     4     3     5     5     4     3     3     5     5     5     3     5 
    ##  7872  7875  7876  7877  7879  7881  7884  7885  7886  7887  7888  7889  7890 
    ##     5     5     4     5     5     5     5     4     5     5     4     5     5 
    ##  7891  7893  7894  7895  7896  7897  7898  7899  7900  7901  7903  7904  7905 
    ##     4     5     3     4     5     5     3     4     3     5     5     4     3 
    ##  7906  7909  7912  7913  7914  7915  7916  7917  7919  7921  7922  7923  7925 
    ##     5     4     3     5     5     5     5     5     4     4     5     5     5 
    ##  7929  7930  7931  7935  7936  7939  7940  7941  7942  7944  7945  7946  7948 
    ##     5     5     5     4     3     5     5     3     5     5     5     5     5 
    ##  7949  7950  7952  7955  7956  7957  7961  7962  7964  7967  7968  7969  7971 
    ##     4     5     3     4     5     5     5     5     4     5     3     3     5 
    ##  7972  7973  7974  7976  7979  7982  7984  7985  7987  7988  7989  7990  7993 
    ##     4     5     5     3     5     4     5     5     3     4     5     4     5 
    ##  7995  7996  8001  8002  8003  8004  8005  8006  8007  8008  8009  8010  8011 
    ##     4     5     5     5     4     5     5     5     4     5     5     5     5 
    ##  8012  8013  8014  8015  8018  8019  8023  8024  8025  8026  8028  8029  8031 
    ##     4     3     4     5     5     4     5     5     3     5     5     3     4 
    ##  8032  8033  8036  8037  8038  8040  8041  8043  8044  8045  8046  8049  8050 
    ##     3     4     5     4     5     4     4     5     5     5     4     5     4 
    ##  8051  8052  8053  8054  8056  8059  8060  8061  8063  8064  8065  8066  8067 
    ##     5     5     3     3     5     4     5     5     5     5     5     3     3 
    ##  8068  8069  8070  8071  8074  8075  8077  8078  8080  8082  8083  8084  8088 
    ##     3     4     5     5     3     3     4     5     5     5     4     4     4 
    ##  8089  8090  8091  8093  8094  8096  8098  8100  8101  8102  8104  8106  8108 
    ##     5     5     5     5     3     4     5     4     5     3     4     5     5 
    ##  8110  8113  8114  8115  8116  8117  8118  8124  8125  8126  8127  8128  8129 
    ##     3     5     5     5     5     3     3     5     5     5     5     5     5 
    ##  8131  8133  8134  8136  8137  8139  8140  8141  8143  8144  8146  8147  8149 
    ##     5     5     5     5     4     5     5     4     5     5     5     3     4 
    ##  8152  8153  8154  8155  8156  8158  8160  8163  8164  8165  8167  8173  8175 
    ##     4     5     3     3     3     5     5     4     5     4     3     4     5 
    ##  8176  8178  8180  8182  8185  8186  8187  8188  8189  8190  8192  8193  8194 
    ##     3     3     5     5     4     5     4     5     5     5     5     5     5 
    ##  8195  8197  8198  8199  8200  8201  8202  8203  8204  8207  8208  8210  8211 
    ##     3     5     5     3     3     3     3     5     5     3     4     5     5 
    ##  8212  8215  8216  8217  8218  8219  8221  8222  8223  8225  8226  8227  8229 
    ##     5     3     3     3     5     4     5     5     4     5     4     5     4 
    ##  8230  8231  8232  8234  8235  8236  8240  8241  8245  8247  8249  8250  8252 
    ##     5     5     5     5     4     5     5     3     5     5     5     4     5 
    ##  8253  8254  8255  8256  8257  8258  8259  8261  8262  8264  8265  8266  8267 
    ##     5     5     4     3     3     4     5     5     5     5     5     4     4 
    ##  8268  8269  8270  8271  8272  8273  8274  8276  8277  8278  8279  8280  8282 
    ##     3     5     5     5     5     5     4     3     4     3     4     3     4 
    ##  8284  8285  8287  8288  8289  8290  8291  8292  8293  8295  8297  8298  8300 
    ##     5     5     5     3     5     4     5     5     5     3     3     5     4 
    ##  8301  8302  8303  8304  8306  8309  8310  8311  8312  8315  8316  8317  8318 
    ##     3     5     5     4     3     5     3     3     3     5     3     5     5 
    ##  8321  8323  8324  8325  8326  8327  8328  8330  8335  8337  8338  8339  8340 
    ##     3     3     3     5     3     5     4     3     4     5     4     5     5 
    ##  8341  8342  8343  8344  8345  8347  8348  8350  8351  8352  8353  8354  8357 
    ##     3     4     5     3     5     5     3     3     3     3     5     5     3 
    ##  8361  8362  8363  8364  8365  8366  8367  8369  8371  8372  8373  8374  8375 
    ##     3     5     5     5     4     3     4     3     3     3     4     5     4 
    ##  8376  8377  8378  8380  8381  8382  8383  8384  8385  8386  8387  8388  8389 
    ##     4     3     5     5     3     4     4     5     5     5     4     5     3 
    ##  8390  8392  8393  8394  8395  8396  8397  8398  8399  8405  8406  8408  8409 
    ##     5     3     5     4     5     5     5     3     5     3     5     5     4 
    ##  8410  8415  8417  8419  8420  8421  8423  8424  8426  8427  8428  8430  8431 
    ##     5     5     5     5     5     4     5     5     5     3     5     5     3 
    ##  8432  8434  8436  8437  8438  8439  8441  8442  8445  8448  8449  8450  8452 
    ##     3     5     5     5     4     5     4     4     5     5     4     5     5 
    ##  8453  8455  8456  8457  8458  8459  8460  8461  8462  8463  8464  8466  8468 
    ##     4     4     5     5     5     5     4     5     4     5     4     5     5 
    ##  8469  8470  8471  8472  8473  8475  8476  8477  8478  8479  8480  8481  8482 
    ##     5     4     3     4     5     5     5     3     3     3     3     5     5 
    ##  8485  8487  8488  8489  8490  8492  8493  8495  8496  8498  8499  8500  8501 
    ##     5     3     5     5     5     5     5     4     5     5     4     5     5 
    ##  8502  8503  8504  8505  8506  8507  8508  8510  8511  8512  8514  8515  8516 
    ##     4     5     4     5     5     3     5     4     5     5     5     4     5 
    ##  8517  8518  8519  8520  8521  8524  8526  8527  8529  8530  8531  8532  8533 
    ##     3     5     3     4     3     5     4     5     4     5     3     5     5 
    ##  8534  8535  8536  8537  8538  8540  8541  8542  8545  8546  8547  8548  8549 
    ##     5     4     3     3     5     4     3     3     5     3     4     5     5 
    ##  8551  8552  8553  8555  8559  8560  8561  8562  8563  8565  8569  8570  8571 
    ##     3     5     5     5     5     3     3     5     3     4     5     5     4 
    ##  8572  8573  8574  8575  8577  8579  8582  8583  8584  8585  8586  8587  8588 
    ##     5     4     3     3     4     3     5     5     4     5     4     5     5 
    ##  8590  8592  8593  8594  8597  8599  8600  8601  8603  8605  8606  8607  8608 
    ##     5     4     5     5     5     5     5     3     5     5     5     5     5 
    ##  8609  8610  8612  8613  8614  8616  8617  8618  8619  8620  8621  8622  8626 
    ##     5     5     5     5     4     5     5     5     5     5     5     5     4 
    ##  8627  8628  8629  8630  8631  8632  8633  8635  8636  8637  8638  8641  8646 
    ##     5     5     5     5     5     5     5     4     4     3     5     5     3 
    ##  8647  8648  8649  8651  8653  8654  8656  8657  8659  8660  8661  8662  8664 
    ##     5     5     5     5     5     5     5     5     5     3     5     5     5 
    ##  8665  8666  8667  8668  8670  8672  8673  8674  8675  8676  8678  8679  8680 
    ##     3     5     5     5     3     5     5     5     5     5     5     5     4 
    ##  8682  8683  8684  8685  8689  8690  8691  8692  8693  8694  8696  8697  8698 
    ##     3     4     4     3     5     5     4     5     5     3     4     3     3 
    ##  8699  8700  8701  8703  8705  8706  8708  8709  8710  8711  8716  8717  8718 
    ##     5     3     5     5     4     5     5     5     5     5     5     5     5 
    ##  8719  8721  8722  8723  8725  8726  8727  8728  8729  8733  8734  8735  8737 
    ##     3     5     5     3     3     3     3     3     5     3     4     5     4 
    ##  8738  8739  8740  8741  8742  8743  8746  8747  8749  8750  8752  8753  8754 
    ##     5     4     5     3     4     4     4     4     3     4     5     3     3 
    ##  8755  8756  8757  8758  8759  8760  8761  8762  8763  8764  8765  8766  8767 
    ##     5     5     4     5     3     5     4     5     4     3     5     5     3 
    ##  8768  8771  8772  8773  8774  8775  8776  8777  8778  8779  8780  8781  8782 
    ##     4     5     3     5     5     3     3     4     3     5     4     5     3 
    ##  8783  8785  8787  8788  8789  8790  8791  8792  8793  8795  8796  8797  8798 
    ##     3     5     5     5     3     3     3     5     5     5     5     3     5 
    ##  8801  8802  8803  8804  8806  8809  8811  8812  8813  8814  8815  8817  8818 
    ##     5     5     3     4     5     5     4     3     5     5     4     5     5 
    ##  8819  8820  8821  8822  8823  8824  8830  8831  8832  8833  8834  8839  8840 
    ##     3     5     3     3     5     4     5     4     5     3     5     3     5 
    ##  8842  8843  8844  8845  8847  8848  8849  8850  8854  8857  8858  8861  8862 
    ##     5     5     4     5     4     4     3     5     5     5     5     5     5 
    ##  8863  8864  8865  8868  8869  8871  8873  8874  8875  8877  8880  8882  8883 
    ##     5     5     3     4     5     3     5     5     3     4     3     4     5 
    ##  8885  8886  8887  8888  8889  8891  8894  8895  8896  8898  8899  8900  8901 
    ##     3     5     4     3     5     3     5     4     5     5     3     4     3 
    ##  8902  8903  8904  8905  8906  8907  8908  8909  8911  8914  8915  8916  8917 
    ##     5     5     5     4     4     5     5     5     5     3     5     3     5 
    ##  8918  8919  8921  8922  8924  8925  8926  8927  8929  8930  8933  8934  8935 
    ##     3     5     5     5     5     5     5     3     5     4     4     3     5 
    ##  8938  8939  8940  8941  8943  8947  8948  8949  8950  8951  8953  8954  8955 
    ##     5     5     5     3     3     4     5     3     5     5     5     5     5 
    ##  8956  8957  8958  8959  8960  8961  8964  8968  8971  8973  8974  8975  8978 
    ##     5     3     3     3     3     5     3     3     5     5     3     5     3 
    ##  8980  8981  8983  8985  8987  8989  8990  8991  8992  8993  8994  8995  8996 
    ##     5     4     5     5     5     4     5     5     5     5     5     5     5 
    ##  8997  8998  8999  9000  9001  9002  9003  9004  9005  9006  9007  9009  9010 
    ##     3     5     3     5     5     3     5     3     4     4     4     3     5 
    ##  9011  9013  9015  9016  9017  9018  9019  9020  9021  9022  9023  9024  9025 
    ##     4     3     5     5     5     5     3     4     4     5     5     5     5 
    ##  9026  9027  9028  9029  9032  9033  9034  9035  9037  9038  9039  9041  9042 
    ##     3     5     4     5     5     5     5     3     3     3     5     5     5 
    ##  9043  9044  9045  9046  9048  9050  9051  9052  9053  9054  9057  9058  9059 
    ##     5     5     4     5     5     3     5     5     5     3     5     5     5 
    ##  9061  9063  9064  9065  9066  9068  9069  9071  9074  9075  9076  9077  9079 
    ##     4     5     4     5     3     5     5     4     5     5     5     3     5 
    ##  9080  9081  9085  9086  9089  9092  9094  9095  9097  9098  9100  9103  9105 
    ##     4     3     5     5     4     3     3     3     5     4     5     5     5 
    ##  9106  9107  9108  9109  9110  9111  9112  9114  9115  9118  9119  9120  9122 
    ##     5     5     3     3     5     5     4     5     5     5     5     3     5 
    ##  9123  9124  9125  9126  9128  9130  9133  9137  9138  9139  9140  9141  9145 
    ##     5     5     5     4     3     5     5     5     4     5     5     4     5 
    ##  9146  9147  9149  9150  9151  9152  9153  9155  9157  9158  9159  9161  9163 
    ##     3     5     4     5     5     5     5     4     5     3     5     5     3 
    ##  9164  9165  9166  9167  9168  9170  9175  9179  9180  9181  9183  9185  9186 
    ##     5     5     4     5     3     5     5     5     4     5     5     5     5 
    ##  9187  9188  9189  9190  9192  9193  9194  9195  9196  9197  9198  9199  9200 
    ##     4     5     5     4     5     5     5     3     4     5     3     3     5 
    ##  9202  9205  9206  9208  9209  9210  9212  9213  9214  9215  9216  9218  9219 
    ##     5     5     3     4     5     5     5     5     5     3     5     5     5 
    ##  9221  9222  9224  9226  9228  9229  9230  9231  9233  9234  9235  9236  9237 
    ##     5     3     5     5     3     5     5     3     5     4     5     3     5 
    ##  9238  9241  9243  9246  9248  9249  9251  9253  9254  9255  9256  9257  9258 
    ##     5     5     5     5     3     5     5     4     5     5     5     5     5 
    ##  9259  9260  9261  9262  9263  9264  9265  9267  9268  9269  9270  9272  9273 
    ##     4     3     3     5     3     5     3     4     3     5     5     5     5 
    ##  9274  9275  9277  9278  9279  9280  9281  9283  9287  9288  9289  9291  9292 
    ##     4     3     3     5     4     5     4     5     5     4     3     4     3 
    ##  9294  9296  9298  9299  9302  9304  9305  9306  9307  9308  9309  9310  9313 
    ##     3     3     3     5     4     4     5     5     5     3     3     5     5 
    ##  9316  9317  9318  9319  9320  9321  9323  9325  9326  9327  9328  9329  9330 
    ##     5     4     4     3     5     3     5     5     4     5     5     3     5 
    ##  9331  9332  9333  9334  9335  9336  9338  9339  9340  9343  9345  9346  9347 
    ##     3     5     4     4     5     5     4     5     5     4     5     5     5 
    ##  9348  9349  9350  9351  9352  9353  9356  9358  9359  9360  9362  9363  9364 
    ##     3     4     3     5     5     5     5     4     5     5     5     5     5 
    ##  9365  9366  9369  9370  9372  9373  9374  9375  9377  9378  9379  9380  9384 
    ##     5     3     5     4     5     5     3     4     5     5     5     5     4 
    ##  9385  9386  9387  9389  9390  9392  9393  9394  9396  9397  9399  9400  9401 
    ##     5     3     3     4     5     5     5     5     4     5     5     3     5 
    ##  9403  9404  9405  9406  9408  9410  9412  9413  9415  9419  9420  9422  9423 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     4 
    ##  9424  9425  9426  9429  9432  9433  9434  9436  9437  9438  9439  9440  9441 
    ##     3     4     5     5     5     3     4     5     5     3     4     5     5 
    ##  9443  9450  9452  9453  9454  9455  9456  9457  9458  9459  9460  9463  9466 
    ##     4     5     5     5     5     5     5     5     5     5     4     5     3 
    ##  9470  9472  9473  9474  9475  9476  9477  9482  9483  9485  9486  9488  9489 
    ##     3     3     5     4     5     5     4     5     5     5     3     5     4 
    ##  9490  9491  9492  9494  9495  9497  9498  9499  9501  9502  9503  9504  9508 
    ##     5     3     3     5     5     4     3     5     3     5     5     5     5 
    ##  9509  9510  9512  9513  9514  9515  9518  9520  9521  9522  9524  9525  9526 
    ##     5     5     5     5     5     5     4     3     4     3     5     4     5 
    ##  9527  9528  9529  9530  9532  9533  9534  9537  9538  9539  9542  9543  9544 
    ##     3     5     5     5     5     5     5     5     5     5     3     5     3 
    ##  9545  9546  9547  9548  9550  9551  9552  9554  9558  9559  9560  9561  9562 
    ##     3     4     5     5     5     3     3     5     5     5     5     5     5 
    ##  9563  9564  9565  9566  9567  9569  9570  9573  9574  9575  9576  9577  9578 
    ##     5     5     3     5     3     3     5     3     5     4     4     5     5 
    ##  9579  9580  9582  9583  9584  9585  9586  9588  9589  9590  9591  9594  9596 
    ##     3     5     4     5     5     4     4     5     5     4     3     5     5 
    ##  9598  9599  9601  9602  9604  9605  9606  9607  9608  9611  9615  9616  9617 
    ##     5     4     4     3     3     3     5     3     3     4     5     3     5 
    ##  9618  9619  9620  9621  9623  9624  9625  9626  9629  9632  9634  9635  9636 
    ##     5     3     4     4     5     5     3     5     5     5     3     3     5 
    ##  9638  9639  9640  9643  9644  9645  9647  9648  9649  9650  9654  9656  9658 
    ##     4     5     5     5     3     5     5     4     5     3     3     4     3 
    ##  9659  9660  9661  9663  9664  9665  9666  9667  9668  9673  9674  9675  9676 
    ##     5     5     5     4     5     5     3     3     3     3     5     3     5 
    ##  9677  9678  9679  9680  9681  9682  9683  9684  9685  9686  9687  9688  9689 
    ##     3     4     5     5     5     5     4     5     5     3     3     5     4 
    ##  9690  9691  9694  9695  9696  9699  9700  9701  9703  9704  9706  9707  9708 
    ##     4     4     4     5     5     3     5     5     5     4     3     5     5 
    ##  9709  9710  9712  9715  9716  9717  9718  9719  9720  9722  9723  9724  9725 
    ##     5     5     5     3     3     5     5     4     3     5     3     5     5 
    ##  9726  9727  9728  9733  9734  9735  9737  9738  9741  9742  9745  9746  9747 
    ##     5     5     3     5     5     5     3     5     3     4     5     5     3 
    ##  9749  9750  9751  9752  9754  9756  9758  9759  9761  9765  9766  9768  9769 
    ##     5     4     5     5     5     5     5     4     5     4     5     5     5 
    ##  9770  9771  9772  9773  9774  9775  9777  9778  9779  9780  9781  9783  9784 
    ##     5     4     5     4     3     5     5     5     5     5     5     5     3 
    ##  9785  9787  9788  9789  9790  9791  9792  9793  9794  9795  9796  9798  9799 
    ##     3     5     3     5     3     4     4     5     5     5     5     5     3 
    ##  9800  9802  9803  9804  9807  9808  9809  9811  9812  9813  9814  9815  9817 
    ##     5     4     5     5     5     5     5     5     5     3     5     5     5 
    ##  9818  9819  9820  9826  9829  9830  9831  9833  9834  9835  9836  9838  9839 
    ##     5     5     5     5     3     5     3     5     3     4     4     5     5 
    ##  9840  9841  9842  9843  9844  9847  9848  9849  9850  9852  9853  9855  9856 
    ##     5     5     4     4     4     4     5     5     5     5     5     5     3 
    ##  9857  9859  9860  9861  9863  9864  9865  9867  9868  9869  9870  9871  9872 
    ##     5     5     4     4     4     5     4     5     5     3     5     4     3 
    ##  9874  9877  9878  9879  9880  9881  9882  9883  9884  9885  9887  9889  9893 
    ##     5     5     5     4     5     5     5     4     5     5     5     5     4 
    ##  9895  9896  9897  9899  9900  9901  9902  9903  9904  9905  9906  9907  9908 
    ##     5     5     5     3     3     5     3     3     5     5     3     5     3 
    ##  9909  9911  9912  9913  9914  9915  9916  9917  9918  9920  9921  9922  9923 
    ##     5     5     4     5     5     5     5     5     5     3     5     5     3 
    ##  9926  9928  9929  9930  9931  9932  9933  9934  9935  9936  9937  9939  9941 
    ##     5     3     4     5     5     4     5     5     4     3     5     5     5 
    ##  9942  9945  9947  9949  9951  9952  9953  9955  9956  9957  9959  9960  9962 
    ##     5     5     3     5     5     5     5     5     4     5     5     5     3 
    ##  9963  9965  9966  9968  9970  9971  9972  9974  9976  9977  9978  9979  9980 
    ##     5     3     5     4     3     5     4     5     5     3     5     4     4 
    ##  9981  9982  9983  9985  9988  9989  9990  9991  9993  9994  9995  9996  9997 
    ##     5     4     3     5     5     3     5     5     5     5     3     5     3 
    ##  9998  9999 10000 10001 10002 10003 10005 10006 10007 10008 10009 10010 10011 
    ##     4     4     5     5     3     5     5     5     4     5     5     3     5 
    ## 10012 10013 10014 10015 10016 10017 10018 10019 10020 10021 10022 10023 10024 
    ##     5     5     3     3     4     5     5     5     5     5     4     5     5 
    ## 10025 10026 10028 10029 10030 10032 10034 10035 10036 10037 10039 10040 10041 
    ##     5     5     5     4     5     4     3     5     5     4     4     5     5 
    ## 10043 10044 10045 10046 10047 10049 10051 10052 10053 10054 10055 10057 10058 
    ##     5     5     5     4     3     4     4     5     5     3     4     5     4 
    ## 10061 10062 10063 10064 10065 10066 10068 10069 10070 10071 10072 10073 10074 
    ##     5     5     5     3     3     5     5     5     5     5     3     3     4 
    ## 10075 10076 10077 10078 10079 10080 10081 10082 10084 10085 10087 10089 10091 
    ##     5     5     3     4     5     5     5     5     5     3     5     4     4 
    ## 10092 10093 10095 10097 10098 10101 10102 10103 10105 10107 10108 10109 10111 
    ##     3     5     4     4     4     3     5     5     5     4     4     3     5 
    ## 10112 10113 10114 10117 10121 10123 10124 10125 10127 10130 10131 10132 10133 
    ##     5     4     5     4     4     5     3     4     5     5     5     5     4 
    ## 10134 10137 10138 10139 10140 10141 10142 10144 10145 10146 10147 10148 10149 
    ##     5     3     5     4     4     3     4     5     4     5     5     5     5 
    ## 10151 10152 10154 10155 10158 10159 10160 10161 10162 10163 10164 10167 10169 
    ##     5     5     3     3     5     3     3     3     5     4     5     3     5 
    ## 10170 10171 10172 10173 10174 10175 10176 10178 10179 10182 10183 10184 10185 
    ##     5     5     3     3     5     5     5     5     5     3     5     5     3 
    ## 10186 10188 10189 10190 10191 10192 10194 10195 10197 10198 10199 10200 10201 
    ##     4     5     3     5     5     5     5     5     3     5     3     5     5 
    ## 10202 10203 10205 10212 10213 10214 10216 10217 10218 10219 10221 10223 10225 
    ##     3     5     5     5     4     3     3     4     3     4     5     3     5 
    ## 10226 10229 10230 10231 10232 10233 10234 10235 10236 10237 10238 10239 10241 
    ##     4     4     3     5     5     5     5     3     5     5     5     3     5 
    ## 10242 10244 10246 10247 10248 10249 10250 10251 10254 10255 10256 10257 10258 
    ##     3     5     5     5     5     4     4     4     3     5     5     5     3 
    ## 10259 10261 10262 10264 10265 10267 10268 10271 10272 10273 10274 10276 10279 
    ##     5     3     4     5     5     5     3     5     5     3     5     4     5 
    ## 10280 10281 10282 10283 10285 10286 10287 10288 10289 10290 10291 10292 10293 
    ##     5     5     5     3     3     3     5     5     3     3     5     5     3 
    ## 10294 10296 10297 10298 10300 10301 10302 10303 10304 10305 10306 10309 10310 
    ##     3     5     3     3     4     3     5     3     5     5     4     5     5 
    ## 10311 10313 10316 10318 10321 10323 10324 10325 10326 10328 10331 10332 10334 
    ##     5     5     3     5     5     5     3     5     4     5     3     3     5 
    ## 10335 10336 10337 10338 10339 10340 10341 10343 10345 10346 10348 10350 10351 
    ##     5     5     5     4     5     3     4     5     5     4     5     4     5 
    ## 10352 10353 10354 10355 10356 10357 10358 10359 10360 10361 10362 10365 10366 
    ##     5     3     5     5     5     5     5     5     5     3     3     5     5 
    ## 10367 10368 10369 10370 10371 10373 10374 10375 10377 10378 10379 10381 10385 
    ##     5     3     3     5     5     5     5     4     5     3     3     4     5 
    ## 10386 10387 10389 10390 10391 10392 10393 10396 10397 10398 10399 10401 10402 
    ##     5     4     3     5     4     4     4     5     5     5     5     4     5 
    ## 10405 10407 10408 10410 10411 10412 10413 10415 10416 10417 10418 10419 10421 
    ##     3     5     5     5     3     5     5     5     4     3     5     3     4 
    ## 10422 10423 10424 10425 10427 10428 10429 10430 10432 10433 10435 10437 10438 
    ##     3     5     5     3     5     5     3     5     5     4     3     4     3 
    ## 10440 10442 10443 10444 10445 10447 10448 10450 10451 10452 10453 10454 10455 
    ##     5     5     3     3     4     5     4     5     5     5     5     3     3 
    ## 10456 10457 10458 10459 10463 10464 10465 10466 10467 10469 10470 10471 10472 
    ##     5     5     5     3     5     5     4     5     5     5     5     3     5 
    ## 10473 10474 10476 10477 10478 10479 10482 10483 10484 10485 10486 10488 10489 
    ##     4     3     5     5     5     4     5     5     5     5     5     5     5 
    ## 10490 10492 10493 10495 10499 10500 10501 10502 10503 10506 10508 10509 10510 
    ##     5     4     3     5     5     5     5     5     4     3     5     5     3 
    ## 10511 10512 10515 10516 10518 10522 10523 10524 10525 10526 10527 10528 10529 
    ##     4     5     5     3     5     5     5     5     5     5     5     5     5 
    ## 10530 10531 10532 10533 10534 10535 10536 10537 10538 10539 10540 10541 10542 
    ##     5     5     4     3     5     3     5     5     5     5     5     4     5 
    ## 10543 10544 10546 10547 10548 10549 10550 10552 10555 10556 10558 10559 10561 
    ##     5     5     5     5     3     5     4     5     5     5     3     4     5 
    ## 10562 10563 10564 10565 10566 10567 10568 10569 10570 10571 10572 10573 10575 
    ##     5     3     5     4     5     3     4     5     5     4     5     5     5 
    ## 10577 10579 10580 10584 10585 10586 10587 10590 10592 10593 10595 10596 10597 
    ##     5     4     5     5     4     5     5     5     3     5     5     3     5 
    ## 10598 10599 10600 10601 10602 10603 10605 10607 10608 10610 10613 10616 10617 
    ##     5     5     3     5     3     4     5     4     5     5     5     5     5 
    ## 10618 10619 10625 10627 10629 10630 10631 10632 10633 10635 10637 10638 10639 
    ##     5     5     5     3     4     4     5     3     4     5     4     5     4 
    ## 10640 10641 10642 10644 10649 10650 10651 10652 10654 10655 10656 10657 10658 
    ##     5     5     5     5     4     4     3     3     3     5     3     4     5 
    ## 10660 10661 10663 10665 10666 10667 10668 10669 10670 10672 10673 10676 10678 
    ##     3     3     5     3     3     4     5     5     3     5     3     3     4 
    ## 10679 10680 10681 10682 10683 10685 10687 10688 10689 10690 10691 10692 10693 
    ##     5     5     5     5     3     4     5     3     4     5     5     4     5 
    ## 10694 10695 10696 10698 10699 10700 10701 10702 10703 10704 10705 10706 10712 
    ##     5     5     5     5     5     4     5     5     5     3     5     5     5 
    ## 10714 10716 10717 10721 10722 10723 10724 10725 10726 10727 10728 10730 10731 
    ##     4     4     5     3     5     5     5     4     5     5     5     3     4 
    ## 10732 10734 10735 10737 10738 10739 10741 10742 10743 10744 10745 10746 10747 
    ##     4     4     3     5     5     5     5     5     5     5     5     5     3 
    ## 10750 10751 10752 10754 10755 10756 10757 10758 10760 10761 10764 10765 10767 
    ##     4     3     5     5     3     5     5     3     5     5     5     3     5 
    ## 10768 10769 10771 10772 10773 10774 10775 10776 10777 10778 10779 10780 10781 
    ##     5     5     5     3     5     5     5     5     4     5     4     5     5 
    ## 10782 10786 10787 10789 10791 10792 10793 10795 10797 10798 10799 10800 10804 
    ##     5     3     3     5     3     3     5     3     5     3     5     3     5 
    ## 10805 10809 10811 10812 10813 10814 10816 10817 10818 10819 10821 10822 10824 
    ##     5     5     5     5     5     3     4     3     5     5     5     5     5 
    ## 10826 10827 10829 10830 10831 10832 10833 10837 10838 10840 10841 10842 10843 
    ##     4     5     3     5     5     5     5     4     5     5     5     3     5 
    ## 10844 10845 10846 10847 10848 10849 10851 10854 10855 10856 10858 10860 10861 
    ##     5     3     3     5     5     5     5     3     5     4     4     5     5 
    ## 10864 10866 10867 10868 10870 10871 10872 10873 10874 10875 10876 10877 10878 
    ##     5     4     4     3     3     5     4     5     5     5     5     5     4 
    ## 10879 10880 10881 10882 10883 10884 10885 10886 10887 10889 10890 10891 10893 
    ##     5     5     5     3     5     4     3     5     5     5     5     5     5 
    ## 10894 10895 10896 10900 10903 10907 10908 10909 10910 10911 10914 10915 10918 
    ##     4     3     5     3     5     5     5     5     3     5     5     5     5 
    ## 10919 10920 10921 10922 10925 10926 10928 10929 10930 10932 10933 10934 10935 
    ##     5     5     5     5     5     5     3     5     3     5     5     5     4 
    ## 10936 10937 10938 10939 10940 10941 10942 10943 10944 10945 10946 10950 10951 
    ##     5     4     4     3     5     5     5     4     4     5     5     5     4 
    ## 10953 10954 10955 10956 10957 10958 10960 10961 10962 10963 10964 10965 10966 
    ##     4     4     4     5     3     5     5     5     3     5     4     5     5 
    ## 10967 10968 10969 10970 10971 10972 10973 10975 10976 10978 10979 10980 10981 
    ##     5     4     5     5     4     5     5     4     5     3     3     5     5 
    ## 10982 10984 10985 10987 10989 10994 10995 10996 10998 11001 11003 11004 11005 
    ##     5     5     5     5     5     5     4     3     5     3     5     3     5 
    ## 11007 11008 11010 11011 11012 11013 11016 11017 11018 11019 11020 11021 11022 
    ##     5     5     3     5     5     5     5     5     3     5     3     3     4 
    ## 11023 11024 11025 11026 11027 11028 11029 11030 11031 11033 11035 11036 11037 
    ##     4     5     5     3     3     5     3     3     5     5     4     4     4 
    ## 11040 11041 11042 11043 11044 11047 11050 11051 11054 11055 11056 11057 11059 
    ##     5     5     4     5     5     5     5     5     5     5     3     5     3 
    ## 11060 11061 11063 11064 11065 11067 11069 11070 11073 11074 11077 11078 11079 
    ##     3     5     3     5     5     5     3     4     5     5     3     3     3 
    ## 11080 11081 11082 11083 11085 11086 11087 11088 11089 11090 11092 11093 11095 
    ##     5     5     5     5     5     5     4     5     5     3     5     4     5 
    ## 11096 11097 11099 11102 11103 11104 11107 11108 11109 11110 11111 11112 11113 
    ##     3     3     5     5     5     3     5     5     5     5     4     4     4 
    ## 11114 11115 11116 11118 11119 11120 11121 11122 11123 11124 11125 11126 11127 
    ##     3     5     5     3     5     4     5     3     5     4     5     5     5 
    ## 11128 11130 11131 11132 11133 11134 11135 11136 11137 11138 11139 11140 11141 
    ##     5     5     5     5     5     5     5     5     3     3     4     5     3 
    ## 11142 11145 11147 11148 11151 11154 11156 11157 11159 11160 11161 11163 11164 
    ##     5     5     5     5     4     3     5     5     5     3     5     5     5 
    ## 11165 11166 11167 11169 11170 11171 11172 11173 11174 11175 11177 11178 11179 
    ##     5     3     4     5     4     5     5     3     5     5     5     5     3 
    ## 11180 11181 11182 11183 11184 11185 11187 11188 11189 11190 11191 11192 11193 
    ##     3     5     4     5     4     3     5     3     5     5     4     5     4 
    ## 11197 11198 11202 11203 11205 11207 11209 11211 11213 11215 11216 11217 11218 
    ##     5     5     5     4     5     3     3     5     4     5     5     3     4 
    ## 11219 11221 11222 11223 11224 11226 11228 11229 11230 11233 11234 11235 11236 
    ##     4     4     5     3     3     5     5     3     3     4     5     5     5 
    ## 11238 11239 11240 11242 11243 11244 11245 11246 11247 11248 11251 11252 11253 
    ##     5     5     5     5     4     3     5     3     5     3     3     5     5 
    ## 11254 11255 11256 11257 11260 11261 11262 11263 11264 11265 11266 11267 11268 
    ##     5     3     4     3     5     5     5     3     5     4     5     5     5 
    ## 11269 11270 11271 11272 11273 11274 11276 11278 11279 11280 11283 11284 11286 
    ##     3     3     5     5     4     5     5     4     4     5     5     5     5 
    ## 11289 11291 11292 11293 11295 11297 11298 11299 11300 11302 11304 11305 11306 
    ##     5     3     5     4     5     3     4     4     4     5     5     5     4 
    ## 11307 11308 11309 11311 11312 11313 11314 11315 11318 11320 11322 11323 11325 
    ##     5     5     5     4     5     5     5     4     3     5     5     5     3 
    ## 11326 11327 11328 11329 11331 11332 11333 11334 11335 11336 11337 11340 11341 
    ##     5     5     5     5     5     4     4     4     3     5     5     4     5 
    ## 11342 11343 11344 11345 11350 11351 11352 11354 11355 11356 11358 11360 11361 
    ##     5     5     3     3     5     4     4     3     5     3     3     3     5 
    ## 11362 11363 11364 11365 11366 11368 11369 11371 11372 11373 11374 11375 11376 
    ##     5     5     5     5     5     5     5     5     3     4     5     5     5 
    ## 11381 11382 11383 11384 11386 11389 11390 11391 11392 11393 11395 11396 11397 
    ##     5     5     5     3     5     3     3     5     5     3     5     3     5 
    ## 11399 11400 11406 11407 11409 11411 11414 11415 11416 11417 11419 11420 11422 
    ##     5     5     5     3     5     5     3     5     5     5     5     4     3 
    ## 11423 11424 11425 11426 11427 11428 11429 11430 11431 11432 11433 11435 11436 
    ##     5     3     5     4     3     4     5     5     3     5     5     5     4 
    ## 11438 11439 11440 11441 11442 11443 11444 11446 11447 11448 11450 11451 11452 
    ##     4     5     5     4     5     3     5     5     3     4     5     3     4 
    ## 11454 11455 11456 11457 11459 11460 11461 11462 11463 11466 11468 11469 11472 
    ##     5     5     4     4     3     4     3     4     5     3     5     5     3 
    ## 11475 11476 11478 11479 11480 11481 11482 11483 11484 11486 11487 11488 11489 
    ##     4     4     5     5     4     4     5     5     5     3     5     5     4 
    ## 11490 11491 11492 11493 11494 11496 11497 11498 11499 11502 11503 11504 11508 
    ##     3     5     4     3     5     5     5     3     5     5     5     5     4 
    ## 11509 11512 11513 11514 11516 11517 11518 11520 11521 11522 11523 11524 11527 
    ##     5     4     4     5     5     5     4     5     4     3     4     5     3 
    ## 11528 11529 11530 11532 11533 11534 11537 11539 11542 11545 11546 11547 11548 
    ##     5     5     4     3     4     5     5     5     5     5     4     5     5 
    ## 11549 11551 11553 11554 11555 11556 11559 11560 11561 11564 11565 11566 11567 
    ##     4     5     5     3     3     5     5     3     5     3     5     3     3 
    ## 11569 11571 11572 11573 11574 11576 11577 11578 11579 11580 11581 11582 11584 
    ##     5     5     5     5     4     4     5     5     5     5     4     5     5 
    ## 11585 11586 11588 11589 11590 11591 11593 11594 11595 11597 11598 11599 11600 
    ##     4     3     4     5     5     3     4     5     4     4     5     5     4 
    ## 11601 11602 11603 11605 11609 11611 11613 11614 11616 11618 11621 11622 11623 
    ##     3     5     3     5     3     4     3     5     3     4     5     3     5 
    ## 11624 11626 11630 11632 11633 11634 11636 11637 11638 11639 11640 11641 11642 
    ##     3     4     5     5     5     4     3     3     3     3     5     5     5 
    ## 11643 11644 11645 11646 11648 11650 11651 11652 11655 11656 11658 11659 11661 
    ##     4     5     5     4     3     5     5     5     3     4     5     5     5 
    ## 11662 11663 11664 11665 11666 11669 11670 11671 11672 11674 11675 11676 11677 
    ##     5     3     3     5     5     5     5     5     3     3     5     5     4 
    ## 11678 11679 11680 11681 11682 11684 11685 11686 11687 11688 11689 11691 11692 
    ##     4     5     5     4     3     5     3     5     3     3     5     4     4 
    ## 11693 11694 11695 11696 11700 11701 11705 11706 11708 11709 11711 11712 11713 
    ##     5     5     5     5     4     5     5     5     4     5     3     4     4 
    ## 11714 11715 11716 11717 11718 11719 11723 11726 11728 11729 11730 11731 11732 
    ##     5     4     4     5     3     5     5     5     5     4     5     5     4 
    ## 11733 11734 11735 11736 11738 11739 11740 11741 11742 11745 11746 11747 11748 
    ##     5     4     4     5     3     5     5     5     5     5     5     5     5 
    ## 11750 11751 11752 11753 11754 11755 11756 11757 11759 11760 11762 11765 11768 
    ##     5     3     3     3     3     4     4     4     4     5     5     4     4 
    ## 11769 11770 11773 11774 11775 11776 11778 11779 11780 11781 11785 11787 11788 
    ##     3     3     5     4     5     3     5     5     4     4     3     5     5 
    ## 11789 11790 11791 11792 11793 11795 11798 11799 11801 11803 11804 11806 11807 
    ##     3     5     3     5     5     3     5     5     5     5     5     5     4 
    ## 11808 11809 11813 11814 11815 11816 11817 11818 11820 11822 11823 11824 11825 
    ##     5     4     3     3     4     3     3     4     4     3     5     5     4 
    ## 11827 11829 11830 11831 11833 11835 11837 11838 11841 11842 11844 11845 11847 
    ##     5     3     5     5     5     5     4     5     5     4     3     5     3 
    ## 11848 11849 11850 11851 11852 11853 11854 11855 11857 11859 11860 11862 11863 
    ##     5     5     5     4     4     4     3     3     5     4     5     5     5 
    ## 11864 11865 11866 11867 11868 11869 11870 11871 11872 11873 11875 11878 11879 
    ##     4     5     5     5     4     4     3     3     3     3     4     5     5 
    ## 11880 11881 11882 11883 11884 11885 11886 11888 11889 11891 11892 11893 11894 
    ##     5     5     3     5     4     4     4     3     4     5     4     4     3 
    ## 11895 11897 11898 11899 11900 11901 11902 11903 11905 11907 11908 11910 11913 
    ##     4     4     5     5     3     4     4     5     5     3     3     4     5 
    ## 11914 11916 11917 11919 11920 11921 11924 11925 11927 11930 11931 11932 11934 
    ##     4     4     5     5     5     3     5     3     5     3     3     5     5 
    ## 11935 11938 11939 11941 11943 11944 11945 11946 11948 11949 11953 11956 11958 
    ##     3     5     3     4     5     3     3     4     5     5     4     4     5 
    ## 11959 11960 11961 11962 11963 11965 11967 11968 11969 11970 11971 11972 11974 
    ##     3     4     3     5     5     3     4     3     5     5     3     4     5 
    ## 11975 11976 11977 11979 11980 11981 11982 11985 11987 11988 11989 11990 11991 
    ##     5     5     5     5     3     4     5     5     3     5     3     3     5 
    ## 11993 11994 11995 11996 11997 11998 11999 12000 12001 12003 12004 12005 12006 
    ##     3     5     4     3     3     4     5     4     3     5     3     5     3 
    ## 12008 12009 12010 12011 12012 12013 12016 12017 12018 12020 12021 12022 12024 
    ##     3     5     5     5     4     3     4     5     5     4     5     5     5 
    ## 12025 12027 12029 12030 12032 12033 12036 12037 12039 12041 12042 12043 12044 
    ##     3     3     5     4     3     5     3     3     5     4     4     5     4 
    ## 12046 12049 12051 12052 12054 12055 12057 12058 12059 12060 12061 12062 12063 
    ##     3     5     3     5     5     3     5     5     5     5     3     5     3 
    ## 12064 12066 12067 12068 12070 12072 12073 12074 12075 12076 12077 12079 12080 
    ##     3     3     3     3     5     4     3     5     5     5     5     4     3 
    ## 12083 12084 12085 12087 12088 12090 12091 12092 12093 12094 12097 12099 12100 
    ##     4     4     5     4     5     3     4     5     5     4     5     5     5 
    ## 12101 12102 12103 12104 12106 12107 12108 12110 12113 12114 12115 12116 12117 
    ##     5     5     4     4     4     4     5     3     5     5     5     3     5 
    ## 12118 12119 12121 12123 12124 12125 12129 12131 12133 12135 12136 12138 12142 
    ##     3     4     5     3     4     5     4     4     4     3     3     4     4 
    ## 12144 12145 12146 12147 12148 12149 12150 12152 12153 12154 12155 12157 12159 
    ##     5     3     4     5     5     5     5     5     3     4     4     4     3 
    ## 12160 12161 12162 12163 12164 12165 12171 12175 12177 12178 12179 12180 12181 
    ##     3     5     3     3     4     5     5     3     5     5     5     4     3 
    ## 12182 12185 12186 12189 12190 12192 12193 12194 12195 12196 12197 12198 12199 
    ##     5     5     5     3     3     5     4     5     5     3     5     5     5 
    ## 12200 12201 12202 12203 12204 12205 12206 12207 12208 12209 12210 12212 12213 
    ##     5     3     3     5     5     3     5     5     5     5     4     5     5 
    ## 12214 12215 12216 12218 12219 12220 12221 12223 12224 12226 12227 12229 12230 
    ##     5     4     5     5     5     5     5     3     3     5     4     4     3 
    ## 12232 12234 12235 12236 12238 12239 12240 12241 12244 12245 12247 12248 12250 
    ##     4     5     5     4     5     5     4     5     5     4     3     4     5 
    ## 12253 12254 12255 12256 12257 12259 12260 12261 12262 12263 12265 12266 12269 
    ##     5     5     5     5     5     5     5     5     3     5     5     5     3 
    ## 12270 12271 12272 12273 12277 12278 12279 12280 12281 12289 12290 12291 12292 
    ##     5     5     5     5     5     3     4     5     5     5     5     4     3 
    ## 12293 12295 12296 12298 12299 12301 12302 12303 12304 12305 12307 12308 12309 
    ##     5     3     5     3     5     4     3     5     3     4     3     5     3 
    ## 12311 12312 12313 12314 12315 12316 12318 12319 12320 12321 12322 12323 12324 
    ##     3     4     3     4     5     5     3     4     5     5     5     5     3 
    ## 12325 12326 12327 12328 12329 12330 12331 12332 12335 12336 12338 12340 12342 
    ##     5     5     3     3     5     5     3     5     4     3     5     5     4 
    ## 12343 12344 12345 12347 12348 12350 12351 12352 12353 12354 12355 12356 12357 
    ##     5     5     5     5     3     5     4     4     5     3     4     5     5 
    ## 12359 12361 12362 12364 12365 12366 12367 12369 12371 12375 12376 12377 12378 
    ##     3     5     4     5     3     4     5     3     5     5     3     4     4 
    ## 12379 12381 12382 12383 12384 12386 12387 12388 12390 12394 12395 12397 12398 
    ##     4     4     5     3     5     5     5     5     5     3     4     5     3 
    ## 12399 12400 12401 12402 12403 12404 12406 12407 12408 12410 12411 12412 12414 
    ##     5     3     5     5     3     5     5     3     5     4     3     3     5 
    ## 12415 12416 12418 12419 12421 12423 12425 12428 12429 12430 12431 12432 12436 
    ##     5     3     5     4     5     4     4     4     5     5     5     5     3 
    ## 12439 12440 12443 12444 12446 12447 12448 12449 12450 12452 12453 12456 12457 
    ##     5     5     3     4     5     3     4     5     4     5     4     4     4 
    ## 12459 12462 12463 12464 12465 12466 12468 12469 12470 12471 12473 12476 12477 
    ##     4     5     5     5     5     5     5     5     5     5     5     3     4 
    ## 12480 12482 12483 12485 12486 12487 12491 12492 12493 12494 12495 12496 12497 
    ##     3     5     5     3     3     3     5     3     4     4     5     4     5 
    ## 12498 12501 12504 12505 12506 12509 12510 12512 12513 12514 12516 12517 12518 
    ##     3     5     5     4     4     5     3     5     4     5     3     4     5 
    ## 12521 12522 12523 12524 12525 12526 12527 12528 12529 12530 12531 12532 12533 
    ##     4     5     3     3     3     5     3     5     5     3     5     4     4 
    ## 12534 12535 12537 12538 12539 12540 12541 12542 12543 12544 12545 12546 12547 
    ##     3     4     3     4     3     5     3     4     4     5     3     5     3 
    ## 12548 12549 12551 12552 12553 12556 12557 12560 12562 12563 12564 12565 12568 
    ##     4     5     5     5     4     5     3     3     4     3     5     5     5 
    ## 12569 12570 12571 12572 12573 12574 12575 12576 12577 12578 12579 12580 12582 
    ##     3     3     5     4     3     4     3     5     4     5     5     4     5 
    ## 12583 12584 12585 12589 12590 12591 12593 12594 12595 12596 12597 12599 12600 
    ##     4     3     5     3     5     5     4     5     3     4     4     4     4 
    ## 12603 12605 12606 12607 12608 12609 12610 12611 12612 12613 12614 12615 12616 
    ##     4     5     3     5     4     5     4     5     4     4     3     4     5 
    ## 12617 12618 12619 12620 12621 12622 12623 12627 12628 12630 12631 12632 12634 
    ##     5     3     3     4     5     5     4     5     4     3     4     4     5 
    ## 12635 12636 12637 12638 12640 12641 12642 12643 12644 12645 12646 12647 12648 
    ##     5     5     5     4     4     5     4     4     3     4     5     5     3 
    ## 12649 12650 12651 12653 12654 12655 12661 12662 12663 12664 12665 12666 12667 
    ##     4     5     3     5     3     3     3     3     3     4     5     4     4 
    ## 12668 12669 12670 12672 12673 12674 12676 12677 12680 12681 12682 12684 12685 
    ##     4     5     5     4     5     3     3     5     5     5     5     3     3 
    ## 12686 12687 12689 12692 12693 12694 12696 12697 12699 12700 12701 12703 12704 
    ##     5     4     3     5     3     3     3     5     5     3     5     5     3 
    ## 12705 12706 12709 12711 12712 12713 12715 12717 12718 12721 12722 12723 12725 
    ##     5     5     3     3     5     5     5     5     5     5     3     3     4 
    ## 12727 12730 12731 12732 12735 12736 12739 12740 12741 12742 12743 12744 12746 
    ##     4     5     5     4     5     4     4     5     5     3     5     5     4 
    ## 12747 12748 12749 12751 12752 12753 12754 12755 12757 12758 12760 12762 12763 
    ##     5     4     5     5     5     5     5     4     5     5     3     5     3 
    ## 12765 12766 12768 12769 12771 12772 12773 12774 12779 12780 12782 12783 12784 
    ##     4     5     3     5     3     4     3     5     4     5     3     5     5 
    ## 12787 12788 12789 12790 12791 12792 12794 12795 12796 12797 12798 12800 12801 
    ##     3     5     4     4     3     3     5     4     3     5     4     5     4 
    ## 12802 12803 12804 12805 12806 12808 12809 12814 12815 12816 12818 12819 12820 
    ##     5     3     5     4     5     5     5     4     3     5     4     3     5 
    ## 12821 12822 12823 12824 12826 12827 12828 12829 12831 12832 12835 12836 12837 
    ##     5     3     3     4     5     4     5     4     5     4     4     5     4 
    ## 12838 12839 12841 12842 12844 12845 12846 12848 12850 12851 12853 12856 12857 
    ##     5     4     4     5     4     3     5     4     5     5     5     5     5 
    ## 12858 12859 12860 12862 12864 12865 12866 12870 12871 12872 12873 12875 12878 
    ##     4     3     3     5     5     5     5     4     5     4     4     4     4 
    ## 12880 12882 12883 12884 12885 12886 12887 12889 12891 12892 12894 12895 12896 
    ##     3     5     5     5     5     4     5     4     4     4     5     4     3 
    ## 12897 12898 12899 12900 12901 12902 12903 12905 12906 12908 12909 12910 12911 
    ##     5     4     5     3     4     4     5     3     3     4     4     3     5 
    ## 12912 12913 12914 12915 12916 12917 12919 12920 12921 12922 12923 12924 12928 
    ##     5     5     3     4     4     5     4     3     4     3     4     4     3 
    ## 12930 12932 12933 12934 12935 12938 12939 12940 12941 12942 12943 12944 12945 
    ##     4     5     4     4     3     5     3     4     4     4     5     4     4 
    ## 12946 12947 12950 12951 12952 12953 12956 12957 12958 12960 12961 12962 12963 
    ##     5     5     3     5     5     4     3     4     3     5     5     4     3 
    ## 12965 12966 12968 12969 12970 12972 12975 12976 12977 12979 12980 12981 12983 
    ##     5     5     3     3     4     5     5     5     4     4     4     5     5 
    ## 12984 12985 12986 12987 12988 12989 12991 12993 12995 12996 12999 13000 13002 
    ##     5     5     5     5     5     5     5     5     5     5     4     3     5 
    ## 13005 13006 13008 13010 13011 13013 13014 13015 13018 13022 13023 13024 13026 
    ##     5     4     5     4     5     5     5     4     5     5     5     5     4 
    ## 13027 13029 13030 13031 13032 13034 13035 13036 13038 13039 13040 13041 13042 
    ##     5     4     5     5     3     5     5     5     5     5     5     4     3 
    ## 13044 13045 13047 13048 13049 13050 13054 13055 13057 13059 13060 13061 13062 
    ##     5     5     5     5     5     3     5     5     5     4     5     4     3 
    ## 13063 13064 13065 13067 13068 13070 13072 13073 13074 13075 13076 13078 13080 
    ##     3     5     5     3     4     5     3     5     5     3     5     5     4 
    ## 13081 13084 13085 13087 13088 13089 13091 13092 13094 13095 13096 13097 13098 
    ##     5     5     3     5     5     5     5     3     3     5     5     4     3 
    ## 13100 13102 13104 13105 13106 13109 13112 13115 13117 13118 13119 13120 13123 
    ##     5     4     4     4     5     5     5     3     5     5     5     5     4 
    ## 13124 13126 13127 13129 13130 13131 13132 13133 13134 13137 13142 13144 13146 
    ##     4     3     5     3     5     5     4     5     5     5     5     5     5 
    ## 13147 13148 13149 13151 13153 13159 13161 13162 13163 13164 13166 13167 13168 
    ##     4     5     3     4     5     5     5     3     3     4     3     5     5 
    ## 13169 13170 13172 13173 13174 13175 13178 13179 13180 13181 13182 13183 13185 
    ##     5     5     3     5     5     5     5     3     5     5     3     4     5 
    ## 13188 13189 13190 13191 13192 13193 13194 13195 13196 13198 13199 13200 13201 
    ##     3     5     5     4     5     3     3     3     3     3     5     5     3 
    ## 13203 13204 13206 13208 13211 13212 13214 13215 13216 13217 13218 13219 13221 
    ##     3     3     3     4     4     5     3     5     5     3     5     5     5 
    ## 13224 13225 13226 13227 13229 13230 13231 13235 13237 13239 13241 13242 13243 
    ##     4     4     3     4     5     3     4     4     4     3     5     5     4 
    ## 13245 13248 13249 13251 13252 13253 13254 13255 13257 13259 13263 13264 13266 
    ##     3     3     5     5     4     5     3     3     5     4     4     3     3 
    ## 13268 13271 13272 13273 13274 13276 13277 13279 13281 13282 13283 13284 13285 
    ##     4     5     4     5     3     4     4     3     5     3     5     3     3 
    ## 13287 13288 13291 13292 13293 13294 13297 13299 13301 13302 13303 13304 13306 
    ##     3     5     3     3     5     3     5     3     5     5     5     5     5 
    ## 13307 13308 13309 13310 13311 13312 13313 13314 13315 13318 13319 13320 13321 
    ##     3     3     5     4     4     3     5     5     3     3     4     5     5 
    ## 13324 13325 13329 13330 13331 13332 13333 13334 13338 13339 13340 13343 13344 
    ##     5     5     3     5     3     5     5     5     4     5     5     4     5 
    ## 13347 13348 13350 13351 13352 13353 13356 13358 13361 13362 13363 13364 13365 
    ##     5     5     4     5     3     5     5     4     5     3     5     5     5 
    ## 13367 13369 13372 13373 13374 13377 13378 13380 13381 13382 13384 13385 13387 
    ##     5     4     5     5     3     5     4     4     3     3     5     4     5 
    ## 13388 13389 13390 13391 13392 13393 13395 13396 13397 13398 13400 13401 13403 
    ##     5     3     4     5     5     3     5     3     4     3     5     5     5 
    ## 13406 13407 13409 13410 13411 13412 13413 13414 13417 13418 13419 13420 13421 
    ##     4     4     5     4     3     5     5     5     5     3     5     5     4 
    ## 13424 13425 13426 13427 13428 13429 13430 13431 13432 13433 13434 13435 13436 
    ##     5     4     4     5     4     3     4     5     3     5     5     3     5 
    ## 13437 13438 13440 13441 13443 13445 13446 13447 13449 13451 13454 13455 13458 
    ##     3     4     5     5     3     5     5     3     3     3     4     4     5 
    ## 13459 13460 13461 13462 13465 13466 13467 13468 13469 13470 13471 13472 13475 
    ##     5     5     5     5     3     3     5     5     5     5     4     5     5 
    ## 13476 13477 13478 13479 13480 13481 13482 13483 13484 13486 13487 13488 13489 
    ##     5     5     5     5     4     5     5     5     3     5     5     4     3 
    ## 13490 13491 13496 13497 13498 13499 13501 13502 13503 13504 13505 13506 13508 
    ##     3     5     5     3     5     3     5     5     4     4     4     5     5 
    ## 13509 13510 13511 13512 13514 13517 13519 13520 13521 13522 13523 13524 13526 
    ##     4     4     4     4     4     5     5     4     4     3     5     3     5 
    ## 13528 13529 13530 13531 13533 13534 13535 13540 13544 13546 13547 13549 13550 
    ##     5     5     5     5     3     4     4     5     5     4     5     5     3 
    ## 13552 13553 13554 13555 13556 13559 13560 13561 13562 13563 13564 13565 13568 
    ##     4     3     4     3     5     5     3     4     4     5     5     4     4 
    ## 13569 13572 13573 13574 13575 13576 13577 13579 13582 13583 13586 13587 13588 
    ##     5     4     5     5     3     5     5     5     3     5     4     3     4 
    ## 13590 13591 13593 13594 13595 13596 13597 13598 13599 13601 13603 13604 13605 
    ##     5     5     5     5     5     3     4     5     5     5     4     3     3 
    ## 13606 13607 13609 13611 13615 13616 13618 13619 13620 13621 13623 13624 13625 
    ##     4     4     3     5     5     4     4     5     4     5     5     3     5 
    ## 13626 13627 13628 13630 13631 13632 13633 13635 13637 13638 13639 13640 13643 
    ##     5     5     5     5     4     3     5     3     3     5     5     5     3 
    ## 13645 13646 13647 13648 13651 13652 13657 13658 13659 13661 13665 13667 13671 
    ##     5     5     5     4     5     5     4     5     4     4     5     5     5 
    ## 13672 13673 13675 13676 13678 13679 13680 13681 13684 13685 13687 13688 13690 
    ##     3     5     5     5     3     4     3     3     5     5     5     4     4 
    ## 13692 13695 13696 13699 13700 13701 13702 13703 13704 13705 13706 13707 13708 
    ##     5     4     5     4     4     3     5     5     5     5     4     3     5 
    ## 13709 13711 13713 13714 13716 13717 13718 13719 13720 13721 13722 13724 13725 
    ##     5     4     5     5     3     5     5     4     5     3     3     3     3 
    ## 13726 13728 13729 13730 13731 13733 13735 13737 13738 13740 13741 13742 13743 
    ##     5     5     3     4     4     5     4     5     5     4     3     4     5 
    ## 13744 13745 13746 13748 13752 13753 13756 13757 13758 13760 13762 13763 13765 
    ##     4     4     5     4     4     4     4     3     5     5     5     5     5 
    ## 13766 13770 13772 13773 13774 13775 13776 13777 13779 13780 13781 13782 13787 
    ##     4     5     5     5     5     4     4     4     5     5     5     5     5 
    ## 13788 13789 13792 13793 13794 13796 13797 13798 13799 13800 13802 13805 13806 
    ##     5     5     5     4     3     4     5     5     3     5     3     5     5 
    ## 13810 13812 13813 13814 13816 13817 13818 13819 13821 13822 13824 13826 13827 
    ##     4     5     5     5     4     5     3     3     5     5     5     4     5 
    ## 13829 13830 13831 13832 13836 13837 13840 13841 13843 13844 13845 13846 13848 
    ##     4     5     3     5     3     5     5     5     4     5     5     4     4 
    ## 13849 13850 13851 13852 13853 13854 13855 13856 13857 13858 13859 13861 13863 
    ##     5     3     3     5     5     3     5     4     3     5     5     4     5 
    ## 13864 13865 13866 13867 13869 13870 13871 13872 13873 13876 13880 13881 13884 
    ##     5     5     5     4     4     3     5     5     5     5     3     4     5 
    ## 13886 13887 13888 13891 13892 13893 13894 13895 13896 13897 13898 13899 13900 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 13902 13907 13909 13910 13912 13913 13914 13915 13917 13918 13919 13920 13921 
    ##     5     5     3     5     4     5     5     5     3     5     5     5     5 
    ## 13924 13925 13926 13927 13928 13929 13930 13932 13934 13935 13937 13938 13939 
    ##     5     5     4     5     4     5     4     5     5     3     3     5     5 
    ## 13944 13945 13952 13953 13955 13956 13958 13960 13961 13962 13963 13965 13968 
    ##     5     4     3     5     3     3     4     5     5     4     4     4     3 
    ## 13970 13971 13972 13973 13974 13976 13977 13981 13992 13994 13995 13997 13999 
    ##     4     5     5     3     4     5     3     3     3     3     4     5     5 
    ## 14001 14002 14003 14004 14005 14006 14007 14009 14010 14011 14012 14013 14014 
    ##     5     5     3     5     5     5     5     5     5     4     5     5     4 
    ## 14015 14016 14017 14020 14022 14023 14024 14025 14026 14027 14028 14031 14035 
    ##     4     4     5     5     5     4     3     5     4     5     5     5     3 
    ## 14036 14039 14041 14042 14046 14048 14049 14052 14054 14056 14057 14061 14062 
    ##     4     5     3     3     3     3     3     5     5     4     5     5     5 
    ## 14063 14064 14065 14066 14067 14068 14069 14071 14072 14073 14074 14075 14076 
    ##     3     5     4     5     5     3     5     5     3     5     5     5     5 
    ## 14077 14080 14082 14083 14084 14086 14087 14088 14091 14093 14094 14095 14097 
    ##     5     5     4     5     5     5     4     5     3     5     5     3     3 
    ## 14098 14099 14101 14102 14103 14104 14105 14106 14107 14108 14110 14111 14114 
    ##     3     4     3     4     4     5     5     4     4     5     5     4     4 
    ## 14116 14118 14119 14120 14121 14123 14125 14126 14129 14130 14132 14133 14136 
    ##     5     4     4     5     3     4     5     3     5     5     5     3     5 
    ## 14137 14138 14139 14140 14141 14143 14144 14146 14147 14148 14150 14151 14153 
    ##     4     5     5     4     4     5     5     5     5     5     3     5     3 
    ## 14154 14156 14157 14159 14162 14165 14166 14169 14170 14171 14172 14173 14174 
    ##     3     4     5     5     3     5     5     5     3     3     3     3     3 
    ## 14175 14176 14177 14178 14179 14180 14181 14182 14183 14184 14185 14187 14189 
    ##     3     5     5     5     5     3     3     5     5     5     4     3     3 
    ## 14190 14191 14192 14193 14194 14195 14196 14197 14198 14200 14201 14205 14207 
    ##     5     4     4     3     5     4     5     3     3     3     4     4     3 
    ## 14209 14211 14212 14214 14216 14218 14222 14223 14224 14225 14226 14227 14228 
    ##     5     3     4     4     5     5     5     5     5     4     3     5     5 
    ## 14229 14230 14236 14238 14239 14240 14241 14242 14244 14245 14246 14248 14249 
    ##     3     5     3     5     5     4     3     4     3     5     4     5     3 
    ## 14250 14251 14252 14254 14256 14259 14260 14262 14263 14265 14267 14269 14270 
    ##     4     5     5     4     3     5     5     3     4     5     3     4     5 
    ## 14273 14274 14275 14276 14277 14282 14283 14287 14289 14290 14292 14293 14294 
    ##     4     3     5     5     5     4     3     5     5     3     4     4     3 
    ## 14295 14296 14297 14298 14299 14300 14302 14303 14305 14307 14311 14313 14314 
    ##     4     5     5     4     5     5     5     5     3     4     4     5     4 
    ## 14315 14316 14318 14319 14321 14322 14323 14326 14328 14331 14332 14333 14335 
    ##     5     3     5     5     4     3     5     4     3     3     5     5     4 
    ## 14336 14337 14340 14341 14342 14343 14344 14346 14347 14350 14351 14353 14354 
    ##     5     5     3     3     5     4     5     5     5     4     3     3     5 
    ## 14355 14356 14357 14358 14359 14360 14361 14362 14363 14364 14366 14368 14370 
    ##     5     4     3     4     4     5     3     4     4     3     4     3     4 
    ## 14373 14379 14380 14381 14382 14383 14384 14385 14386 14388 14389 14390 14391 
    ##     3     5     4     5     4     3     4     3     5     4     5     5     5 
    ## 14393 14394 14399 14400 14401 14402 14403 14404 14405 14406 14407 14410 14411 
    ##     4     4     5     4     3     3     3     4     4     5     3     4     4 
    ## 14412 14413 14415 14416 14417 14418 14419 14420 14421 14422 14424 14425 14426 
    ##     4     3     4     4     5     4     5     4     5     5     5     3     3 
    ## 14427 14429 14430 14432 14433 14436 14437 14439 14441 14442 14443 14444 14445 
    ##     5     5     5     4     5     4     5     5     4     5     3     5     3 
    ## 14446 14448 14449 14450 14451 14452 14454 14455 14456 14458 14459 14460 14461 
    ##     5     5     3     4     5     3     3     5     4     5     4     5     5 
    ## 14462 14463 14464 14465 14466 14467 14468 14469 14471 14473 14474 14476 14477 
    ##     4     5     4     5     4     5     5     5     4     4     5     4     5 
    ## 14479 14480 14482 14485 14486 14487 14488 14490 14494 14498 14500 14501 14502 
    ##     3     4     5     3     5     5     5     5     5     3     4     3     4 
    ## 14503 14505 14506 14508 14509 14511 14513 14515 14516 14517 14518 14519 14521 
    ##     5     5     4     3     5     3     5     4     3     5     5     3     4 
    ## 14523 14526 14528 14529 14530 14531 14533 14534 14536 14537 14538 14539 14540 
    ##     5     3     5     5     3     5     5     5     5     5     5     3     3 
    ## 14543 14544 14546 14549 14550 14551 14553 14554 14557 14559 14560 14561 14563 
    ##     5     4     5     4     5     3     4     5     5     4     5     5     5 
    ## 14566 14567 14568 14569 14571 14572 14573 14574 14575 14576 14578 14579 14580 
    ##     5     3     3     4     5     4     5     5     5     3     3     4     5 
    ## 14582 14583 14584 14585 14586 14587 14589 14592 14593 14594 14595 14597 14598 
    ##     4     4     3     3     5     5     5     5     5     3     5     3     5 
    ## 14599 14600 14601 14602 14603 14604 14605 14606 14607 14608 14613 14614 14615 
    ##     5     4     3     5     4     5     3     5     5     3     5     3     5 
    ## 14616 14617 14618 14619 14620 14622 14624 14625 14627 14629 14630 14631 14632 
    ##     5     3     5     5     4     5     4     5     5     3     3     5     4 
    ## 14634 14635 14636 14637 14640 14641 14643 14644 14646 14647 14648 14649 14650 
    ##     4     4     5     5     3     4     5     5     3     5     3     5     4 
    ## 14651 14653 14654 14655 14656 14657 14658 14659 14660 14662 14663 14664 14665 
    ##     5     5     4     4     4     5     3     5     4     5     4     3     4 
    ## 14666 14668 14669 14672 14673 14674 14675 14677 14678 14679 14682 14683 14684 
    ##     4     5     3     4     3     3     4     4     5     4     3     4     4 
    ## 14685 14686 14687 14688 14689 14691 14695 14696 14698 14699 14701 14704 14705 
    ##     5     4     5     5     5     5     5     4     3     4     5     5     5 
    ## 14706 14707 14708 14710 14711 14716 14717 14719 14720 14721 14723 14724 14725 
    ##     3     5     4     3     5     5     5     4     4     3     3     5     4 
    ## 14726 14727 14728 14729 14732 14733 14735 14737 14738 14739 14741 14743 14744 
    ##     5     5     5     3     5     5     5     5     5     3     5     3     5 
    ## 14745 14746 14747 14748 14752 14753 14755 14756 14757 14758 14759 14760 14761 
    ##     3     5     3     5     3     4     3     4     5     5     4     5     3 
    ## 14764 14765 14766 14767 14768 14769 14770 14772 14774 14775 14776 14777 14778 
    ##     5     4     5     4     4     4     3     4     5     5     3     5     5 
    ## 14779 14783 14784 14785 14786 14788 14789 14790 14791 14794 14795 14796 14797 
    ##     3     5     5     3     3     5     5     3     5     5     3     5     4 
    ## 14798 14799 14800 14801 14803 14804 14805 14806 14807 14809 14810 14811 14812 
    ##     4     3     4     4     5     5     5     5     5     5     4     3     5 
    ## 14813 14816 14817 14818 14819 14820 14821 14822 14823 14825 14827 14828 14831 
    ##     3     5     3     5     5     3     5     4     4     5     5     5     5 
    ## 14832 14833 14834 14835 14836 14837 14838 14839 14840 14842 14844 14847 14848 
    ##     4     5     5     5     5     5     5     5     5     4     5     5     4 
    ## 14850 14852 14855 14858 14860 14862 14863 14864 14865 14866 14867 14870 14871 
    ##     5     4     5     4     5     5     5     4     4     5     4     5     5 
    ## 14872 14873 14876 14877 14879 14882 14883 14884 14886 14888 14889 14890 14891 
    ##     4     5     3     4     3     5     5     5     3     5     3     5     4 
    ## 14892 14893 14894 14895 14896 14897 14898 14899 14900 14901 14903 14904 14905 
    ##     5     5     5     3     3     5     5     5     3     5     5     5     5 
    ## 14908 14909 14910 14913 14914 14916 14917 14918 14919 14920 14921 14922 14923 
    ##     5     3     3     4     4     5     5     5     4     3     5     3     5 
    ## 14924 14925 14926 14929 14930 14931 14933 14935 14936 14937 14938 14939 14941 
    ##     5     4     5     5     5     5     5     5     5     5     4     4     5 
    ## 14943 14945 14946 14947 14948 14949 14951 14952 14953 14954 14955 14957 14958 
    ##     3     3     5     4     5     4     4     5     3     4     5     5     4 
    ## 14960 14962 14963 14964 14965 14966 14967 14968 14969 14970 14971 14972 14973 
    ##     5     5     5     5     3     3     4     4     5     4     5     5     5 
    ## 14974 14977 14978 14979 14981 14982 14985 14987 14989 14990 14992 14993 14994 
    ##     5     3     5     3     4     5     4     4     4     5     4     5     5 
    ## 14995 15000 15001 15003 15004 15005 15006 15007 15008 15012 15013 15014 15015 
    ##     5     5     5     4     5     4     5     3     4     5     5     3     5 
    ## 15018 15019 15022 15023 15024 15025 15026 15027 15029 15030 15031 15032 15035 
    ##     5     3     4     5     5     5     5     5     5     3     3     3     5 
    ## 15037 15038 15039 15040 15041 15042 15044 15045 15046 15047 15048 15049 15050 
    ##     5     3     5     3     4     5     5     5     4     5     5     4     5 
    ## 15051 15052 15054 15056 15057 15058 15059 15061 15062 15064 15065 15066 15067 
    ##     3     5     5     5     4     5     5     3     5     4     4     5     5 
    ## 15068 15071 15072 15073 15074 15075 15076 15077 15079 15080 15082 15084 15085 
    ##     3     5     5     5     5     4     3     3     3     3     3     5     5 
    ## 15086 15087 15088 15089 15090 15092 15094 15095 15096 15097 15098 15099 15100 
    ##     5     4     3     5     5     4     5     5     5     4     5     4     5 
    ## 15102 15103 15104 15106 15108 15109 15110 15111 15112 15114 15115 15116 15117 
    ##     5     3     5     3     3     5     4     4     5     5     5     3     4 
    ## 15118 15119 15120 15123 15124 15127 15128 15129 15131 15132 15133 15135 15137 
    ##     5     5     5     5     5     5     5     5     4     4     3     5     3 
    ## 15138 15140 15141 15142 15143 15146 15148 15149 15150 15153 15154 15155 15156 
    ##     3     4     5     4     4     3     5     3     5     4     5     4     3 
    ## 15157 15158 15159 15160 15162 15163 15164 15166 15167 15169 15171 15173 15175 
    ##     5     5     3     4     5     5     4     4     5     3     4     4     5 
    ## 15176 15177 15179 15181 15182 15185 15186 15190 15192 15193 15194 15196 15197 
    ##     4     3     4     4     4     5     5     4     3     5     4     4     4 
    ## 15198 15201 15202 15204 15206 15207 15208 15209 15210 15211 15212 15215 15218 
    ##     3     5     5     5     3     4     5     3     3     5     5     3     3 
    ## 15220 15221 15222 15223 15224 15225 15226 15227 15229 15230 15231 15233 15235 
    ##     5     3     5     3     3     5     5     5     3     5     5     4     5 
    ## 15236 15237 15238 15239 15240 15241 15242 15243 15245 15246 15247 15248 15250 
    ##     5     5     5     5     5     4     4     4     4     5     5     4     5 
    ## 15251 15252 15254 15255 15256 15258 15260 15261 15262 15263 15264 15265 15266 
    ##     4     5     3     5     4     4     5     5     5     5     5     4     3 
    ## 15268 15269 15271 15273 15274 15275 15276 15277 15278 15279 15280 15282 15283 
    ##     3     3     3     5     5     5     4     5     4     4     3     5     5 
    ## 15284 15285 15286 15288 15289 15291 15292 15293 15294 15296 15297 15298 15299 
    ##     4     3     5     5     4     5     3     3     5     5     3     4     3 
    ## 15303 15305 15307 15308 15309 15310 15311 15312 15313 15314 15315 15318 15321 
    ##     5     3     5     5     3     4     5     5     5     5     5     5     4 
    ## 15322 15324 15326 15327 15328 15329 15330 15331 15332 15333 15334 15337 15338 
    ##     5     4     4     5     5     5     4     5     3     5     3     5     5 
    ## 15339 15340 15342 15343 15344 15345 15346 15347 15348 15353 15354 15356 15358 
    ##     5     5     3     4     5     3     3     4     3     5     5     4     5 
    ## 15359 15360 15361 15362 15363 15364 15365 15366 15367 15368 15369 15372 15373 
    ##     5     3     5     3     4     5     5     3     5     5     5     5     3 
    ## 15374 15376 15377 15378 15379 15382 15383 15386 15387 15388 15389 15390 15391 
    ##     4     5     5     5     5     3     5     5     3     3     3     5     4 
    ## 15392 15393 15395 15396 15397 15398 15399 15401 15402 15404 15405 15406 15407 
    ##     4     4     3     3     5     4     5     4     4     5     5     5     5 
    ## 15410 15412 15414 15415 15419 15420 15421 15422 15423 15424 15425 15426 15430 
    ##     3     3     3     4     5     3     3     5     5     4     4     5     3 
    ## 15431 15432 15434 15435 15436 15437 15438 15439 15440 15441 15442 15443 15444 
    ##     4     3     5     5     5     5     3     4     5     5     5     3     3 
    ## 15445 15447 15448 15449 15451 15452 15453 15454 15456 15457 15458 15461 15462 
    ##     4     5     3     3     5     4     4     5     4     5     3     5     3 
    ## 15463 15464 15465 15466 15467 15468 15469 15470 15471 15472 15473 15474 15476 
    ##     5     3     5     5     5     5     3     4     3     3     5     5     4 
    ## 15478 15480 15481 15483 15484 15485 15486 15487 15488 15490 15491 15492 15495 
    ##     5     4     4     3     4     5     5     5     5     5     4     5     5 
    ## 15497 15500 15501 15502 15503 15504 15508 15510 15511 15512 15513 15514 15515 
    ##     4     4     3     5     4     4     3     5     4     4     5     5     5 
    ## 15516 15517 15518 15520 15521 15523 15525 15526 15527 15528 15530 15531 15533 
    ##     5     3     5     5     3     3     3     5     5     5     4     5     5 
    ## 15534 15535 15536 15538 15542 15543 15544 15546 15547 15549 15550 15551 15552 
    ##     5     4     4     5     4     4     5     5     5     3     4     5     4 
    ## 15553 15554 15556 15557 15560 15562 15564 15565 15566 15568 15569 15571 15572 
    ##     3     5     3     5     5     3     5     4     3     5     3     5     5 
    ## 15574 15575 15576 15577 15578 15579 15580 15583 15585 15587 15588 15589 15590 
    ##     5     3     5     3     3     4     5     4     5     5     5     5     4 
    ## 15591 15592 15593 15594 15595 15596 15597 15599 15601 15602 15603 15604 15606 
    ##     5     3     5     5     3     5     5     5     4     3     4     5     5 
    ## 15607 15609 15610 15611 15612 15613 15614 15615 15616 15617 15618 15621 15622 
    ##     5     4     5     5     4     5     5     3     3     3     5     5     4 
    ## 15624 15625 15626 15627 15628 15629 15631 15632 15633 15634 15635 15638 15640 
    ##     3     5     5     3     5     5     3     3     5     3     5     3     4 
    ## 15641 15642 15643 15646 15647 15649 15652 15654 15655 15656 15657 15658 15659 
    ##     3     5     5     3     5     5     5     3     4     4     5     4     5 
    ## 15660 15662 15664 15666 15667 15668 15669 15670 15671 15673 15674 15675 15678 
    ##     3     4     4     4     5     4     5     5     5     4     3     5     4 
    ## 15679 15680 15681 15682 15684 15685 15686 15687 15688 15690 15693 15694 15695 
    ##     5     5     5     4     5     5     5     5     3     4     3     4     5 
    ## 15696 15697 15701 15703 15704 15705 15707 15712 15716 15717 15718 15719 15720 
    ##     3     5     5     4     5     3     5     5     4     5     5     4     3 
    ## 15722 15723 15724 15727 15730 15731 15734 15735 15736 15737 15738 15739 15740 
    ##     5     3     4     5     3     5     5     5     5     5     5     3     5 
    ## 15741 15742 15745 15747 15748 15749 15751 15752 15753 15754 15756 15757 15759 
    ##     5     5     5     5     5     5     4     5     5     3     4     5     5 
    ## 15760 15762 15763 15764 15767 15769 15770 15771 15772 15773 15776 15777 15778 
    ##     4     4     5     5     5     3     3     3     3     5     5     4     5 
    ## 15779 15780 15781 15782 15784 15785 15787 15788 15789 15790 15791 15792 15796 
    ##     4     5     5     3     5     5     5     4     5     5     4     5     5 
    ## 15797 15798 15799 15801 15805 15807 15808 15809 15813 15814 15816 15817 15818 
    ##     5     5     4     5     4     3     5     5     5     3     5     4     5 
    ## 15820 15822 15824 15826 15827 15828 15830 15831 15832 15833 15834 15835 15837 
    ##     5     5     5     4     4     5     5     5     3     4     4     5     3 
    ## 15840 15841 15842 15846 15847 15851 15853 15855 15856 15858 15859 15860 15861 
    ##     5     5     4     3     4     5     3     4     4     5     3     5     3 
    ## 15862 15863 15864 15865 15866 15867 15869 15871 15872 15873 15874 15876 15877 
    ##     4     3     3     5     3     4     3     5     5     5     5     5     5 
    ## 15878 15879 15880 15881 15883 15885 15886 15887 15888 15889 15890 15891 15892 
    ##     5     5     5     5     5     5     5     5     5     4     5     4     5 
    ## 15893 15894 15895 15896 15898 15899 15900 15901 15902 15903 15904 15905 15906 
    ##     5     5     5     5     3     5     5     3     5     5     5     5     5 
    ## 15907 15910 15911 15912 15913 15914 15915 15917 15918 15919 15920 15921 15922 
    ##     5     3     4     5     4     5     4     5     5     5     5     3     5 
    ## 15923 15925 15926 15928 15929 15930 15933 15935 15936 15937 15938 15942 15945 
    ##     5     4     3     5     5     5     4     3     3     3     5     5     5 
    ## 15946 15947 15948 15951 15952 15953 15954 15955 15957 15958 15959 15962 15963 
    ##     4     5     5     4     3     4     5     3     4     4     5     4     5 
    ## 15964 15965 15968 15969 15972 15977 15978 15979 15980 15981 15982 15983 15984 
    ##     4     5     4     4     3     5     5     5     5     5     4     4     5 
    ## 15985 15987 15989 15992 15993 15994 15997 15998 15999 16000 16001 16003 16004 
    ##     5     5     5     5     3     5     3     3     5     5     5     5     3 
    ## 16005 16006 16007 16008 16010 16012 16013 16014 16015 16016 16017 16018 16020 
    ##     5     3     5     5     5     5     5     4     5     4     3     5     5 
    ## 16021 16022 16023 16024 16026 16027 16028 16030 16031 16033 16036 16037 16038 
    ##     5     3     3     4     4     5     4     5     5     4     4     5     5 
    ## 16040 16041 16042 16043 16044 16045 16049 16050 16051 16054 16055 16056 16057 
    ##     4     5     4     5     3     5     4     3     5     5     5     5     5 
    ## 16061 16063 16064 16065 16066 16069 16070 16071 16072 16073 16074 16075 16077 
    ##     4     4     5     5     5     5     4     5     5     5     3     5     4 
    ## 16078 16080 16081 16082 16083 16085 16086 16087 16088 16089 16090 16092 16093 
    ##     4     4     3     5     4     5     5     5     5     4     4     5     3 
    ## 16096 16097 16098 16099 16100 16101 16102 16103 16104 16105 16107 16108 16109 
    ##     5     5     4     4     3     4     4     3     3     4     5     5     5 
    ## 16111 16112 16115 16118 16120 16121 16122 16124 16125 16126 16127 16128 16130 
    ##     3     5     3     5     4     5     5     4     3     5     5     5     5 
    ## 16132 16133 16135 16136 16137 16139 16140 16141 16143 16148 16149 16151 16152 
    ##     5     5     5     4     3     5     5     5     5     3     5     5     5 
    ## 16153 16154 16155 16158 16159 16160 16161 16162 16163 16164 16167 16168 16172 
    ##     5     5     5     5     5     5     5     5     5     3     5     5     3 
    ## 16173 16174 16175 16176 16178 16179 16180 16181 16182 16184 16185 16186 16187 
    ##     4     3     5     5     5     3     4     5     4     4     3     4     5 
    ## 16190 16191 16192 16193 16194 16196 16197 16198 16200 16202 16204 16205 16206 
    ##     5     5     3     3     5     5     5     3     4     5     5     5     5 
    ## 16207 16208 16209 16210 16211 16212 16213 16214 16216 16218 16219 16220 16221 
    ##     5     5     5     4     5     5     5     5     5     5     3     5     4 
    ## 16222 16224 16225 16226 16228 16229 16230 16231 16232 16233 16234 16237 16239 
    ##     5     4     3     5     5     3     3     5     5     5     3     5     5 
    ## 16240 16241 16242 16243 16244 16245 16246 16248 16250 16251 16252 16254 16255 
    ##     5     5     4     5     4     3     4     4     3     5     4     5     5 
    ## 16257 16258 16260 16261 16262 16263 16266 16267 16268 16269 16271 16273 16274 
    ##     3     3     5     5     4     5     4     5     5     5     5     5     4 
    ## 16275 16278 16279 16280 16281 16286 16288 16289 16290 16291 16292 16294 16296 
    ##     5     3     5     5     4     3     5     5     5     5     5     5     3 
    ## 16299 16300 16301 16303 16305 16307 16308 16311 16313 16314 16315 16316 16320 
    ##     4     4     5     5     5     4     5     4     5     5     3     4     5 
    ## 16321 16322 16323 16324 16325 16326 16328 16329 16331 16332 16335 16336 16338 
    ##     4     5     3     3     5     5     5     4     5     4     5     5     3 
    ## 16339 16340 16341 16342 16343 16344 16345 16346 16347 16348 16349 16351 16352 
    ##     3     5     5     4     4     5     3     4     5     5     4     3     5 
    ## 16354 16355 16356 16358 16359 16363 16364 16366 16367 16368 16369 16370 16371 
    ##     4     3     5     4     3     5     3     5     5     5     5     5     3 
    ## 16372 16373 16374 16375 16376 16378 16379 16380 16385 16387 16389 16391 16393 
    ##     3     4     5     5     5     5     3     4     5     4     4     4     5 
    ## 16394 16395 16396 16397 16398 16399 16400 16401 16402 16403 16405 16406 16408 
    ##     5     5     5     5     5     4     5     4     3     3     3     3     5 
    ## 16411 16412 16413 16416 16417 16420 16421 16422 16425 16426 16427 16428 16429 
    ##     5     4     5     5     4     5     5     5     3     4     5     5     5 
    ## 16430 16431 16432 16433 16434 16435 16436 16438 16440 16441 16442 16443 16445 
    ##     4     3     3     3     5     4     5     5     5     5     3     4     5 
    ## 16446 16447 16448 16449 16450 16451 16453 16454 16455 16456 16457 16461 16462 
    ##     5     5     5     5     5     4     5     5     4     5     5     4     3 
    ## 16463 16464 16465 16467 16470 16471 16473 16474 16475 16476 16477 16479 16480 
    ##     4     5     5     5     5     3     5     4     5     5     3     5     5 
    ## 16481 16483 16484 16485 16486 16487 16490 16491 16492 16493 16495 16497 16498 
    ##     4     5     4     3     3     5     3     5     5     4     4     5     3 
    ## 16499 16500 16501 16502 16503 16506 16508 16509 16510 16515 16516 16517 16518 
    ##     5     3     5     5     4     4     4     3     4     5     4     4     5 
    ## 16519 16520 16522 16524 16526 16527 16528 16530 16532 16533 16534 16536 16537 
    ##     3     3     5     4     3     5     3     3     3     4     5     5     4 
    ## 16539 16540 16541 16542 16543 16544 16545 16546 16547 16548 16549 16550 16551 
    ##     5     3     5     5     3     3     3     5     5     5     5     5     5 
    ## 16552 16553 16554 16556 16558 16559 16560 16561 16562 16563 16564 16567 16568 
    ##     5     4     5     3     3     5     4     5     5     5     5     3     5 
    ## 16569 16570 16571 16572 16573 16574 16578 16579 16580 16581 16583 16584 16586 
    ##     5     3     5     4     3     5     3     5     5     5     5     4     3 
    ## 16587 16590 16591 16593 16594 16595 16596 16597 16598 16600 16602 16603 16605 
    ##     5     4     5     5     5     3     4     5     3     5     3     5     5 
    ## 16607 16608 16609 16610 16611 16612 16614 16615 16616 16617 16622 16623 16624 
    ##     3     5     5     4     5     4     3     5     5     4     3     4     5 
    ## 16625 16626 16627 16629 16630 16633 16634 16635 16637 16638 16639 16641 16644 
    ##     3     4     5     3     5     3     5     3     4     5     4     4     4 
    ## 16645 16646 16648 16650 16652 16653 16654 16655 16656 16657 16658 16659 16662 
    ##     5     5     5     4     5     3     3     3     5     5     5     3     5 
    ## 16663 16664 16665 16666 16667 16669 16670 16674 16675 16676 16681 16683 16685 
    ##     4     5     3     4     3     3     5     4     3     5     3     3     4 
    ## 16686 16688 16689 16690 16691 16692 16693 16694 16695 16696 16697 16698 16700 
    ##     4     5     3     3     5     5     5     5     4     4     5     4     4 
    ## 16701 16702 16703 16704 16705 16706 16707 16708 16709 16710 16711 16712 16713 
    ##     3     5     4     5     5     5     5     4     3     5     3     5     4 
    ## 16714 16717 16718 16719 16721 16724 16725 16726 16727 16729 16730 16731 16734 
    ##     3     3     3     3     5     3     4     3     5     5     4     5     3 
    ## 16736 16737 16738 16740 16743 16746 16748 16749 16750 16751 16752 16753 16755 
    ##     5     3     5     5     5     5     4     3     4     3     4     5     5 
    ## 16756 16757 16758 16759 16760 16761 16764 16765 16766 16768 16770 16771 16772 
    ##     4     4     5     5     5     4     5     5     3     4     5     3     5 
    ## 16774 16775 16777 16778 16779 16780 16781 16783 16785 16786 16788 16789 16790 
    ##     3     5     5     5     4     3     3     5     5     3     4     5     5 
    ## 16791 16792 16793 16794 16795 16796 16798 16799 16800 16801 16802 16803 16804 
    ##     5     5     4     5     5     5     4     5     4     5     5     4     3 
    ## 16807 16808 16812 16813 16814 16815 16817 16818 16820 16821 16822 16824 16827 
    ##     4     5     4     4     5     5     5     5     5     5     5     4     5 
    ## 16828 16829 16830 16831 16832 16833 16836 16837 16839 16840 16842 16844 16845 
    ##     5     5     4     4     3     5     5     4     3     5     5     5     3 
    ## 16846 16847 16848 16850 16852 16853 16855 16856 16857 16859 16860 16861 16862 
    ##     3     5     5     5     5     5     3     5     5     5     5     5     5 
    ## 16863 16865 16866 16867 16868 16870 16871 16875 16876 16877 16880 16881 16883 
    ##     3     5     3     5     5     5     4     4     3     5     4     5     5 
    ## 16885 16888 16889 16891 16892 16897 16898 16900 16901 16903 16904 16905 16906 
    ##     5     5     4     5     5     4     3     5     3     5     3     3     4 
    ## 16907 16909 16910 16911 16913 16914 16915 16916 16917 16920 16922 16924 16925 
    ##     3     4     4     5     5     3     3     5     3     3     3     4     5 
    ## 16927 16928 16929 16930 16931 16933 16934 16935 16937 16938 16939 16940 16942 
    ##     4     5     4     5     5     4     5     4     4     4     4     5     4 
    ## 16943 16946 16947 16948 16952 16954 16955 16956 16957 16958 16960 16962 16964 
    ##     5     3     3     5     4     4     3     4     5     5     4     4     4 
    ## 16966 16967 16969 16971 16972 16973 16976 16978 16980 16981 16982 16987 16988 
    ##     3     4     3     5     5     5     5     4     5     3     5     3     3 
    ## 16989 16990 16991 16992 16995 16998 17000 17002 17003 17005 17006 17008 17009 
    ##     5     3     3     5     5     5     5     5     5     3     5     3     3 
    ## 17012 17014 17015 17016 17018 17019 17020 17021 17022 17023 17024 17025 17026 
    ##     5     3     5     3     4     3     5     3     5     4     3     3     5 
    ## 17027 17028 17030 17031 17034 17035 17039 17040 17043 17045 17046 17051 17052 
    ##     3     5     4     5     3     5     3     5     4     4     3     5     5 
    ## 17055 17056 17057 17058 17059 17060 17061 17062 17063 17064 17065 17067 17068 
    ##     5     4     4     5     4     5     5     5     3     3     5     4     5 
    ## 17069 17071 17072 17073 17075 17076 17077 17080 17081 17082 17085 17088 17089 
    ##     5     5     3     4     4     3     4     3     5     4     3     4     5 
    ## 17090 17091 17092 17095 17097 17099 17101 17102 17103 17105 17107 17108 17109 
    ##     5     5     3     5     5     5     5     5     5     5     5     5     5 
    ## 17111 17113 17114 17115 17116 17117 17119 17120 17121 17122 17123 17124 17125 
    ##     5     5     4     4     4     5     5     3     3     5     5     5     5 
    ## 17126 17127 17128 17129 17131 17132 17133 17134 17135 17136 17137 17138 17139 
    ##     5     4     4     4     3     4     5     3     3     5     4     3     5 
    ## 17141 17142 17143 17144 17145 17146 17147 17148 17150 17151 17152 17156 17157 
    ##     3     3     5     5     3     5     4     5     5     5     5     5     5 
    ## 17161 17162 17164 17165 17166 17167 17169 17172 17173 17177 17178 17179 17183 
    ##     5     4     5     5     3     5     3     3     3     4     4     5     5 
    ## 17185 17187 17189 17190 17191 17192 17193 17195 17196 17200 17201 17202 17203 
    ##     5     3     3     5     5     5     5     5     5     5     5     5     5 
    ## 17205 17206 17207 17209 17212 17213 17214 17217 17220 17221 17223 17224 17225 
    ##     4     5     5     3     4     4     3     4     3     4     4     5     5 
    ## 17228 17231 17232 17233 17234 17235 17236 17237 17238 17239 17240 17242 17243 
    ##     4     5     5     4     4     5     3     5     5     4     5     4     5 
    ## 17244 17245 17248 17249 17250 17252 17255 17257 17258 17259 17261 17262 17263 
    ##     5     4     5     3     3     5     5     5     5     4     5     3     5 
    ## 17264 17267 17268 17271 17272 17273 17274 17275 17276 17277 17278 17279 17281 
    ##     5     5     4     4     5     3     3     5     5     3     4     5     5 
    ## 17282 17283 17284 17285 17288 17289 17290 17293 17294 17295 17296 17297 17300 
    ##     5     4     5     3     5     5     5     4     3     5     4     5     5 
    ## 17302 17303 17304 17305 17306 17307 17308 17309 17310 17311 17312 17313 17314 
    ##     5     4     5     5     4     5     5     5     5     5     5     3     5 
    ## 17315 17316 17317 17318 17319 17321 17323 17324 17325 17326 17327 17328 17329 
    ##     4     5     3     4     4     4     5     5     5     5     3     4     5 
    ## 17330 17331 17332 17333 17334 17336 17337 17338 17339 17340 17341 17342 17343 
    ##     5     3     5     5     5     3     4     5     5     3     5     4     4 
    ## 17344 17345 17347 17348 17349 17350 17351 17353 17355 17356 17357 17358 17360 
    ##     5     5     5     5     4     5     5     4     4     5     5     5     5 
    ## 17361 17362 17365 17366 17368 17369 17371 17372 17374 17375 17377 17378 17379 
    ##     5     5     3     4     3     5     4     5     5     5     3     4     5 
    ## 17382 17383 17384 17387 17388 17389 17390 17392 17393 17395 17396 17397 17398 
    ##     4     5     3     4     4     4     4     4     5     5     5     5     5 
    ## 17402 17404 17406 17407 17408 17409 17410 17411 17412 17413 17414 17415 17416 
    ##     5     5     4     5     5     5     5     4     5     3     3     5     4 
    ## 17419 17421 17423 17424 17425 17426 17429 17430 17431 17432 17433 17435 17436 
    ##     5     5     3     5     5     5     4     5     5     5     5     5     5 
    ## 17439 17440 17441 17442 17443 17444 17445 17447 17448 17449 17450 17452 17454 
    ##     5     5     4     3     3     5     5     5     4     5     4     5     3 
    ## 17455 17456 17457 17458 17459 17460 17461 17462 17464 17465 17466 17468 17470 
    ##     5     3     5     3     5     4     4     4     4     3     5     4     4 
    ## 17472 17473 17478 17479 17480 17481 17484 17485 17488 17489 17491 17495 17496 
    ##     5     5     5     4     3     5     5     3     5     3     4     4     5 
    ## 17497 17498 17500 17501 17502 17503 17505 17507 17509 17511 17512 17513 17514 
    ##     5     4     3     5     5     5     5     5     4     5     5     3     5 
    ## 17515 17516 17517 17519 17521 17522 17524 17525 17528 17529 17530 17531 17533 
    ##     5     5     5     5     5     5     5     5     3     3     4     5     5 
    ## 17534 17535 17536 17537 17538 17540 17541 17543 17544 17548 17549 17550 17552 
    ##     5     3     4     4     5     4     5     5     4     4     5     5     5 
    ## 17554 17555 17558 17559 17561 17562 17563 17564 17565 17566 17570 17571 17573 
    ##     5     5     5     5     5     5     3     3     5     4     4     5     5 
    ## 17574 17576 17577 17578 17579 17585 17586 17588 17589 17591 17592 17593 17594 
    ##     4     5     5     3     3     5     5     4     5     5     5     4     5 
    ## 17595 17596 17597 17598 17599 17600 17602 17603 17604 17605 17606 17609 17611 
    ##     5     4     5     5     5     5     5     4     3     5     5     4     5 
    ## 17612 17613 17615 17616 17618 17619 17620 17621 17622 17623 17624 17626 17629 
    ##     3     5     5     5     3     5     5     5     4     5     5     4     4 
    ## 17633 17634 17635 17636 17637 17640 17641 17642 17643 17645 17646 17652 17653 
    ##     5     5     5     3     4     5     5     5     5     5     5     5     5 
    ## 17656 17657 17658 17661 17662 17665 17667 17668 17670 17671 17672 17673 17674 
    ##     5     5     4     5     3     3     5     4     5     5     5     3     3 
    ## 17676 17677 17678 17679 17680 17681 17683 17684 17685 17687 17688 17689 17692 
    ##     5     4     3     5     5     3     5     5     5     5     4     4     5 
    ## 17695 17696 17697 17698 17699 17700 17701 17703 17704 17706 17707 17709 17710 
    ##     5     3     5     3     5     5     3     5     3     5     4     4     3 
    ## 17711 17712 17713 17717 17718 17720 17721 17722 17723 17724 17725 17726 17727 
    ##     3     5     3     3     5     5     3     4     4     5     3     5     5 
    ## 17728 17729 17730 17731 17732 17733 17734 17736 17737 17738 17739 17740 17741 
    ##     3     4     4     5     5     5     3     5     5     5     4     3     3 
    ## 17742 17744 17745 17746 17747 17748 17749 17750 17751 17756 17757 17758 17759 
    ##     4     5     5     3     3     3     5     4     5     5     5     5     5 
    ## 17760 17761 17762 17763 17764 17765 17766 17767 17768 17770 17774 17775 17777 
    ##     5     4     4     3     3     4     3     5     3     5     5     4     5 
    ## 17778 17779 17781 17782 17783 17784 17785 17786 17788 17789 17790 17791 17792 
    ##     5     5     4     5     5     5     5     3     5     5     5     4     4 
    ## 17793 17795 17796 17798 17799 17802 17803 17804 17805 17806 17808 17809 17810 
    ##     4     3     4     5     5     5     5     5     5     3     5     4     3 
    ## 17812 17813 17814 17815 17816 17817 17818 17819 17822 17823 17824 17827 17828 
    ##     5     3     5     4     5     3     5     5     5     4     5     5     5 
    ## 17829 17830 17831 17832 17834 17835 17837 17838 17839 17840 17842 17844 17846 
    ##     5     5     4     3     5     3     5     3     3     5     4     5     3 
    ## 17847 17848 17849 17850 17851 17852 17853 17855 17856 17857 17858 17859 17862 
    ##     5     3     3     5     4     5     3     5     5     4     5     4     4 
    ## 17863 17865 17866 17869 17870 17871 17872 17873 17876 17877 17879 17883 17884 
    ##     5     4     5     4     5     5     4     5     5     3     4     3     5 
    ## 17885 17886 17887 17888 17889 17890 17891 17893 17894 17895 17897 17898 17899 
    ##     4     5     4     5     3     5     5     3     5     5     5     4     4 
    ## 17900 17901 17904 17909 17911 17912 17914 17916 17917 17920 17921 17922 17925 
    ##     3     4     5     5     3     3     4     3     5     5     4     5     4 
    ## 17926 17927 17928 17929 17930 17931 17932 17933 17934 17935 17938 17940 17941 
    ##     4     4     5     5     4     3     3     5     5     4     5     5     5 
    ## 17942 17944 17945 17946 17947 17949 17950 17951 17953 17954 17955 17957 17958 
    ##     4     3     5     3     4     5     4     5     5     3     5     5     5 
    ## 17959 17961 17962 17965 17966 17967 17969 17971 17973 17977 17978 17979 17980 
    ##     5     5     3     5     5     5     5     4     5     5     3     5     5 
    ## 17981 17986 17988 17989 17991 17992 17994 17996 17997 17999 18000 18003 18005 
    ##     3     4     5     5     4     5     5     5     4     5     5     3     5 
    ## 18006 18009 18010 18011 18013 18017 18018 18020 18022 18026 18027 18028 18029 
    ##     4     5     5     5     5     3     5     5     5     5     3     3     5 
    ## 18030 18031 18033 18035 18036 18038 18039 18041 18044 18045 18046 18049 18050 
    ##     5     5     5     4     4     4     5     5     5     4     5     4     4 
    ## 18051 18052 18053 18054 18056 18057 18058 18059 18060 18061 18063 18065 18066 
    ##     4     4     4     4     5     5     5     4     5     4     5     5     5 
    ## 18067 18069 18070 18071 18073 18074 18075 18077 18078 18080 18081 18082 18083 
    ##     5     5     4     5     5     5     5     4     5     5     3     3     4 
    ## 18084 18085 18086 18087 18089 18090 18092 18093 18095 18096 18097 18098 18099 
    ##     4     5     5     4     4     3     3     5     3     5     4     5     3 
    ## 18100 18101 18102 18103 18105 18107 18108 18109 18110 18111 18113 18114 18115 
    ##     4     5     5     4     5     3     4     5     4     4     3     3     4 
    ## 18116 18118 18119 18120 18122 18125 18126 18127 18128 18129 18130 18131 18132 
    ##     5     3     5     5     4     3     5     4     4     5     5     4     4 
    ## 18133 18134 18135 18136 18137 18139 18140 18141 18142 18144 18146 18150 18151 
    ##     3     4     3     5     5     3     3     5     5     5     4     5     4 
    ## 18152 18154 18155 18158 18160 18161 18162 18163 18165 18166 18167 18168 18169 
    ##     3     5     5     4     5     5     3     5     5     5     5     3     4 
    ## 18171 18172 18173 18174 18175 18176 18177 18178 18179 18181 18183 18184 18185 
    ##     5     3     4     3     3     3     5     3     4     3     3     3     5 
    ## 18186 18187 18188 18189 18190 18194 18195 18196 18198 18201 18204 18205 18206 
    ##     4     5     5     5     5     5     5     5     3     3     3     4     3 
    ## 18207 18208 18209 18210 18211 18213 18214 18215 18216 18217 18219 18220 18221 
    ##     3     5     4     5     3     3     4     5     3     4     5     3     5 
    ## 18222 18223 18224 18227 18228 18229 18230 18232 18234 18235 18236 18238 18241 
    ##     5     3     3     3     3     5     5     5     5     5     5     3     4 
    ## 18242 18244 18245 18247 18248 18249 18251 18252 18254 18255 18256 18257 18258 
    ##     5     3     5     5     4     4     4     5     3     5     5     5     5 
    ## 18259 18260 18261 18262 18263 18264 18266 18267 18269 18270 18271 18273 18274 
    ##     3     5     5     4     3     5     5     3     5     5     5     5     3 
    ## 18275 18276 18278 18279 18280 18281 18282 18284 18285 18286 18288 18289 18290 
    ##     3     5     5     5     5     5     5     3     3     5     4     4     5 
    ## 18292 18293 18294 18295 18296 18297 18298 18299 18300 18301 18302 18303 18304 
    ##     5     5     3     3     4     5     3     5     4     3     5     5     5 
    ## 18305 18306 18307 18308 18309 18310 18311 18312 18313 18314 18318 18320 18323 
    ##     5     5     5     3     5     5     3     5     3     5     5     5     5 
    ## 18324 18325 18326 18328 18329 18331 18334 18337 18338 18341 18342 18343 18347 
    ##     3     4     5     5     5     3     5     5     4     5     3     4     5 
    ## 18349 18350 18351 18352 18354 18355 18356 18357 18359 18363 18364 18365 18366 
    ##     5     5     5     5     5     5     3     3     5     5     4     5     4 
    ## 18367 18368 18369 18370 18371 18373 18375 18376 18377 18378 18379 18381 18383 
    ##     5     5     4     5     3     3     5     5     4     4     5     5     5 
    ## 18384 18386 18387 18388 18391 18392 18393 18394 18395 18396 18397 18398 18399 
    ##     4     4     3     3     5     4     5     3     5     5     5     4     5 
    ## 18400 18401 18403 18404 18405 18406 18410 18411 18413 18415 18416 18417 18418 
    ##     3     3     3     4     5     5     5     5     4     5     5     4     5 
    ## 18420 18421 18422 18424 18425 18426 18428 18429 18431 18432 18433 18434 18435 
    ##     5     5     5     5     4     3     5     5     5     5     5     4     4 
    ## 18437 18438 18439 18440 18445 18446 18447 18450 18451 18454 18455 18457 18458 
    ##     5     3     3     5     5     5     3     5     5     4     3     4     4 
    ## 18459 18461 18462 18464 18466 18467 18468 18471 18472 18474 18475 18476 18477 
    ##     3     5     5     5     5     5     5     3     4     4     5     5     4 
    ## 18478 18479 18483 18484 18485 18486 18487 18489 18492 18493 18496 18498 18499 
    ##     4     5     5     4     4     3     4     5     3     5     5     5     3 
    ## 18500 18501 18503 18505 18507 18508 18510 18513 18516 18517 18518 18520 18521 
    ##     3     5     5     4     4     5     5     4     3     3     5     5     5 
    ## 18522 18523 18524 18526 18528 18529 18531 18532 18534 18535 18538 18540 18541 
    ##     5     3     5     5     3     4     3     5     5     4     3     3     4 
    ## 18542 18543 18544 18545 18546 18547 18548 18549 18551 18552 18553 18554 18556 
    ##     3     5     4     4     4     4     4     3     5     5     5     5     5 
    ## 18557 18559 18560 18561 18562 18563 18564 18565 18566 18568 18569 18570 18572 
    ##     4     4     5     5     5     4     5     5     4     3     4     3     5 
    ## 18573 18574 18575 18576 18577 18578 18579 18580 18581 18582 18583 18584 18586 
    ##     5     5     3     4     4     5     5     4     4     3     5     4     5 
    ## 18587 18590 18592 18593 18596 18598 18600 18601 18602 18605 18606 18607 18608 
    ##     5     4     3     5     3     4     4     5     3     5     3     5     4 
    ## 18609 18610 18611 18614 18616 18618 18620 18621 18622 18623 18625 18626 18627 
    ##     5     3     4     5     5     5     4     3     5     5     3     5     5 
    ## 18629 18631 18636 18637 18638 18639 18640 18641 18642 18645 18646 18647 18648 
    ##     5     3     3     4     5     5     5     3     3     3     4     4     3 
    ## 18649 18650 18653 18654 18655 18656 18658 18659 18660 18661 18665 18666 18667 
    ##     5     4     5     3     5     5     4     4     5     5     5     5     3 
    ## 18668 18670 18671 18672 18675 18676 18677 18678 18679 18681 18682 18683 18684 
    ##     4     5     5     3     3     4     5     3     5     5     5     5     4 
    ## 18686 18689 18691 18692 18693 18695 18696 18701 18703 18706 18708 18709 18710 
    ##     5     5     4     4     5     4     5     5     3     5     3     5     5 
    ## 18712 18715 18716 18718 18720 18721 18722 18723 18724 18725 18727 18728 18729 
    ##     5     5     5     5     5     5     5     3     5     5     4     5     3 
    ## 18732 18733 18734 18735 18736 18737 18739 18740 18741 18742 18744 18745 18746 
    ##     5     4     5     4     5     5     3     4     3     4     5     4     5 
    ## 18747 18748 18749 18750 18751 18752 18755 18756 18757 18759 18760 18761 18763 
    ##     5     4     5     4     5     4     4     3     5     5     3     3     5 
    ## 18764 18766 18767 18768 18770 18771 18772 18773 18775 18776 18777 18779 18780 
    ##     5     3     5     3     5     5     5     3     3     3     5     5     3 
    ## 18782 18783 18785 18787 18788 18789 18790 18791 18792 18793 18794 18795 18797 
    ##     3     3     3     4     5     3     5     3     5     3     5     4     5 
    ## 18799 18800 18801 18802 18806 18807 18808 18810 18811 18812 18813 18814 18815 
    ##     4     4     4     5     5     5     3     5     5     3     5     5     5 
    ## 18816 18817 18818 18819 18820 18821 18823 18824 18826 18827 18828 18830 18831 
    ##     5     5     4     4     5     5     5     5     5     3     5     5     3 
    ## 18832 18833 18834 18835 18836 18837 18839 18841 18843 18844 18845 18846 18847 
    ##     5     5     3     5     4     5     5     4     5     5     4     5     4 
    ## 18848 18851 18852 18854 18855 18857 18861 18864 18865 18869 18870 18874 18876 
    ##     4     3     3     5     5     5     5     3     3     5     5     5     5 
    ## 18879 18881 18882 18883 18886 18887 18888 18890 18892 18893 18895 18897 18901 
    ##     5     5     3     5     5     5     4     3     5     5     5     3     5 
    ## 18902 18903 18906 18907 18908 18909 18911 18912 18913 18914 18915 18916 18917 
    ##     5     5     5     5     5     5     3     5     3     5     5     3     3 
    ## 18918 18921 18924 18925 18926 18927 18928 18929 18931 18932 18933 18938 18940 
    ##     3     4     4     4     5     3     5     4     4     5     5     4     5 
    ## 18943 18946 18948 18949 18950 18951 18952 18953 18956 18959 18960 18962 18964 
    ##     4     4     3     5     5     5     4     3     3     5     3     5     3 
    ## 18965 18966 18967 18968 18969 18970 18972 18973 18975 18976 18977 18979 18980 
    ##     5     4     5     5     5     5     5     3     5     3     5     3     5 
    ## 18981 18982 18984 18985 18987 18988 18990 18991 18993 18995 18996 18997 19000 
    ##     5     4     5     4     4     5     5     3     4     5     3     5     3 
    ## 19001 19004 19006 19008 19009 19010 19011 19012 19014 19015 19016 19017 19018 
    ##     5     4     3     5     5     3     5     4     5     5     4     4     5 
    ## 19021 19022 19024 19025 19027 19030 19034 19035 19036 19037 19042 19043 19044 
    ##     5     4     3     5     4     3     3     5     5     5     3     5     5 
    ## 19047 19049 19051 19052 19053 19054 19055 19056 19057 19058 19059 19060 19061 
    ##     5     5     3     4     5     5     5     5     5     5     3     5     5 
    ## 19062 19063 19064 19065 19066 19069 19070 19072 19073 19074 19075 19076 19077 
    ##     5     5     4     5     3     4     5     3     5     3     5     4     4 
    ## 19079 19080 19081 19082 19083 19085 19088 19089 19090 19092 19093 19094 19095 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     3 
    ## 19096 19097 19098 19100 19103 19105 19106 19107 19109 19110 19111 19112 19113 
    ##     5     5     4     5     4     5     4     5     5     4     5     4     5 
    ## 19114 19115 19116 19117 19118 19120 19121 19122 19123 19124 19125 19127 19128 
    ##     5     5     5     3     4     5     3     3     3     5     3     4     5 
    ## 19130 19131 19132 19133 19134 19136 19138 19139 19140 19141 19142 19144 19145 
    ##     5     5     3     5     5     5     5     4     5     3     5     4     4 
    ## 19146 19147 19148 19149 19151 19152 19153 19157 19158 19159 19160 19161 19162 
    ##     5     5     4     5     4     5     5     3     3     4     5     5     5 
    ## 19163 19164 19166 19167 19169 19170 19171 19172 19174 19175 19177 19178 19179 
    ##     5     3     5     5     5     5     3     5     3     5     3     5     5 
    ## 19181 19182 19183 19184 19185 19187 19194 19197 19198 19199 19202 19203 19204 
    ##     5     3     5     5     5     4     5     5     5     4     3     3     3 
    ## 19206 19207 19208 19209 19214 19217 19218 19219 19220 19221 19222 19224 19226 
    ##     3     3     3     3     5     5     4     5     3     5     3     3     5 
    ## 19230 19232 19233 19234 19236 19237 19238 19239 19242 19243 19244 19246 19247 
    ##     5     4     5     5     5     5     3     5     5     5     4     5     3 
    ## 19248 19249 19251 19252 19256 19257 19260 19261 19262 19263 19264 19265 19267 
    ##     5     5     5     3     5     3     3     4     3     4     3     5     5 
    ## 19272 19273 19274 19275 19276 19277 19279 19280 19285 19286 19287 19289 19292 
    ##     4     5     4     5     4     5     5     5     5     5     5     5     5 
    ## 19293 19294 19295 19296 19297 19298 19299 19300 19301 19302 19303 19305 19307 
    ##     3     5     4     3     5     3     5     5     5     5     3     3     5 
    ## 19308 19310 19311 19313 19314 19315 19316 19317 19318 19319 19325 19326 19327 
    ##     5     5     4     3     5     5     5     5     5     3     3     5     3 
    ## 19328 19329 19330 19332 19335 19336 19340 19341 19342 19344 19345 19347 19348 
    ##     5     5     5     5     5     4     5     5     5     5     5     5     4 
    ## 19349 19351 19352 19353 19354 19356 19357 19358 19359 19360 19361 19362 19363 
    ##     5     3     5     3     5     4     4     5     5     5     4     5     4 
    ## 19365 19366 19369 19372 19373 19374 19375 19376 19377 19379 19380 19381 19382 
    ##     3     3     5     3     4     5     5     5     5     5     5     3     4 
    ## 19384 19385 19386 19387 19388 19389 19391 19393 19396 19398 19399 19400 19402 
    ##     3     5     4     3     5     4     5     5     5     5     5     5     5 
    ## 19403 19404 19405 19406 19407 19408 19409 19410 19414 19415 19416 19418 19420 
    ##     3     5     3     5     4     5     3     4     5     3     5     5     5 
    ## 19422 19424 19426 19429 19430 19432 19433 19435 19437 19439 19442 19443 19444 
    ##     3     3     5     3     5     5     5     3     3     5     5     5     4 
    ## 19445 19447 19448 19449 19450 19451 19452 19454 19455 19457 19458 19459 19460 
    ##     4     3     5     5     3     3     5     5     3     3     5     4     5 
    ## 19461 19462 19463 19464 19465 19467 19468 19469 19471 19474 19477 19479 19480 
    ##     4     5     5     5     5     5     5     4     5     5     3     3     3 
    ## 19481 19482 19483 19484 19485 19486 19487 19488 19489 19490 19491 19492 19494 
    ##     5     4     4     5     5     5     5     4     5     4     5     5     5 
    ## 19495 19496 19497 19498 19500 19501 19502 19503 19504 19505 19506 19507 19511 
    ##     3     5     5     5     5     5     5     5     4     5     4     5     4 
    ## 19512 19513 19515 19516 19517 19519 19520 19521 19522 19524 19525 19526 19528 
    ##     5     3     4     5     4     4     5     3     3     5     4     3     3 
    ## 19529 19532 19534 19535 19536 19538 19541 19545 19548 19549 19552 19553 19554 
    ##     3     4     5     3     3     3     5     5     4     5     3     5     3 
    ## 19555 19557 19559 19562 19563 19564 19565 19566 19567 19568 19569 19570 19572 
    ##     5     5     5     5     5     5     5     5     3     4     4     5     3 
    ## 19573 19574 19576 19578 19579 19580 19582 19583 19585 19586 19589 19590 19594 
    ##     5     5     5     5     5     4     5     3     5     5     3     4     4 
    ## 19595 19596 19597 19598 19599 19601 19603 19604 19605 19606 19607 19608 19609 
    ##     5     5     5     5     5     5     5     5     5     3     5     3     4 
    ## 19610 19612 19613 19614 19615 19617 19618 19619 19620 19621 19622 19623 19624 
    ##     5     5     3     3     5     5     5     5     5     5     5     3     5 
    ## 19625 19626 19628 19629 19630 19633 19636 19637 19638 19639 19640 19641 19644 
    ##     5     5     5     5     5     5     5     4     4     3     5     5     5 
    ## 19645 19647 19648 19650 19651 19652 19653 19654 19655 19656 19658 19659 19660 
    ##     5     3     3     3     5     5     3     4     5     3     4     3     5 
    ## 19661 19663 19664 19665 19666 19667 19668 19669 19672 19673 19675 19676 19678 
    ##     4     5     3     3     5     5     5     5     5     4     4     3     5 
    ## 19680 19682 19684 19685 19686 19687 19689 19691 19692 19694 19696 19697 19699 
    ##     5     5     3     4     5     3     3     5     5     5     4     4     3 
    ## 19700 19701 19702 19703 19704 19705 19706 19707 19709 19710 19711 19712 19713 
    ##     3     4     3     5     3     3     3     5     5     5     5     3     5 
    ## 19714 19715 19716 19717 19720 19721 19725 19727 19728 19729 19730 19731 19732 
    ##     4     5     4     3     5     5     3     4     4     5     4     3     5 
    ## 19733 19734 19735 19736 19737 19738 19739 19741 19743 19744 19748 19749 19751 
    ##     5     4     3     4     5     5     4     5     5     4     4     5     5 
    ## 19752 19753 19754 19755 19756 19757 19760 19763 19766 19767 19768 19769 19770 
    ##     3     5     3     4     5     5     3     5     5     5     3     3     4 
    ## 19771 19772 19776 19777 19778 19779 19781 19782 19784 19787 19790 19791 19794 
    ##     5     5     3     3     5     5     5     4     3     4     5     3     5 
    ## 19795 19796 19797 19803 19804 19806 19807 19808 19809 19810 19811 19812 19813 
    ##     5     3     3     5     4     4     5     5     3     4     3     5     5 
    ## 19815 19816 19817 19819 19820 19821 19822 19823 19825 19826 19827 19829 19830 
    ##     4     5     4     5     5     5     5     4     5     5     3     5     5 
    ## 19831 19833 19834 19835 19836 19838 19840 19841 19842 19843 19844 19845 19847 
    ##     4     5     5     5     4     5     5     4     4     4     5     4     5 
    ## 19848 19851 19852 19853 19854 19855 19856 19858 19860 19861 19863 19864 19865 
    ##     3     3     5     5     5     3     5     3     3     5     3     5     5 
    ## 19866 19867 19868 19869 19870 19871 19872 19874 19875 19878 19879 19880 19881 
    ##     3     3     5     3     4     5     5     3     4     5     5     5     5 
    ## 19882 19884 19885 19886 19889 19890 19893 19894 19897 19900 19901 19904 19906 
    ##     4     5     3     5     3     5     3     5     5     4     4     5     5 
    ## 19907 19909 19910 19911 19912 19913 19915 19916 19917 19918 19919 19920 19922 
    ##     4     5     3     5     3     5     5     5     5     5     4     5     5 
    ## 19924 19925 19928 19930 19932 19933 19934 19935 19937 19938 19939 19940 19941 
    ##     3     5     3     4     3     3     5     3     4     4     5     3     3 
    ## 19942 19944 19945 19946 19947 19949 19950 19951 19952 19953 19956 19957 19958 
    ##     3     5     3     3     3     5     3     5     3     4     3     4     4 
    ## 19959 19960 19961 19963 19964 19965 19968 19969 19970 19971 19974 19976 19978 
    ##     5     5     3     3     4     5     3     3     3     4     3     4     5 
    ## 19979 19980 19982 19983 19984 19985 19987 19988 19989 19990 19991 19992 19994 
    ##     3     5     3     5     5     5     3     4     5     3     3     4     5 
    ## 19995 19997 19999 20000 20002 20003 20005 20007 20008 20009 20012 20014 20015 
    ##     3     4     3     5     5     4     5     3     5     5     5     4     5 
    ## 20017 20018 20019 20022 20024 20025 20026 20027 20028 20029 20030 20033 20034 
    ##     3     5     3     5     5     5     3     5     4     3     5     3     4 
    ## 20036 20037 20038 20039 20040 20042 20043 20045 20046 20047 20048 20050 20053 
    ##     5     5     4     4     5     3     3     4     5     3     5     4     5 
    ## 20055 20057 20058 20060 20061 20062 20064 20065 20066 20067 20069 20070 20071 
    ##     5     5     5     4     5     5     4     4     5     3     5     5     5 
    ## 20072 20073 20074 20078 20080 20082 20084 20085 20086 20087 20088 20089 20090 
    ##     5     5     5     5     5     3     5     5     5     3     3     5     5 
    ## 20096 20097 20100 20102 20103 20104 20105 20107 20108 20109 20110 20112 20115 
    ##     5     4     4     5     4     5     5     4     5     3     5     3     5 
    ## 20117 20118 20119 20120 20122 20123 20124 20125 20128 20129 20130 20131 20132 
    ##     3     5     4     3     3     4     4     4     4     5     5     5     5 
    ## 20133 20134 20136 20137 20138 20139 20140 20141 20143 20145 20146 20147 20148 
    ##     5     5     5     5     3     4     3     5     5     3     4     5     5 
    ## 20149 20150 20151 20153 20154 20158 20159 20160 20161 20162 20165 20166 20167 
    ##     5     3     3     3     5     5     5     5     5     5     5     5     5 
    ## 20168 20171 20172 20173 20174 20175 20177 20178 20179 20180 20181 20182 20183 
    ##     5     3     5     5     5     3     5     5     5     4     4     5     5 
    ## 20184 20186 20187 20188 20189 20191 20192 20193 20196 20197 20198 20199 20200 
    ##     5     4     5     5     5     4     5     4     3     4     5     4     5 
    ## 20201 20204 20205 20206 20207 20209 20210 20211 20213 20214 20215 20217 20219 
    ##     5     5     5     5     5     4     5     5     5     5     5     4     3 
    ## 20220 20222 20223 20225 20226 20227 20228 20229 20232 20233 20236 20237 20238 
    ##     4     3     3     3     4     5     5     5     4     4     5     3     3 
    ## 20239 20240 20241 20243 20244 20245 20246 20247 20248 20250 20252 20254 20256 
    ##     3     4     5     5     3     5     3     5     4     5     5     3     4 
    ## 20257 20258 20260 20261 20262 20264 20265 20267 20268 20269 20270 20271 20272 
    ##     5     5     5     5     4     4     3     3     4     5     5     5     5 
    ## 20273 20274 20276 20277 20278 20279 20280 20281 20282 20283 20284 20286 20287 
    ##     3     4     5     3     5     5     3     5     3     4     5     5     4 
    ## 20288 20289 20291 20295 20296 20297 20298 20300 20301 20302 20303 20304 20305 
    ##     3     5     4     5     3     5     4     3     5     5     5     5     4 
    ## 20306 20308 20309 20310 20311 20312 20313 20314 20315 20316 20320 20321 20322 
    ##     3     3     3     5     3     3     5     5     5     5     3     3     4 
    ## 20324 20325 20326 20328 20330 20331 20332 20333 20334 20335 20336 20338 20339 
    ##     5     4     3     5     5     3     5     3     3     4     5     5     5 
    ## 20341 20342 20343 20344 20346 20350 20351 20352 20354 20355 20356 20359 20361 
    ##     3     5     5     5     4     5     5     4     4     4     4     5     3 
    ## 20363 20364 20365 20366 20367 20370 20372 20376 20379 20380 20382 20384 20386 
    ##     5     5     5     5     3     5     5     5     5     4     4     4     5 
    ## 20388 20389 20390 20391 20392 20393 20394 20395 20396 20397 20398 20400 20401 
    ##     5     5     4     5     3     3     5     5     5     5     4     3     3 
    ## 20402 20403 20404 20405 20406 20407 20409 20411 20412 20413 20414 20415 20416 
    ##     5     4     4     3     5     3     3     5     5     3     3     4     5 
    ## 20417 20419 20420 20421 20422 20423 20425 20426 20427 20430 20432 20433 20434 
    ##     5     4     4     4     5     5     5     3     3     5     3     5     5 
    ## 20437 20438 20439 20441 20443 20444 20445 20448 20449 20451 20452 20455 20456 
    ##     5     3     3     5     5     5     5     4     5     5     5     5     5 
    ## 20457 20458 20461 20462 20463 20465 20467 20468 20469 20471 20472 20474 20478 
    ##     5     4     4     3     4     5     5     5     3     5     5     3     5 
    ## 20479 20480 20481 20482 20483 20485 20486 20487 20490 20491 20492 20497 20498 
    ##     3     4     4     5     5     5     5     3     5     5     3     3     5 
    ## 20500 20501 20502 20503 20504 20505 20506 20509 20511 20514 20515 20516 20517 
    ##     4     5     4     5     5     5     4     4     5     4     4     5     5 
    ## 20518 20519 20520 20521 20522 20523 20524 20526 20528 20530 20531 20534 20535 
    ##     4     4     3     4     5     5     5     3     3     5     4     3     3 
    ## 20537 20538 20539 20540 20541 20542 20543 20544 20546 20547 20548 20549 20550 
    ##     5     5     5     5     5     3     4     5     5     5     5     5     5 
    ## 20551 20553 20554 20555 20556 20557 20558 20559 20561 20564 20565 20566 20567 
    ##     4     5     4     5     5     5     5     4     5     5     5     4     5 
    ## 20568 20570 20571 20573 20574 20575 20577 20578 20580 20581 20582 20584 20586 
    ##     5     5     3     5     4     5     5     3     3     5     5     5     5 
    ## 20587 20588 20589 20593 20594 20595 20596 20597 20598 20599 20600 20602 20603 
    ##     5     5     5     5     5     5     3     3     3     5     5     5     5 
    ## 20604 20605 20606 20608 20609 20610 20611 20612 20613 20614 20615 20617 20619 
    ##     3     3     3     5     5     5     3     4     5     4     4     5     5 
    ## 20621 20622 20623 20626 20627 20628 20629 20631 20633 20635 20636 20637 20638 
    ##     5     4     4     5     5     4     4     4     4     5     5     5     5 
    ## 20640 20641 20642 20643 20644 20645 20646 20648 20649 20650 20651 20655 20657 
    ##     5     5     5     5     4     5     5     3     5     5     5     3     3 
    ## 20658 20659 20660 20661 20663 20664 20665 20666 20667 20668 20669 20670 20671 
    ##     4     5     5     3     4     5     5     5     5     5     4     5     5 
    ## 20672 20673 20674 20675 20679 20680 20681 20683 20684 20685 20686 20687 20689 
    ##     3     5     3     5     3     3     5     5     4     4     5     5     4 
    ## 20690 20691 20692 20693 20697 20698 20700 20702 20703 20704 20705 20706 20707 
    ##     4     4     5     3     4     3     4     5     5     3     3     5     5 
    ## 20708 20709 20710 20712 20714 20715 20716 20717 20718 20720 20721 20723 20724 
    ##     5     5     5     5     4     4     3     3     3     3     5     4     5 
    ## 20725 20726 20727 20728 20730 20732 20734 20735 20736 20737 20738 20739 20740 
    ##     4     3     5     5     5     3     3     5     5     5     3     3     5 
    ## 20741 20742 20745 20746 20747 20748 20749 20751 20752 20753 20754 20755 20756 
    ##     5     5     4     5     4     4     3     5     5     5     5     3     3 
    ## 20758 20759 20760 20762 20763 20765 20766 20767 20768 20769 20770 20774 20775 
    ##     5     4     5     5     5     3     4     5     5     4     4     5     4 
    ## 20777 20778 20779 20780 20781 20782 20785 20787 20789 20791 20792 20793 20794 
    ##     4     5     5     3     5     5     3     4     4     3     5     5     5 
    ## 20795 20796 20798 20800 20801 20802 20803 20805 20806 20807 20808 20809 20810 
    ##     5     5     3     5     4     5     5     5     5     5     5     5     5 
    ## 20811 20812 20813 20814 20815 20816 20819 20821 20822 20823 20824 20825 20826 
    ##     5     5     3     4     5     5     5     5     5     4     5     4     4 
    ## 20827 20828 20830 20831 20832 20836 20837 20839 20841 20843 20844 20846 20850 
    ##     3     4     5     3     3     5     5     5     5     5     3     5     4 
    ## 20851 20854 20858 20859 20860 20861 20863 20864 20866 20867 20868 20869 20870 
    ##     5     5     4     5     4     4     5     3     5     5     5     4     4 
    ## 20871 20872 20873 20875 20878 20880 20881 20882 20884 20885 20886 20888 20890 
    ##     3     5     3     5     4     5     5     5     4     3     4     3     3 
    ## 20891 20892 20893 20894 20895 20897 20898 20899 20900 20902 20904 20906 20907 
    ##     5     5     3     3     4     5     4     4     3     5     4     3     5 
    ## 20909 20910 20912 20914 20915 20916 20918 20919 20920 20921 20922 20924 20926 
    ##     5     5     3     3     4     3     3     5     5     3     5     3     5 
    ## 20927 20928 20929 20930 20931 20934 20935 20937 20938 20939 20940 20942 20944 
    ##     3     3     5     5     3     4     4     3     5     5     4     4     3 
    ## 20946 20948 20949 20950 20952 20953 20954 20955 20956 20957 20958 20960 20961 
    ##     4     4     5     4     3     4     5     5     5     5     5     5     3 
    ## 20964 20966 20968 20969 20970 20972 20974 20975 20976 20977 20981 20982 20983 
    ##     4     3     5     3     5     3     5     5     3     5     4     5     4 
    ## 20985 20986 20988 20989 20990 20991 20992 20993 20995 20996 20997 20998 20999 
    ##     5     5     3     4     5     5     5     5     5     3     5     3     5 
    ## 21001 21004 21006 21007 21010 21011 21014 21015 21017 21018 21021 21022 21023 
    ##     4     3     5     3     5     5     5     3     5     4     5     3     3 
    ## 21026 21028 21029 21031 21032 21034 21036 21037 21038 21039 21042 21045 21046 
    ##     4     4     4     5     3     4     5     5     4     5     4     4     4 
    ## 21047 21048 21049 21050 21052 21053 21054 21056 21058 21061 21062 21063 21066 
    ##     3     3     5     3     5     5     4     5     5     5     3     5     4 
    ## 21067 21069 21070 21071 21072 21073 21074 21076 21077 21078 21083 21085 21086 
    ##     5     5     5     3     3     5     4     5     4     4     3     4     3 
    ## 21087 21089 21090 21093 21094 21095 21096 21097 21098 21099 21100 21101 21102 
    ##     3     5     5     4     3     5     3     3     3     3     4     3     3 
    ## 21104 21106 21110 21111 21112 21113 21114 21117 21118 21119 21120 21122 21123 
    ##     3     5     3     4     3     5     3     5     4     3     5     4     5 
    ## 21124 21125 21126 21128 21129 21130 21132 21133 21135 21139 21140 21141 21143 
    ##     5     5     5     3     5     5     3     5     5     3     4     5     5 
    ## 21144 21145 21147 21149 21151 21152 21153 21154 21155 21156 21157 21158 21159 
    ##     4     5     5     4     4     5     5     3     5     5     3     4     3 
    ## 21161 21162 21163 21164 21165 21166 21167 21168 21169 21170 21171 21172 21173 
    ##     5     5     3     3     5     5     4     4     4     5     5     4     3 
    ## 21174 21175 21176 21177 21180 21182 21185 21187 21190 21191 21192 21194 21195 
    ##     4     3     5     4     4     4     5     5     4     3     4     5     4 
    ## 21196 21198 21200 21201 21202 21203 21205 21209 21210 21211 21212 21214 21216 
    ##     5     5     3     5     5     5     3     5     3     4     4     4     3 
    ## 21218 21221 21222 21223 21224 21225 21226 21227 21228 21229 21234 21235 21236 
    ##     4     3     3     5     4     4     3     5     5     3     5     4     5 
    ## 21237 21242 21245 21246 21247 21248 21250 21252 21253 21254 21256 21259 21260 
    ##     5     5     4     3     3     3     5     5     3     5     4     3     3 
    ## 21261 21263 21264 21265 21266 21267 21268 21269 21270 21271 21274 21275 21277 
    ##     5     5     5     5     4     5     4     5     5     4     5     5     5 
    ## 21278 21279 21280 21282 21284 21287 21290 21291 21293 21294 21295 21296 21298 
    ##     4     3     4     5     5     5     5     3     5     5     4     5     3 
    ## 21299 21300 21301 21302 21303 21304 21306 21307 21308 21310 21312 21313 21314 
    ##     5     3     3     5     5     5     5     4     4     5     3     3     4 
    ## 21316 21318 21319 21320 21321 21322 21324 21325 21326 21327 21329 21331 21332 
    ##     3     5     4     3     5     4     4     4     4     5     3     3     4 
    ## 21333 21336 21337 21339 21341 21342 21343 21344 21345 21347 21348 21349 21350 
    ##     5     5     3     5     3     4     5     4     5     5     5     5     3 
    ## 21351 21352 21353 21354 21355 21356 21357 21358 21359 21360 21361 21362 21363 
    ##     5     3     5     5     3     3     3     5     4     5     4     5     3 
    ## 21367 21368 21369 21370 21371 21372 21373 21375 21376 21377 21379 21380 21381 
    ##     4     4     3     5     4     3     4     5     3     5     5     5     3 
    ## 21382 21383 21384 21385 21386 21387 21388 21390 21391 21392 21394 21395 21397 
    ##     5     3     3     3     4     5     4     4     5     3     4     3     3 
    ## 21398 21399 21400 21403 21407 21408 21409 21410 21412 21413 21414 21415 21416 
    ##     3     5     5     3     4     3     4     5     5     5     3     4     5 
    ## 21417 21418 21419 21420 21421 21426 21429 21430 21431 21434 21435 21436 21437 
    ##     3     5     5     4     5     5     5     3     3     4     3     3     3 
    ## 21439 21440 21441 21443 21445 21446 21447 21448 21450 21451 21452 21453 21455 
    ##     4     5     5     5     3     3     4     4     3     3     5     4     4 
    ## 21456 21458 21460 21461 21462 21463 21464 21465 21467 21468 21471 21472 21473 
    ##     3     3     3     5     3     4     5     4     4     4     5     5     3 
    ## 21474 21475 21476 21477 21478 21479 21480 21481 21482 21483 21484 21486 21487 
    ##     3     4     3     4     5     3     5     3     5     5     3     4     3 
    ## 21489 21490 21492 21495 21496 21497 21498 21500 21503 21504 21505 21506 21508 
    ##     3     3     3     3     3     3     5     5     5     5     3     5     5 
    ## 21509 21510 21511 21512 21513 21514 21515 21516 21517 21518 21520 21521 21523 
    ##     5     4     5     3     3     5     5     5     4     3     5     4     4 
    ## 21525 21526 21527 21528 21530 21531 21533 21534 21535 21536 21537 21538 21539 
    ##     3     3     5     5     3     3     4     3     4     3     4     5     3 
    ## 21540 21541 21544 21545 21546 21548 21549 21550 21551 21552 21553 21554 21555 
    ##     5     5     4     3     4     5     4     5     5     4     4     3     5 
    ## 21559 21560 21563 21564 21565 21566 21567 21569 21570 21571 21572 21573 21574 
    ##     3     5     5     4     5     3     5     4     4     3     5     4     5 
    ## 21575 21577 21582 21583 21584 21586 21587 21589 21590 21591 21592 21594 21596 
    ##     5     3     5     5     4     5     3     4     5     5     4     5     5 
    ## 21597 21599 21600 21601 21602 21603 21604 21605 21607 21608 21609 21611 21612 
    ##     3     3     5     5     3     4     3     3     4     5     5     5     4 
    ## 21614 21618 21619 21621 21622 21623 21624 21626 21627 21628 21629 21631 21632 
    ##     5     5     3     4     4     4     4     3     3     5     4     5     5 
    ## 21633 21635 21636 21637 21638 21639 21640 21641 21642 21643 21644 21645 21646 
    ##     4     5     3     5     5     4     5     5     5     5     5     5     3 
    ## 21647 21648 21651 21652 21653 21654 21655 21657 21658 21660 21661 21662 21663 
    ##     5     4     5     4     3     5     3     3     5     3     5     4     5 
    ## 21666 21667 21668 21670 21671 21674 21677 21682 21685 21686 21687 21688 21689 
    ##     3     5     3     5     5     5     3     5     4     4     3     3     5 
    ## 21696 21697 21698 21700 21701 21702 21703 21704 21706 21707 21708 21711 21714 
    ##     5     5     5     3     4     5     3     5     4     4     4     5     3 
    ## 21716 21717 21719 21720 21721 21722 21723 21724 21725 21726 21727 21728 21731 
    ##     5     5     5     3     4     5     3     4     4     4     5     4     5 
    ## 21732 21733 21735 21736 21737 21738 21739 21741 21742 21745 21748 21749 21750 
    ##     5     5     3     4     5     4     4     4     5     5     3     5     5 
    ## 21751 21752 21753 21754 21755 21756 21757 21758 21759 21760 21761 21762 21763 
    ##     5     5     5     5     5     5     5     4     3     5     5     5     5 
    ## 21764 21766 21767 21768 21769 21770 21771 21772 21773 21774 21775 21776 21777 
    ##     5     3     4     5     5     5     4     3     5     5     5     3     5 
    ## 21778 21779 21780 21782 21783 21784 21785 21786 21787 21788 21789 21790 21791 
    ##     4     4     4     5     5     5     3     4     5     3     5     5     5 
    ## 21792 21793 21794 21795 21796 21797 21799 21800 21804 21805 21806 21807 21808 
    ##     5     5     5     4     4     5     5     5     3     5     4     4     3 
    ## 21809 21810 21811 21813 21814 21816 21817 21818 21819 21820 21822 21823 21824 
    ##     3     4     3     5     3     5     3     4     4     5     4     5     4 
    ## 21825 21826 21827 21828 21829 21830 21831 21833 21835 21837 21839 21841 21842 
    ##     4     3     5     5     5     3     5     4     4     5     3     4     5 
    ## 21843 21844 21847 21848 21849 21850 21851 21852 21854 21856 21857 21858 21859 
    ##     5     3     4     5     3     5     4     5     5     4     5     3     4 
    ## 21861 21863 21864 21865 21866 21868 21869 21870 21874 21875 21876 21877 21879 
    ##     5     5     5     3     4     5     4     3     4     5     5     5     3 
    ## 21880 21881 21883 21884 21885 21886 21887 21889 21891 21892 21893 21895 21896 
    ##     3     5     3     5     4     3     4     3     5     4     4     5     5 
    ## 21897 21898 21900 21901 21902 21903 21904 21905 21909 21910 21911 21912 21913 
    ##     5     4     5     4     5     3     5     3     5     5     3     5     3 
    ## 21914 21915 21917 21918 21920 21921 21922 21923 21924 21925 21926 21929 21930 
    ##     5     5     5     3     4     5     4     5     5     5     5     5     5 
    ## 21933 21934 21935 21937 21939 21940 21941 21942 21943 21944 21945 21946 21947 
    ##     5     3     4     5     5     5     5     3     4     5     5     4     5 
    ## 21948 21949 21950 21954 21957 21959 21960 21961 21962 21966 21968 21971 21974 
    ##     4     5     5     5     3     4     3     5     4     5     5     5     3 
    ## 21980 21981 21982 21984 21985 21986 21987 21990 21991 21994 21997 21998 21999 
    ##     5     5     5     4     4     5     5     4     3     5     5     3     5 
    ## 22001 22002 22003 22004 22006 22007 22009 22011 22014 22015 22017 22019 22020 
    ##     5     5     5     3     3     4     4     5     5     4     5     5     5 
    ## 22021 22024 22025 22026 22027 22028 22029 22031 22032 22033 22034 22036 22037 
    ##     5     5     3     4     4     3     5     3     5     5     5     4     4 
    ## 22038 22040 22041 22042 22046 22047 22050 22051 22053 22056 22060 22063 22064 
    ##     5     5     5     5     3     5     3     3     4     5     5     3     4 
    ## 22065 22066 22068 22070 22075 22078 22079 22080 22081 22084 22085 22086 22088 
    ##     5     5     5     5     5     4     5     3     5     5     3     5     4 
    ## 22090 22091 22092 22093 22094 22095 22096 22097 22099 22100 22101 22103 22105 
    ##     3     3     5     3     5     5     5     5     4     5     5     5     3 
    ## 22106 22108 22109 22110 22112 22113 22114 22115 22116 22117 22118 22119 22120 
    ##     5     5     4     5     5     3     3     5     3     5     5     5     5 
    ## 22121 22122 22123 22125 22126 22128 22129 22131 22135 22136 22139 22140 22141 
    ##     3     5     4     5     5     3     3     3     5     5     4     5     4 
    ## 22142 22146 22147 22148 22151 22152 22153 22154 22155 22156 22157 22158 22160 
    ##     3     5     5     5     5     5     5     5     5     4     4     5     5 
    ## 22162 22163 22164 22166 22167 22168 22169 22171 22172 22173 22175 22176 22177 
    ##     3     5     5     4     3     3     5     3     5     5     3     5     4 
    ## 22179 22181 22182 22186 22189 22191 22192 22193 22194 22196 22198 22199 22201 
    ##     5     4     5     5     5     3     5     5     5     3     5     4     3 
    ## 22202 22203 22204 22205 22206 22207 22208 22209 22210 22211 22212 22213 22214 
    ##     5     3     5     3     3     5     5     3     3     3     5     4     5 
    ## 22215 22216 22217 22218 22219 22220 22221 22222 22223 22225 22226 22228 22230 
    ##     5     5     4     4     5     5     3     3     3     5     4     5     3 
    ## 22238 22239 22240 22241 22242 22243 22244 22245 22246 22248 22249 22251 22252 
    ##     5     5     5     4     4     5     5     3     4     5     5     5     4 
    ## 22253 22254 22255 22256 22257 22259 22260 22261 22262 22263 22265 22267 22268 
    ##     5     5     4     4     3     4     3     3     4     3     3     5     5 
    ## 22269 22270 22274 22275 22278 22279 22280 22281 22282 22283 22284 22289 22291 
    ##     5     5     5     4     5     5     4     5     3     4     5     5     4 
    ## 22294 22295 22296 22299 22300 22301 22302 22304 22306 22308 22309 22310 22312 
    ##     5     4     5     5     5     5     5     3     4     3     5     5     5 
    ## 22313 22314 22315 22317 22318 22321 22324 22325 22326 22328 22329 22330 22331 
    ##     4     5     5     5     4     4     4     3     4     4     4     5     3 
    ## 22332 22334 22336 22337 22338 22339 22340 22342 22343 22345 22346 22347 22348 
    ##     5     5     4     5     5     3     5     4     4     5     5     4     5 
    ## 22349 22354 22355 22356 22359 22361 22362 22363 22366 22367 22368 22369 22370 
    ##     5     5     5     5     5     4     5     5     5     4     5     4     5 
    ## 22371 22372 22374 22376 22378 22379 22381 22382 22384 22387 22391 22393 22396 
    ##     3     5     4     4     5     3     5     5     4     5     5     5     4 
    ## 22398 22399 22400 22401 22402 22404 22405 22406 22407 22408 22410 22411 22414 
    ##     4     5     4     4     4     3     5     5     5     5     5     4     5 
    ## 22416 22417 22418 22420 22422 22423 22424 22425 22426 22427 22428 22430 22431 
    ##     3     5     5     3     3     5     4     5     5     5     5     4     5 
    ## 22432 22435 22436 22438 22439 22440 22442 22443 22445 22447 22449 22451 22452 
    ##     4     5     5     4     5     3     3     4     5     4     3     5     5 
    ## 22454 22455 22457 22458 22459 22460 22461 22462 22463 22465 22467 22469 22471 
    ##     5     5     5     5     5     4     3     4     5     4     5     4     5 
    ## 22472 22473 22475 22476 22477 22478 22482 22483 22484 22486 22487 22488 22490 
    ##     4     5     5     3     5     3     5     5     5     3     4     5     5 
    ## 22491 22493 22494 22495 22497 22498 22499 22500 22502 22506 22507 22509 22511 
    ##     4     5     5     5     5     4     5     5     5     3     3     5     3 
    ## 22514 22516 22517 22519 22522 22523 22526 22527 22529 22530 22531 22533 22535 
    ##     4     4     5     5     4     3     5     3     4     5     3     4     5 
    ## 22537 22539 22542 22544 22545 22546 22547 22548 22549 22550 22551 22553 22554 
    ##     4     5     5     5     5     5     4     3     5     5     5     5     5 
    ## 22555 22556 22557 22558 22559 22561 22562 22565 22566 22567 22570 22572 22573 
    ##     5     4     4     3     5     5     4     5     5     3     4     5     5 
    ## 22574 22575 22576 22577 22578 22580 22582 22583 22584 22585 22586 22588 22589 
    ##     5     4     5     5     4     5     5     4     5     3     3     4     3 
    ## 22591 22592 22593 22595 22596 22597 22598 22599 22601 22603 22605 22606 22607 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 22608 22610 22613 22614 22615 22616 22617 22618 22619 22622 22624 22625 22626 
    ##     5     5     5     3     5     4     3     5     5     3     5     5     3 
    ## 22627 22628 22629 22630 22631 22632 22634 22636 22637 22638 22641 22642 22643 
    ##     5     3     5     5     3     4     5     4     3     3     5     5     5 
    ## 22644 22646 22648 22650 22651 22654 22656 22657 22658 22659 22663 22665 22667 
    ##     5     5     5     4     5     3     3     4     5     5     5     4     4 
    ## 22668 22671 22673 22676 22677 22678 22679 22680 22681 22683 22686 22687 22688 
    ##     3     5     5     4     4     4     5     5     5     5     3     5     5 
    ## 22689 22690 22692 22693 22694 22695 22696 22697 22698 22699 22700 22701 22702 
    ##     4     3     4     5     4     5     5     5     5     5     5     5     5 
    ## 22703 22705 22706 22708 22709 22710 22711 22714 22715 22718 22719 22720 22721 
    ##     5     4     5     4     5     5     3     4     3     5     4     5     4 
    ## 22722 22723 22725 22728 22730 22731 22732 22733 22734 22735 22736 22737 22738 
    ##     4     5     5     5     5     3     5     5     5     5     5     4     5 
    ## 22739 22740 22741 22743 22744 22745 22746 22747 22749 22750 22751 22752 22753 
    ##     5     5     3     4     3     5     5     3     4     5     4     5     5 
    ## 22754 22755 22757 22758 22759 22760 22761 22762 22763 22764 22765 22766 22767 
    ##     5     5     5     4     3     4     5     3     4     4     5     4     5 
    ## 22770 22771 22772 22773 22775 22778 22779 22780 22781 22782 22783 22784 22785 
    ##     5     3     3     5     5     5     5     4     5     4     5     4     5 
    ## 22786 22789 22792 22794 22795 22796 22797 22798 22799 22800 22801 22802 22803 
    ##     5     5     4     5     5     4     3     3     3     3     5     3     4 
    ## 22804 22805 22806 22809 22811 22812 22813 22814 22815 22816 22817 22820 22823 
    ##     5     5     4     4     4     3     5     3     4     5     3     4     3 
    ## 22824 22825 22826 22828 22829 22830 22832 22833 22836 22837 22838 22840 22841 
    ##     5     4     5     4     5     3     5     5     5     5     5     5     5 
    ## 22844 22845 22846 22848 22849 22850 22851 22853 22854 22855 22856 22857 22858 
    ##     5     5     5     5     4     5     4     5     3     5     5     4     5 
    ## 22859 22860 22861 22863 22864 22865 22866 22868 22871 22872 22873 22874 22876 
    ##     3     4     5     5     4     3     5     5     5     5     4     4     5 
    ## 22877 22878 22879 22880 22881 22883 22885 22886 22888 22889 22890 22891 22893 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     4 
    ## 22894 22897 22900 22901 22904 22906 22909 22911 22912 22913 22914 22915 22916 
    ##     5     4     5     5     4     5     5     4     5     3     5     3     4 
    ## 22917 22918 22919 22920 22921 22922 22923 22925 22926 22928 22929 22930 22931 
    ##     3     3     5     5     3     5     5     4     4     5     3     5     4 
    ## 22932 22933 22934 22935 22936 22937 22938 22939 22940 22942 22943 22945 22946 
    ##     5     3     3     5     5     5     5     5     5     4     5     4     4 
    ## 22947 22948 22949 22950 22951 22952 22953 22955 22956 22958 22960 22961 22963 
    ##     5     5     4     5     4     5     5     4     5     4     4     5     3 
    ## 22964 22965 22967 22968 22969 22970 22975 22976 22977 22978 22980 22982 22983 
    ##     5     5     4     5     4     4     5     5     5     3     5     5     3 
    ## 22984 22985 22986 22987 22990 22996 22997 22999 23001 23002 23004 23005 23006 
    ##     3     5     3     5     5     5     4     5     5     5     5     3     3 
    ## 23007 23010 23011 23014 23015 23016 23017 23018 23019 23020 23021 23022 23023 
    ##     4     3     5     5     5     5     5     5     5     5     5     5     5 
    ## 23026 23028 23029 23030 23031 23032 23033 23034 23036 23037 23039 23041 23043 
    ##     5     5     5     4     5     5     4     5     5     4     5     5     4 
    ## 23045 23046 23047 23048 23049 23050 23051 23053 23054 23055 23057 23059 23060 
    ##     4     5     4     5     5     5     5     3     5     4     5     5     5 
    ## 23061 23064 23066 23067 23068 23069 23070 23071 23072 23073 23074 23075 23076 
    ##     5     5     5     3     3     5     4     5     3     3     5     4     5 
    ## 23077 23079 23080 23082 23083 23085 23086 23089 23093 23094 23095 23096 23098 
    ##     5     5     4     3     5     5     3     5     4     4     5     5     5 
    ## 23101 23102 23103 23105 23106 23107 23108 23110 23113 23114 23115 23116 23118 
    ##     5     5     5     5     5     5     3     3     5     5     3     3     5 
    ## 23119 23120 23121 23122 23126 23127 23128 23129 23130 23131 23133 23137 23138 
    ##     4     5     4     3     5     5     5     5     4     4     5     4     5 
    ## 23140 23141 23143 23144 23146 23147 23149 23150 23151 23152 23153 23155 23156 
    ##     5     3     5     5     5     4     4     5     5     5     3     5     4 
    ## 23160 23161 23162 23163 23166 23167 23168 23169 23170 23171 23172 23173 23174 
    ##     5     5     5     5     5     4     5     5     5     5     4     5     5 
    ## 23175 23177 23178 23179 23181 23182 23183 23184 23185 23187 23188 23190 23192 
    ##     5     5     5     4     5     5     4     5     4     5     3     5     5 
    ## 23193 23194 23195 23196 23199 23200 23201 23202 23203 23205 23206 23207 23209 
    ##     5     5     4     3     4     3     4     5     5     4     4     3     5 
    ## 23210 23212 23213 23214 23215 23216 23217 23218 23220 23222 23225 23226 23230 
    ##     5     3     5     5     5     5     5     5     5     3     5     5     3 
    ## 23232 23233 23234 23236 23237 23238 23239 23243 23244 23245 23246 23247 23249 
    ##     5     5     5     5     5     5     3     5     5     3     5     5     3 
    ## 23251 23255 23256 23257 23258 23259 23263 23264 23265 23266 23267 23268 23269 
    ##     5     5     5     5     5     4     5     5     5     5     5     5     5 
    ## 23270 23272 23273 23275 23276 23277 23278 23279 23280 23281 23282 23283 23284 
    ##     5     3     4     4     5     5     5     4     5     5     3     5     3 
    ## 23286 23287 23288 23289 23290 23293 23294 23295 23297 23298 23299 23300 23301 
    ##     5     5     5     4     3     3     5     5     4     5     5     4     5 
    ## 23302 23303 23304 23306 23309 23310 23312 23313 23314 23316 23317 23318 23319 
    ##     5     5     5     3     4     5     5     5     5     3     5     5     5 
    ## 23321 23322 23323 23324 23325 23326 23327 23330 23331 23332 23333 23335 23339 
    ##     5     3     5     3     5     5     5     5     3     5     3     3     5 
    ## 23340 23341 23342 23343 23344 23345 23346 23347 23349 23351 23352 23353 23355 
    ##     3     5     4     5     4     5     5     5     5     5     3     5     3 
    ## 23356 23357 23358 23359 23363 23366 23367 23368 23369 23370 23371 23372 23373 
    ##     5     5     5     4     5     5     5     5     5     5     4     4     5 
    ## 23375 23377 23378 23379 23380 23381 23382 23383 23384 23385 23387 23388 23389 
    ##     5     3     3     5     4     5     3     5     4     3     5     5     3 
    ## 23390 23391 23392 23393 23394 23395 23396 23398 23399 23401 23403 23404 23405 
    ##     3     5     5     5     5     3     3     3     5     4     4     3     4 
    ## 23407 23408 23411 23415 23416 23417 23418 23421 23422 23423 23426 23427 23428 
    ##     3     5     4     5     3     5     3     3     3     3     4     5     3 
    ## 23429 23430 23431 23432 23433 23434 23436 23437 23438 23439 23440 23441 23443 
    ##     5     4     5     5     3     5     3     3     5     5     5     5     4 
    ## 23445 23446 23447 23448 23449 23450 23451 23452 23455 23456 23457 23459 23460 
    ##     3     4     3     3     5     4     3     3     5     3     4     3     3 
    ## 23461 23462 23463 23466 23467 23468 23471 23473 23475 23478 23479 23480 23481 
    ##     5     4     5     3     5     4     5     5     3     3     3     3     5 
    ## 23482 23483 23485 23486 23487 23491 23492 23494 23496 23498 23500 23501 23502 
    ##     5     3     5     5     3     3     5     5     4     5     4     4     3 
    ## 23503 23504 23505 23506 23507 23509 23510 23511 23512 23515 23516 23517 23518 
    ##     4     3     5     4     5     3     4     4     5     3     5     3     5 
    ## 23520 23521 23523 23524 23525 23526 23528 23529 23530 23531 23532 23534 23535 
    ##     5     5     5     3     3     5     3     4     5     5     5     5     5 
    ## 23536 23537 23538 23539 23541 23543 23544 23545 23546 23547 23549 23550 23551 
    ##     5     3     5     5     3     5     4     5     3     5     5     5     4 
    ## 23552 23553 23554 23555 23556 23558 23561 23562 23565 23566 23568 23569 23573 
    ##     5     4     4     5     5     5     5     5     4     4     4     5     5 
    ## 23574 23575 23576 23577 23580 23581 23582 23583 23586 23588 23589 23591 23592 
    ##     3     5     5     5     5     5     5     3     4     5     5     5     5 
    ## 23593 23595 23596 23597 23598 23600 23601 23603 23604 23605 23606 23607 23608 
    ##     3     5     3     3     3     4     5     5     5     5     3     5     5 
    ## 23610 23612 23613 23614 23615 23617 23619 23623 23624 23625 23626 23627 23628 
    ##     3     4     3     4     5     3     5     5     5     5     5     3     4 
    ## 23630 23631 23632 23634 23635 23636 23637 23638 23639 23642 23643 23645 23646 
    ##     5     4     5     5     5     5     5     3     5     4     5     5     4 
    ## 23647 23648 23649 23650 23652 23653 23658 23659 23660 23661 23662 23665 23666 
    ##     5     3     4     3     5     4     5     3     5     3     5     3     3 
    ## 23667 23668 23671 23673 23674 23676 23677 23678 23679 23680 23681 23683 23684 
    ##     5     5     3     4     4     5     5     5     5     5     5     5     5 
    ## 23685 23686 23688 23691 23692 23693 23697 23699 23700 23702 23704 23705 23706 
    ##     4     3     5     5     4     4     5     3     4     3     5     5     5 
    ## 23707 23708 23709 23710 23711 23712 23713 23714 23715 23716 23717 23719 23720 
    ##     5     4     3     5     5     5     4     4     5     3     4     5     5 
    ## 23721 23722 23723 23726 23728 23729 23730 23731 23732 23733 23735 23737 23743 
    ##     5     4     5     5     5     5     3     5     5     5     5     5     5 
    ## 23745 23746 23747 23749 23750 23755 23756 23758 23759 23760 23761 23762 23763 
    ##     5     4     5     5     5     5     4     4     3     5     4     5     5 
    ## 23765 23766 23768 23769 23770 23772 23773 23776 23777 23779 23780 23781 23782 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     3 
    ## 23783 23787 23788 23790 23791 23793 23794 23795 23797 23798 23799 23800 23801 
    ##     5     4     5     5     4     3     5     5     3     5     3     3     4 
    ## 23802 23803 23804 23806 23809 23810 23811 23812 23813 23815 23816 23817 23818 
    ##     4     5     3     4     5     5     5     3     3     4     3     5     3 
    ## 23820 23821 23822 23825 23827 23828 23830 23831 23832 23833 23834 23837 23838 
    ##     3     5     5     5     3     4     3     5     5     4     5     5     4 
    ## 23839 23840 23841 23842 23844 23847 23848 23850 23851 23852 23853 23854 23857 
    ##     3     5     5     3     5     5     5     5     3     3     5     5     5 
    ## 23858 23859 23860 23862 23863 23865 23866 23867 23868 23869 23872 23873 23874 
    ##     5     5     4     5     5     4     5     3     3     5     4     5     4 
    ## 23876 23877 23879 23880 23882 23883 23884 23885 23887 23888 23889 23890 23891 
    ##     5     5     5     4     3     5     5     5     5     5     4     4     4 
    ## 23892 23893 23894 23895 23896 23899 23901 23902 23903 23904 23905 23906 23908 
    ##     4     3     5     5     5     4     5     5     5     5     3     5     5 
    ## 23910 23912 23914 23915 23916 23917 23918 23923 23924 23925 23926 23928 23929 
    ##     3     5     4     5     4     4     5     5     3     4     4     4     3 
    ## 23930 23932 23935 23938 23944 23945 23947 23948 23949 23953 23954 23959 23960 
    ##     5     3     3     4     4     5     4     4     5     4     5     4     5 
    ## 23961 23963 23964 23965 23968 23969 23970 23972 23973 23977 23978 23979 23981 
    ##     5     5     5     5     3     3     5     5     3     5     3     5     4 
    ## 23982 23985 23986 23987 23989 23990 23992 23993 23994 23995 23996 23997 23998 
    ##     3     5     4     3     3     5     4     4     4     3     3     3     5 
    ## 24000 24001 24003 24005 24006 24007 24009 24010 24011 24012 24013 24015 24016 
    ##     5     5     5     5     5     4     3     5     5     5     4     3     3 
    ## 24017 24018 24020 24021 24022 24024 24025 24026 24027 24028 24029 24030 24031 
    ##     5     4     3     4     4     5     5     5     3     5     4     4     5 
    ## 24032 24033 24034 24035 24037 24038 24039 24041 24042 24045 24046 24047 24048 
    ##     5     3     5     3     3     3     5     5     5     3     3     5     5 
    ## 24049 24050 24051 24053 24054 24055 24056 24059 24060 24061 24063 24064 24066 
    ##     3     4     3     4     4     3     5     4     5     5     3     5     5 
    ## 24067 24068 24072 24073 24074 24077 24078 24079 24080 24081 24082 24083 24085 
    ##     4     4     4     5     5     4     5     5     3     4     3     5     5 
    ## 24086 24088 24090 24093 24094 24095 24096 24097 24099 24100 24101 24102 24103 
    ##     5     5     3     3     5     4     4     5     4     5     3     5     5 
    ## 24104 24105 24106 24107 24108 24109 24110 24112 24113 24114 24115 24117 24118 
    ##     5     3     5     5     4     4     4     5     5     5     5     4     5 
    ## 24119 24123 24125 24127 24130 24131 24132 24135 24136 24137 24138 24141 24143 
    ##     4     5     5     3     5     5     5     5     4     5     5     5     5 
    ## 24146 24147 24148 24149 24151 24152 24153 24154 24156 24157 24158 24159 24160 
    ##     5     4     5     4     5     5     4     3     5     5     4     5     5 
    ## 24161 24162 24164 24165 24166 24167 24168 24169 24170 24172 24175 24177 24179 
    ##     4     5     5     5     5     5     5     3     4     4     5     5     4 
    ## 24180 24181 24182 24183 24184 24187 24188 24189 24190 24192 24194 24195 24199 
    ##     5     3     5     5     3     5     5     3     5     5     5     5     5 
    ## 24201 24202 24208 24209 24210 24211 24212 24213 24215 24216 24217 24218 24220 
    ##     5     5     3     5     4     4     5     4     5     5     3     3     5 
    ## 24221 24223 24224 24225 24227 24231 24232 24233 24236 24237 24238 24240 24241 
    ##     4     5     5     5     5     5     5     5     3     3     5     5     5 
    ## 24245 24247 24249 24250 24252 24253 24254 24255 24256 24257 24259 24261 24262 
    ##     5     3     4     5     5     5     5     3     5     3     5     5     5 
    ## 24263 24264 24269 24270 24271 24272 24273 24278 24279 24281 24282 24283 24284 
    ##     5     3     5     3     5     5     3     3     5     3     3     5     3 
    ## 24286 24289 24290 24291 24292 24293 24294 24296 24297 24299 24300 24301 24304 
    ##     5     5     5     3     5     3     5     5     4     5     3     3     5 
    ## 24305 24306 24308 24309 24312 24313 24314 24316 24319 24320 24321 24322 24323 
    ##     4     3     5     5     4     3     5     3     5     5     5     5     4 
    ## 24324 24326 24327 24328 24331 24333 24334 24335 24336 24339 24340 24341 24342 
    ##     3     3     3     5     3     4     5     5     5     4     5     5     4 
    ## 24343 24344 24345 24346 24347 24348 24349 24352 24353 24354 24355 24356 24357 
    ##     4     5     5     5     4     3     5     5     5     5     5     4     3 
    ## 24359 24360 24361 24363 24364 24365 24369 24370 24371 24372 24373 24374 24375 
    ##     5     5     5     5     5     5     5     5     4     3     5     3     5 
    ## 24377 24380 24381 24382 24383 24385 24386 24387 24390 24391 24393 24394 24395 
    ##     5     4     4     3     4     5     5     5     5     3     5     4     3 
    ## 24396 24397 24400 24401 24402 24403 24405 24406 24407 24408 24409 24413 24415 
    ##     5     4     4     5     4     5     5     5     4     4     4     5     5 
    ## 24416 24417 24418 24420 24421 24423 24424 24425 24426 24428 24431 24433 24434 
    ##     5     5     4     5     5     5     5     4     4     5     4     4     5 
    ## 24435 24436 24438 24440 24442 24446 24448 24449 24450 24451 24455 24456 24459 
    ##     3     3     5     5     5     5     5     5     5     3     4     4     3 
    ## 24460 24461 24463 24464 24465 24467 24468 24470 24471 24472 24473 24474 24475 
    ##     5     3     5     5     5     5     3     5     5     5     3     3     5 
    ## 24476 24477 24478 24479 24480 24481 24482 24483 24484 24485 24486 24487 24488 
    ##     5     4     5     3     3     5     4     4     5     5     5     5     3 
    ## 24489 24490 24492 24493 24494 24495 24496 24497 24499 24501 24504 24506 24507 
    ##     5     4     5     5     5     4     5     5     3     5     3     5     3 
    ## 24508 24510 24511 24513 24516 24517 24518 24519 24522 24523 24524 24526 24527 
    ##     5     5     5     3     5     3     5     3     5     5     5     3     5 
    ## 24528 24529 24530 24531 24532 24533 24534 24535 24536 24537 24539 24540 24541 
    ##     4     5     5     5     4     5     5     4     3     3     5     5     4 
    ## 24542 24543 24545 24547 24548 24549 24550 24551 24552 24553 24555 24557 24559 
    ##     4     4     5     5     3     5     5     5     3     5     5     5     3 
    ## 24560 24562 24563 24564 24566 24567 24568 24569 24572 24573 24575 24576 24577 
    ##     3     5     4     3     5     5     5     5     3     5     4     5     5 
    ## 24578 24579 24580 24582 24583 24584 24585 24587 24588 24589 24590 24591 24593 
    ##     5     4     5     3     3     5     3     5     5     5     5     5     5 
    ## 24594 24596 24597 24598 24599 24601 24602 24603 24604 24606 24607 24608 24609 
    ##     3     5     5     5     4     3     4     5     5     5     5     4     5 
    ## 24611 24613 24614 24616 24617 24618 24619 24620 24621 24622 24623 24624 24625 
    ##     5     3     5     4     5     3     4     4     5     3     5     5     3 
    ## 24626 24627 24628 24629 24630 24633 24635 24638 24641 24642 24644 24645 24646 
    ##     4     5     4     5     5     3     5     4     5     5     5     5     5 
    ## 24647 24649 24650 24652 24653 24654 24655 24657 24659 24661 24662 24666 24667 
    ##     4     5     3     5     4     3     3     5     4     3     5     4     5 
    ## 24668 24670 24671 24672 24673 24676 24678 24680 24681 24682 24683 24684 24685 
    ##     5     3     5     3     4     3     4     5     5     5     4     5     4 
    ## 24686 24689 24691 24692 24693 24694 24695 24697 24698 24700 24701 24702 24703 
    ##     3     5     5     4     5     5     5     5     5     5     5     5     5 
    ## 24704 24705 24706 24708 24709 24710 24711 24712 24713 24714 24715 24716 24719 
    ##     5     5     5     4     5     5     3     5     5     5     5     5     5 
    ## 24722 24724 24726 24727 24729 24730 24731 24732 24733 24734 24736 24737 24738 
    ##     5     5     3     4     3     3     5     5     5     3     5     3     4 
    ## 24740 24742 24743 24744 24745 24747 24749 24750 24751 24753 24754 24757 24758 
    ##     5     5     3     5     4     4     3     3     5     5     5     3     5 
    ## 24759 24760 24761 24763 24765 24766 24767 24768 24769 24770 24771 24772 24773 
    ##     3     4     5     5     4     3     5     4     4     5     5     5     4 
    ## 24774 24775 24776 24777 24778 24780 24781 24782 24783 24784 24785 24786 24787 
    ##     4     5     5     4     3     5     5     5     3     3     5     4     4 
    ## 24789 24793 24794 24798 24799 24800 24802 24803 24804 24805 24808 24809 24811 
    ##     4     5     4     5     5     5     5     5     5     3     4     4     5 
    ## 24812 24813 24814 24815 24816 24817 24818 24820 24821 24822 24823 24824 24825 
    ##     4     4     4     5     3     5     4     4     5     3     4     4     5 
    ## 24827 24828 24829 24830 24833 24834 24835 24836 24837 24839 24840 24841 24843 
    ##     5     5     3     5     4     5     3     5     3     5     3     3     4 
    ## 24844 24845 24847 24849 24852 24855 24857 24858 24859 24860 24864 24865 24866 
    ##     5     4     5     5     3     5     3     5     5     5     5     3     3 
    ## 24868 24869 24871 24872 24874 24875 24876 24877 24878 24879 24881 24882 24883 
    ##     3     5     5     4     5     3     5     5     5     3     5     5     3 
    ## 24884 24885 24886 24887 24888 24889 24890 24891 24892 24893 24901 24902 24904 
    ##     3     4     4     5     5     5     3     5     5     5     4     5     4 
    ## 24906 24907 24909 24910 24911 24912 24913 24914 24915 24916 24919 24920 24922 
    ##     5     3     3     5     4     5     5     5     5     3     5     3     5 
    ## 24925 24926 24928 24929 24930 24931 24934 24935 24938 24940 24941 24944 24946 
    ##     3     5     4     5     5     4     4     5     5     3     4     5     5 
    ## 24948 24949 24950 24951 24952 24953 24955 24956 24958 24960 24961 24963 24965 
    ##     4     3     5     4     3     5     4     5     4     5     5     5     3 
    ## 24966 24967 24968 24970 24973 24974 24975 24977 24979 24981 24982 24983 24984 
    ##     3     4     5     5     5     5     5     5     5     5     5     5     5 
    ## 24985 24986 24988 24990 24991 24992 24993 24994 24995 24996 24997 24999 25000 
    ##     3     3     4     4     4     3     5     5     4     5     5     5     4 
    ## 25001 25002 25004 25006 25007 25008 25009 25010 25012 25013 25014 25015 25016 
    ##     5     4     5     3     4     5     3     5     5     5     5     5     5 
    ## 25017 25018 25020 25021 25022 25023 25024 25025 25026 25028 25029 25030 25031 
    ##     5     5     4     3     5     3     5     5     5     5     5     5     5 
    ## 25032 25033 25035 25038 25040 25041 25042 25043 25045 25046 25048 25049 25052 
    ##     5     5     3     5     5     4     5     5     5     5     3     3     5 
    ## 25053 25054 25056 25057 25058 25059 25060 25061 25062 25063 25064 25066 25067 
    ##     5     4     4     5     5     3     5     4     4     5     3     5     5 
    ## 25069 25070 25071 25072 25073 25075 25076 25077 25078 25081 25083 25084 25085 
    ##     5     5     3     5     5     3     5     4     4     5     4     3     3 
    ## 25086 25087 25088 25090 25091 25092 25094 25095 25096 25097 25098 25099 25100 
    ##     5     5     4     3     5     5     5     5     5     5     5     4     4 
    ## 25103 25104 25105 25106 25107 25108 25109 25110 25111 25112 25114 25115 25116 
    ##     5     4     5     3     4     5     5     5     5     4     4     5     5 
    ## 25117 25118 25119 25120 25121 25122 25123 25124 25125 25126 25127 25128 25130 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 25131 25132 25133 25135 25136 25137 25138 25139 25140 25141 25144 25146 25147 
    ##     5     4     5     5     5     3     4     5     4     5     3     3     5 
    ## 25149 25150 25151 25152 25153 25155 25159 25161 25162 25163 25165 25166 25167 
    ##     5     5     5     5     5     3     5     5     4     5     5     5     4 
    ## 25168 25169 25170 25171 25172 25173 25174 25175 25176 25177 25179 25180 25181 
    ##     5     5     5     5     3     4     5     4     5     4     3     5     5 
    ## 25182 25184 25186 25188 25190 25191 25192 25194 25196 25198 25199 25200 25201 
    ##     4     3     5     5     5     5     5     4     5     5     4     5     5 
    ## 25202 25203 25204 25205 25206 25207 25208 25210 25211 25212 25213 25215 25221 
    ##     5     5     3     4     5     5     3     5     5     5     4     4     5 
    ## 25222 25224 25225 25226 25229 25231 25232 25233 25234 25235 25236 25238 25240 
    ##     3     3     3     5     5     5     5     5     3     5     4     5     4 
    ## 25241 25242 25243 25245 25246 25247 25249 25251 25253 25254 25255 25258 25259 
    ##     5     5     4     5     3     5     4     3     4     5     5     5     4 
    ## 25260 25261 25263 25264 25266 25267 25272 25274 25276 25277 25280 25282 25283 
    ##     5     5     4     5     3     4     5     4     5     5     5     5     5 
    ## 25284 25287 25288 25289 25291 25292 25293 25295 25296 25297 25298 25299 25300 
    ##     5     4     5     4     3     5     5     5     5     5     5     3     4 
    ## 25302 25303 25304 25305 25307 25308 25309 25311 25312 25313 25314 25316 25317 
    ##     4     5     5     3     3     5     5     5     3     5     5     4     3 
    ## 25318 25319 25320 25321 25322 25323 25325 25326 25327 25328 25329 25331 25332 
    ##     3     5     5     5     3     4     3     5     4     4     4     3     4 
    ## 25334 25335 25336 25338 25339 25340 25341 25342 25343 25344 25345 25346 25347 
    ##     5     5     4     5     4     4     3     5     5     3     5     5     4 
    ## 25348 25349 25351 25352 25354 25355 25356 25358 25361 25363 25364 25365 25366 
    ##     4     4     5     5     5     3     3     5     5     4     5     3     5 
    ## 25367 25369 25370 25371 25372 25373 25374 25375 25377 25378 25380 25381 25383 
    ##     4     5     5     5     5     5     5     5     5     5     5     3     5 
    ## 25384 25386 25388 25389 25390 25391 25392 25395 25396 25397 25398 25399 25400 
    ##     3     4     5     5     5     5     5     5     5     3     5     3     4 
    ## 25401 25402 25404 25408 25409 25412 25414 25415 25416 25418 25419 25420 25421 
    ##     5     5     4     4     5     3     5     5     4     5     5     3     4 
    ## 25424 25425 25426 25428 25430 25431 25432 25433 25434 25435 25437 25438 25439 
    ##     5     3     3     4     5     4     4     5     3     4     5     5     5 
    ## 25440 25442 25443 25445 25446 25448 25449 25451 25452 25453 25455 25456 25457 
    ##     5     5     4     5     5     5     4     4     5     4     3     3     4 
    ## 25459 25460 25461 25462 25463 25466 25467 25468 25469 25471 25474 25476 25477 
    ##     5     5     5     3     5     5     5     4     4     5     5     3     5 
    ## 25480 25481 25483 25486 25487 25488 25489 25490 25491 25492 25494 25496 25500 
    ##     4     5     4     3     4     5     3     5     4     4     5     4     4 
    ## 25504 25505 25506 25508 25509 25510 25514 25515 25516 25519 25520 25521 25522 
    ##     5     4     3     4     3     3     5     3     5     5     4     3     5 
    ## 25523 25524 25525 25526 25528 25529 25530 25531 25532 25533 25534 25535 25536 
    ##     5     3     4     3     5     5     5     5     5     3     5     4     5 
    ## 25537 25538 25539 25540 25541 25542 25543 25544 25545 25547 25548 25549 25551 
    ##     5     5     3     4     3     5     4     4     5     5     5     5     5 
    ## 25554 25555 25556 25557 25558 25559 25560 25561 25562 25563 25564 25565 25566 
    ##     5     5     4     5     5     5     5     5     5     5     5     5     4 
    ## 25568 25569 25570 25571 25572 25575 25576 25579 25581 25582 25583 25584 25586 
    ##     5     4     3     5     3     4     4     5     5     5     5     3     5 
    ## 25587 25589 25590 25591 25592 25593 25595 25597 25598 25600 25602 25603 25605 
    ##     5     3     5     3     3     5     3     5     3     3     5     5     3 
    ## 25607 25608 25610 25612 25613 25614 25615 25616 25617 25618 25619 25620 25622 
    ##     5     5     5     4     5     5     3     3     5     3     4     5     5 
    ## 25623 25624 25626 25627 25629 25630 25631 25632 25633 25634 25636 25637 25638 
    ##     3     4     4     3     5     5     5     5     5     4     5     3     5 
    ## 25640 25641 25642 25643 25644 25645 25647 25648 25650 25651 25652 25654 25656 
    ##     4     5     5     5     4     5     3     5     5     4     5     5     5 
    ## 25658 25659 25660 25661 25662 25663 25665 25669 25670 25671 25673 25675 25676 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 25677 25678 25679 25680 25681 25682 25683 25684 25685 25686 25687 25688 25692 
    ##     5     5     4     3     5     5     5     3     4     5     5     4     5 
    ## 25694 25696 25697 25699 25700 25701 25703 25704 25705 25708 25710 25711 25712 
    ##     3     3     3     4     5     5     5     5     5     4     3     5     3 
    ## 25713 25714 25717 25719 25720 25721 25722 25723 25724 25725 25726 25727 25729 
    ##     5     3     5     4     4     3     4     3     5     5     4     3     3 
    ## 25730 25731 25732 25733 25735 25736 25737 25741 25743 25744 25746 25747 25748 
    ##     5     3     5     3     3     5     4     5     4     3     4     5     5 
    ## 25749 25751 25752 25753 25758 25759 25760 25761 25762 25763 25764 25766 25767 
    ##     5     3     5     3     3     5     5     3     5     5     3     5     3 
    ## 25768 25769 25771 25772 25773 25775 25776 25777 25778 25780 25784 25785 25786 
    ##     5     3     5     5     4     3     3     5     4     4     3     5     5 
    ## 25788 25789 25790 25791 25792 25793 25794 25795 25796 25797 25799 25800 25801 
    ##     3     5     4     3     3     5     5     3     4     5     3     3     4 
    ## 25804 25807 25808 25809 25810 25812 25813 25814 25815 25816 25817 25818 25819 
    ##     3     3     5     4     3     5     4     3     3     4     5     5     3 
    ## 25821 25822 25827 25828 25829 25830 25834 25836 25837 25838 25839 25840 25841 
    ##     5     4     3     5     5     5     5     3     5     5     5     4     5 
    ## 25843 25844 25845 25847 25848 25849 25850 25851 25852 25853 25854 25855 25857 
    ##     5     3     4     3     4     5     3     3     5     5     5     5     5 
    ## 25858 25859 25860 25861 25862 25863 25865 25866 25867 25868 25869 25870 25871 
    ##     3     5     5     5     5     3     5     3     5     5     4     5     3 
    ## 25872 25874 25875 25876 25877 25878 25879 25880 25881 25883 25885 25886 25887 
    ##     5     5     5     3     5     5     3     5     5     4     3     5     4 
    ## 25888 25889 25890 25893 25895 25898 25899 25900 25901 25904 25908 25911 25912 
    ##     5     5     5     5     5     5     4     5     5     3     5     5     5 
    ## 25913 25914 25916 25917 25918 25919 25922 25924 25925 25926 25930 25931 25932 
    ##     5     4     5     5     4     5     5     3     5     4     4     5     3 
    ## 25933 25935 25938 25939 25940 25941 25942 25943 25944 25946 25947 25948 25949 
    ##     4     3     5     4     4     3     3     3     4     5     4     5     4 
    ## 25951 25953 25955 25956 25957 25958 25959 25960 25961 25962 25964 25965 25966 
    ##     4     5     5     3     3     5     5     5     3     5     5     5     5 
    ## 25967 25968 25969 25972 25976 25977 25978 25979 25981 25982 25983 25985 25986 
    ##     5     5     5     3     5     4     4     5     4     3     5     3     4 
    ## 25992 25993 25994 25995 25996 25997 25999 26000 26003 26004 26007 26008 26009 
    ##     4     5     5     5     3     5     4     5     5     3     5     5     3 
    ## 26010 26011 26012 26014 26015 26016 26017 26019 26021 26023 26025 26026 26027 
    ##     4     3     4     3     3     3     5     3     4     5     4     4     5 
    ## 26028 26029 26030 26031 26033 26034 26035 26036 26037 26038 26039 26040 26041 
    ##     5     5     5     5     5     5     4     5     4     3     5     5     5 
    ## 26043 26045 26047 26048 26050 26052 26053 26055 26056 26057 26058 26059 26060 
    ##     3     4     3     5     5     5     5     5     5     4     4     5     4 
    ## 26063 26065 26067 26069 26071 26072 26074 26075 26076 26077 26078 26081 26082 
    ##     5     5     5     5     5     3     5     3     4     5     4     5     5 
    ## 26085 26087 26088 26089 26090 26091 26092 26093 26094 26095 26097 26098 26100 
    ##     3     5     5     5     3     5     4     5     5     5     3     5     5 
    ## 26101 26102 26103 26104 26105 26106 26107 26108 26109 26110 26111 26112 26114 
    ##     5     5     5     5     3     3     4     4     5     5     5     5     5 
    ## 26117 26118 26119 26120 26121 26122 26123 26124 26126 26127 26129 26132 26133 
    ##     5     5     5     3     5     4     5     5     5     5     4     5     3 
    ## 26134 26135 26136 26137 26138 26139 26142 26143 26145 26146 26147 26148 26149 
    ##     3     4     3     4     5     5     5     5     5     5     5     4     4 
    ## 26150 26151 26153 26154 26155 26156 26157 26158 26159 26160 26161 26162 26164 
    ##     4     5     5     5     5     5     5     4     5     5     3     5     5 
    ## 26165 26166 26167 26170 26171 26172 26173 26174 26175 26178 26180 26181 26182 
    ##     5     5     4     5     5     5     5     5     4     4     5     5     5 
    ## 26184 26185 26186 26188 26189 26190 26191 26193 26194 26195 26196 26197 26198 
    ##     5     5     3     5     4     5     5     5     5     4     5     4     5 
    ## 26199 26201 26203 26207 26208 26211 26212 26213 26214 26216 26217 26218 26219 
    ##     5     5     5     4     5     4     5     5     4     5     5     5     5 
    ## 26222 26223 26225 26226 26227 26228 26231 26232 26233 26235 26236 26237 26240 
    ##     3     4     5     5     4     5     5     3     4     4     3     3     5 
    ## 26241 26242 26243 26244 26245 26247 26248 26249 26250 26252 26253 26255 26256 
    ##     5     4     5     5     5     5     5     5     5     3     5     5     3 
    ## 26257 26258 26259 26261 26263 26264 26265 26266 26268 26270 26271 26273 26275 
    ##     5     4     5     5     5     4     5     5     5     5     5     5     5 
    ## 26278 26281 26282 26283 26288 26289 26290 26292 26294 26295 26296 26297 26298 
    ##     3     3     4     5     5     5     4     5     5     3     5     3     5 
    ## 26300 26301 26303 26304 26305 26306 26307 26310 26311 26312 26313 26314 26315 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 26316 26318 26319 26320 26324 26326 26327 26328 26329 26330 26331 26332 26335 
    ##     5     4     5     5     4     3     4     4     5     4     5     3     5 
    ## 26336 26337 26339 26340 26341 26345 26346 26347 26348 26349 26350 26351 26355 
    ##     5     5     5     5     5     5     5     5     5     3     3     5     5 
    ## 26356 26357 26358 26359 26360 26362 26363 26365 26368 26369 26370 26371 26373 
    ##     5     5     4     5     3     5     4     4     3     5     4     5     4 
    ## 26375 26376 26379 26380 26382 26383 26384 26385 26386 26387 26388 26390 26392 
    ##     4     5     5     3     5     4     5     5     5     4     3     5     4 
    ## 26393 26394 26397 26398 26399 26400 26401 26402 26404 26405 26407 26408 26409 
    ##     5     3     5     5     5     4     4     5     5     5     4     5     5 
    ## 26411 26412 26413 26416 26417 26418 26420 26421 26422 26425 26426 26427 26428 
    ##     4     3     5     3     4     5     3     3     4     4     4     5     3 
    ## 26432 26433 26434 26435 26436 26439 26440 26442 26443 26444 26445 26446 26448 
    ##     5     4     4     5     5     5     5     5     5     3     3     5     4 
    ## 26449 26451 26454 26455 26457 26458 26459 26460 26461 26462 26468 26469 26471 
    ##     3     5     3     5     5     5     3     3     5     3     5     5     3 
    ## 26472 26475 26476 26479 26480 26481 26482 26483 26484 26485 26488 26490 26491 
    ##     5     5     4     4     5     3     5     5     5     5     5     5     5 
    ## 26492 26494 26495 26496 26497 26498 26499 26501 26504 26506 26507 26508 26510 
    ##     5     5     5     3     3     5     4     5     5     4     4     5     4 
    ## 26511 26512 26513 26515 26516 26517 26518 26519 26520 26522 26526 26527 26529 
    ##     5     5     5     5     4     5     4     5     5     5     5     3     4 
    ## 26530 26533 26534 26535 26536 26538 26539 26541 26543 26544 26545 26546 26548 
    ##     3     4     5     3     4     3     5     5     5     5     5     5     3 
    ## 26549 26550 26551 26552 26553 26554 26555 26556 26557 26558 26560 26562 26563 
    ##     3     4     3     3     3     4     3     5     5     5     4     5     5 
    ## 26564 26565 26567 26568 26569 26570 26572 26573 26574 26575 26576 26577 26578 
    ##     4     3     3     3     3     4     5     3     5     3     5     5     5 
    ## 26579 26581 26582 26583 26584 26585 26587 26589 26590 26591 26592 26593 26595 
    ##     4     5     4     4     3     5     4     5     4     5     3     5     5 
    ## 26596 26597 26598 26599 26600 26605 26606 26608 26609 26610 26611 26612 26613 
    ##     5     5     4     3     5     4     5     5     4     3     5     3     3 
    ## 26614 26615 26616 26617 26619 26620 26622 26625 26628 26629 26630 26631 26632 
    ##     5     3     5     3     4     5     3     5     4     5     5     5     4 
    ## 26633 26634 26635 26636 26637 26638 26639 26640 26641 26642 26643 26645 26647 
    ##     5     5     5     5     5     5     4     4     3     5     5     5     5 
    ## 26648 26650 26651 26652 26653 26655 26657 26659 26661 26662 26663 26664 26665 
    ##     4     4     4     5     5     5     4     5     4     4     3     5     5 
    ## 26666 26667 26668 26670 26672 26673 26674 26676 26677 26678 26679 26683 26684 
    ##     5     5     5     5     5     5     5     5     4     5     5     4     5 
    ## 26685 26686 26687 26688 26689 26690 26695 26698 26699 26700 26703 26705 26706 
    ##     3     5     3     5     4     5     5     5     5     3     5     4     5 
    ## 26707 26709 26710 26711 26712 26713 26714 26716 26717 26719 26720 26722 26724 
    ##     3     5     5     3     5     5     3     4     3     4     5     4     4 
    ## 26725 26726 26727 26728 26729 26730 26731 26733 26734 26735 26736 26737 26738 
    ##     5     5     4     5     5     5     5     3     5     3     4     5     5 
    ## 26739 26740 26742 26743 26744 26745 26746 26747 26748 26750 26752 26753 26754 
    ##     5     3     5     5     5     5     4     5     5     5     3     5     5 
    ## 26755 26757 26759 26760 26761 26762 26763 26764 26765 26766 26767 26768 26769 
    ##     3     3     5     3     4     5     3     3     5     5     3     5     3 
    ## 26770 26771 26772 26773 26774 26776 26778 26779 26780 26781 26783 26784 26785 
    ##     5     5     5     4     5     5     4     4     4     3     3     3     3 
    ## 26786 26789 26790 26791 26792 26794 26795 26797 26800 26802 26804 26806 26809 
    ##     5     5     5     5     5     4     3     5     3     5     4     4     5 
    ## 26810 26811 26812 26813 26814 26816 26818 26820 26822 26823 26824 26826 26827 
    ##     4     4     4     5     5     5     5     5     5     3     3     4     5 
    ## 26829 26830 26832 26834 26836 26837 26838 26839 26840 26841 26843 26844 26845 
    ##     5     5     5     5     5     4     5     5     3     5     4     5     3 
    ## 26846 26847 26849 26850 26853 26854 26855 26856 26857 26858 26859 26860 26862 
    ##     3     5     5     4     4     5     4     5     5     5     5     5     5 
    ## 26864 26865 26866 26867 26868 26870 26871 26872 26873 26874 26875 26876 26878 
    ##     3     4     5     5     5     4     4     5     5     5     5     5     3 
    ## 26879 26880 26882 26884 26885 26886 26887 26889 26890 26891 26892 26894 26896 
    ##     5     4     4     4     4     5     3     5     5     4     5     5     5 
    ## 26898 26899 26901 26905 26907 26908 26909 26910 26913 26914 26915 26916 26917 
    ##     3     4     4     5     5     5     5     5     5     5     4     5     3 
    ## 26918 26919 26920 26921 26922 26923 26924 26925 26926 26930 26932 26933 26934 
    ##     3     5     5     4     5     4     3     3     3     3     5     5     5 
    ## 26936 26937 26938 26939 26940 26942 26943 26944 26948 26949 26950 26952 26953 
    ##     5     3     4     3     5     3     4     5     5     3     5     3     5 
    ## 26955 26956 26957 26959 26960 26966 26967 26968 26969 26970 26972 26973 26974 
    ##     5     5     5     4     5     4     5     5     5     5     5     3     4 
    ## 26976 26978 26979 26980 26983 26984 26985 26986 26988 26989 26990 26992 26993 
    ##     5     3     5     5     5     5     5     5     4     5     5     4     3 
    ## 26994 26995 26996 26997 26999 27000 27001 27002 27005 27007 27008 27009 27011 
    ##     5     3     5     5     5     5     5     5     5     5     5     5     4 
    ## 27012 27013 27014 27015 27016 27017 27019 27021 27023 27024 27025 27026 27028 
    ##     4     5     4     4     3     5     5     4     5     4     5     5     4 
    ## 27031 27032 27033 27035 27039 27040 27041 27043 27045 27046 27047 27048 27049 
    ##     5     5     5     5     4     3     3     3     5     5     5     4     5 
    ## 27051 27052 27053 27054 27055 27056 27057 27059 27060 27061 27063 27064 27066 
    ##     4     5     3     5     5     5     5     5     3     5     4     3     5 
    ## 27068 27069 27071 27072 27073 27074 27076 27077 27078 27079 27082 27083 27085 
    ##     4     4     5     5     5     5     4     5     5     3     5     5     4 
    ## 27086 27087 27088 27090 27091 27095 27097 27098 27099 27100 27101 27106 27107 
    ##     4     5     5     4     5     5     5     3     5     5     5     5     5 
    ## 27108 27109 27110 27111 27113 27114 27115 27116 27117 27118 27120 27122 27123 
    ##     5     4     5     5     3     5     4     5     5     5     5     3     4 
    ## 27126 27128 27129 27131 27132 27133 27134 27135 27136 27137 27138 27140 27141 
    ##     4     5     5     5     5     5     5     3     5     5     4     3     5 
    ## 27142 27144 27145 27146 27148 27149 27150 27152 27153 27154 27156 27157 27159 
    ##     5     4     4     3     5     5     4     5     3     3     5     3     5 
    ## 27160 27161 27163 27164 27165 27166 27167 27168 27169 27171 27172 27175 27176 
    ##     3     5     3     3     5     5     4     5     5     3     5     3     3 
    ## 27177 27178 27179 27180 27181 27182 27183 27185 27186 27187 27188 27189 27190 
    ##     4     5     5     5     5     5     4     5     4     3     3     5     3 
    ## 27192 27193 27194 27196 27197 27198 27200 27201 27202 27203 27205 27206 27207 
    ##     3     3     5     4     5     4     5     3     4     5     5     5     5 
    ## 27210 27211 27212 27213 27216 27217 27220 27221 27223 27224 27225 27226 27227 
    ##     5     5     5     5     5     5     5     5     5     4     5     3     5 
    ## 27228 27229 27230 27232 27233 27235 27236 27239 27242 27243 27244 27245 27246 
    ##     3     3     3     5     4     5     4     4     5     4     4     5     5 
    ## 27248 27250 27251 27252 27254 27259 27260 27261 27262 27263 27265 27266 27267 
    ##     5     3     4     5     4     4     5     5     3     3     4     5     4 
    ## 27268 27269 27270 27271 27272 27274 27275 27277 27278 27279 27280 27281 27282 
    ##     5     3     4     4     5     4     4     5     5     5     4     5     3 
    ## 27284 27286 27287 27288 27290 27291 27292 27293 27294 27295 27296 27297 27298 
    ##     5     3     5     5     3     3     5     5     4     5     5     5     5 
    ## 27301 27306 27307 27308 27310 27311 27312 27313 27314 27316 27318 27319 27320 
    ##     5     3     5     5     3     5     5     5     5     4     3     5     5 
    ## 27321 27323 27326 27328 27330 27331 27332 27333 27334 27335 27337 27338 27339 
    ##     5     3     4     5     3     5     5     5     5     5     3     4     3 
    ## 27340 27341 27342 27343 27346 27347 27348 27349 27350 27353 27354 27355 27359 
    ##     5     5     3     5     5     5     4     5     4     3     3     5     5 
    ## 27360 27362 27364 27366 27367 27368 27369 27370 27371 27372 27374 27376 27377 
    ##     3     3     4     5     5     5     5     5     5     3     4     5     5 
    ## 27378 27379 27380 27381 27384 27386 27388 27390 27391 27392 27393 27395 27396 
    ##     4     5     3     3     5     5     4     4     3     3     3     4     3 
    ## 27397 27398 27399 27401 27402 27404 27405 27406 27407 27408 27409 27410 27413 
    ##     5     4     5     5     3     5     5     5     4     4     5     3     5 
    ## 27414 27415 27416 27418 27419 27420 27421 27422 27423 27424 27425 27426 27428 
    ##     4     3     4     4     5     4     4     5     5     5     5     3     5 
    ## 27429 27430 27431 27433 27434 27436 27437 27438 27439 27440 27441 27442 27444 
    ##     5     3     5     5     5     4     3     3     3     4     5     4     5 
    ## 27445 27446 27447 27448 27450 27451 27453 27454 27455 27457 27458 27459 27461 
    ##     5     5     5     5     5     5     3     5     5     3     5     5     4 
    ## 27462 27463 27465 27466 27467 27468 27469 27470 27473 27474 27475 27476 27478 
    ##     3     3     5     5     5     5     5     5     3     3     4     5     5 
    ## 27479 27481 27482 27485 27486 27488 27489 27490 27491 27496 27497 27498 27499 
    ##     5     3     5     3     5     5     5     5     5     5     5     5     5 
    ## 27500 27501 27502 27503 27504 27505 27506 27508 27510 27515 27516 27517 27519 
    ##     5     3     3     5     4     5     5     4     3     5     5     5     4 
    ## 27520 27522 27523 27525 27527 27530 27532 27533 27535 27536 27537 27538 
    ##     3     5     3     4     5     3     3     3     4     5     4     5 
    ## 
    ## $call
    ## rpart(formula = Depresion_cat ~ ., data = df_entrenamiento_depresion, 
    ##     method = "class", control = rpart.control(maxdepth = 10))
    ## 
    ## $terms
    ## Depresion_cat ~ Extraversion + Agreeableness + Conscientiousness + 
    ##     EmotionalStability + Openness
    ## attr(,"variables")
    ## list(Depresion_cat, Extraversion, Agreeableness, Conscientiousness, 
    ##     EmotionalStability, Openness)
    ## attr(,"factors")
    ##                    Extraversion Agreeableness Conscientiousness
    ## Depresion_cat                 0             0                 0
    ## Extraversion                  1             0                 0
    ## Agreeableness                 0             1                 0
    ## Conscientiousness             0             0                 1
    ## EmotionalStability            0             0                 0
    ## Openness                      0             0                 0
    ##                    EmotionalStability Openness
    ## Depresion_cat                       0        0
    ## Extraversion                        0        0
    ## Agreeableness                       0        0
    ## Conscientiousness                   0        0
    ## EmotionalStability                  1        0
    ## Openness                            0        1
    ## attr(,"term.labels")
    ## [1] "Extraversion"       "Agreeableness"      "Conscientiousness" 
    ## [4] "EmotionalStability" "Openness"          
    ## attr(,"order")
    ## [1] 1 1 1 1 1
    ## attr(,"intercept")
    ## [1] 1
    ## attr(,"response")
    ## [1] 1
    ## attr(,".Environment")
    ## <environment: R_GlobalEnv>
    ## attr(,"predvars")
    ## list(Depresion_cat, Extraversion, Agreeableness, Conscientiousness, 
    ##     EmotionalStability, Openness)
    ## attr(,"dataClasses")
    ##      Depresion_cat       Extraversion      Agreeableness  Conscientiousness 
    ##        "character"          "numeric"          "numeric"          "numeric" 
    ## EmotionalStability           Openness 
    ##          "numeric"          "numeric" 
    ## 
    ## $cptable
    ##           CP nsplit rel error    xerror        xstd
    ## 1 0.08023914      0 1.0000000 1.0000000 0.007281152
    ## 2 0.05821271      1 0.9197609 0.9167191 0.007249844
    ## 3 0.01000000      2 0.8615481 0.8633312 0.007203480
    ## 
    ## $method
    ## [1] "class"

    #Imprimimos el arbol
    rpart.plot(modelo_cart_depresion_md)


    #Fit 2
    modelo_cart_depresion_mb <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = "class", control = rpart.control(minbucket = 2))

    #Resumen del modelo
    print(paste("Resumen del modelo para", 'Depresion'))

    ## [1] "Resumen del modelo para Depresion"

    print(head(summary(modelo_cart_depresion_mb)))

    ## Call:
    ## rpart(formula = Depresion_cat ~ ., data = df_entrenamiento_depresion, 
    ##     method = "class", control = rpart.control(minbucket = 2))
    ##   n= 19278 
    ## 
    ##           CP nsplit rel error    xerror        xstd
    ## 1 0.08023914      0 1.0000000 1.0000000 0.007281152
    ## 2 0.05821271      1 0.9197609 0.9167191 0.007249844
    ## 3 0.01000000      2 0.8615481 0.8654290 0.007205694
    ## 
    ## Variable importance
    ## EmotionalStability  Conscientiousness      Agreeableness           Openness 
    ##                 79                  8                  5                  5 
    ##       Extraversion 
    ##                  3 
    ## 
    ## Node number 1: 19278 observations,    complexity param=0.08023914
    ##   predicted class=Severo  expected loss=0.4945534  P(node) =1
    ##     class counts:  1811  3456  4267  9744
    ##    probabilities: 0.094 0.179 0.221 0.505 
    ##   left son=2 (8566 obs) right son=3 (10712 obs)
    ##   Primary splits:
    ##       EmotionalStability < 3.25 to the right, improve=1101.90400, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve= 343.14590, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve= 292.54760, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 228.50220, (0 missing)
    ##       Agreeableness      < 4.25 to the right, improve=  64.67917, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.25 to the right, agree=0.600, adj=0.101, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.582, adj=0.060, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.577, adj=0.048, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.568, adj=0.027, (0 split)
    ## 
    ## Node number 2: 8566 observations,    complexity param=0.05821271
    ##   predicted class=Normal  expected loss=0.6096194  P(node) =0.4443407
    ##     class counts:  1093  1550  3344  2579
    ##    probabilities: 0.128 0.181 0.390 0.301 
    ##   left son=4 (4353 obs) right son=5 (4213 obs)
    ##   Primary splits:
    ##       EmotionalStability < 4.25 to the right, improve=221.81220, (0 missing)
    ##       Extraversion       < 3.25 to the right, improve=148.19480, (0 missing)
    ##       Conscientiousness  < 4.25 to the right, improve=127.33130, (0 missing)
    ##       Openness           < 4.25 to the right, improve= 87.23226, (0 missing)
    ##       Agreeableness      < 3.75 to the right, improve= 16.80514, (0 missing)
    ##   Surrogate splits:
    ##       Conscientiousness < 4.75 to the right, agree=0.570, adj=0.125, (0 split)
    ##       Openness          < 5.25 to the right, agree=0.568, adj=0.122, (0 split)
    ##       Agreeableness     < 5.25 to the right, agree=0.547, adj=0.079, (0 split)
    ##       Extraversion      < 4.25 to the right, agree=0.544, adj=0.073, (0 split)
    ## 
    ## Node number 3: 10712 observations
    ##   predicted class=Severo  expected loss=0.331124  P(node) =0.5556593
    ##     class counts:   718  1906   923  7165
    ##    probabilities: 0.067 0.178 0.086 0.669 
    ## 
    ## Node number 4: 4353 observations
    ##   predicted class=Normal  expected loss=0.4812773  P(node) =0.2258014
    ##     class counts:   520   637  2258   938
    ##    probabilities: 0.119 0.146 0.519 0.215 
    ## 
    ## Node number 5: 4213 observations
    ##   predicted class=Severo  expected loss=0.6104913  P(node) =0.2185393
    ##     class counts:   573   913  1086  1641
    ##    probabilities: 0.136 0.217 0.258 0.390 
    ## 
    ## $frame
    ##                  var     n    wt  dev yval  complexity ncompete nsurrogate
    ## 1 EmotionalStability 19278 19278 9534    4 0.080239144        4          4
    ## 2 EmotionalStability  8566  8566 5222    3 0.058212712        4          4
    ## 4             <leaf>  4353  4353 2095    3 0.005506608        0          0
    ## 5             <leaf>  4213  4213 2572    4 0.006660373        0          0
    ## 3             <leaf> 10712 10712 3547    4 0.000000000        0          0
    ##       yval2.V1     yval2.V2     yval2.V3     yval2.V4     yval2.V5     yval2.V6
    ## 1 4.000000e+00 1.811000e+03 3.456000e+03 4.267000e+03 9.744000e+03 9.394128e-02
    ## 2 3.000000e+00 1.093000e+03 1.550000e+03 3.344000e+03 2.579000e+03 1.275975e-01
    ## 4 3.000000e+00 5.200000e+02 6.370000e+02 2.258000e+03 9.380000e+02 1.194578e-01
    ## 5 4.000000e+00 5.730000e+02 9.130000e+02 1.086000e+03 1.641000e+03 1.360076e-01
    ## 3 4.000000e+00 7.180000e+02 1.906000e+03 9.230000e+02 7.165000e+03 6.702763e-02
    ##       yval2.V7     yval2.V8     yval2.V9 yval2.nodeprob
    ## 1 1.792717e-01 2.213404e-01 5.054466e-01   1.000000e+00
    ## 2 1.809479e-01 3.903806e-01 3.010740e-01   4.443407e-01
    ## 4 1.463359e-01 5.187227e-01 2.154836e-01   2.258014e-01
    ## 5 2.167102e-01 2.577736e-01 3.895087e-01   2.185393e-01
    ## 3 1.779313e-01 8.616505e-02 6.688760e-01   5.556593e-01
    ## 
    ## $where
    ##     3     6     7     8    10    11    12    13    15    16    17    18    20 
    ##     3     3     5     4     3     5     5     3     5     5     5     5     5 
    ##    21    22    23    26    27    28    29    31    32    33    34    35    36 
    ##     3     5     5     5     3     3     5     3     4     5     5     5     5 
    ##    38    39    41    44    45    47    48    50    52    53    55    56    57 
    ##     5     3     3     3     5     3     4     4     3     3     5     5     5 
    ##    58    59    60    62    63    64    65    66    67    70    71    73    74 
    ##     4     5     3     3     5     5     3     5     5     4     5     5     5 
    ##    75    80    81    83    84    85    86    87    88    90    91    92    93 
    ##     5     5     5     5     4     5     5     5     3     3     5     4     5 
    ##    96    97    98    99   101   102   103   104   105   106   107   109   112 
    ##     3     5     5     4     3     5     3     4     5     4     3     5     3 
    ##   113   114   115   116   118   119   120   121   122   123   124   126   127 
    ##     5     5     4     5     5     3     5     3     3     5     5     5     5 
    ##   128   129   130   131   134   135   136   138   139   140   143   145   146 
    ##     5     5     4     4     5     3     4     3     5     5     4     5     5 
    ##   147   149   150   151   153   154   155   156   158   159   161   162   163 
    ##     4     5     5     5     5     3     5     5     4     3     5     5     5 
    ##   164   165   167   168   169   170   171   173   174   175   176   177   178 
    ##     4     5     3     5     3     5     5     4     5     5     5     3     5 
    ##   179   180   182   184   185   186   187   189   191   193   194   195   196 
    ##     3     5     4     5     3     3     3     3     5     3     5     3     5 
    ##   197   199   200   202   203   204   205   206   207   208   211   212   213 
    ##     3     5     5     4     5     5     3     5     5     3     4     5     3 
    ##   214   215   219   220   221   222   223   226   227   228   230   232   233 
    ##     3     4     4     5     4     5     5     5     5     3     3     4     5 
    ##   235   236   237   239   241   243   244   245   246   247   248   249   250 
    ##     4     4     5     5     3     3     5     5     3     4     5     5     4 
    ##   251   252   253   254   256   258   259   260   262   263   264   265   267 
    ##     3     3     5     3     4     5     5     4     5     4     5     5     5 
    ##   269   270   272   273   277   278   280   281   282   283   284   285   286 
    ##     3     5     5     5     5     5     5     5     5     4     4     3     3 
    ##   287   288   290   291   292   293   294   297   299   301   302   304   305 
    ##     5     5     5     5     3     5     4     3     5     4     5     4     4 
    ##   307   308   310   311   312   313   314   315   316   317   318   321   322 
    ##     5     3     5     3     5     4     5     4     4     5     5     5     5 
    ##   326   327   328   329   330   333   334   335   337   338   340   342   343 
    ##     5     5     4     5     5     3     5     5     5     3     3     4     5 
    ##   344   345   348   350   351   352   353   354   355   356   357   358   359 
    ##     5     5     3     3     5     5     5     3     3     5     5     5     5 
    ##   360   361   362   364   367   369   371   372   373   374   375   376   377 
    ##     5     5     5     5     5     3     5     5     5     5     3     5     4 
    ##   378   379   381   382   383   384   387   388   389   390   391   397   398 
    ##     5     4     3     3     3     3     5     4     4     5     5     5     5 
    ##   401   402   403   404   405   406   410   411   414   415   416   417   418 
    ##     5     4     5     4     3     5     5     5     4     4     5     5     3 
    ##   420   422   423   424   425   426   427   428   429   431   435   436   437 
    ##     4     5     5     4     5     4     5     3     3     5     3     5     3 
    ##   440   442   443   445   446   447   449   450   454   455   457   459   460 
    ##     5     3     5     3     3     5     3     5     4     5     5     4     5 
    ##   462   463   465   466   467   469   470   471   475   476   478   479   480 
    ##     4     4     3     4     4     5     5     3     5     5     5     5     5 
    ##   482   483   484   485   486   487   489   491   492   493   494   495   496 
    ##     4     5     3     3     4     5     5     5     3     5     5     5     5 
    ##   498   499   501   502   505   506   507   508   509   510   514   515   516 
    ##     4     5     3     5     4     3     3     5     5     5     3     5     5 
    ##   517   518   519   522   526   527   528   530   531   532   534   535   536 
    ##     5     5     3     5     3     5     3     5     5     5     5     5     4 
    ##   537   538   540   541   543   544   545   546   547   550   551   554   555 
    ##     5     5     5     5     5     5     3     4     5     5     4     3     4 
    ##   556   557   558   559   560   562   564   566   567   568   569   570   571 
    ##     5     3     5     5     5     5     5     5     3     5     5     5     4 
    ##   572   573   574   575   576   577   578   580   581   583   584   587   588 
    ##     5     3     5     3     5     5     3     3     5     3     5     4     5 
    ##   594   596   597   598   600   601   603   605   606   607   608   609   611 
    ##     5     4     3     3     5     5     5     5     3     5     5     3     5 
    ##   612   613   615   616   618   620   621   622   623   624   625   627   628 
    ##     3     4     4     5     5     5     4     5     3     4     4     5     3 
    ##   630   631   632   633   634   635   636   640   642   645   646   647   648 
    ##     3     3     5     4     5     5     5     5     3     4     3     5     5 
    ##   649   653   654   657   659   661   665   668   669   670   671   674   676 
    ##     3     5     5     5     5     5     5     3     5     5     5     5     3 
    ##   678   680   681   682   684   686   687   690   691   692   693   694   695 
    ##     3     5     5     3     3     5     4     3     5     5     5     3     3 
    ##   698   699   700   701   703   704   705   708   709   710   711   712   715 
    ##     5     5     5     5     3     5     4     5     5     3     3     5     5 
    ##   716   717   718   719   721   722   723   726   727   728   729   730   731 
    ##     5     3     5     5     3     3     4     3     3     5     4     5     3 
    ##   733   734   736   740   742   743   744   745   746   747   749   751   753 
    ##     3     4     5     3     5     4     4     5     5     4     5     3     4 
    ##   754   755   756   757   758   762   764   765   766   767   768   770   771 
    ##     5     5     5     4     5     5     5     5     5     5     5     3     5 
    ##   772   773   774   775   776   777   778   779   780   782   783   784   785 
    ##     5     5     5     4     3     4     5     4     3     3     5     4     5 
    ##   786   787   788   789   790   791   793   794   795   796   797   798   799 
    ##     5     5     5     3     5     4     4     4     5     5     5     5     5 
    ##   802   803   805   807   809   810   814   817   818   819   823   824   825 
    ##     3     5     5     5     4     3     3     5     3     5     5     5     5 
    ##   827   828   829   830   831   834   836   838   839   841   842   843   844 
    ##     5     5     5     5     5     3     5     3     5     3     3     5     3 
    ##   845   849   850   851   852   854   855   858   859   860   862   863   864 
    ##     5     5     3     5     3     5     4     4     5     5     4     4     3 
    ##   865   866   867   869   870   872   873   877   879   881   882   884   886 
    ##     5     4     3     3     5     3     5     5     4     5     5     5     5 
    ##   888   889   890   891   892   893   894   895   896   898   899   900   901 
    ##     5     5     3     5     5     5     5     4     3     5     5     4     5 
    ##   902   903   904   905   909   910   911   912   913   914   915   916   917 
    ##     5     5     5     5     5     5     4     5     4     5     4     5     4 
    ##   919   920   923   924   926   928   930   931   932   933   934   936   938 
    ##     4     5     3     3     5     3     5     4     5     5     5     3     5 
    ##   939   940   941   942   943   944   945   946   947   948   949   950   953 
    ##     3     3     5     5     4     5     4     5     5     5     3     5     5 
    ##   954   955   956   957   959   961   962   963   964   965   966   967   968 
    ##     5     5     5     5     5     5     5     5     3     5     4     5     5 
    ##   969   970   971   972   974   975   976   980   981   982   983   985   986 
    ##     4     4     5     5     3     5     5     5     5     5     5     4     5 
    ##   987   989   990   991   992   993   994   995   996   997   999  1000  1003 
    ##     5     5     5     5     5     5     4     5     5     4     5     5     5 
    ##  1004  1005  1007  1008  1009  1010  1011  1012  1013  1014  1015  1017  1018 
    ##     5     5     5     4     5     5     3     5     5     4     3     3     5 
    ##  1027  1029  1030  1031  1032  1033  1034  1037  1039  1040  1041  1043  1044 
    ##     3     3     4     5     5     5     3     3     3     5     3     5     3 
    ##  1045  1046  1054  1055  1056  1057  1058  1059  1061  1062  1063  1064  1065 
    ##     4     3     3     5     5     5     5     5     4     5     5     3     5 
    ##  1066  1067  1068  1069  1070  1071  1072  1073  1074  1075  1076  1077  1078 
    ##     4     5     4     3     5     3     4     3     5     5     3     5     5 
    ##  1081  1082  1083  1085  1086  1087  1088  1090  1091  1092  1093  1095  1096 
    ##     5     3     3     5     4     4     3     3     4     5     5     5     5 
    ##  1097  1098  1099  1100  1101  1102  1104  1107  1108  1109  1110  1111  1113 
    ##     5     5     3     5     3     5     5     3     5     5     5     5     4 
    ##  1114  1116  1117  1118  1120  1122  1124  1126  1128  1129  1131  1132  1134 
    ##     5     4     5     5     5     5     5     3     4     5     5     4     4 
    ##  1138  1139  1140  1141  1142  1143  1144  1145  1146  1148  1149  1152  1154 
    ##     3     5     5     5     5     5     5     4     5     4     3     4     3 
    ##  1156  1157  1160  1161  1164  1165  1166  1167  1169  1170  1171  1172  1174 
    ##     4     5     5     5     3     5     5     5     5     3     4     5     4 
    ##  1175  1176  1179  1181  1183  1184  1185  1186  1187  1188  1189  1190  1191 
    ##     4     5     5     5     4     5     3     5     5     5     5     4     3 
    ##  1193  1194  1196  1197  1198  1201  1202  1203  1204  1205  1206  1208  1209 
    ##     5     5     3     4     5     5     5     4     5     5     5     4     3 
    ##  1211  1212  1213  1214  1215  1216  1218  1219  1220  1222  1225  1226  1229 
    ##     5     5     3     4     5     4     3     5     5     4     4     5     5 
    ##  1230  1232  1233  1234  1236  1237  1239  1242  1243  1244  1245  1246  1248 
    ##     5     3     5     4     5     5     3     3     5     5     5     5     5 
    ##  1249  1251  1252  1256  1257  1258  1260  1261  1262  1264  1266  1267  1268 
    ##     3     3     4     5     5     4     5     5     5     5     4     3     5 
    ##  1269  1270  1271  1272  1274  1277  1278  1279  1280  1281  1282  1285  1286 
    ##     4     4     5     5     5     4     5     4     4     3     4     3     3 
    ##  1287  1289  1291  1294  1295  1296  1297  1299  1302  1303  1305  1308  1311 
    ##     4     3     3     4     3     5     5     4     3     3     5     5     5 
    ##  1312  1314  1315  1318  1320  1321  1322  1323  1326  1327  1330  1331  1333 
    ##     5     3     3     5     4     5     4     4     4     4     5     3     5 
    ##  1335  1336  1337  1338  1340  1341  1342  1344  1345  1347  1350  1351  1353 
    ##     5     3     5     5     5     3     5     5     4     4     3     3     3 
    ##  1354  1355  1357  1358  1359  1360  1361  1362  1364  1366  1367  1370  1371 
    ##     5     5     5     3     5     5     5     3     4     5     5     4     5 
    ##  1372  1373  1374  1375  1376  1377  1378  1379  1381  1382  1383  1385  1387 
    ##     5     5     4     5     4     4     5     5     5     5     5     3     5 
    ##  1388  1389  1390  1391  1394  1395  1396  1398  1399  1400  1402  1404  1405 
    ##     5     3     5     5     5     4     5     5     5     5     3     5     5 
    ##  1406  1408  1409  1410  1411  1412  1413  1414  1415  1416  1417  1418  1421 
    ##     3     3     5     3     5     3     3     5     5     5     5     3     4 
    ##  1423  1426  1428  1429  1430  1431  1432  1434  1435  1438  1439  1440  1442 
    ##     3     5     5     3     4     5     4     5     5     5     5     5     5 
    ##  1443  1444  1445  1446  1447  1449  1451  1453  1454  1455  1456  1458  1459 
    ##     5     4     5     5     5     5     5     3     3     5     5     5     4 
    ##  1460  1462  1463  1465  1467  1468  1469  1470  1473  1475  1476  1481  1486 
    ##     4     5     5     5     5     3     5     5     4     5     5     5     4 
    ##  1487  1488  1491  1493  1494  1496  1497  1498  1499  1500  1501  1502  1503 
    ##     5     5     5     5     5     3     3     5     5     3     5     5     5 
    ##  1505  1508  1509  1512  1513  1514  1515  1516  1517  1518  1519  1520  1521 
    ##     5     4     4     5     4     5     5     5     5     4     5     5     5 
    ##  1522  1524  1525  1526  1529  1531  1532  1533  1534  1535  1536  1538  1540 
    ##     3     5     4     5     4     5     5     5     5     4     3     4     5 
    ##  1541  1543  1544  1545  1546  1547  1548  1549  1550  1551  1552  1553  1554 
    ##     5     5     5     5     5     5     5     5     5     5     4     5     5 
    ##  1555  1556  1558  1559  1560  1562  1566  1567  1569  1571  1573  1574  1575 
    ##     5     5     5     3     5     5     5     3     4     5     4     5     4 
    ##  1576  1579  1580  1581  1584  1585  1588  1589  1590  1591  1592  1593  1594 
    ##     3     4     5     5     4     5     5     5     4     4     4     3     5 
    ##  1595  1596  1598  1599  1601  1602  1604  1605  1607  1609  1610  1611  1612 
    ##     5     5     5     3     3     5     5     4     5     4     3     5     5 
    ##  1614  1615  1616  1618  1619  1620  1621  1622  1624  1625  1626  1629  1631 
    ##     4     4     3     5     5     5     5     5     5     5     5     4     3 
    ##  1633  1636  1637  1638  1640  1641  1643  1646  1647  1648  1649  1650  1652 
    ##     4     5     5     5     5     5     5     4     5     5     5     5     5 
    ##  1655  1657  1658  1659  1660  1661  1662  1663  1664  1665  1666  1668  1669 
    ##     5     5     5     5     4     5     3     5     5     3     5     5     3 
    ##  1670  1671  1672  1674  1675  1677  1678  1679  1680  1681  1682  1683  1685 
    ##     4     5     5     5     4     5     5     3     5     5     5     4     5 
    ##  1686  1687  1688  1689  1693  1694  1696  1697  1700  1702  1703  1705  1708 
    ##     5     3     3     3     5     5     3     3     4     4     3     4     3 
    ##  1709  1711  1713  1714  1715  1716  1720  1721  1722  1724  1725  1726  1727 
    ##     4     4     4     5     5     5     5     5     5     3     4     5     4 
    ##  1728  1729  1730  1731  1732  1733  1734  1735  1736  1737  1739  1740  1741 
    ##     5     5     4     3     5     5     5     3     5     3     5     4     4 
    ##  1744  1745  1746  1747  1748  1749  1750  1752  1753  1755  1756  1757  1758 
    ##     5     5     5     5     3     5     5     5     5     5     3     5     5 
    ##  1759  1761  1762  1763  1764  1765  1766  1767  1769  1770  1771  1772  1773 
    ##     3     5     5     5     5     3     5     5     5     5     5     3     5 
    ##  1774  1776  1779  1780  1782  1783  1785  1786  1788  1789  1790  1791  1792 
    ##     4     5     5     4     5     5     5     3     5     4     3     3     3 
    ##  1793  1794  1795  1797  1798  1799  1800  1801  1802  1803  1804  1806  1807 
    ##     5     5     4     4     5     5     5     3     5     3     5     5     5 
    ##  1808  1809  1810  1811  1812  1813  1815  1816  1817  1819  1820  1821  1822 
    ##     5     5     5     5     4     5     3     3     3     4     5     3     5 
    ##  1823  1824  1828  1830  1831  1834  1836  1837  1838  1839  1840  1842  1843 
    ##     4     5     3     5     4     3     5     3     4     5     5     5     4 
    ##  1844  1845  1847  1848  1849  1851  1852  1853  1854  1855  1858  1859  1860 
    ##     4     3     5     5     5     5     5     5     5     4     5     5     5 
    ##  1862  1864  1866  1868  1869  1870  1871  1872  1873  1874  1875  1877  1878 
    ##     5     3     4     5     3     5     4     5     4     4     3     3     4 
    ##  1880  1881  1882  1884  1885  1887  1889  1890  1891  1892  1896  1897  1900 
    ##     5     5     3     3     5     3     3     3     5     4     5     4     5 
    ##  1904  1905  1906  1908  1910  1911  1912  1914  1916  1917  1921  1922  1923 
    ##     5     4     5     5     3     5     5     3     3     3     4     5     4 
    ##  1924  1926  1927  1928  1929  1931  1932  1936  1937  1938  1939  1940  1941 
    ##     3     4     5     3     4     5     5     5     5     5     3     5     5 
    ##  1942  1943  1944  1945  1946  1947  1949  1950  1953  1955  1957  1958  1959 
    ##     5     3     5     3     5     5     5     5     4     5     3     5     5 
    ##  1960  1961  1962  1963  1964  1966  1967  1970  1972  1973  1974  1976  1977 
    ##     3     5     5     3     5     5     5     5     5     5     3     5     5 
    ##  1978  1979  1980  1982  1983  1984  1985  1987  1989  1990  1991  1992  1994 
    ##     5     5     5     5     5     4     5     3     5     5     5     3     5 
    ##  1996  1997  1998  1999  2001  2002  2003  2004  2005  2006  2007  2009  2010 
    ##     5     5     5     5     5     5     5     4     5     3     5     3     3 
    ##  2011  2012  2014  2016  2017  2018  2021  2022  2025  2026  2027  2029  2030 
    ##     3     5     4     4     4     5     4     5     3     5     5     3     5 
    ##  2031  2032  2036  2037  2038  2040  2041  2044  2045  2046  2047  2050  2051 
    ##     3     5     5     5     5     3     5     5     5     4     5     4     3 
    ##  2054  2057  2058  2059  2060  2061  2062  2063  2064  2065  2068  2069  2070 
    ##     4     5     5     5     5     5     5     5     4     5     4     4     5 
    ##  2071  2072  2073  2074  2075  2076  2077  2078  2079  2080  2081  2082  2083 
    ##     4     5     5     5     5     5     4     5     5     5     5     5     4 
    ##  2084  2085  2087  2091  2092  2093  2094  2095  2098  2099  2100  2101  2102 
    ##     5     3     5     5     5     3     3     3     5     5     5     4     5 
    ##  2104  2105  2106  2107  2108  2109  2110  2111  2112  2113  2114  2115  2117 
    ##     5     4     5     5     5     4     5     4     3     5     3     5     5 
    ##  2119  2121  2122  2124  2125  2127  2128  2129  2130  2131  2132  2133  2135 
    ##     5     5     5     3     4     3     5     4     5     5     4     3     5 
    ##  2136  2137  2140  2141  2142  2144  2145  2147  2149  2150  2151  2152  2153 
    ##     5     5     3     3     3     4     4     5     5     3     5     5     5 
    ##  2154  2155  2157  2158  2159  2160  2164  2165  2166  2167  2168  2169  2172 
    ##     5     5     5     5     3     5     4     4     3     5     5     5     3 
    ##  2173  2176  2178  2180  2182  2183  2184  2185  2187  2189  2190  2193  2194 
    ##     3     3     5     5     3     5     4     3     4     4     5     4     4 
    ##  2195  2196  2197  2198  2200  2201  2202  2203  2204  2205  2206  2207  2209 
    ##     5     4     4     5     4     5     3     3     5     5     5     5     4 
    ##  2210  2211  2212  2214  2215  2217  2218  2220  2221  2222  2224  2225  2226 
    ##     5     5     5     5     5     3     5     3     5     5     3     5     4 
    ##  2227  2228  2231  2232  2234  2235  2236  2237  2238  2239  2240  2241  2242 
    ##     5     3     5     4     4     3     5     5     4     5     5     5     3 
    ##  2243  2244  2245  2246  2247  2248  2249  2250  2251  2254  2255  2257  2259 
    ##     5     3     5     5     5     4     3     3     5     5     3     5     5 
    ##  2260  2261  2262  2263  2264  2265  2267  2268  2269  2272  2273  2274  2275 
    ##     5     5     3     3     3     5     4     5     5     5     5     5     5 
    ##  2276  2277  2278  2279  2280  2281  2282  2283  2284  2285  2286  2287  2288 
    ##     5     5     5     4     4     3     3     5     5     5     3     5     5 
    ##  2290  2291  2292  2294  2295  2297  2298  2300  2301  2304  2305  2307  2308 
    ##     3     5     4     5     4     5     4     4     5     3     5     5     5 
    ##  2309  2311  2312  2313  2314  2316  2317  2318  2320  2321  2322  2325  2326 
    ##     3     5     5     5     5     5     5     5     5     5     3     5     3 
    ##  2327  2328  2329  2330  2331  2332  2333  2335  2336  2337  2338  2339  2340 
    ##     3     4     5     5     5     5     3     5     5     3     5     3     4 
    ##  2341  2343  2345  2346  2350  2351  2352  2353  2354  2355  2357  2359  2360 
    ##     5     4     5     4     5     5     4     4     3     3     5     5     5 
    ##  2361  2362  2364  2366  2367  2369  2370  2372  2373  2374  2375  2376  2377 
    ##     3     5     3     4     5     5     3     3     3     4     4     5     3 
    ##  2378  2379  2380  2382  2383  2384  2385  2388  2389  2391  2393  2395  2398 
    ##     4     5     3     5     5     5     5     3     3     3     3     3     4 
    ##  2399  2400  2401  2402  2403  2404  2406  2407  2408  2409  2410  2412  2414 
    ##     5     5     5     3     3     3     3     5     4     5     4     5     5 
    ##  2415  2416  2417  2419  2420  2421  2423  2424  2425  2426  2427  2428  2431 
    ##     5     5     3     5     5     5     3     5     4     5     3     4     3 
    ##  2432  2433  2434  2435  2436  2438  2439  2440  2441  2442  2444  2445  2448 
    ##     5     5     4     5     4     5     5     4     5     5     4     5     5 
    ##  2449  2450  2451  2452  2453  2454  2456  2457  2459  2460  2462  2465  2466 
    ##     3     3     5     4     5     3     5     4     5     3     3     5     5 
    ##  2467  2471  2473  2474  2475  2476  2477  2478  2479  2480  2481  2482  2483 
    ##     3     5     5     4     5     5     3     5     3     3     5     5     5 
    ##  2484  2485  2487  2488  2489  2490  2493  2494  2496  2498  2500  2501  2502 
    ##     5     5     3     5     3     5     5     4     5     5     5     5     5 
    ##  2503  2505  2506  2510  2512  2514  2516  2518  2520  2522  2523  2525  2526 
    ##     4     5     5     3     5     5     5     5     4     5     5     5     3 
    ##  2527  2529  2531  2532  2533  2535  2536  2541  2542  2543  2544  2545  2546 
    ##     3     5     5     5     4     3     5     5     4     4     5     5     5 
    ##  2547  2549  2550  2551  2552  2554  2555  2556  2558  2559  2562  2563  2564 
    ##     5     4     3     5     4     4     5     5     5     3     5     3     5 
    ##  2566  2567  2568  2569  2572  2573  2575  2576  2578  2579  2580  2581  2583 
    ##     5     3     3     3     5     5     5     4     5     5     5     5     5 
    ##  2584  2585  2586  2587  2588  2589  2590  2591  2592  2594  2595  2596  2597 
    ##     5     5     5     3     4     5     5     5     4     5     5     5     3 
    ##  2598  2599  2600  2601  2602  2604  2605  2608  2609  2610  2611  2613  2616 
    ##     5     5     3     3     4     3     4     5     4     3     3     3     5 
    ##  2617  2618  2620  2621  2622  2623  2625  2626  2627  2629  2634  2637  2638 
    ##     4     5     5     4     5     5     5     5     5     5     3     4     5 
    ##  2640  2641  2642  2644  2645  2646  2647  2649  2650  2651  2652  2653  2657 
    ##     5     3     5     4     5     5     5     5     3     4     5     4     4 
    ##  2658  2661  2662  2663  2665  2668  2669  2671  2672  2674  2675  2676  2679 
    ##     5     5     3     5     5     5     5     5     4     5     3     3     4 
    ##  2681  2682  2683  2685  2687  2688  2689  2690  2691  2693  2694  2700  2702 
    ##     5     5     3     3     5     5     5     5     4     4     5     5     5 
    ##  2703  2704  2705  2706  2707  2708  2709  2710  2711  2712  2714  2715  2716 
    ##     4     5     5     5     3     5     4     3     5     3     5     5     5 
    ##  2717  2718  2720  2721  2723  2724  2726  2729  2731  2733  2734  2736  2737 
    ##     5     5     5     4     5     5     5     5     5     5     5     4     5 
    ##  2740  2741  2742  2743  2744  2747  2748  2750  2752  2753  2754  2756  2759 
    ##     4     5     5     5     5     5     5     5     4     5     5     5     5 
    ##  2761  2762  2765  2766  2768  2769  2770  2771  2772  2775  2776  2777  2779 
    ##     5     3     3     4     4     5     5     5     3     3     3     5     5 
    ##  2782  2783  2784  2785  2787  2788  2789  2790  2791  2794  2795  2796  2797 
    ##     5     4     3     5     5     5     5     3     5     5     4     5     4 
    ##  2798  2801  2802  2808  2811  2813  2815  2818  2820  2823  2828  2829  2830 
    ##     4     3     5     3     5     5     4     5     5     5     3     4     3 
    ##  2831  2832  2833  2834  2835  2836  2837  2838  2839  2840  2841  2842  2843 
    ##     5     5     5     5     4     3     5     5     5     5     5     5     5 
    ##  2844  2845  2846  2847  2848  2850  2855  2856  2857  2858  2859  2860  2863 
    ##     4     5     4     5     5     4     4     5     4     5     3     5     3 
    ##  2864  2867  2868  2869  2870  2871  2872  2873  2875  2877  2878  2879  2880 
    ##     5     5     5     5     5     5     5     4     5     5     5     5     4 
    ##  2881  2882  2884  2885  2886  2887  2888  2889  2890  2891  2892  2894  2896 
    ##     5     5     4     5     5     3     5     5     5     5     4     5     5 
    ##  2897  2898  2899  2900  2901  2902  2903  2904  2906  2907  2908  2909  2910 
    ##     5     3     3     5     4     5     4     5     3     5     4     3     5 
    ##  2912  2913  2914  2916  2917  2918  2919  2920  2922  2923  2924  2925  2926 
    ##     5     5     5     5     3     5     5     5     5     5     5     5     5 
    ##  2927  2929  2930  2932  2934  2935  2936  2937  2938  2940  2941  2945  2947 
    ##     3     4     5     5     3     5     5     3     4     3     5     4     5 
    ##  2948  2949  2950  2951  2955  2956  2958  2959  2960  2962  2964  2965  2966 
    ##     4     5     5     5     5     5     5     4     4     3     4     3     5 
    ##  2967  2970  2971  2972  2973  2975  2977  2978  2979  2980  2981  2983  2984 
    ##     5     5     4     3     3     5     5     5     5     3     5     5     5 
    ##  2985  2986  2987  2988  2989  2990  2992  2993  2994  2996  2997  2998  3000 
    ##     4     5     3     5     5     5     5     5     4     5     5     3     5 
    ##  3002  3003  3004  3005  3006  3007  3008  3009  3011  3012  3013  3014  3015 
    ##     4     4     5     3     5     5     3     3     4     5     5     4     5 
    ##  3016  3018  3019  3020  3021  3022  3026  3027  3028  3029  3032  3033  3034 
    ##     5     4     4     3     5     3     5     3     5     4     3     5     5 
    ##  3035  3036  3037  3040  3041  3042  3043  3044  3046  3048  3051  3054  3056 
    ##     5     5     4     5     3     4     5     4     5     5     5     5     5 
    ##  3057  3058  3060  3061  3062  3063  3065  3066  3068  3069  3071  3072  3073 
    ##     5     4     5     3     5     4     5     3     5     3     3     3     3 
    ##  3074  3075  3078  3079  3081  3082  3083  3084  3085  3086  3087  3088  3091 
    ##     5     5     5     3     5     5     5     5     5     4     5     3     4 
    ##  3092  3095  3096  3097  3099  3101  3102  3103  3106  3107  3108  3109  3111 
    ##     5     5     5     5     3     5     5     3     5     5     5     4     5 
    ##  3113  3114  3115  3116  3119  3121  3125  3126  3127  3128  3129  3130  3131 
    ##     3     3     5     5     3     4     4     5     5     4     4     4     5 
    ##  3134  3137  3138  3139  3143  3144  3147  3148  3149  3152  3155  3157  3158 
    ##     4     4     5     3     5     4     5     4     5     5     4     4     3 
    ##  3159  3160  3162  3163  3165  3166  3167  3168  3169  3171  3173  3174  3175 
    ##     5     5     5     5     5     5     5     5     3     3     4     4     5 
    ##  3176  3178  3179  3180  3182  3183  3184  3188  3190  3191  3193  3195  3196 
    ##     4     5     3     5     5     3     5     3     5     5     5     5     4 
    ##  3199  3200  3201  3202  3204  3205  3206  3211  3212  3214  3215  3216  3217 
    ##     4     5     4     4     5     5     5     5     4     5     5     3     5 
    ##  3218  3219  3220  3221  3222  3223  3225  3226  3227  3229  3230  3231  3232 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     3 
    ##  3234  3235  3236  3237  3238  3239  3240  3241  3242  3243  3244  3246  3247 
    ##     5     5     5     5     5     5     5     5     5     5     3     5     5 
    ##  3248  3249  3251  3253  3254  3255  3257  3258  3262  3263  3264  3265  3266 
    ##     5     4     5     4     4     5     5     5     4     5     5     4     5 
    ##  3267  3268  3270  3271  3273  3274  3276  3277  3278  3279  3281  3282  3283 
    ##     3     5     5     5     3     5     5     5     5     3     4     5     5 
    ##  3284  3285  3287  3288  3289  3290  3295  3296  3297  3298  3299  3300  3301 
    ##     3     5     4     3     3     4     5     4     3     4     5     5     4 
    ##  3302  3303  3304  3305  3306  3307  3308  3309  3310  3311  3312  3313  3317 
    ##     5     5     5     5     4     5     5     3     5     5     5     5     3 
    ##  3318  3319  3320  3321  3322  3324  3325  3327  3328  3329  3330  3331  3332 
    ##     4     5     4     5     5     4     3     5     3     5     4     5     4 
    ##  3333  3334  3337  3339  3340  3341  3345  3348  3349  3350  3352  3353  3355 
    ##     5     5     3     4     5     5     3     5     5     3     3     5     4 
    ##  3356  3357  3358  3359  3362  3364  3365  3366  3368  3369  3370  3371  3372 
    ##     3     5     5     3     3     5     4     3     5     3     3     5     5 
    ##  3373  3375  3376  3377  3378  3379  3380  3383  3385  3386  3387  3388  3389 
    ##     5     3     3     5     5     5     4     5     3     5     5     5     4 
    ##  3390  3391  3392  3394  3396  3397  3398  3399  3400  3401  3403  3404  3405 
    ##     4     3     3     5     5     4     5     4     5     5     5     5     5 
    ##  3406  3407  3409  3410  3413  3414  3417  3418  3421  3422  3425  3427  3429 
    ##     5     3     4     4     5     5     5     3     4     5     4     3     5 
    ##  3431  3432  3434  3435  3436  3437  3439  3442  3443  3446  3447  3448  3450 
    ##     5     4     3     5     5     4     4     3     4     4     5     3     3 
    ##  3451  3455  3456  3457  3459  3460  3461  3462  3463  3465  3466  3467  3468 
    ##     5     5     5     3     5     5     3     5     5     3     5     3     3 
    ##  3469  3470  3473  3474  3475  3476  3478  3480  3481  3483  3487  3488  3489 
    ##     4     5     5     5     5     4     5     5     4     5     4     5     3 
    ##  3490  3491  3492  3495  3496  3498  3499  3500  3501  3502  3503  3507  3508 
    ##     3     4     5     4     4     5     3     3     5     4     5     3     4 
    ##  3509  3512  3515  3516  3518  3519  3521  3523  3526  3527  3529  3531  3533 
    ##     3     3     3     5     5     3     5     5     4     5     5     4     5 
    ##  3537  3538  3540  3541  3543  3544  3547  3549  3551  3552  3553  3554  3556 
    ##     3     5     5     4     4     5     5     5     3     5     5     4     5 
    ##  3559  3560  3563  3564  3566  3567  3568  3569  3571  3572  3573  3576  3577 
    ##     5     5     3     4     5     5     3     4     4     3     4     4     4 
    ##  3578  3581  3582  3586  3587  3588  3589  3590  3591  3593  3597  3598  3599 
    ##     5     4     5     3     5     5     5     5     4     5     5     3     5 
    ##  3600  3601  3603  3604  3606  3607  3608  3609  3611  3612  3613  3614  3616 
    ##     4     5     4     5     5     3     5     5     4     4     3     5     4 
    ##  3618  3619  3621  3622  3623  3627  3631  3632  3634  3635  3638  3640  3641 
    ##     4     4     3     5     5     3     5     3     3     5     4     3     3 
    ##  3642  3643  3644  3645  3646  3647  3648  3649  3650  3651  3652  3653  3654 
    ##     5     5     3     4     5     4     4     3     4     5     5     4     3 
    ##  3655  3656  3658  3659  3660  3661  3662  3663  3665  3666  3667  3668  3671 
    ##     3     5     5     4     3     5     3     5     5     3     3     5     4 
    ##  3673  3674  3676  3677  3678  3679  3681  3682  3683  3684  3686  3687  3689 
    ##     3     4     5     3     3     5     5     3     5     5     4     3     4 
    ##  3690  3691  3692  3693  3694  3695  3697  3698  3699  3701  3702  3704  3705 
    ##     4     3     4     5     4     5     4     3     3     5     5     3     5 
    ##  3706  3707  3711  3714  3715  3716  3717  3719  3720  3723  3724  3725  3726 
    ##     5     3     5     5     4     3     4     3     5     5     5     5     3 
    ##  3727  3728  3729  3730  3732  3733  3734  3735  3737  3741  3742  3743  3746 
    ##     4     4     3     5     4     5     5     5     3     3     3     3     3 
    ##  3747  3748  3749  3750  3752  3754  3755  3756  3762  3763  3764  3767  3769 
    ##     5     4     5     3     3     4     5     3     4     5     3     3     5 
    ##  3772  3774  3775  3776  3777  3778  3779  3783  3784  3785  3786  3787  3788 
    ##     5     5     5     5     3     5     5     3     4     5     4     5     4 
    ##  3789  3792  3796  3798  3799  3800  3801  3802  3804  3808  3809  3810  3811 
    ##     5     3     3     5     5     3     5     5     5     5     5     3     5 
    ##  3812  3814  3815  3816  3817  3818  3821  3822  3823  3825  3827  3828  3829 
    ##     5     4     4     4     5     3     5     4     3     5     4     4     5 
    ##  3830  3831  3833  3834  3835  3836  3837  3838  3840  3841  3842  3843  3844 
    ##     5     5     5     3     4     5     5     3     3     5     3     3     5 
    ##  3845  3846  3848  3849  3850  3852  3854  3855  3857  3858  3859  3860  3862 
    ##     5     5     4     5     3     5     5     4     5     4     5     5     3 
    ##  3863  3865  3867  3868  3869  3870  3871  3874  3875  3876  3877  3878  3879 
    ##     3     3     3     4     4     5     5     3     3     3     5     5     5 
    ##  3881  3882  3883  3887  3888  3889  3891  3892  3893  3894  3896  3897  3900 
    ##     4     5     3     5     5     5     5     4     3     5     5     3     4 
    ##  3901  3905  3906  3907  3908  3909  3910  3911  3912  3914  3915  3916  3917 
    ##     3     5     5     5     3     4     5     5     4     3     5     4     4 
    ##  3918  3919  3921  3923  3924  3925  3926  3927  3929  3930  3931  3932  3934 
    ##     3     5     5     5     5     5     5     5     4     4     5     4     5 
    ##  3935  3936  3937  3938  3939  3940  3943  3944  3945  3946  3947  3948  3951 
    ##     4     5     5     3     3     4     5     3     5     5     5     5     5 
    ##  3952  3954  3955  3956  3957  3958  3959  3961  3962  3965  3967  3968  3969 
    ##     5     5     4     3     5     3     5     5     5     4     5     3     5 
    ##  3970  3971  3972  3974  3975  3976  3978  3979  3980  3985  3986  3987  3988 
    ##     5     3     3     3     3     4     5     5     5     4     5     5     4 
    ##  3990  3993  3994  3997  4000  4001  4002  4003  4006  4007  4008  4009  4010 
    ##     5     5     5     3     5     3     3     3     5     4     5     5     4 
    ##  4012  4014  4016  4017  4018  4019  4020  4021  4022  4024  4025  4028  4029 
    ##     5     5     4     5     3     5     5     5     3     4     5     3     5 
    ##  4030  4032  4033  4035  4037  4039  4040  4042  4043  4044  4045  4046  4049 
    ##     4     3     5     4     5     3     3     3     3     3     4     5     5 
    ##  4052  4053  4054  4055  4056  4057  4058  4059  4060  4061  4062  4065  4070 
    ##     5     5     3     5     3     3     5     5     4     5     3     4     5 
    ##  4071  4072  4073  4075  4077  4078  4081  4082  4083  4086  4087  4088  4090 
    ##     3     5     5     5     5     4     3     4     3     4     3     4     5 
    ##  4091  4092  4093  4094  4096  4097  4098  4099  4100  4106  4107  4108  4109 
    ##     5     4     4     4     4     3     5     5     3     4     5     5     3 
    ##  4110  4111  4112  4114  4115  4116  4117  4118  4119  4122  4123  4124  4125 
    ##     5     5     5     5     4     5     4     4     5     3     3     3     5 
    ##  4126  4127  4128  4131  4132  4133  4134  4136  4137  4138  4139  4141  4142 
    ##     5     4     5     3     5     3     5     5     4     5     5     4     3 
    ##  4143  4144  4145  4147  4148  4149  4150  4151  4153  4154  4156  4157  4160 
    ##     5     5     4     5     5     5     3     5     5     3     3     4     5 
    ##  4161  4162  4163  4166  4167  4168  4169  4170  4174  4175  4177  4178  4179 
    ##     5     5     5     4     5     5     3     4     5     3     5     5     5 
    ##  4180  4182  4184  4185  4186  4187  4188  4190  4191  4194  4196  4197  4199 
    ##     5     3     3     5     5     5     5     5     4     3     5     4     3 
    ##  4200  4201  4202  4203  4204  4206  4209  4210  4211  4213  4214  4215  4217 
    ##     5     5     4     5     5     5     3     5     5     5     5     4     5 
    ##  4219  4220  4221  4222  4223  4224  4226  4228  4229  4230  4231  4232  4236 
    ##     3     5     4     5     5     4     3     4     4     5     5     4     4 
    ##  4237  4238  4239  4240  4241  4242  4244  4246  4247  4248  4251  4252  4253 
    ##     3     5     3     5     4     4     5     4     5     5     5     4     5 
    ##  4254  4256  4258  4259  4260  4261  4265  4266  4268  4269  4270  4271  4272 
    ##     3     3     5     5     3     3     5     3     5     5     5     5     5 
    ##  4273  4274  4275  4276  4278  4279  4283  4284  4285  4286  4287  4288  4289 
    ##     5     3     5     5     4     5     5     4     5     5     4     5     5 
    ##  4290  4293  4295  4296  4297  4298  4299  4300  4301  4302  4303  4305  4308 
    ##     3     3     3     4     4     5     3     5     5     5     5     5     5 
    ##  4309  4310  4312  4313  4314  4316  4318  4319  4320  4322  4323  4324  4325 
    ##     5     5     4     5     5     4     4     5     5     3     4     4     4 
    ##  4326  4329  4330  4332  4333  4335  4339  4340  4341  4343  4344  4345  4346 
    ##     4     5     4     5     4     4     5     5     5     3     4     5     5 
    ##  4347  4348  4350  4351  4352  4353  4354  4355  4357  4358  4360  4362  4363 
    ##     5     4     5     3     5     3     4     4     5     3     5     5     5 
    ##  4365  4368  4369  4371  4372  4375  4376  4379  4380  4381  4382  4383  4384 
    ##     5     5     3     3     5     3     5     5     5     3     5     5     4 
    ##  4385  4386  4387  4388  4389  4390  4391  4392  4393  4394  4396  4398  4400 
    ##     5     5     5     4     5     4     5     3     4     3     3     3     5 
    ##  4401  4402  4404  4406  4407  4408  4409  4410  4411  4413  4414  4416  4418 
    ##     3     4     5     3     4     5     4     3     3     5     5     5     5 
    ##  4419  4420  4421  4422  4423  4426  4427  4429  4432  4434  4435  4436  4437 
    ##     3     5     4     5     5     4     5     4     4     3     4     5     4 
    ##  4439  4440  4442  4443  4444  4445  4446  4447  4448  4450  4451  4452  4455 
    ##     4     5     5     4     3     5     3     3     3     4     4     5     5 
    ##  4456  4457  4458  4459  4462  4463  4466  4470  4472  4473  4474  4475  4477 
    ##     3     5     5     5     3     3     4     5     3     5     5     3     5 
    ##  4478  4479  4480  4482  4484  4485  4486  4487  4489  4490  4491  4492  4493 
    ##     5     5     5     4     5     5     3     3     5     4     4     5     5 
    ##  4495  4496  4497  4498  4500  4503  4504  4506  4507  4509  4510  4511  4512 
    ##     5     3     3     5     5     3     3     5     3     3     5     5     5 
    ##  4513  4514  4516  4518  4520  4521  4522  4523  4524  4525  4526  4527  4528 
    ##     5     3     4     4     5     5     5     4     5     3     3     4     4 
    ##  4529  4531  4533  4535  4536  4537  4538  4540  4542  4543  4544  4545  4546 
    ##     3     4     4     5     5     3     4     5     4     4     3     5     5 
    ##  4547  4548  4549  4550  4551  4552  4553  4554  4555  4557  4562  4563  4564 
    ##     3     5     4     4     5     5     5     5     5     3     3     5     5 
    ##  4565  4566  4567  4568  4569  4571  4572  4574  4575  4576  4578  4579  4580 
    ##     3     4     3     5     5     4     4     4     4     4     3     3     5 
    ##  4581  4582  4583  4584  4585  4586  4588  4589  4590  4591  4592  4593  4596 
    ##     4     5     5     4     5     4     4     5     5     3     5     5     3 
    ##  4597  4598  4599  4600  4602  4604  4606  4607  4608  4609  4611  4613  4614 
    ##     4     4     4     5     5     3     5     3     4     4     5     3     4 
    ##  4616  4617  4618  4619  4620  4621  4622  4623  4624  4625  4626  4627  4628 
    ##     3     4     4     5     3     5     5     4     5     5     5     3     5 
    ##  4631  4632  4633  4636  4637  4638  4639  4641  4642  4644  4646  4647  4649 
    ##     5     5     5     3     3     4     4     4     4     3     4     4     5 
    ##  4651  4654  4655  4657  4658  4661  4662  4663  4664  4665  4666  4667  4668 
    ##     3     5     4     3     4     3     5     5     5     5     5     4     3 
    ##  4670  4671  4672  4673  4674  4677  4678  4680  4681  4684  4685  4686  4687 
    ##     5     3     5     3     5     5     4     5     5     4     5     3     5 
    ##  4688  4689  4690  4691  4692  4693  4695  4698  4699  4700  4701  4703  4704 
    ##     5     3     5     3     4     4     3     4     5     5     4     4     5 
    ##  4705  4706  4708  4709  4710  4711  4713  4714  4715  4716  4717  4718  4719 
    ##     5     3     5     5     4     5     5     4     5     5     5     4     5 
    ##  4721  4722  4724  4725  4726  4728  4730  4731  4732  4734  4736  4738  4739 
    ##     5     5     5     3     5     5     3     5     5     5     5     3     5 
    ##  4741  4742  4743  4744  4746  4749  4750  4751  4753  4754  4756  4757  4758 
    ##     5     5     4     4     4     5     4     5     5     3     5     5     4 
    ##  4759  4761  4762  4764  4765  4767  4769  4771  4772  4773  4774  4776  4778 
    ##     4     4     5     5     5     5     5     3     5     5     4     5     5 
    ##  4779  4781  4782  4783  4785  4786  4787  4789  4790  4792  4795  4798  4802 
    ##     3     3     3     3     3     5     5     3     5     3     5     5     5 
    ##  4804  4805  4807  4808  4810  4811  4812  4813  4815  4818  4819  4820  4822 
    ##     4     5     4     4     5     5     5     5     5     4     5     3     5 
    ##  4823  4825  4826  4827  4828  4829  4830  4831  4832  4833  4834  4836  4837 
    ##     5     4     4     5     5     5     5     3     5     5     3     3     5 
    ##  4840  4841  4844  4845  4847  4848  4849  4850  4851  4852  4853  4854  4857 
    ##     3     5     5     5     5     5     5     4     5     3     3     5     4 
    ##  4858  4859  4860  4861  4862  4863  4866  4867  4868  4870  4874  4877  4878 
    ##     4     5     5     5     3     4     3     5     3     5     5     5     3 
    ##  4881  4882  4883  4884  4886  4887  4888  4889  4890  4891  4892  4893  4894 
    ##     5     5     5     4     5     5     5     4     5     3     3     4     5 
    ##  4895  4896  4897  4898  4899  4902  4903  4905  4906  4907  4908  4909  4910 
    ##     5     4     4     5     5     5     5     3     5     3     4     5     4 
    ##  4912  4913  4914  4915  4916  4918  4919  4920  4922  4923  4925  4926  4928 
    ##     5     3     3     5     4     4     3     5     5     5     4     4     4 
    ##  4929  4930  4931  4932  4933  4934  4935  4936  4940  4942  4943  4944  4946 
    ##     3     3     4     4     5     5     5     3     5     5     4     3     5 
    ##  4948  4949  4950  4951  4952  4953  4954  4955  4956  4957  4958  4959  4960 
    ##     5     5     4     4     5     4     3     4     5     3     5     4     5 
    ##  4961  4963  4964  4965  4968  4970  4972  4973  4974  4975  4978  4980  4981 
    ##     4     4     5     5     3     3     4     3     5     4     5     5     3 
    ##  4982  4983  4984  4985  4987  4988  4991  4992  4993  4994  4996  4999  5000 
    ##     4     4     3     3     3     3     5     4     3     3     4     3     3 
    ##  5001  5002  5004  5006  5008  5011  5014  5016  5018  5019  5020  5021  5022 
    ##     5     5     5     3     4     4     5     5     5     4     5     4     5 
    ##  5023  5024  5026  5028  5031  5032  5034  5036  5037  5039  5040  5042  5044 
    ##     5     5     5     5     5     4     5     4     5     5     4     3     5 
    ##  5046  5048  5049  5050  5051  5052  5053  5054  5055  5057  5058  5059  5060 
    ##     5     5     3     3     3     3     5     4     3     5     4     5     5 
    ##  5063  5064  5066  5067  5068  5069  5070  5071  5073  5075  5076  5077  5079 
    ##     5     5     4     5     5     5     5     4     5     5     3     5     5 
    ##  5080  5084  5085  5086  5087  5088  5090  5091  5094  5095  5097  5098  5099 
    ##     3     5     3     5     5     5     4     3     3     4     5     5     4 
    ##  5100  5101  5102  5103  5104  5106  5107  5109  5111  5113  5114  5116  5117 
    ##     3     3     5     3     5     4     3     5     5     5     4     5     5 
    ##  5118  5121  5122  5123  5127  5128  5130  5131  5132  5133  5135  5136  5137 
    ##     5     3     4     5     3     5     4     5     4     5     5     4     4 
    ##  5138  5140  5141  5144  5145  5146  5147  5149  5150  5152  5153  5154  5155 
    ##     5     5     4     5     5     5     3     3     4     4     4     3     5 
    ##  5156  5157  5158  5159  5160  5161  5162  5163  5164  5166  5168  5169  5170 
    ##     5     5     4     5     5     5     3     4     3     3     3     5     5 
    ##  5172  5173  5177  5178  5179  5180  5181  5182  5184  5186  5187  5188  5189 
    ##     5     5     3     3     5     5     5     3     5     5     3     5     5 
    ##  5190  5192  5193  5195  5197  5199  5200  5203  5206  5207  5211  5212  5214 
    ##     5     5     4     5     4     3     5     3     3     5     4     4     4 
    ##  5216  5218  5222  5223  5227  5228  5229  5230  5232  5233  5234  5235  5236 
    ##     3     3     3     5     5     5     4     3     4     5     4     5     5 
    ##  5238  5243  5244  5245  5246  5247  5250  5251  5252  5253  5256  5257  5258 
    ##     4     5     5     3     3     3     5     5     5     5     5     3     3 
    ##  5260  5261  5262  5263  5264  5265  5267  5268  5269  5270  5272  5274  5275 
    ##     4     5     5     5     5     5     4     3     4     5     4     4     4 
    ##  5276  5278  5280  5281  5282  5283  5284  5285  5287  5288  5289  5290  5292 
    ##     5     3     5     5     5     5     4     5     3     4     5     4     5 
    ##  5294  5295  5296  5297  5298  5299  5300  5301  5303  5304  5305  5306  5308 
    ##     4     5     4     5     5     5     5     3     5     5     3     5     3 
    ##  5309  5310  5311  5316  5317  5319  5322  5323  5324  5326  5327  5328  5329 
    ##     3     5     4     5     5     3     3     5     5     5     5     3     4 
    ##  5330  5331  5333  5336  5337  5338  5339  5340  5343  5344  5346  5347  5349 
    ##     4     3     4     5     5     5     3     3     4     5     5     5     5 
    ##  5351  5352  5353  5354  5355  5356  5358  5361  5363  5364  5365  5367  5369 
    ##     5     3     4     3     5     5     5     5     5     5     5     4     4 
    ##  5370  5372  5373  5374  5375  5376  5378  5379  5380  5381  5382  5384  5387 
    ##     5     3     5     3     5     5     5     5     5     5     5     3     4 
    ##  5389  5390  5391  5392  5393  5394  5395  5396  5397  5398  5399  5400  5401 
    ##     3     5     3     4     4     5     3     3     5     4     4     4     4 
    ##  5402  5403  5405  5407  5408  5409  5410  5411  5412  5413  5414  5417  5418 
    ##     3     3     3     4     4     3     5     4     4     5     5     4     4 
    ##  5419  5422  5424  5427  5430  5431  5432  5433  5434  5436  5437  5438  5439 
    ##     5     5     3     5     5     5     5     5     5     4     5     5     3 
    ##  5442  5444  5446  5449  5450  5451  5452  5453  5454  5455  5456  5458  5460 
    ##     5     5     5     5     3     5     3     3     5     5     3     4     5 
    ##  5462  5463  5465  5466  5467  5468  5469  5470  5472  5474  5475  5476  5477 
    ##     3     3     5     3     3     5     5     5     5     4     3     5     4 
    ##  5478  5480  5482  5484  5485  5486  5487  5488  5489  5491  5492  5493  5495 
    ##     3     3     5     5     5     5     3     5     5     5     5     5     4 
    ##  5496  5497  5498  5500  5501  5504  5505  5506  5507  5508  5509  5510  5511 
    ##     5     3     5     4     3     5     5     4     5     4     4     5     5 
    ##  5512  5514  5515  5516  5517  5518  5519  5520  5521  5522  5523  5524  5525 
    ##     4     3     4     5     5     5     5     3     3     3     5     5     4 
    ##  5526  5528  5532  5533  5536  5537  5539  5542  5545  5546  5549  5550  5551 
    ##     5     4     5     3     5     5     4     4     4     4     4     3     3 
    ##  5553  5554  5555  5556  5559  5560  5562  5563  5564  5565  5566  5567  5568 
    ##     5     5     4     5     4     3     4     3     4     4     3     4     3 
    ##  5570  5572  5573  5575  5578  5579  5580  5584  5586  5587  5588  5589  5591 
    ##     3     5     5     3     5     5     4     3     4     3     4     5     5 
    ##  5592  5593  5596  5598  5600  5601  5602  5603  5604  5606  5607  5609  5610 
    ##     4     5     5     3     5     3     4     3     5     5     4     5     5 
    ##  5611  5615  5617  5619  5622  5623  5624  5625  5626  5627  5628  5629  5630 
    ##     5     4     3     4     5     4     5     3     4     4     4     4     5 
    ##  5631  5632  5633  5634  5635  5636  5638  5639  5640  5642  5643  5644  5645 
    ##     3     3     5     4     4     3     4     4     5     5     5     5     5 
    ##  5646  5647  5649  5650  5651  5652  5653  5654  5655  5656  5660  5661  5665 
    ##     5     3     4     4     3     3     4     3     5     3     3     5     4 
    ##  5666  5667  5668  5669  5670  5671  5672  5673  5675  5676  5677  5678  5679 
    ##     4     5     5     4     5     3     3     3     5     3     5     5     4 
    ##  5680  5681  5682  5683  5685  5687  5688  5691  5693  5696  5698  5699  5700 
    ##     4     5     4     3     4     4     5     5     4     5     3     4     4 
    ##  5701  5702  5703  5704  5705  5706  5707  5708  5709  5710  5711  5712  5714 
    ##     5     4     5     5     4     3     3     4     5     4     3     4     3 
    ##  5715  5716  5717  5718  5719  5720  5723  5724  5725  5728  5730  5731  5732 
    ##     3     3     5     3     5     5     5     4     3     4     5     3     5 
    ##  5733  5734  5735  5737  5738  5739  5740  5741  5742  5743  5746  5747  5748 
    ##     4     3     4     5     4     3     3     3     3     5     5     3     4 
    ##  5749  5751  5752  5753  5754  5756  5757  5758  5759  5760  5761  5763  5764 
    ##     3     5     3     3     5     3     3     5     3     5     5     4     5 
    ##  5765  5766  5769  5770  5771  5772  5774  5775  5776  5781  5782  5786  5787 
    ##     5     5     4     3     5     5     4     3     5     5     5     4     3 
    ##  5788  5790  5791  5793  5794  5795  5798  5799  5800  5802  5803  5806  5807 
    ##     5     5     5     5     3     5     5     5     4     5     4     4     5 
    ##  5808  5813  5814  5815  5818  5820  5821  5822  5824  5825  5828  5829  5830 
    ##     4     5     5     3     4     3     4     3     4     4     5     5     3 
    ##  5831  5832  5833  5835  5836  5837  5840  5841  5843  5844  5846  5847  5848 
    ##     4     3     3     5     5     5     3     3     5     4     5     4     5 
    ##  5849  5850  5851  5853  5854  5856  5857  5859  5860  5863  5864  5865  5866 
    ##     3     3     5     5     4     5     5     5     5     5     5     5     5 
    ##  5867  5868  5869  5873  5874  5877  5878  5879  5881  5882  5883  5884  5885 
    ##     3     3     5     3     4     3     3     3     4     3     3     3     3 
    ##  5887  5890  5891  5892  5893  5896  5897  5898  5899  5900  5901  5902  5903 
    ##     5     3     5     3     5     5     3     5     3     4     4     3     4 
    ##  5904  5905  5906  5908  5910  5914  5915  5916  5917  5918  5920  5921  5922 
    ##     4     5     4     3     5     5     3     5     4     3     5     3     4 
    ##  5923  5926  5927  5930  5931  5932  5933  5935  5936  5938  5939  5941  5942 
    ##     5     5     3     5     4     4     4     3     5     4     5     5     5 
    ##  5944  5945  5946  5947  5948  5949  5951  5952  5953  5954  5958  5959  5960 
    ##     4     3     5     5     3     5     4     3     5     3     4     5     3 
    ##  5962  5963  5964  5965  5967  5968  5969  5972  5973  5974  5975  5976  5978 
    ##     3     3     4     3     5     5     4     4     5     3     5     5     5 
    ##  5980  5982  5983  5984  5985  5986  5988  5990  5993  5994  5996  5997  5998 
    ##     3     5     4     5     5     3     4     4     5     4     4     3     5 
    ##  5999  6000  6004  6006  6007  6008  6009  6011  6014  6018  6019  6020  6021 
    ##     3     5     5     5     4     4     5     5     5     3     4     3     4 
    ##  6023  6024  6025  6027  6029  6031  6032  6033  6035  6037  6039  6040  6041 
    ##     5     4     3     3     5     5     3     4     5     5     4     5     4 
    ##  6042  6043  6044  6045  6047  6048  6049  6052  6053  6054  6055  6058  6059 
    ##     5     5     5     5     5     5     5     4     3     4     5     5     5 
    ##  6062  6063  6064  6065  6066  6067  6069  6070  6072  6074  6076  6078  6080 
    ##     5     5     5     3     5     4     5     3     3     5     5     4     5 
    ##  6081  6083  6084  6085  6086  6087  6088  6089  6090  6092  6095  6097  6098 
    ##     4     5     5     5     5     3     5     4     3     5     3     3     5 
    ##  6099  6100  6102  6103  6104  6105  6106  6107  6108  6110  6111  6112  6113 
    ##     3     5     3     3     3     5     4     3     3     3     5     5     5 
    ##  6114  6115  6116  6117  6118  6119  6121  6122  6123  6124  6125  6126  6127 
    ##     4     5     4     4     5     3     3     5     5     5     5     4     4 
    ##  6129  6130  6132  6133  6134  6136  6139  6140  6141  6142  6143  6145  6146 
    ##     3     5     5     5     5     5     4     3     3     3     3     4     5 
    ##  6147  6148  6149  6150  6153  6154  6155  6156  6157  6158  6159  6162  6163 
    ##     3     3     3     3     3     5     5     4     3     4     4     5     3 
    ##  6165  6166  6167  6169  6173  6174  6177  6179  6180  6181  6182  6183  6184 
    ##     3     5     5     3     5     5     3     3     5     4     5     4     4 
    ##  6186  6188  6189  6191  6194  6195  6196  6197  6198  6199  6200  6201  6202 
    ##     5     5     5     5     4     4     3     5     5     5     4     5     5 
    ##  6208  6210  6211  6213  6215  6216  6217  6218  6220  6221  6222  6223  6224 
    ##     4     5     4     3     5     5     4     3     3     3     4     3     5 
    ##  6226  6227  6228  6229  6230  6234  6237  6238  6239  6240  6242  6243  6244 
    ##     4     4     3     4     5     5     5     5     4     5     4     5     4 
    ##  6246  6247  6250  6251  6252  6255  6257  6258  6260  6261  6262  6263  6265 
    ##     4     5     4     3     4     5     5     5     5     5     5     5     5 
    ##  6266  6267  6269  6270  6272  6273  6275  6276  6277  6279  6281  6282  6283 
    ##     4     5     5     5     3     4     5     5     5     4     5     3     5 
    ##  6284  6285  6288  6289  6290  6292  6294  6296  6298  6299  6301  6302  6305 
    ##     5     5     5     4     5     5     3     4     4     4     4     5     5 
    ##  6306  6307  6308  6309  6310  6312  6313  6315  6316  6318  6319  6322  6323 
    ##     5     4     4     3     5     5     3     5     5     5     4     4     5 
    ##  6324  6327  6328  6330  6331  6334  6336  6337  6338  6340  6342  6344  6345 
    ##     4     3     5     5     5     5     4     4     5     3     3     3     4 
    ##  6346  6347  6348  6349  6350  6351  6352  6353  6357  6359  6360  6361  6363 
    ##     5     5     5     3     5     3     3     4     5     5     5     4     4 
    ##  6365  6366  6367  6368  6369  6370  6372  6374  6375  6376  6377  6379  6381 
    ##     5     3     5     3     3     5     3     5     5     5     3     5     4 
    ##  6383  6384  6385  6386  6387  6388  6389  6390  6391  6392  6393  6394  6395 
    ##     3     3     4     3     4     4     4     4     3     3     3     5     4 
    ##  6396  6398  6399  6401  6402  6404  6405  6406  6407  6408  6409  6410  6411 
    ##     3     4     5     4     5     3     4     4     5     3     5     5     3 
    ##  6413  6416  6417  6419  6421  6422  6423  6424  6425  6429  6430  6432  6433 
    ##     4     3     5     3     3     4     5     5     5     5     3     4     5 
    ##  6434  6435  6436  6438  6440  6442  6443  6445  6446  6447  6448  6449  6452 
    ##     4     4     4     5     3     5     3     3     5     5     4     4     4 
    ##  6455  6456  6457  6458  6459  6462  6465  6467  6468  6469  6470  6471  6473 
    ##     5     5     5     5     5     5     5     4     5     5     5     4     4 
    ##  6474  6475  6476  6477  6478  6479  6480  6481  6482  6485  6486  6487  6489 
    ##     4     4     3     5     5     4     5     3     5     3     3     4     3 
    ##  6490  6491  6492  6493  6497  6498  6499  6500  6501  6505  6506  6507  6510 
    ##     5     4     3     4     5     4     5     5     4     4     5     3     5 
    ##  6512  6513  6515  6516  6517  6518  6519  6520  6521  6522  6523  6524  6525 
    ##     3     3     3     4     5     5     3     5     5     3     4     5     5 
    ##  6526  6528  6529  6530  6531  6532  6533  6535  6536  6538  6539  6541  6542 
    ##     5     5     5     5     5     5     4     5     5     5     3     3     3 
    ##  6543  6545  6548  6549  6550  6551  6552  6553  6554  6555  6556  6557  6558 
    ##     3     5     5     5     4     5     3     5     5     4     5     4     5 
    ##  6559  6560  6561  6563  6565  6567  6568  6569  6573  6574  6575  6576  6577 
    ##     5     3     4     5     5     4     3     3     5     5     5     5     5 
    ##  6578  6580  6581  6582  6583  6584  6585  6586  6587  6589  6592  6594  6596 
    ##     3     3     5     5     3     4     5     5     5     5     4     5     4 
    ##  6597  6598  6599  6601  6602  6604  6605  6606  6607  6610  6611  6612  6613 
    ##     5     3     4     5     4     5     5     5     5     5     4     5     5 
    ##  6616  6617  6618  6619  6620  6624  6625  6626  6627  6628  6629  6631  6633 
    ##     5     5     3     4     3     3     5     5     4     4     4     5     3 
    ##  6634  6635  6636  6641  6642  6644  6645  6646  6647  6648  6649  6650  6651 
    ##     4     5     5     5     5     3     4     5     5     4     3     5     3 
    ##  6653  6655  6656  6657  6658  6659  6660  6663  6665  6666  6669  6670  6671 
    ##     4     4     3     4     3     5     5     4     4     3     5     3     5 
    ##  6672  6673  6674  6676  6677  6679  6680  6682  6683  6687  6689  6691  6692 
    ##     4     5     5     3     4     3     5     5     5     5     4     5     5 
    ##  6693  6694  6695  6698  6699  6700  6701  6702  6703  6706  6707  6709  6710 
    ##     5     5     5     5     3     4     4     3     4     4     5     4     5 
    ##  6714  6717  6718  6720  6721  6726  6727  6729  6730  6731  6732  6733  6737 
    ##     3     5     3     4     5     5     4     5     5     5     5     4     4 
    ##  6738  6740  6741  6742  6743  6744  6745  6748  6749  6750  6752  6754  6757 
    ##     3     3     5     3     5     3     4     3     5     5     5     5     5 
    ##  6759  6762  6763  6764  6765  6766  6767  6768  6769  6770  6773  6774  6777 
    ##     5     3     5     5     5     5     5     5     3     5     3     3     4 
    ##  6782  6783  6784  6786  6787  6789  6792  6793  6794  6795  6796  6797  6798 
    ##     5     4     5     5     5     5     5     4     3     5     5     3     4 
    ##  6799  6802  6803  6804  6805  6806  6808  6809  6810  6814  6815  6818  6819 
    ##     4     5     4     5     5     5     5     3     5     3     5     4     3 
    ##  6820  6822  6824  6825  6827  6828  6832  6834  6835  6836  6837  6839  6841 
    ##     3     3     4     3     3     5     5     3     5     5     3     3     5 
    ##  6843  6844  6847  6848  6849  6851  6852  6853  6854  6855  6856  6857  6858 
    ##     5     4     4     5     5     5     3     3     5     5     5     3     5 
    ##  6859  6861  6863  6865  6866  6867  6868  6869  6870  6871  6872  6876  6880 
    ##     5     5     3     3     4     3     5     5     3     5     5     5     3 
    ##  6881  6883  6884  6885  6886  6889  6890  6891  6892  6893  6894  6895  6896 
    ##     4     4     4     5     3     4     5     4     4     5     5     3     5 
    ##  6897  6898  6899  6901  6902  6903  6904  6908  6909  6910  6911  6914  6916 
    ##     5     5     4     5     4     5     4     3     4     3     3     5     4 
    ##  6917  6918  6919  6920  6921  6923  6925  6927  6929  6930  6931  6935  6936 
    ##     3     5     3     4     5     4     5     5     5     4     4     3     5 
    ##  6938  6939  6940  6941  6942  6943  6944  6945  6946  6947  6948  6949  6950 
    ##     3     4     5     5     5     5     5     4     5     4     5     4     5 
    ##  6952  6953  6955  6956  6957  6958  6959  6960  6962  6963  6964  6965  6967 
    ##     5     4     5     3     5     5     4     5     5     5     5     4     3 
    ##  6968  6969  6970  6971  6972  6974  6975  6977  6978  6979  6980  6981  6982 
    ##     5     5     5     5     4     5     4     5     5     5     4     5     4 
    ##  6983  6984  6985  6986  6989  6990  6991  6993  6995  6996  6997  7001  7002 
    ##     4     5     5     3     5     5     5     5     4     5     4     5     5 
    ##  7005  7006  7007  7008  7010  7012  7013  7015  7016  7017  7019  7020  7023 
    ##     4     5     4     5     3     5     4     5     5     4     4     5     5 
    ##  7025  7026  7027  7028  7029  7033  7034  7035  7036  7037  7038  7040  7041 
    ##     3     5     3     3     3     5     4     4     5     3     4     5     4 
    ##  7042  7044  7045  7047  7051  7052  7053  7054  7056  7057  7059  7060  7061 
    ##     4     4     5     4     4     4     5     3     5     5     3     5     5 
    ##  7063  7066  7067  7068  7069  7070  7071  7072  7074  7075  7076  7077  7078 
    ##     5     4     4     5     5     3     5     3     5     5     5     5     3 
    ##  7079  7080  7081  7082  7083  7084  7086  7088  7089  7090  7091  7092  7094 
    ##     5     5     5     3     3     4     3     3     3     5     4     5     5 
    ##  7095  7096  7098  7099  7101  7102  7103  7104  7105  7106  7108  7109  7110 
    ##     5     4     5     4     5     3     5     5     4     5     5     5     5 
    ##  7111  7112  7113  7114  7116  7118  7120  7121  7123  7124  7125  7127  7130 
    ##     5     5     5     5     4     5     3     5     5     5     4     5     4 
    ##  7131  7132  7133  7134  7136  7137  7138  7139  7140  7141  7143  7144  7145 
    ##     3     3     5     5     5     5     4     3     5     5     5     4     5 
    ##  7146  7148  7149  7150  7152  7153  7154  7156  7157  7158  7159  7160  7161 
    ##     3     5     3     3     5     4     3     4     5     3     4     5     5 
    ##  7162  7163  7164  7167  7168  7169  7170  7171  7172  7173  7175  7176  7177 
    ##     5     5     4     5     4     5     3     4     4     4     3     4     5 
    ##  7178  7180  7181  7182  7184  7185  7187  7188  7189  7190  7191  7192  7193 
    ##     5     5     5     4     3     4     5     5     3     5     5     5     5 
    ##  7194  7195  7197  7198  7199  7200  7201  7203  7204  7206  7207  7209  7210 
    ##     5     3     5     3     3     3     5     4     4     4     4     5     5 
    ##  7211  7213  7214  7215  7216  7217  7218  7219  7221  7222  7223  7224  7226 
    ##     5     5     5     5     5     3     4     5     5     5     5     5     5 
    ##  7227  7228  7229  7231  7233  7234  7235  7236  7237  7239  7241  7242  7243 
    ##     3     5     5     5     5     5     5     5     5     5     5     3     5 
    ##  7244  7245  7252  7253  7254  7255  7256  7257  7258  7259  7262  7263  7264 
    ##     4     3     4     5     4     5     4     3     5     5     5     4     5 
    ##  7268  7272  7273  7274  7275  7276  7277  7278  7280  7281  7282  7283  7285 
    ##     5     5     3     3     5     4     5     5     5     3     5     5     4 
    ##  7286  7287  7288  7289  7290  7291  7292  7293  7294  7295  7296  7297  7300 
    ##     5     5     4     4     5     3     3     5     3     5     5     5     5 
    ##  7302  7305  7306  7307  7308  7309  7312  7313  7315  7316  7318  7319  7320 
    ##     5     5     5     5     5     4     3     5     5     4     5     4     5 
    ##  7321  7324  7325  7327  7330  7332  7333  7334  7335  7336  7337  7338  7340 
    ##     3     5     5     5     3     5     4     5     5     4     5     3     5 
    ##  7341  7342  7344  7345  7346  7347  7348  7350  7351  7353  7354  7357  7359 
    ##     3     5     4     5     5     3     5     5     4     5     5     5     5 
    ##  7360  7361  7362  7363  7366  7368  7369  7371  7372  7373  7374  7375  7377 
    ##     5     4     4     5     5     5     5     5     3     5     5     5     5 
    ##  7378  7380  7381  7382  7383  7384  7385  7386  7387  7389  7391  7395  7397 
    ##     5     4     5     4     4     5     3     5     3     3     4     4     5 
    ##  7398  7399  7400  7401  7402  7403  7406  7407  7408  7409  7410  7412  7413 
    ##     3     4     5     5     5     5     5     5     3     5     4     5     5 
    ##  7414  7416  7417  7418  7419  7420  7421  7422  7424  7425  7426  7427  7428 
    ##     5     5     4     5     4     5     5     3     4     5     5     3     5 
    ##  7430  7431  7433  7435  7436  7437  7438  7440  7442  7444  7445  7449  7450 
    ##     4     4     5     5     5     5     5     5     5     4     5     5     5 
    ##  7451  7452  7454  7455  7456  7457  7458  7460  7461  7462  7464  7465  7467 
    ##     5     4     5     3     5     4     4     5     3     5     4     3     4 
    ##  7471  7473  7474  7475  7476  7477  7478  7481  7482  7483  7484  7485  7487 
    ##     3     5     5     3     5     4     4     3     5     5     5     5     5 
    ##  7488  7490  7491  7494  7496  7498  7500  7501  7503  7504  7506  7508  7511 
    ##     4     5     5     3     4     4     4     5     4     5     4     3     5 
    ##  7512  7513  7516  7518  7519  7520  7521  7522  7524  7526  7527  7528  7533 
    ##     5     3     3     4     5     5     5     5     3     3     3     3     4 
    ##  7534  7535  7536  7537  7538  7539  7540  7541  7542  7543  7544  7546  7547 
    ##     3     5     5     5     5     5     5     4     5     5     5     5     5 
    ##  7549  7552  7553  7554  7555  7556  7557  7558  7559  7560  7561  7563  7564 
    ##     4     5     3     5     4     4     3     3     5     5     5     5     4 
    ##  7565  7567  7569  7570  7571  7574  7575  7576  7577  7578  7579  7580  7581 
    ##     5     5     5     5     5     3     5     5     5     5     4     5     5 
    ##  7582  7583  7586  7588  7590  7591  7592  7594  7595  7597  7598  7599  7600 
    ##     5     5     5     4     4     4     5     5     5     5     4     5     4 
    ##  7602  7603  7604  7605  7607  7608  7609  7610  7611  7613  7615  7617  7618 
    ##     5     5     4     5     5     5     5     5     4     5     5     3     4 
    ##  7619  7620  7621  7622  7623  7624  7626  7627  7628  7631  7633  7635  7637 
    ##     3     3     5     4     5     4     5     5     5     4     5     5     4 
    ##  7639  7640  7642  7643  7644  7645  7647  7648  7650  7651  7652  7654  7655 
    ##     5     4     5     5     5     5     4     5     4     5     5     5     5 
    ##  7656  7657  7658  7660  7661  7662  7664  7667  7669  7670  7671  7672  7675 
    ##     5     4     3     5     5     4     5     4     5     5     5     5     5 
    ##  7676  7677  7678  7680  7681  7682  7683  7684  7685  7686  7688  7689  7691 
    ##     5     3     4     5     5     4     5     4     4     3     5     5     5 
    ##  7694  7695  7696  7697  7698  7699  7700  7702  7703  7704  7705  7708  7709 
    ##     4     5     4     5     5     3     5     3     5     3     3     5     4 
    ##  7710  7711  7712  7713  7714  7716  7717  7718  7720  7721  7723  7724  7725 
    ##     5     3     4     5     5     4     5     5     4     5     3     5     5 
    ##  7726  7727  7730  7731  7732  7733  7734  7736  7737  7738  7739  7740  7741 
    ##     5     5     5     3     4     4     5     4     4     5     5     5     4 
    ##  7742  7745  7746  7748  7749  7750  7751  7752  7753  7754  7755  7756  7757 
    ##     3     5     5     3     5     5     5     5     5     3     5     3     5 
    ##  7758  7760  7762  7763  7764  7766  7767  7768  7770  7772  7773  7774  7775 
    ##     5     4     4     5     5     5     4     5     5     5     3     5     3 
    ##  7778  7779  7780  7781  7782  7784  7785  7786  7787  7788  7789  7790  7791 
    ##     5     3     5     3     5     5     4     5     5     5     5     5     5 
    ##  7792  7794  7796  7798  7799  7801  7802  7803  7804  7805  7806  7807  7808 
    ##     5     5     3     3     5     5     4     5     4     5     5     5     5 
    ##  7809  7810  7812  7813  7814  7815  7816  7817  7818  7819  7820  7822  7823 
    ##     3     5     5     5     4     5     5     5     5     5     5     4     3 
    ##  7824  7825  7826  7827  7828  7830  7831  7832  7834  7835  7836  7837  7838 
    ##     3     5     5     5     4     3     5     5     3     5     5     3     5 
    ##  7839  7841  7842  7843  7845  7846  7847  7848  7850  7851  7852  7853  7854 
    ##     4     5     5     5     5     4     5     5     4     5     5     5     3 
    ##  7858  7859  7860  7861  7862  7863  7864  7865  7866  7867  7868  7870  7871 
    ##     4     4     3     5     5     4     3     3     5     5     5     3     5 
    ##  7872  7875  7876  7877  7879  7881  7884  7885  7886  7887  7888  7889  7890 
    ##     5     5     4     5     5     5     5     4     5     5     4     5     5 
    ##  7891  7893  7894  7895  7896  7897  7898  7899  7900  7901  7903  7904  7905 
    ##     4     5     3     4     5     5     3     4     3     5     5     4     3 
    ##  7906  7909  7912  7913  7914  7915  7916  7917  7919  7921  7922  7923  7925 
    ##     5     4     3     5     5     5     5     5     4     4     5     5     5 
    ##  7929  7930  7931  7935  7936  7939  7940  7941  7942  7944  7945  7946  7948 
    ##     5     5     5     4     3     5     5     3     5     5     5     5     5 
    ##  7949  7950  7952  7955  7956  7957  7961  7962  7964  7967  7968  7969  7971 
    ##     4     5     3     4     5     5     5     5     4     5     3     3     5 
    ##  7972  7973  7974  7976  7979  7982  7984  7985  7987  7988  7989  7990  7993 
    ##     4     5     5     3     5     4     5     5     3     4     5     4     5 
    ##  7995  7996  8001  8002  8003  8004  8005  8006  8007  8008  8009  8010  8011 
    ##     4     5     5     5     4     5     5     5     4     5     5     5     5 
    ##  8012  8013  8014  8015  8018  8019  8023  8024  8025  8026  8028  8029  8031 
    ##     4     3     4     5     5     4     5     5     3     5     5     3     4 
    ##  8032  8033  8036  8037  8038  8040  8041  8043  8044  8045  8046  8049  8050 
    ##     3     4     5     4     5     4     4     5     5     5     4     5     4 
    ##  8051  8052  8053  8054  8056  8059  8060  8061  8063  8064  8065  8066  8067 
    ##     5     5     3     3     5     4     5     5     5     5     5     3     3 
    ##  8068  8069  8070  8071  8074  8075  8077  8078  8080  8082  8083  8084  8088 
    ##     3     4     5     5     3     3     4     5     5     5     4     4     4 
    ##  8089  8090  8091  8093  8094  8096  8098  8100  8101  8102  8104  8106  8108 
    ##     5     5     5     5     3     4     5     4     5     3     4     5     5 
    ##  8110  8113  8114  8115  8116  8117  8118  8124  8125  8126  8127  8128  8129 
    ##     3     5     5     5     5     3     3     5     5     5     5     5     5 
    ##  8131  8133  8134  8136  8137  8139  8140  8141  8143  8144  8146  8147  8149 
    ##     5     5     5     5     4     5     5     4     5     5     5     3     4 
    ##  8152  8153  8154  8155  8156  8158  8160  8163  8164  8165  8167  8173  8175 
    ##     4     5     3     3     3     5     5     4     5     4     3     4     5 
    ##  8176  8178  8180  8182  8185  8186  8187  8188  8189  8190  8192  8193  8194 
    ##     3     3     5     5     4     5     4     5     5     5     5     5     5 
    ##  8195  8197  8198  8199  8200  8201  8202  8203  8204  8207  8208  8210  8211 
    ##     3     5     5     3     3     3     3     5     5     3     4     5     5 
    ##  8212  8215  8216  8217  8218  8219  8221  8222  8223  8225  8226  8227  8229 
    ##     5     3     3     3     5     4     5     5     4     5     4     5     4 
    ##  8230  8231  8232  8234  8235  8236  8240  8241  8245  8247  8249  8250  8252 
    ##     5     5     5     5     4     5     5     3     5     5     5     4     5 
    ##  8253  8254  8255  8256  8257  8258  8259  8261  8262  8264  8265  8266  8267 
    ##     5     5     4     3     3     4     5     5     5     5     5     4     4 
    ##  8268  8269  8270  8271  8272  8273  8274  8276  8277  8278  8279  8280  8282 
    ##     3     5     5     5     5     5     4     3     4     3     4     3     4 
    ##  8284  8285  8287  8288  8289  8290  8291  8292  8293  8295  8297  8298  8300 
    ##     5     5     5     3     5     4     5     5     5     3     3     5     4 
    ##  8301  8302  8303  8304  8306  8309  8310  8311  8312  8315  8316  8317  8318 
    ##     3     5     5     4     3     5     3     3     3     5     3     5     5 
    ##  8321  8323  8324  8325  8326  8327  8328  8330  8335  8337  8338  8339  8340 
    ##     3     3     3     5     3     5     4     3     4     5     4     5     5 
    ##  8341  8342  8343  8344  8345  8347  8348  8350  8351  8352  8353  8354  8357 
    ##     3     4     5     3     5     5     3     3     3     3     5     5     3 
    ##  8361  8362  8363  8364  8365  8366  8367  8369  8371  8372  8373  8374  8375 
    ##     3     5     5     5     4     3     4     3     3     3     4     5     4 
    ##  8376  8377  8378  8380  8381  8382  8383  8384  8385  8386  8387  8388  8389 
    ##     4     3     5     5     3     4     4     5     5     5     4     5     3 
    ##  8390  8392  8393  8394  8395  8396  8397  8398  8399  8405  8406  8408  8409 
    ##     5     3     5     4     5     5     5     3     5     3     5     5     4 
    ##  8410  8415  8417  8419  8420  8421  8423  8424  8426  8427  8428  8430  8431 
    ##     5     5     5     5     5     4     5     5     5     3     5     5     3 
    ##  8432  8434  8436  8437  8438  8439  8441  8442  8445  8448  8449  8450  8452 
    ##     3     5     5     5     4     5     4     4     5     5     4     5     5 
    ##  8453  8455  8456  8457  8458  8459  8460  8461  8462  8463  8464  8466  8468 
    ##     4     4     5     5     5     5     4     5     4     5     4     5     5 
    ##  8469  8470  8471  8472  8473  8475  8476  8477  8478  8479  8480  8481  8482 
    ##     5     4     3     4     5     5     5     3     3     3     3     5     5 
    ##  8485  8487  8488  8489  8490  8492  8493  8495  8496  8498  8499  8500  8501 
    ##     5     3     5     5     5     5     5     4     5     5     4     5     5 
    ##  8502  8503  8504  8505  8506  8507  8508  8510  8511  8512  8514  8515  8516 
    ##     4     5     4     5     5     3     5     4     5     5     5     4     5 
    ##  8517  8518  8519  8520  8521  8524  8526  8527  8529  8530  8531  8532  8533 
    ##     3     5     3     4     3     5     4     5     4     5     3     5     5 
    ##  8534  8535  8536  8537  8538  8540  8541  8542  8545  8546  8547  8548  8549 
    ##     5     4     3     3     5     4     3     3     5     3     4     5     5 
    ##  8551  8552  8553  8555  8559  8560  8561  8562  8563  8565  8569  8570  8571 
    ##     3     5     5     5     5     3     3     5     3     4     5     5     4 
    ##  8572  8573  8574  8575  8577  8579  8582  8583  8584  8585  8586  8587  8588 
    ##     5     4     3     3     4     3     5     5     4     5     4     5     5 
    ##  8590  8592  8593  8594  8597  8599  8600  8601  8603  8605  8606  8607  8608 
    ##     5     4     5     5     5     5     5     3     5     5     5     5     5 
    ##  8609  8610  8612  8613  8614  8616  8617  8618  8619  8620  8621  8622  8626 
    ##     5     5     5     5     4     5     5     5     5     5     5     5     4 
    ##  8627  8628  8629  8630  8631  8632  8633  8635  8636  8637  8638  8641  8646 
    ##     5     5     5     5     5     5     5     4     4     3     5     5     3 
    ##  8647  8648  8649  8651  8653  8654  8656  8657  8659  8660  8661  8662  8664 
    ##     5     5     5     5     5     5     5     5     5     3     5     5     5 
    ##  8665  8666  8667  8668  8670  8672  8673  8674  8675  8676  8678  8679  8680 
    ##     3     5     5     5     3     5     5     5     5     5     5     5     4 
    ##  8682  8683  8684  8685  8689  8690  8691  8692  8693  8694  8696  8697  8698 
    ##     3     4     4     3     5     5     4     5     5     3     4     3     3 
    ##  8699  8700  8701  8703  8705  8706  8708  8709  8710  8711  8716  8717  8718 
    ##     5     3     5     5     4     5     5     5     5     5     5     5     5 
    ##  8719  8721  8722  8723  8725  8726  8727  8728  8729  8733  8734  8735  8737 
    ##     3     5     5     3     3     3     3     3     5     3     4     5     4 
    ##  8738  8739  8740  8741  8742  8743  8746  8747  8749  8750  8752  8753  8754 
    ##     5     4     5     3     4     4     4     4     3     4     5     3     3 
    ##  8755  8756  8757  8758  8759  8760  8761  8762  8763  8764  8765  8766  8767 
    ##     5     5     4     5     3     5     4     5     4     3     5     5     3 
    ##  8768  8771  8772  8773  8774  8775  8776  8777  8778  8779  8780  8781  8782 
    ##     4     5     3     5     5     3     3     4     3     5     4     5     3 
    ##  8783  8785  8787  8788  8789  8790  8791  8792  8793  8795  8796  8797  8798 
    ##     3     5     5     5     3     3     3     5     5     5     5     3     5 
    ##  8801  8802  8803  8804  8806  8809  8811  8812  8813  8814  8815  8817  8818 
    ##     5     5     3     4     5     5     4     3     5     5     4     5     5 
    ##  8819  8820  8821  8822  8823  8824  8830  8831  8832  8833  8834  8839  8840 
    ##     3     5     3     3     5     4     5     4     5     3     5     3     5 
    ##  8842  8843  8844  8845  8847  8848  8849  8850  8854  8857  8858  8861  8862 
    ##     5     5     4     5     4     4     3     5     5     5     5     5     5 
    ##  8863  8864  8865  8868  8869  8871  8873  8874  8875  8877  8880  8882  8883 
    ##     5     5     3     4     5     3     5     5     3     4     3     4     5 
    ##  8885  8886  8887  8888  8889  8891  8894  8895  8896  8898  8899  8900  8901 
    ##     3     5     4     3     5     3     5     4     5     5     3     4     3 
    ##  8902  8903  8904  8905  8906  8907  8908  8909  8911  8914  8915  8916  8917 
    ##     5     5     5     4     4     5     5     5     5     3     5     3     5 
    ##  8918  8919  8921  8922  8924  8925  8926  8927  8929  8930  8933  8934  8935 
    ##     3     5     5     5     5     5     5     3     5     4     4     3     5 
    ##  8938  8939  8940  8941  8943  8947  8948  8949  8950  8951  8953  8954  8955 
    ##     5     5     5     3     3     4     5     3     5     5     5     5     5 
    ##  8956  8957  8958  8959  8960  8961  8964  8968  8971  8973  8974  8975  8978 
    ##     5     3     3     3     3     5     3     3     5     5     3     5     3 
    ##  8980  8981  8983  8985  8987  8989  8990  8991  8992  8993  8994  8995  8996 
    ##     5     4     5     5     5     4     5     5     5     5     5     5     5 
    ##  8997  8998  8999  9000  9001  9002  9003  9004  9005  9006  9007  9009  9010 
    ##     3     5     3     5     5     3     5     3     4     4     4     3     5 
    ##  9011  9013  9015  9016  9017  9018  9019  9020  9021  9022  9023  9024  9025 
    ##     4     3     5     5     5     5     3     4     4     5     5     5     5 
    ##  9026  9027  9028  9029  9032  9033  9034  9035  9037  9038  9039  9041  9042 
    ##     3     5     4     5     5     5     5     3     3     3     5     5     5 
    ##  9043  9044  9045  9046  9048  9050  9051  9052  9053  9054  9057  9058  9059 
    ##     5     5     4     5     5     3     5     5     5     3     5     5     5 
    ##  9061  9063  9064  9065  9066  9068  9069  9071  9074  9075  9076  9077  9079 
    ##     4     5     4     5     3     5     5     4     5     5     5     3     5 
    ##  9080  9081  9085  9086  9089  9092  9094  9095  9097  9098  9100  9103  9105 
    ##     4     3     5     5     4     3     3     3     5     4     5     5     5 
    ##  9106  9107  9108  9109  9110  9111  9112  9114  9115  9118  9119  9120  9122 
    ##     5     5     3     3     5     5     4     5     5     5     5     3     5 
    ##  9123  9124  9125  9126  9128  9130  9133  9137  9138  9139  9140  9141  9145 
    ##     5     5     5     4     3     5     5     5     4     5     5     4     5 
    ##  9146  9147  9149  9150  9151  9152  9153  9155  9157  9158  9159  9161  9163 
    ##     3     5     4     5     5     5     5     4     5     3     5     5     3 
    ##  9164  9165  9166  9167  9168  9170  9175  9179  9180  9181  9183  9185  9186 
    ##     5     5     4     5     3     5     5     5     4     5     5     5     5 
    ##  9187  9188  9189  9190  9192  9193  9194  9195  9196  9197  9198  9199  9200 
    ##     4     5     5     4     5     5     5     3     4     5     3     3     5 
    ##  9202  9205  9206  9208  9209  9210  9212  9213  9214  9215  9216  9218  9219 
    ##     5     5     3     4     5     5     5     5     5     3     5     5     5 
    ##  9221  9222  9224  9226  9228  9229  9230  9231  9233  9234  9235  9236  9237 
    ##     5     3     5     5     3     5     5     3     5     4     5     3     5 
    ##  9238  9241  9243  9246  9248  9249  9251  9253  9254  9255  9256  9257  9258 
    ##     5     5     5     5     3     5     5     4     5     5     5     5     5 
    ##  9259  9260  9261  9262  9263  9264  9265  9267  9268  9269  9270  9272  9273 
    ##     4     3     3     5     3     5     3     4     3     5     5     5     5 
    ##  9274  9275  9277  9278  9279  9280  9281  9283  9287  9288  9289  9291  9292 
    ##     4     3     3     5     4     5     4     5     5     4     3     4     3 
    ##  9294  9296  9298  9299  9302  9304  9305  9306  9307  9308  9309  9310  9313 
    ##     3     3     3     5     4     4     5     5     5     3     3     5     5 
    ##  9316  9317  9318  9319  9320  9321  9323  9325  9326  9327  9328  9329  9330 
    ##     5     4     4     3     5     3     5     5     4     5     5     3     5 
    ##  9331  9332  9333  9334  9335  9336  9338  9339  9340  9343  9345  9346  9347 
    ##     3     5     4     4     5     5     4     5     5     4     5     5     5 
    ##  9348  9349  9350  9351  9352  9353  9356  9358  9359  9360  9362  9363  9364 
    ##     3     4     3     5     5     5     5     4     5     5     5     5     5 
    ##  9365  9366  9369  9370  9372  9373  9374  9375  9377  9378  9379  9380  9384 
    ##     5     3     5     4     5     5     3     4     5     5     5     5     4 
    ##  9385  9386  9387  9389  9390  9392  9393  9394  9396  9397  9399  9400  9401 
    ##     5     3     3     4     5     5     5     5     4     5     5     3     5 
    ##  9403  9404  9405  9406  9408  9410  9412  9413  9415  9419  9420  9422  9423 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     4 
    ##  9424  9425  9426  9429  9432  9433  9434  9436  9437  9438  9439  9440  9441 
    ##     3     4     5     5     5     3     4     5     5     3     4     5     5 
    ##  9443  9450  9452  9453  9454  9455  9456  9457  9458  9459  9460  9463  9466 
    ##     4     5     5     5     5     5     5     5     5     5     4     5     3 
    ##  9470  9472  9473  9474  9475  9476  9477  9482  9483  9485  9486  9488  9489 
    ##     3     3     5     4     5     5     4     5     5     5     3     5     4 
    ##  9490  9491  9492  9494  9495  9497  9498  9499  9501  9502  9503  9504  9508 
    ##     5     3     3     5     5     4     3     5     3     5     5     5     5 
    ##  9509  9510  9512  9513  9514  9515  9518  9520  9521  9522  9524  9525  9526 
    ##     5     5     5     5     5     5     4     3     4     3     5     4     5 
    ##  9527  9528  9529  9530  9532  9533  9534  9537  9538  9539  9542  9543  9544 
    ##     3     5     5     5     5     5     5     5     5     5     3     5     3 
    ##  9545  9546  9547  9548  9550  9551  9552  9554  9558  9559  9560  9561  9562 
    ##     3     4     5     5     5     3     3     5     5     5     5     5     5 
    ##  9563  9564  9565  9566  9567  9569  9570  9573  9574  9575  9576  9577  9578 
    ##     5     5     3     5     3     3     5     3     5     4     4     5     5 
    ##  9579  9580  9582  9583  9584  9585  9586  9588  9589  9590  9591  9594  9596 
    ##     3     5     4     5     5     4     4     5     5     4     3     5     5 
    ##  9598  9599  9601  9602  9604  9605  9606  9607  9608  9611  9615  9616  9617 
    ##     5     4     4     3     3     3     5     3     3     4     5     3     5 
    ##  9618  9619  9620  9621  9623  9624  9625  9626  9629  9632  9634  9635  9636 
    ##     5     3     4     4     5     5     3     5     5     5     3     3     5 
    ##  9638  9639  9640  9643  9644  9645  9647  9648  9649  9650  9654  9656  9658 
    ##     4     5     5     5     3     5     5     4     5     3     3     4     3 
    ##  9659  9660  9661  9663  9664  9665  9666  9667  9668  9673  9674  9675  9676 
    ##     5     5     5     4     5     5     3     3     3     3     5     3     5 
    ##  9677  9678  9679  9680  9681  9682  9683  9684  9685  9686  9687  9688  9689 
    ##     3     4     5     5     5     5     4     5     5     3     3     5     4 
    ##  9690  9691  9694  9695  9696  9699  9700  9701  9703  9704  9706  9707  9708 
    ##     4     4     4     5     5     3     5     5     5     4     3     5     5 
    ##  9709  9710  9712  9715  9716  9717  9718  9719  9720  9722  9723  9724  9725 
    ##     5     5     5     3     3     5     5     4     3     5     3     5     5 
    ##  9726  9727  9728  9733  9734  9735  9737  9738  9741  9742  9745  9746  9747 
    ##     5     5     3     5     5     5     3     5     3     4     5     5     3 
    ##  9749  9750  9751  9752  9754  9756  9758  9759  9761  9765  9766  9768  9769 
    ##     5     4     5     5     5     5     5     4     5     4     5     5     5 
    ##  9770  9771  9772  9773  9774  9775  9777  9778  9779  9780  9781  9783  9784 
    ##     5     4     5     4     3     5     5     5     5     5     5     5     3 
    ##  9785  9787  9788  9789  9790  9791  9792  9793  9794  9795  9796  9798  9799 
    ##     3     5     3     5     3     4     4     5     5     5     5     5     3 
    ##  9800  9802  9803  9804  9807  9808  9809  9811  9812  9813  9814  9815  9817 
    ##     5     4     5     5     5     5     5     5     5     3     5     5     5 
    ##  9818  9819  9820  9826  9829  9830  9831  9833  9834  9835  9836  9838  9839 
    ##     5     5     5     5     3     5     3     5     3     4     4     5     5 
    ##  9840  9841  9842  9843  9844  9847  9848  9849  9850  9852  9853  9855  9856 
    ##     5     5     4     4     4     4     5     5     5     5     5     5     3 
    ##  9857  9859  9860  9861  9863  9864  9865  9867  9868  9869  9870  9871  9872 
    ##     5     5     4     4     4     5     4     5     5     3     5     4     3 
    ##  9874  9877  9878  9879  9880  9881  9882  9883  9884  9885  9887  9889  9893 
    ##     5     5     5     4     5     5     5     4     5     5     5     5     4 
    ##  9895  9896  9897  9899  9900  9901  9902  9903  9904  9905  9906  9907  9908 
    ##     5     5     5     3     3     5     3     3     5     5     3     5     3 
    ##  9909  9911  9912  9913  9914  9915  9916  9917  9918  9920  9921  9922  9923 
    ##     5     5     4     5     5     5     5     5     5     3     5     5     3 
    ##  9926  9928  9929  9930  9931  9932  9933  9934  9935  9936  9937  9939  9941 
    ##     5     3     4     5     5     4     5     5     4     3     5     5     5 
    ##  9942  9945  9947  9949  9951  9952  9953  9955  9956  9957  9959  9960  9962 
    ##     5     5     3     5     5     5     5     5     4     5     5     5     3 
    ##  9963  9965  9966  9968  9970  9971  9972  9974  9976  9977  9978  9979  9980 
    ##     5     3     5     4     3     5     4     5     5     3     5     4     4 
    ##  9981  9982  9983  9985  9988  9989  9990  9991  9993  9994  9995  9996  9997 
    ##     5     4     3     5     5     3     5     5     5     5     3     5     3 
    ##  9998  9999 10000 10001 10002 10003 10005 10006 10007 10008 10009 10010 10011 
    ##     4     4     5     5     3     5     5     5     4     5     5     3     5 
    ## 10012 10013 10014 10015 10016 10017 10018 10019 10020 10021 10022 10023 10024 
    ##     5     5     3     3     4     5     5     5     5     5     4     5     5 
    ## 10025 10026 10028 10029 10030 10032 10034 10035 10036 10037 10039 10040 10041 
    ##     5     5     5     4     5     4     3     5     5     4     4     5     5 
    ## 10043 10044 10045 10046 10047 10049 10051 10052 10053 10054 10055 10057 10058 
    ##     5     5     5     4     3     4     4     5     5     3     4     5     4 
    ## 10061 10062 10063 10064 10065 10066 10068 10069 10070 10071 10072 10073 10074 
    ##     5     5     5     3     3     5     5     5     5     5     3     3     4 
    ## 10075 10076 10077 10078 10079 10080 10081 10082 10084 10085 10087 10089 10091 
    ##     5     5     3     4     5     5     5     5     5     3     5     4     4 
    ## 10092 10093 10095 10097 10098 10101 10102 10103 10105 10107 10108 10109 10111 
    ##     3     5     4     4     4     3     5     5     5     4     4     3     5 
    ## 10112 10113 10114 10117 10121 10123 10124 10125 10127 10130 10131 10132 10133 
    ##     5     4     5     4     4     5     3     4     5     5     5     5     4 
    ## 10134 10137 10138 10139 10140 10141 10142 10144 10145 10146 10147 10148 10149 
    ##     5     3     5     4     4     3     4     5     4     5     5     5     5 
    ## 10151 10152 10154 10155 10158 10159 10160 10161 10162 10163 10164 10167 10169 
    ##     5     5     3     3     5     3     3     3     5     4     5     3     5 
    ## 10170 10171 10172 10173 10174 10175 10176 10178 10179 10182 10183 10184 10185 
    ##     5     5     3     3     5     5     5     5     5     3     5     5     3 
    ## 10186 10188 10189 10190 10191 10192 10194 10195 10197 10198 10199 10200 10201 
    ##     4     5     3     5     5     5     5     5     3     5     3     5     5 
    ## 10202 10203 10205 10212 10213 10214 10216 10217 10218 10219 10221 10223 10225 
    ##     3     5     5     5     4     3     3     4     3     4     5     3     5 
    ## 10226 10229 10230 10231 10232 10233 10234 10235 10236 10237 10238 10239 10241 
    ##     4     4     3     5     5     5     5     3     5     5     5     3     5 
    ## 10242 10244 10246 10247 10248 10249 10250 10251 10254 10255 10256 10257 10258 
    ##     3     5     5     5     5     4     4     4     3     5     5     5     3 
    ## 10259 10261 10262 10264 10265 10267 10268 10271 10272 10273 10274 10276 10279 
    ##     5     3     4     5     5     5     3     5     5     3     5     4     5 
    ## 10280 10281 10282 10283 10285 10286 10287 10288 10289 10290 10291 10292 10293 
    ##     5     5     5     3     3     3     5     5     3     3     5     5     3 
    ## 10294 10296 10297 10298 10300 10301 10302 10303 10304 10305 10306 10309 10310 
    ##     3     5     3     3     4     3     5     3     5     5     4     5     5 
    ## 10311 10313 10316 10318 10321 10323 10324 10325 10326 10328 10331 10332 10334 
    ##     5     5     3     5     5     5     3     5     4     5     3     3     5 
    ## 10335 10336 10337 10338 10339 10340 10341 10343 10345 10346 10348 10350 10351 
    ##     5     5     5     4     5     3     4     5     5     4     5     4     5 
    ## 10352 10353 10354 10355 10356 10357 10358 10359 10360 10361 10362 10365 10366 
    ##     5     3     5     5     5     5     5     5     5     3     3     5     5 
    ## 10367 10368 10369 10370 10371 10373 10374 10375 10377 10378 10379 10381 10385 
    ##     5     3     3     5     5     5     5     4     5     3     3     4     5 
    ## 10386 10387 10389 10390 10391 10392 10393 10396 10397 10398 10399 10401 10402 
    ##     5     4     3     5     4     4     4     5     5     5     5     4     5 
    ## 10405 10407 10408 10410 10411 10412 10413 10415 10416 10417 10418 10419 10421 
    ##     3     5     5     5     3     5     5     5     4     3     5     3     4 
    ## 10422 10423 10424 10425 10427 10428 10429 10430 10432 10433 10435 10437 10438 
    ##     3     5     5     3     5     5     3     5     5     4     3     4     3 
    ## 10440 10442 10443 10444 10445 10447 10448 10450 10451 10452 10453 10454 10455 
    ##     5     5     3     3     4     5     4     5     5     5     5     3     3 
    ## 10456 10457 10458 10459 10463 10464 10465 10466 10467 10469 10470 10471 10472 
    ##     5     5     5     3     5     5     4     5     5     5     5     3     5 
    ## 10473 10474 10476 10477 10478 10479 10482 10483 10484 10485 10486 10488 10489 
    ##     4     3     5     5     5     4     5     5     5     5     5     5     5 
    ## 10490 10492 10493 10495 10499 10500 10501 10502 10503 10506 10508 10509 10510 
    ##     5     4     3     5     5     5     5     5     4     3     5     5     3 
    ## 10511 10512 10515 10516 10518 10522 10523 10524 10525 10526 10527 10528 10529 
    ##     4     5     5     3     5     5     5     5     5     5     5     5     5 
    ## 10530 10531 10532 10533 10534 10535 10536 10537 10538 10539 10540 10541 10542 
    ##     5     5     4     3     5     3     5     5     5     5     5     4     5 
    ## 10543 10544 10546 10547 10548 10549 10550 10552 10555 10556 10558 10559 10561 
    ##     5     5     5     5     3     5     4     5     5     5     3     4     5 
    ## 10562 10563 10564 10565 10566 10567 10568 10569 10570 10571 10572 10573 10575 
    ##     5     3     5     4     5     3     4     5     5     4     5     5     5 
    ## 10577 10579 10580 10584 10585 10586 10587 10590 10592 10593 10595 10596 10597 
    ##     5     4     5     5     4     5     5     5     3     5     5     3     5 
    ## 10598 10599 10600 10601 10602 10603 10605 10607 10608 10610 10613 10616 10617 
    ##     5     5     3     5     3     4     5     4     5     5     5     5     5 
    ## 10618 10619 10625 10627 10629 10630 10631 10632 10633 10635 10637 10638 10639 
    ##     5     5     5     3     4     4     5     3     4     5     4     5     4 
    ## 10640 10641 10642 10644 10649 10650 10651 10652 10654 10655 10656 10657 10658 
    ##     5     5     5     5     4     4     3     3     3     5     3     4     5 
    ## 10660 10661 10663 10665 10666 10667 10668 10669 10670 10672 10673 10676 10678 
    ##     3     3     5     3     3     4     5     5     3     5     3     3     4 
    ## 10679 10680 10681 10682 10683 10685 10687 10688 10689 10690 10691 10692 10693 
    ##     5     5     5     5     3     4     5     3     4     5     5     4     5 
    ## 10694 10695 10696 10698 10699 10700 10701 10702 10703 10704 10705 10706 10712 
    ##     5     5     5     5     5     4     5     5     5     3     5     5     5 
    ## 10714 10716 10717 10721 10722 10723 10724 10725 10726 10727 10728 10730 10731 
    ##     4     4     5     3     5     5     5     4     5     5     5     3     4 
    ## 10732 10734 10735 10737 10738 10739 10741 10742 10743 10744 10745 10746 10747 
    ##     4     4     3     5     5     5     5     5     5     5     5     5     3 
    ## 10750 10751 10752 10754 10755 10756 10757 10758 10760 10761 10764 10765 10767 
    ##     4     3     5     5     3     5     5     3     5     5     5     3     5 
    ## 10768 10769 10771 10772 10773 10774 10775 10776 10777 10778 10779 10780 10781 
    ##     5     5     5     3     5     5     5     5     4     5     4     5     5 
    ## 10782 10786 10787 10789 10791 10792 10793 10795 10797 10798 10799 10800 10804 
    ##     5     3     3     5     3     3     5     3     5     3     5     3     5 
    ## 10805 10809 10811 10812 10813 10814 10816 10817 10818 10819 10821 10822 10824 
    ##     5     5     5     5     5     3     4     3     5     5     5     5     5 
    ## 10826 10827 10829 10830 10831 10832 10833 10837 10838 10840 10841 10842 10843 
    ##     4     5     3     5     5     5     5     4     5     5     5     3     5 
    ## 10844 10845 10846 10847 10848 10849 10851 10854 10855 10856 10858 10860 10861 
    ##     5     3     3     5     5     5     5     3     5     4     4     5     5 
    ## 10864 10866 10867 10868 10870 10871 10872 10873 10874 10875 10876 10877 10878 
    ##     5     4     4     3     3     5     4     5     5     5     5     5     4 
    ## 10879 10880 10881 10882 10883 10884 10885 10886 10887 10889 10890 10891 10893 
    ##     5     5     5     3     5     4     3     5     5     5     5     5     5 
    ## 10894 10895 10896 10900 10903 10907 10908 10909 10910 10911 10914 10915 10918 
    ##     4     3     5     3     5     5     5     5     3     5     5     5     5 
    ## 10919 10920 10921 10922 10925 10926 10928 10929 10930 10932 10933 10934 10935 
    ##     5     5     5     5     5     5     3     5     3     5     5     5     4 
    ## 10936 10937 10938 10939 10940 10941 10942 10943 10944 10945 10946 10950 10951 
    ##     5     4     4     3     5     5     5     4     4     5     5     5     4 
    ## 10953 10954 10955 10956 10957 10958 10960 10961 10962 10963 10964 10965 10966 
    ##     4     4     4     5     3     5     5     5     3     5     4     5     5 
    ## 10967 10968 10969 10970 10971 10972 10973 10975 10976 10978 10979 10980 10981 
    ##     5     4     5     5     4     5     5     4     5     3     3     5     5 
    ## 10982 10984 10985 10987 10989 10994 10995 10996 10998 11001 11003 11004 11005 
    ##     5     5     5     5     5     5     4     3     5     3     5     3     5 
    ## 11007 11008 11010 11011 11012 11013 11016 11017 11018 11019 11020 11021 11022 
    ##     5     5     3     5     5     5     5     5     3     5     3     3     4 
    ## 11023 11024 11025 11026 11027 11028 11029 11030 11031 11033 11035 11036 11037 
    ##     4     5     5     3     3     5     3     3     5     5     4     4     4 
    ## 11040 11041 11042 11043 11044 11047 11050 11051 11054 11055 11056 11057 11059 
    ##     5     5     4     5     5     5     5     5     5     5     3     5     3 
    ## 11060 11061 11063 11064 11065 11067 11069 11070 11073 11074 11077 11078 11079 
    ##     3     5     3     5     5     5     3     4     5     5     3     3     3 
    ## 11080 11081 11082 11083 11085 11086 11087 11088 11089 11090 11092 11093 11095 
    ##     5     5     5     5     5     5     4     5     5     3     5     4     5 
    ## 11096 11097 11099 11102 11103 11104 11107 11108 11109 11110 11111 11112 11113 
    ##     3     3     5     5     5     3     5     5     5     5     4     4     4 
    ## 11114 11115 11116 11118 11119 11120 11121 11122 11123 11124 11125 11126 11127 
    ##     3     5     5     3     5     4     5     3     5     4     5     5     5 
    ## 11128 11130 11131 11132 11133 11134 11135 11136 11137 11138 11139 11140 11141 
    ##     5     5     5     5     5     5     5     5     3     3     4     5     3 
    ## 11142 11145 11147 11148 11151 11154 11156 11157 11159 11160 11161 11163 11164 
    ##     5     5     5     5     4     3     5     5     5     3     5     5     5 
    ## 11165 11166 11167 11169 11170 11171 11172 11173 11174 11175 11177 11178 11179 
    ##     5     3     4     5     4     5     5     3     5     5     5     5     3 
    ## 11180 11181 11182 11183 11184 11185 11187 11188 11189 11190 11191 11192 11193 
    ##     3     5     4     5     4     3     5     3     5     5     4     5     4 
    ## 11197 11198 11202 11203 11205 11207 11209 11211 11213 11215 11216 11217 11218 
    ##     5     5     5     4     5     3     3     5     4     5     5     3     4 
    ## 11219 11221 11222 11223 11224 11226 11228 11229 11230 11233 11234 11235 11236 
    ##     4     4     5     3     3     5     5     3     3     4     5     5     5 
    ## 11238 11239 11240 11242 11243 11244 11245 11246 11247 11248 11251 11252 11253 
    ##     5     5     5     5     4     3     5     3     5     3     3     5     5 
    ## 11254 11255 11256 11257 11260 11261 11262 11263 11264 11265 11266 11267 11268 
    ##     5     3     4     3     5     5     5     3     5     4     5     5     5 
    ## 11269 11270 11271 11272 11273 11274 11276 11278 11279 11280 11283 11284 11286 
    ##     3     3     5     5     4     5     5     4     4     5     5     5     5 
    ## 11289 11291 11292 11293 11295 11297 11298 11299 11300 11302 11304 11305 11306 
    ##     5     3     5     4     5     3     4     4     4     5     5     5     4 
    ## 11307 11308 11309 11311 11312 11313 11314 11315 11318 11320 11322 11323 11325 
    ##     5     5     5     4     5     5     5     4     3     5     5     5     3 
    ## 11326 11327 11328 11329 11331 11332 11333 11334 11335 11336 11337 11340 11341 
    ##     5     5     5     5     5     4     4     4     3     5     5     4     5 
    ## 11342 11343 11344 11345 11350 11351 11352 11354 11355 11356 11358 11360 11361 
    ##     5     5     3     3     5     4     4     3     5     3     3     3     5 
    ## 11362 11363 11364 11365 11366 11368 11369 11371 11372 11373 11374 11375 11376 
    ##     5     5     5     5     5     5     5     5     3     4     5     5     5 
    ## 11381 11382 11383 11384 11386 11389 11390 11391 11392 11393 11395 11396 11397 
    ##     5     5     5     3     5     3     3     5     5     3     5     3     5 
    ## 11399 11400 11406 11407 11409 11411 11414 11415 11416 11417 11419 11420 11422 
    ##     5     5     5     3     5     5     3     5     5     5     5     4     3 
    ## 11423 11424 11425 11426 11427 11428 11429 11430 11431 11432 11433 11435 11436 
    ##     5     3     5     4     3     4     5     5     3     5     5     5     4 
    ## 11438 11439 11440 11441 11442 11443 11444 11446 11447 11448 11450 11451 11452 
    ##     4     5     5     4     5     3     5     5     3     4     5     3     4 
    ## 11454 11455 11456 11457 11459 11460 11461 11462 11463 11466 11468 11469 11472 
    ##     5     5     4     4     3     4     3     4     5     3     5     5     3 
    ## 11475 11476 11478 11479 11480 11481 11482 11483 11484 11486 11487 11488 11489 
    ##     4     4     5     5     4     4     5     5     5     3     5     5     4 
    ## 11490 11491 11492 11493 11494 11496 11497 11498 11499 11502 11503 11504 11508 
    ##     3     5     4     3     5     5     5     3     5     5     5     5     4 
    ## 11509 11512 11513 11514 11516 11517 11518 11520 11521 11522 11523 11524 11527 
    ##     5     4     4     5     5     5     4     5     4     3     4     5     3 
    ## 11528 11529 11530 11532 11533 11534 11537 11539 11542 11545 11546 11547 11548 
    ##     5     5     4     3     4     5     5     5     5     5     4     5     5 
    ## 11549 11551 11553 11554 11555 11556 11559 11560 11561 11564 11565 11566 11567 
    ##     4     5     5     3     3     5     5     3     5     3     5     3     3 
    ## 11569 11571 11572 11573 11574 11576 11577 11578 11579 11580 11581 11582 11584 
    ##     5     5     5     5     4     4     5     5     5     5     4     5     5 
    ## 11585 11586 11588 11589 11590 11591 11593 11594 11595 11597 11598 11599 11600 
    ##     4     3     4     5     5     3     4     5     4     4     5     5     4 
    ## 11601 11602 11603 11605 11609 11611 11613 11614 11616 11618 11621 11622 11623 
    ##     3     5     3     5     3     4     3     5     3     4     5     3     5 
    ## 11624 11626 11630 11632 11633 11634 11636 11637 11638 11639 11640 11641 11642 
    ##     3     4     5     5     5     4     3     3     3     3     5     5     5 
    ## 11643 11644 11645 11646 11648 11650 11651 11652 11655 11656 11658 11659 11661 
    ##     4     5     5     4     3     5     5     5     3     4     5     5     5 
    ## 11662 11663 11664 11665 11666 11669 11670 11671 11672 11674 11675 11676 11677 
    ##     5     3     3     5     5     5     5     5     3     3     5     5     4 
    ## 11678 11679 11680 11681 11682 11684 11685 11686 11687 11688 11689 11691 11692 
    ##     4     5     5     4     3     5     3     5     3     3     5     4     4 
    ## 11693 11694 11695 11696 11700 11701 11705 11706 11708 11709 11711 11712 11713 
    ##     5     5     5     5     4     5     5     5     4     5     3     4     4 
    ## 11714 11715 11716 11717 11718 11719 11723 11726 11728 11729 11730 11731 11732 
    ##     5     4     4     5     3     5     5     5     5     4     5     5     4 
    ## 11733 11734 11735 11736 11738 11739 11740 11741 11742 11745 11746 11747 11748 
    ##     5     4     4     5     3     5     5     5     5     5     5     5     5 
    ## 11750 11751 11752 11753 11754 11755 11756 11757 11759 11760 11762 11765 11768 
    ##     5     3     3     3     3     4     4     4     4     5     5     4     4 
    ## 11769 11770 11773 11774 11775 11776 11778 11779 11780 11781 11785 11787 11788 
    ##     3     3     5     4     5     3     5     5     4     4     3     5     5 
    ## 11789 11790 11791 11792 11793 11795 11798 11799 11801 11803 11804 11806 11807 
    ##     3     5     3     5     5     3     5     5     5     5     5     5     4 
    ## 11808 11809 11813 11814 11815 11816 11817 11818 11820 11822 11823 11824 11825 
    ##     5     4     3     3     4     3     3     4     4     3     5     5     4 
    ## 11827 11829 11830 11831 11833 11835 11837 11838 11841 11842 11844 11845 11847 
    ##     5     3     5     5     5     5     4     5     5     4     3     5     3 
    ## 11848 11849 11850 11851 11852 11853 11854 11855 11857 11859 11860 11862 11863 
    ##     5     5     5     4     4     4     3     3     5     4     5     5     5 
    ## 11864 11865 11866 11867 11868 11869 11870 11871 11872 11873 11875 11878 11879 
    ##     4     5     5     5     4     4     3     3     3     3     4     5     5 
    ## 11880 11881 11882 11883 11884 11885 11886 11888 11889 11891 11892 11893 11894 
    ##     5     5     3     5     4     4     4     3     4     5     4     4     3 
    ## 11895 11897 11898 11899 11900 11901 11902 11903 11905 11907 11908 11910 11913 
    ##     4     4     5     5     3     4     4     5     5     3     3     4     5 
    ## 11914 11916 11917 11919 11920 11921 11924 11925 11927 11930 11931 11932 11934 
    ##     4     4     5     5     5     3     5     3     5     3     3     5     5 
    ## 11935 11938 11939 11941 11943 11944 11945 11946 11948 11949 11953 11956 11958 
    ##     3     5     3     4     5     3     3     4     5     5     4     4     5 
    ## 11959 11960 11961 11962 11963 11965 11967 11968 11969 11970 11971 11972 11974 
    ##     3     4     3     5     5     3     4     3     5     5     3     4     5 
    ## 11975 11976 11977 11979 11980 11981 11982 11985 11987 11988 11989 11990 11991 
    ##     5     5     5     5     3     4     5     5     3     5     3     3     5 
    ## 11993 11994 11995 11996 11997 11998 11999 12000 12001 12003 12004 12005 12006 
    ##     3     5     4     3     3     4     5     4     3     5     3     5     3 
    ## 12008 12009 12010 12011 12012 12013 12016 12017 12018 12020 12021 12022 12024 
    ##     3     5     5     5     4     3     4     5     5     4     5     5     5 
    ## 12025 12027 12029 12030 12032 12033 12036 12037 12039 12041 12042 12043 12044 
    ##     3     3     5     4     3     5     3     3     5     4     4     5     4 
    ## 12046 12049 12051 12052 12054 12055 12057 12058 12059 12060 12061 12062 12063 
    ##     3     5     3     5     5     3     5     5     5     5     3     5     3 
    ## 12064 12066 12067 12068 12070 12072 12073 12074 12075 12076 12077 12079 12080 
    ##     3     3     3     3     5     4     3     5     5     5     5     4     3 
    ## 12083 12084 12085 12087 12088 12090 12091 12092 12093 12094 12097 12099 12100 
    ##     4     4     5     4     5     3     4     5     5     4     5     5     5 
    ## 12101 12102 12103 12104 12106 12107 12108 12110 12113 12114 12115 12116 12117 
    ##     5     5     4     4     4     4     5     3     5     5     5     3     5 
    ## 12118 12119 12121 12123 12124 12125 12129 12131 12133 12135 12136 12138 12142 
    ##     3     4     5     3     4     5     4     4     4     3     3     4     4 
    ## 12144 12145 12146 12147 12148 12149 12150 12152 12153 12154 12155 12157 12159 
    ##     5     3     4     5     5     5     5     5     3     4     4     4     3 
    ## 12160 12161 12162 12163 12164 12165 12171 12175 12177 12178 12179 12180 12181 
    ##     3     5     3     3     4     5     5     3     5     5     5     4     3 
    ## 12182 12185 12186 12189 12190 12192 12193 12194 12195 12196 12197 12198 12199 
    ##     5     5     5     3     3     5     4     5     5     3     5     5     5 
    ## 12200 12201 12202 12203 12204 12205 12206 12207 12208 12209 12210 12212 12213 
    ##     5     3     3     5     5     3     5     5     5     5     4     5     5 
    ## 12214 12215 12216 12218 12219 12220 12221 12223 12224 12226 12227 12229 12230 
    ##     5     4     5     5     5     5     5     3     3     5     4     4     3 
    ## 12232 12234 12235 12236 12238 12239 12240 12241 12244 12245 12247 12248 12250 
    ##     4     5     5     4     5     5     4     5     5     4     3     4     5 
    ## 12253 12254 12255 12256 12257 12259 12260 12261 12262 12263 12265 12266 12269 
    ##     5     5     5     5     5     5     5     5     3     5     5     5     3 
    ## 12270 12271 12272 12273 12277 12278 12279 12280 12281 12289 12290 12291 12292 
    ##     5     5     5     5     5     3     4     5     5     5     5     4     3 
    ## 12293 12295 12296 12298 12299 12301 12302 12303 12304 12305 12307 12308 12309 
    ##     5     3     5     3     5     4     3     5     3     4     3     5     3 
    ## 12311 12312 12313 12314 12315 12316 12318 12319 12320 12321 12322 12323 12324 
    ##     3     4     3     4     5     5     3     4     5     5     5     5     3 
    ## 12325 12326 12327 12328 12329 12330 12331 12332 12335 12336 12338 12340 12342 
    ##     5     5     3     3     5     5     3     5     4     3     5     5     4 
    ## 12343 12344 12345 12347 12348 12350 12351 12352 12353 12354 12355 12356 12357 
    ##     5     5     5     5     3     5     4     4     5     3     4     5     5 
    ## 12359 12361 12362 12364 12365 12366 12367 12369 12371 12375 12376 12377 12378 
    ##     3     5     4     5     3     4     5     3     5     5     3     4     4 
    ## 12379 12381 12382 12383 12384 12386 12387 12388 12390 12394 12395 12397 12398 
    ##     4     4     5     3     5     5     5     5     5     3     4     5     3 
    ## 12399 12400 12401 12402 12403 12404 12406 12407 12408 12410 12411 12412 12414 
    ##     5     3     5     5     3     5     5     3     5     4     3     3     5 
    ## 12415 12416 12418 12419 12421 12423 12425 12428 12429 12430 12431 12432 12436 
    ##     5     3     5     4     5     4     4     4     5     5     5     5     3 
    ## 12439 12440 12443 12444 12446 12447 12448 12449 12450 12452 12453 12456 12457 
    ##     5     5     3     4     5     3     4     5     4     5     4     4     4 
    ## 12459 12462 12463 12464 12465 12466 12468 12469 12470 12471 12473 12476 12477 
    ##     4     5     5     5     5     5     5     5     5     5     5     3     4 
    ## 12480 12482 12483 12485 12486 12487 12491 12492 12493 12494 12495 12496 12497 
    ##     3     5     5     3     3     3     5     3     4     4     5     4     5 
    ## 12498 12501 12504 12505 12506 12509 12510 12512 12513 12514 12516 12517 12518 
    ##     3     5     5     4     4     5     3     5     4     5     3     4     5 
    ## 12521 12522 12523 12524 12525 12526 12527 12528 12529 12530 12531 12532 12533 
    ##     4     5     3     3     3     5     3     5     5     3     5     4     4 
    ## 12534 12535 12537 12538 12539 12540 12541 12542 12543 12544 12545 12546 12547 
    ##     3     4     3     4     3     5     3     4     4     5     3     5     3 
    ## 12548 12549 12551 12552 12553 12556 12557 12560 12562 12563 12564 12565 12568 
    ##     4     5     5     5     4     5     3     3     4     3     5     5     5 
    ## 12569 12570 12571 12572 12573 12574 12575 12576 12577 12578 12579 12580 12582 
    ##     3     3     5     4     3     4     3     5     4     5     5     4     5 
    ## 12583 12584 12585 12589 12590 12591 12593 12594 12595 12596 12597 12599 12600 
    ##     4     3     5     3     5     5     4     5     3     4     4     4     4 
    ## 12603 12605 12606 12607 12608 12609 12610 12611 12612 12613 12614 12615 12616 
    ##     4     5     3     5     4     5     4     5     4     4     3     4     5 
    ## 12617 12618 12619 12620 12621 12622 12623 12627 12628 12630 12631 12632 12634 
    ##     5     3     3     4     5     5     4     5     4     3     4     4     5 
    ## 12635 12636 12637 12638 12640 12641 12642 12643 12644 12645 12646 12647 12648 
    ##     5     5     5     4     4     5     4     4     3     4     5     5     3 
    ## 12649 12650 12651 12653 12654 12655 12661 12662 12663 12664 12665 12666 12667 
    ##     4     5     3     5     3     3     3     3     3     4     5     4     4 
    ## 12668 12669 12670 12672 12673 12674 12676 12677 12680 12681 12682 12684 12685 
    ##     4     5     5     4     5     3     3     5     5     5     5     3     3 
    ## 12686 12687 12689 12692 12693 12694 12696 12697 12699 12700 12701 12703 12704 
    ##     5     4     3     5     3     3     3     5     5     3     5     5     3 
    ## 12705 12706 12709 12711 12712 12713 12715 12717 12718 12721 12722 12723 12725 
    ##     5     5     3     3     5     5     5     5     5     5     3     3     4 
    ## 12727 12730 12731 12732 12735 12736 12739 12740 12741 12742 12743 12744 12746 
    ##     4     5     5     4     5     4     4     5     5     3     5     5     4 
    ## 12747 12748 12749 12751 12752 12753 12754 12755 12757 12758 12760 12762 12763 
    ##     5     4     5     5     5     5     5     4     5     5     3     5     3 
    ## 12765 12766 12768 12769 12771 12772 12773 12774 12779 12780 12782 12783 12784 
    ##     4     5     3     5     3     4     3     5     4     5     3     5     5 
    ## 12787 12788 12789 12790 12791 12792 12794 12795 12796 12797 12798 12800 12801 
    ##     3     5     4     4     3     3     5     4     3     5     4     5     4 
    ## 12802 12803 12804 12805 12806 12808 12809 12814 12815 12816 12818 12819 12820 
    ##     5     3     5     4     5     5     5     4     3     5     4     3     5 
    ## 12821 12822 12823 12824 12826 12827 12828 12829 12831 12832 12835 12836 12837 
    ##     5     3     3     4     5     4     5     4     5     4     4     5     4 
    ## 12838 12839 12841 12842 12844 12845 12846 12848 12850 12851 12853 12856 12857 
    ##     5     4     4     5     4     3     5     4     5     5     5     5     5 
    ## 12858 12859 12860 12862 12864 12865 12866 12870 12871 12872 12873 12875 12878 
    ##     4     3     3     5     5     5     5     4     5     4     4     4     4 
    ## 12880 12882 12883 12884 12885 12886 12887 12889 12891 12892 12894 12895 12896 
    ##     3     5     5     5     5     4     5     4     4     4     5     4     3 
    ## 12897 12898 12899 12900 12901 12902 12903 12905 12906 12908 12909 12910 12911 
    ##     5     4     5     3     4     4     5     3     3     4     4     3     5 
    ## 12912 12913 12914 12915 12916 12917 12919 12920 12921 12922 12923 12924 12928 
    ##     5     5     3     4     4     5     4     3     4     3     4     4     3 
    ## 12930 12932 12933 12934 12935 12938 12939 12940 12941 12942 12943 12944 12945 
    ##     4     5     4     4     3     5     3     4     4     4     5     4     4 
    ## 12946 12947 12950 12951 12952 12953 12956 12957 12958 12960 12961 12962 12963 
    ##     5     5     3     5     5     4     3     4     3     5     5     4     3 
    ## 12965 12966 12968 12969 12970 12972 12975 12976 12977 12979 12980 12981 12983 
    ##     5     5     3     3     4     5     5     5     4     4     4     5     5 
    ## 12984 12985 12986 12987 12988 12989 12991 12993 12995 12996 12999 13000 13002 
    ##     5     5     5     5     5     5     5     5     5     5     4     3     5 
    ## 13005 13006 13008 13010 13011 13013 13014 13015 13018 13022 13023 13024 13026 
    ##     5     4     5     4     5     5     5     4     5     5     5     5     4 
    ## 13027 13029 13030 13031 13032 13034 13035 13036 13038 13039 13040 13041 13042 
    ##     5     4     5     5     3     5     5     5     5     5     5     4     3 
    ## 13044 13045 13047 13048 13049 13050 13054 13055 13057 13059 13060 13061 13062 
    ##     5     5     5     5     5     3     5     5     5     4     5     4     3 
    ## 13063 13064 13065 13067 13068 13070 13072 13073 13074 13075 13076 13078 13080 
    ##     3     5     5     3     4     5     3     5     5     3     5     5     4 
    ## 13081 13084 13085 13087 13088 13089 13091 13092 13094 13095 13096 13097 13098 
    ##     5     5     3     5     5     5     5     3     3     5     5     4     3 
    ## 13100 13102 13104 13105 13106 13109 13112 13115 13117 13118 13119 13120 13123 
    ##     5     4     4     4     5     5     5     3     5     5     5     5     4 
    ## 13124 13126 13127 13129 13130 13131 13132 13133 13134 13137 13142 13144 13146 
    ##     4     3     5     3     5     5     4     5     5     5     5     5     5 
    ## 13147 13148 13149 13151 13153 13159 13161 13162 13163 13164 13166 13167 13168 
    ##     4     5     3     4     5     5     5     3     3     4     3     5     5 
    ## 13169 13170 13172 13173 13174 13175 13178 13179 13180 13181 13182 13183 13185 
    ##     5     5     3     5     5     5     5     3     5     5     3     4     5 
    ## 13188 13189 13190 13191 13192 13193 13194 13195 13196 13198 13199 13200 13201 
    ##     3     5     5     4     5     3     3     3     3     3     5     5     3 
    ## 13203 13204 13206 13208 13211 13212 13214 13215 13216 13217 13218 13219 13221 
    ##     3     3     3     4     4     5     3     5     5     3     5     5     5 
    ## 13224 13225 13226 13227 13229 13230 13231 13235 13237 13239 13241 13242 13243 
    ##     4     4     3     4     5     3     4     4     4     3     5     5     4 
    ## 13245 13248 13249 13251 13252 13253 13254 13255 13257 13259 13263 13264 13266 
    ##     3     3     5     5     4     5     3     3     5     4     4     3     3 
    ## 13268 13271 13272 13273 13274 13276 13277 13279 13281 13282 13283 13284 13285 
    ##     4     5     4     5     3     4     4     3     5     3     5     3     3 
    ## 13287 13288 13291 13292 13293 13294 13297 13299 13301 13302 13303 13304 13306 
    ##     3     5     3     3     5     3     5     3     5     5     5     5     5 
    ## 13307 13308 13309 13310 13311 13312 13313 13314 13315 13318 13319 13320 13321 
    ##     3     3     5     4     4     3     5     5     3     3     4     5     5 
    ## 13324 13325 13329 13330 13331 13332 13333 13334 13338 13339 13340 13343 13344 
    ##     5     5     3     5     3     5     5     5     4     5     5     4     5 
    ## 13347 13348 13350 13351 13352 13353 13356 13358 13361 13362 13363 13364 13365 
    ##     5     5     4     5     3     5     5     4     5     3     5     5     5 
    ## 13367 13369 13372 13373 13374 13377 13378 13380 13381 13382 13384 13385 13387 
    ##     5     4     5     5     3     5     4     4     3     3     5     4     5 
    ## 13388 13389 13390 13391 13392 13393 13395 13396 13397 13398 13400 13401 13403 
    ##     5     3     4     5     5     3     5     3     4     3     5     5     5 
    ## 13406 13407 13409 13410 13411 13412 13413 13414 13417 13418 13419 13420 13421 
    ##     4     4     5     4     3     5     5     5     5     3     5     5     4 
    ## 13424 13425 13426 13427 13428 13429 13430 13431 13432 13433 13434 13435 13436 
    ##     5     4     4     5     4     3     4     5     3     5     5     3     5 
    ## 13437 13438 13440 13441 13443 13445 13446 13447 13449 13451 13454 13455 13458 
    ##     3     4     5     5     3     5     5     3     3     3     4     4     5 
    ## 13459 13460 13461 13462 13465 13466 13467 13468 13469 13470 13471 13472 13475 
    ##     5     5     5     5     3     3     5     5     5     5     4     5     5 
    ## 13476 13477 13478 13479 13480 13481 13482 13483 13484 13486 13487 13488 13489 
    ##     5     5     5     5     4     5     5     5     3     5     5     4     3 
    ## 13490 13491 13496 13497 13498 13499 13501 13502 13503 13504 13505 13506 13508 
    ##     3     5     5     3     5     3     5     5     4     4     4     5     5 
    ## 13509 13510 13511 13512 13514 13517 13519 13520 13521 13522 13523 13524 13526 
    ##     4     4     4     4     4     5     5     4     4     3     5     3     5 
    ## 13528 13529 13530 13531 13533 13534 13535 13540 13544 13546 13547 13549 13550 
    ##     5     5     5     5     3     4     4     5     5     4     5     5     3 
    ## 13552 13553 13554 13555 13556 13559 13560 13561 13562 13563 13564 13565 13568 
    ##     4     3     4     3     5     5     3     4     4     5     5     4     4 
    ## 13569 13572 13573 13574 13575 13576 13577 13579 13582 13583 13586 13587 13588 
    ##     5     4     5     5     3     5     5     5     3     5     4     3     4 
    ## 13590 13591 13593 13594 13595 13596 13597 13598 13599 13601 13603 13604 13605 
    ##     5     5     5     5     5     3     4     5     5     5     4     3     3 
    ## 13606 13607 13609 13611 13615 13616 13618 13619 13620 13621 13623 13624 13625 
    ##     4     4     3     5     5     4     4     5     4     5     5     3     5 
    ## 13626 13627 13628 13630 13631 13632 13633 13635 13637 13638 13639 13640 13643 
    ##     5     5     5     5     4     3     5     3     3     5     5     5     3 
    ## 13645 13646 13647 13648 13651 13652 13657 13658 13659 13661 13665 13667 13671 
    ##     5     5     5     4     5     5     4     5     4     4     5     5     5 
    ## 13672 13673 13675 13676 13678 13679 13680 13681 13684 13685 13687 13688 13690 
    ##     3     5     5     5     3     4     3     3     5     5     5     4     4 
    ## 13692 13695 13696 13699 13700 13701 13702 13703 13704 13705 13706 13707 13708 
    ##     5     4     5     4     4     3     5     5     5     5     4     3     5 
    ## 13709 13711 13713 13714 13716 13717 13718 13719 13720 13721 13722 13724 13725 
    ##     5     4     5     5     3     5     5     4     5     3     3     3     3 
    ## 13726 13728 13729 13730 13731 13733 13735 13737 13738 13740 13741 13742 13743 
    ##     5     5     3     4     4     5     4     5     5     4     3     4     5 
    ## 13744 13745 13746 13748 13752 13753 13756 13757 13758 13760 13762 13763 13765 
    ##     4     4     5     4     4     4     4     3     5     5     5     5     5 
    ## 13766 13770 13772 13773 13774 13775 13776 13777 13779 13780 13781 13782 13787 
    ##     4     5     5     5     5     4     4     4     5     5     5     5     5 
    ## 13788 13789 13792 13793 13794 13796 13797 13798 13799 13800 13802 13805 13806 
    ##     5     5     5     4     3     4     5     5     3     5     3     5     5 
    ## 13810 13812 13813 13814 13816 13817 13818 13819 13821 13822 13824 13826 13827 
    ##     4     5     5     5     4     5     3     3     5     5     5     4     5 
    ## 13829 13830 13831 13832 13836 13837 13840 13841 13843 13844 13845 13846 13848 
    ##     4     5     3     5     3     5     5     5     4     5     5     4     4 
    ## 13849 13850 13851 13852 13853 13854 13855 13856 13857 13858 13859 13861 13863 
    ##     5     3     3     5     5     3     5     4     3     5     5     4     5 
    ## 13864 13865 13866 13867 13869 13870 13871 13872 13873 13876 13880 13881 13884 
    ##     5     5     5     4     4     3     5     5     5     5     3     4     5 
    ## 13886 13887 13888 13891 13892 13893 13894 13895 13896 13897 13898 13899 13900 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 13902 13907 13909 13910 13912 13913 13914 13915 13917 13918 13919 13920 13921 
    ##     5     5     3     5     4     5     5     5     3     5     5     5     5 
    ## 13924 13925 13926 13927 13928 13929 13930 13932 13934 13935 13937 13938 13939 
    ##     5     5     4     5     4     5     4     5     5     3     3     5     5 
    ## 13944 13945 13952 13953 13955 13956 13958 13960 13961 13962 13963 13965 13968 
    ##     5     4     3     5     3     3     4     5     5     4     4     4     3 
    ## 13970 13971 13972 13973 13974 13976 13977 13981 13992 13994 13995 13997 13999 
    ##     4     5     5     3     4     5     3     3     3     3     4     5     5 
    ## 14001 14002 14003 14004 14005 14006 14007 14009 14010 14011 14012 14013 14014 
    ##     5     5     3     5     5     5     5     5     5     4     5     5     4 
    ## 14015 14016 14017 14020 14022 14023 14024 14025 14026 14027 14028 14031 14035 
    ##     4     4     5     5     5     4     3     5     4     5     5     5     3 
    ## 14036 14039 14041 14042 14046 14048 14049 14052 14054 14056 14057 14061 14062 
    ##     4     5     3     3     3     3     3     5     5     4     5     5     5 
    ## 14063 14064 14065 14066 14067 14068 14069 14071 14072 14073 14074 14075 14076 
    ##     3     5     4     5     5     3     5     5     3     5     5     5     5 
    ## 14077 14080 14082 14083 14084 14086 14087 14088 14091 14093 14094 14095 14097 
    ##     5     5     4     5     5     5     4     5     3     5     5     3     3 
    ## 14098 14099 14101 14102 14103 14104 14105 14106 14107 14108 14110 14111 14114 
    ##     3     4     3     4     4     5     5     4     4     5     5     4     4 
    ## 14116 14118 14119 14120 14121 14123 14125 14126 14129 14130 14132 14133 14136 
    ##     5     4     4     5     3     4     5     3     5     5     5     3     5 
    ## 14137 14138 14139 14140 14141 14143 14144 14146 14147 14148 14150 14151 14153 
    ##     4     5     5     4     4     5     5     5     5     5     3     5     3 
    ## 14154 14156 14157 14159 14162 14165 14166 14169 14170 14171 14172 14173 14174 
    ##     3     4     5     5     3     5     5     5     3     3     3     3     3 
    ## 14175 14176 14177 14178 14179 14180 14181 14182 14183 14184 14185 14187 14189 
    ##     3     5     5     5     5     3     3     5     5     5     4     3     3 
    ## 14190 14191 14192 14193 14194 14195 14196 14197 14198 14200 14201 14205 14207 
    ##     5     4     4     3     5     4     5     3     3     3     4     4     3 
    ## 14209 14211 14212 14214 14216 14218 14222 14223 14224 14225 14226 14227 14228 
    ##     5     3     4     4     5     5     5     5     5     4     3     5     5 
    ## 14229 14230 14236 14238 14239 14240 14241 14242 14244 14245 14246 14248 14249 
    ##     3     5     3     5     5     4     3     4     3     5     4     5     3 
    ## 14250 14251 14252 14254 14256 14259 14260 14262 14263 14265 14267 14269 14270 
    ##     4     5     5     4     3     5     5     3     4     5     3     4     5 
    ## 14273 14274 14275 14276 14277 14282 14283 14287 14289 14290 14292 14293 14294 
    ##     4     3     5     5     5     4     3     5     5     3     4     4     3 
    ## 14295 14296 14297 14298 14299 14300 14302 14303 14305 14307 14311 14313 14314 
    ##     4     5     5     4     5     5     5     5     3     4     4     5     4 
    ## 14315 14316 14318 14319 14321 14322 14323 14326 14328 14331 14332 14333 14335 
    ##     5     3     5     5     4     3     5     4     3     3     5     5     4 
    ## 14336 14337 14340 14341 14342 14343 14344 14346 14347 14350 14351 14353 14354 
    ##     5     5     3     3     5     4     5     5     5     4     3     3     5 
    ## 14355 14356 14357 14358 14359 14360 14361 14362 14363 14364 14366 14368 14370 
    ##     5     4     3     4     4     5     3     4     4     3     4     3     4 
    ## 14373 14379 14380 14381 14382 14383 14384 14385 14386 14388 14389 14390 14391 
    ##     3     5     4     5     4     3     4     3     5     4     5     5     5 
    ## 14393 14394 14399 14400 14401 14402 14403 14404 14405 14406 14407 14410 14411 
    ##     4     4     5     4     3     3     3     4     4     5     3     4     4 
    ## 14412 14413 14415 14416 14417 14418 14419 14420 14421 14422 14424 14425 14426 
    ##     4     3     4     4     5     4     5     4     5     5     5     3     3 
    ## 14427 14429 14430 14432 14433 14436 14437 14439 14441 14442 14443 14444 14445 
    ##     5     5     5     4     5     4     5     5     4     5     3     5     3 
    ## 14446 14448 14449 14450 14451 14452 14454 14455 14456 14458 14459 14460 14461 
    ##     5     5     3     4     5     3     3     5     4     5     4     5     5 
    ## 14462 14463 14464 14465 14466 14467 14468 14469 14471 14473 14474 14476 14477 
    ##     4     5     4     5     4     5     5     5     4     4     5     4     5 
    ## 14479 14480 14482 14485 14486 14487 14488 14490 14494 14498 14500 14501 14502 
    ##     3     4     5     3     5     5     5     5     5     3     4     3     4 
    ## 14503 14505 14506 14508 14509 14511 14513 14515 14516 14517 14518 14519 14521 
    ##     5     5     4     3     5     3     5     4     3     5     5     3     4 
    ## 14523 14526 14528 14529 14530 14531 14533 14534 14536 14537 14538 14539 14540 
    ##     5     3     5     5     3     5     5     5     5     5     5     3     3 
    ## 14543 14544 14546 14549 14550 14551 14553 14554 14557 14559 14560 14561 14563 
    ##     5     4     5     4     5     3     4     5     5     4     5     5     5 
    ## 14566 14567 14568 14569 14571 14572 14573 14574 14575 14576 14578 14579 14580 
    ##     5     3     3     4     5     4     5     5     5     3     3     4     5 
    ## 14582 14583 14584 14585 14586 14587 14589 14592 14593 14594 14595 14597 14598 
    ##     4     4     3     3     5     5     5     5     5     3     5     3     5 
    ## 14599 14600 14601 14602 14603 14604 14605 14606 14607 14608 14613 14614 14615 
    ##     5     4     3     5     4     5     3     5     5     3     5     3     5 
    ## 14616 14617 14618 14619 14620 14622 14624 14625 14627 14629 14630 14631 14632 
    ##     5     3     5     5     4     5     4     5     5     3     3     5     4 
    ## 14634 14635 14636 14637 14640 14641 14643 14644 14646 14647 14648 14649 14650 
    ##     4     4     5     5     3     4     5     5     3     5     3     5     4 
    ## 14651 14653 14654 14655 14656 14657 14658 14659 14660 14662 14663 14664 14665 
    ##     5     5     4     4     4     5     3     5     4     5     4     3     4 
    ## 14666 14668 14669 14672 14673 14674 14675 14677 14678 14679 14682 14683 14684 
    ##     4     5     3     4     3     3     4     4     5     4     3     4     4 
    ## 14685 14686 14687 14688 14689 14691 14695 14696 14698 14699 14701 14704 14705 
    ##     5     4     5     5     5     5     5     4     3     4     5     5     5 
    ## 14706 14707 14708 14710 14711 14716 14717 14719 14720 14721 14723 14724 14725 
    ##     3     5     4     3     5     5     5     4     4     3     3     5     4 
    ## 14726 14727 14728 14729 14732 14733 14735 14737 14738 14739 14741 14743 14744 
    ##     5     5     5     3     5     5     5     5     5     3     5     3     5 
    ## 14745 14746 14747 14748 14752 14753 14755 14756 14757 14758 14759 14760 14761 
    ##     3     5     3     5     3     4     3     4     5     5     4     5     3 
    ## 14764 14765 14766 14767 14768 14769 14770 14772 14774 14775 14776 14777 14778 
    ##     5     4     5     4     4     4     3     4     5     5     3     5     5 
    ## 14779 14783 14784 14785 14786 14788 14789 14790 14791 14794 14795 14796 14797 
    ##     3     5     5     3     3     5     5     3     5     5     3     5     4 
    ## 14798 14799 14800 14801 14803 14804 14805 14806 14807 14809 14810 14811 14812 
    ##     4     3     4     4     5     5     5     5     5     5     4     3     5 
    ## 14813 14816 14817 14818 14819 14820 14821 14822 14823 14825 14827 14828 14831 
    ##     3     5     3     5     5     3     5     4     4     5     5     5     5 
    ## 14832 14833 14834 14835 14836 14837 14838 14839 14840 14842 14844 14847 14848 
    ##     4     5     5     5     5     5     5     5     5     4     5     5     4 
    ## 14850 14852 14855 14858 14860 14862 14863 14864 14865 14866 14867 14870 14871 
    ##     5     4     5     4     5     5     5     4     4     5     4     5     5 
    ## 14872 14873 14876 14877 14879 14882 14883 14884 14886 14888 14889 14890 14891 
    ##     4     5     3     4     3     5     5     5     3     5     3     5     4 
    ## 14892 14893 14894 14895 14896 14897 14898 14899 14900 14901 14903 14904 14905 
    ##     5     5     5     3     3     5     5     5     3     5     5     5     5 
    ## 14908 14909 14910 14913 14914 14916 14917 14918 14919 14920 14921 14922 14923 
    ##     5     3     3     4     4     5     5     5     4     3     5     3     5 
    ## 14924 14925 14926 14929 14930 14931 14933 14935 14936 14937 14938 14939 14941 
    ##     5     4     5     5     5     5     5     5     5     5     4     4     5 
    ## 14943 14945 14946 14947 14948 14949 14951 14952 14953 14954 14955 14957 14958 
    ##     3     3     5     4     5     4     4     5     3     4     5     5     4 
    ## 14960 14962 14963 14964 14965 14966 14967 14968 14969 14970 14971 14972 14973 
    ##     5     5     5     5     3     3     4     4     5     4     5     5     5 
    ## 14974 14977 14978 14979 14981 14982 14985 14987 14989 14990 14992 14993 14994 
    ##     5     3     5     3     4     5     4     4     4     5     4     5     5 
    ## 14995 15000 15001 15003 15004 15005 15006 15007 15008 15012 15013 15014 15015 
    ##     5     5     5     4     5     4     5     3     4     5     5     3     5 
    ## 15018 15019 15022 15023 15024 15025 15026 15027 15029 15030 15031 15032 15035 
    ##     5     3     4     5     5     5     5     5     5     3     3     3     5 
    ## 15037 15038 15039 15040 15041 15042 15044 15045 15046 15047 15048 15049 15050 
    ##     5     3     5     3     4     5     5     5     4     5     5     4     5 
    ## 15051 15052 15054 15056 15057 15058 15059 15061 15062 15064 15065 15066 15067 
    ##     3     5     5     5     4     5     5     3     5     4     4     5     5 
    ## 15068 15071 15072 15073 15074 15075 15076 15077 15079 15080 15082 15084 15085 
    ##     3     5     5     5     5     4     3     3     3     3     3     5     5 
    ## 15086 15087 15088 15089 15090 15092 15094 15095 15096 15097 15098 15099 15100 
    ##     5     4     3     5     5     4     5     5     5     4     5     4     5 
    ## 15102 15103 15104 15106 15108 15109 15110 15111 15112 15114 15115 15116 15117 
    ##     5     3     5     3     3     5     4     4     5     5     5     3     4 
    ## 15118 15119 15120 15123 15124 15127 15128 15129 15131 15132 15133 15135 15137 
    ##     5     5     5     5     5     5     5     5     4     4     3     5     3 
    ## 15138 15140 15141 15142 15143 15146 15148 15149 15150 15153 15154 15155 15156 
    ##     3     4     5     4     4     3     5     3     5     4     5     4     3 
    ## 15157 15158 15159 15160 15162 15163 15164 15166 15167 15169 15171 15173 15175 
    ##     5     5     3     4     5     5     4     4     5     3     4     4     5 
    ## 15176 15177 15179 15181 15182 15185 15186 15190 15192 15193 15194 15196 15197 
    ##     4     3     4     4     4     5     5     4     3     5     4     4     4 
    ## 15198 15201 15202 15204 15206 15207 15208 15209 15210 15211 15212 15215 15218 
    ##     3     5     5     5     3     4     5     3     3     5     5     3     3 
    ## 15220 15221 15222 15223 15224 15225 15226 15227 15229 15230 15231 15233 15235 
    ##     5     3     5     3     3     5     5     5     3     5     5     4     5 
    ## 15236 15237 15238 15239 15240 15241 15242 15243 15245 15246 15247 15248 15250 
    ##     5     5     5     5     5     4     4     4     4     5     5     4     5 
    ## 15251 15252 15254 15255 15256 15258 15260 15261 15262 15263 15264 15265 15266 
    ##     4     5     3     5     4     4     5     5     5     5     5     4     3 
    ## 15268 15269 15271 15273 15274 15275 15276 15277 15278 15279 15280 15282 15283 
    ##     3     3     3     5     5     5     4     5     4     4     3     5     5 
    ## 15284 15285 15286 15288 15289 15291 15292 15293 15294 15296 15297 15298 15299 
    ##     4     3     5     5     4     5     3     3     5     5     3     4     3 
    ## 15303 15305 15307 15308 15309 15310 15311 15312 15313 15314 15315 15318 15321 
    ##     5     3     5     5     3     4     5     5     5     5     5     5     4 
    ## 15322 15324 15326 15327 15328 15329 15330 15331 15332 15333 15334 15337 15338 
    ##     5     4     4     5     5     5     4     5     3     5     3     5     5 
    ## 15339 15340 15342 15343 15344 15345 15346 15347 15348 15353 15354 15356 15358 
    ##     5     5     3     4     5     3     3     4     3     5     5     4     5 
    ## 15359 15360 15361 15362 15363 15364 15365 15366 15367 15368 15369 15372 15373 
    ##     5     3     5     3     4     5     5     3     5     5     5     5     3 
    ## 15374 15376 15377 15378 15379 15382 15383 15386 15387 15388 15389 15390 15391 
    ##     4     5     5     5     5     3     5     5     3     3     3     5     4 
    ## 15392 15393 15395 15396 15397 15398 15399 15401 15402 15404 15405 15406 15407 
    ##     4     4     3     3     5     4     5     4     4     5     5     5     5 
    ## 15410 15412 15414 15415 15419 15420 15421 15422 15423 15424 15425 15426 15430 
    ##     3     3     3     4     5     3     3     5     5     4     4     5     3 
    ## 15431 15432 15434 15435 15436 15437 15438 15439 15440 15441 15442 15443 15444 
    ##     4     3     5     5     5     5     3     4     5     5     5     3     3 
    ## 15445 15447 15448 15449 15451 15452 15453 15454 15456 15457 15458 15461 15462 
    ##     4     5     3     3     5     4     4     5     4     5     3     5     3 
    ## 15463 15464 15465 15466 15467 15468 15469 15470 15471 15472 15473 15474 15476 
    ##     5     3     5     5     5     5     3     4     3     3     5     5     4 
    ## 15478 15480 15481 15483 15484 15485 15486 15487 15488 15490 15491 15492 15495 
    ##     5     4     4     3     4     5     5     5     5     5     4     5     5 
    ## 15497 15500 15501 15502 15503 15504 15508 15510 15511 15512 15513 15514 15515 
    ##     4     4     3     5     4     4     3     5     4     4     5     5     5 
    ## 15516 15517 15518 15520 15521 15523 15525 15526 15527 15528 15530 15531 15533 
    ##     5     3     5     5     3     3     3     5     5     5     4     5     5 
    ## 15534 15535 15536 15538 15542 15543 15544 15546 15547 15549 15550 15551 15552 
    ##     5     4     4     5     4     4     5     5     5     3     4     5     4 
    ## 15553 15554 15556 15557 15560 15562 15564 15565 15566 15568 15569 15571 15572 
    ##     3     5     3     5     5     3     5     4     3     5     3     5     5 
    ## 15574 15575 15576 15577 15578 15579 15580 15583 15585 15587 15588 15589 15590 
    ##     5     3     5     3     3     4     5     4     5     5     5     5     4 
    ## 15591 15592 15593 15594 15595 15596 15597 15599 15601 15602 15603 15604 15606 
    ##     5     3     5     5     3     5     5     5     4     3     4     5     5 
    ## 15607 15609 15610 15611 15612 15613 15614 15615 15616 15617 15618 15621 15622 
    ##     5     4     5     5     4     5     5     3     3     3     5     5     4 
    ## 15624 15625 15626 15627 15628 15629 15631 15632 15633 15634 15635 15638 15640 
    ##     3     5     5     3     5     5     3     3     5     3     5     3     4 
    ## 15641 15642 15643 15646 15647 15649 15652 15654 15655 15656 15657 15658 15659 
    ##     3     5     5     3     5     5     5     3     4     4     5     4     5 
    ## 15660 15662 15664 15666 15667 15668 15669 15670 15671 15673 15674 15675 15678 
    ##     3     4     4     4     5     4     5     5     5     4     3     5     4 
    ## 15679 15680 15681 15682 15684 15685 15686 15687 15688 15690 15693 15694 15695 
    ##     5     5     5     4     5     5     5     5     3     4     3     4     5 
    ## 15696 15697 15701 15703 15704 15705 15707 15712 15716 15717 15718 15719 15720 
    ##     3     5     5     4     5     3     5     5     4     5     5     4     3 
    ## 15722 15723 15724 15727 15730 15731 15734 15735 15736 15737 15738 15739 15740 
    ##     5     3     4     5     3     5     5     5     5     5     5     3     5 
    ## 15741 15742 15745 15747 15748 15749 15751 15752 15753 15754 15756 15757 15759 
    ##     5     5     5     5     5     5     4     5     5     3     4     5     5 
    ## 15760 15762 15763 15764 15767 15769 15770 15771 15772 15773 15776 15777 15778 
    ##     4     4     5     5     5     3     3     3     3     5     5     4     5 
    ## 15779 15780 15781 15782 15784 15785 15787 15788 15789 15790 15791 15792 15796 
    ##     4     5     5     3     5     5     5     4     5     5     4     5     5 
    ## 15797 15798 15799 15801 15805 15807 15808 15809 15813 15814 15816 15817 15818 
    ##     5     5     4     5     4     3     5     5     5     3     5     4     5 
    ## 15820 15822 15824 15826 15827 15828 15830 15831 15832 15833 15834 15835 15837 
    ##     5     5     5     4     4     5     5     5     3     4     4     5     3 
    ## 15840 15841 15842 15846 15847 15851 15853 15855 15856 15858 15859 15860 15861 
    ##     5     5     4     3     4     5     3     4     4     5     3     5     3 
    ## 15862 15863 15864 15865 15866 15867 15869 15871 15872 15873 15874 15876 15877 
    ##     4     3     3     5     3     4     3     5     5     5     5     5     5 
    ## 15878 15879 15880 15881 15883 15885 15886 15887 15888 15889 15890 15891 15892 
    ##     5     5     5     5     5     5     5     5     5     4     5     4     5 
    ## 15893 15894 15895 15896 15898 15899 15900 15901 15902 15903 15904 15905 15906 
    ##     5     5     5     5     3     5     5     3     5     5     5     5     5 
    ## 15907 15910 15911 15912 15913 15914 15915 15917 15918 15919 15920 15921 15922 
    ##     5     3     4     5     4     5     4     5     5     5     5     3     5 
    ## 15923 15925 15926 15928 15929 15930 15933 15935 15936 15937 15938 15942 15945 
    ##     5     4     3     5     5     5     4     3     3     3     5     5     5 
    ## 15946 15947 15948 15951 15952 15953 15954 15955 15957 15958 15959 15962 15963 
    ##     4     5     5     4     3     4     5     3     4     4     5     4     5 
    ## 15964 15965 15968 15969 15972 15977 15978 15979 15980 15981 15982 15983 15984 
    ##     4     5     4     4     3     5     5     5     5     5     4     4     5 
    ## 15985 15987 15989 15992 15993 15994 15997 15998 15999 16000 16001 16003 16004 
    ##     5     5     5     5     3     5     3     3     5     5     5     5     3 
    ## 16005 16006 16007 16008 16010 16012 16013 16014 16015 16016 16017 16018 16020 
    ##     5     3     5     5     5     5     5     4     5     4     3     5     5 
    ## 16021 16022 16023 16024 16026 16027 16028 16030 16031 16033 16036 16037 16038 
    ##     5     3     3     4     4     5     4     5     5     4     4     5     5 
    ## 16040 16041 16042 16043 16044 16045 16049 16050 16051 16054 16055 16056 16057 
    ##     4     5     4     5     3     5     4     3     5     5     5     5     5 
    ## 16061 16063 16064 16065 16066 16069 16070 16071 16072 16073 16074 16075 16077 
    ##     4     4     5     5     5     5     4     5     5     5     3     5     4 
    ## 16078 16080 16081 16082 16083 16085 16086 16087 16088 16089 16090 16092 16093 
    ##     4     4     3     5     4     5     5     5     5     4     4     5     3 
    ## 16096 16097 16098 16099 16100 16101 16102 16103 16104 16105 16107 16108 16109 
    ##     5     5     4     4     3     4     4     3     3     4     5     5     5 
    ## 16111 16112 16115 16118 16120 16121 16122 16124 16125 16126 16127 16128 16130 
    ##     3     5     3     5     4     5     5     4     3     5     5     5     5 
    ## 16132 16133 16135 16136 16137 16139 16140 16141 16143 16148 16149 16151 16152 
    ##     5     5     5     4     3     5     5     5     5     3     5     5     5 
    ## 16153 16154 16155 16158 16159 16160 16161 16162 16163 16164 16167 16168 16172 
    ##     5     5     5     5     5     5     5     5     5     3     5     5     3 
    ## 16173 16174 16175 16176 16178 16179 16180 16181 16182 16184 16185 16186 16187 
    ##     4     3     5     5     5     3     4     5     4     4     3     4     5 
    ## 16190 16191 16192 16193 16194 16196 16197 16198 16200 16202 16204 16205 16206 
    ##     5     5     3     3     5     5     5     3     4     5     5     5     5 
    ## 16207 16208 16209 16210 16211 16212 16213 16214 16216 16218 16219 16220 16221 
    ##     5     5     5     4     5     5     5     5     5     5     3     5     4 
    ## 16222 16224 16225 16226 16228 16229 16230 16231 16232 16233 16234 16237 16239 
    ##     5     4     3     5     5     3     3     5     5     5     3     5     5 
    ## 16240 16241 16242 16243 16244 16245 16246 16248 16250 16251 16252 16254 16255 
    ##     5     5     4     5     4     3     4     4     3     5     4     5     5 
    ## 16257 16258 16260 16261 16262 16263 16266 16267 16268 16269 16271 16273 16274 
    ##     3     3     5     5     4     5     4     5     5     5     5     5     4 
    ## 16275 16278 16279 16280 16281 16286 16288 16289 16290 16291 16292 16294 16296 
    ##     5     3     5     5     4     3     5     5     5     5     5     5     3 
    ## 16299 16300 16301 16303 16305 16307 16308 16311 16313 16314 16315 16316 16320 
    ##     4     4     5     5     5     4     5     4     5     5     3     4     5 
    ## 16321 16322 16323 16324 16325 16326 16328 16329 16331 16332 16335 16336 16338 
    ##     4     5     3     3     5     5     5     4     5     4     5     5     3 
    ## 16339 16340 16341 16342 16343 16344 16345 16346 16347 16348 16349 16351 16352 
    ##     3     5     5     4     4     5     3     4     5     5     4     3     5 
    ## 16354 16355 16356 16358 16359 16363 16364 16366 16367 16368 16369 16370 16371 
    ##     4     3     5     4     3     5     3     5     5     5     5     5     3 
    ## 16372 16373 16374 16375 16376 16378 16379 16380 16385 16387 16389 16391 16393 
    ##     3     4     5     5     5     5     3     4     5     4     4     4     5 
    ## 16394 16395 16396 16397 16398 16399 16400 16401 16402 16403 16405 16406 16408 
    ##     5     5     5     5     5     4     5     4     3     3     3     3     5 
    ## 16411 16412 16413 16416 16417 16420 16421 16422 16425 16426 16427 16428 16429 
    ##     5     4     5     5     4     5     5     5     3     4     5     5     5 
    ## 16430 16431 16432 16433 16434 16435 16436 16438 16440 16441 16442 16443 16445 
    ##     4     3     3     3     5     4     5     5     5     5     3     4     5 
    ## 16446 16447 16448 16449 16450 16451 16453 16454 16455 16456 16457 16461 16462 
    ##     5     5     5     5     5     4     5     5     4     5     5     4     3 
    ## 16463 16464 16465 16467 16470 16471 16473 16474 16475 16476 16477 16479 16480 
    ##     4     5     5     5     5     3     5     4     5     5     3     5     5 
    ## 16481 16483 16484 16485 16486 16487 16490 16491 16492 16493 16495 16497 16498 
    ##     4     5     4     3     3     5     3     5     5     4     4     5     3 
    ## 16499 16500 16501 16502 16503 16506 16508 16509 16510 16515 16516 16517 16518 
    ##     5     3     5     5     4     4     4     3     4     5     4     4     5 
    ## 16519 16520 16522 16524 16526 16527 16528 16530 16532 16533 16534 16536 16537 
    ##     3     3     5     4     3     5     3     3     3     4     5     5     4 
    ## 16539 16540 16541 16542 16543 16544 16545 16546 16547 16548 16549 16550 16551 
    ##     5     3     5     5     3     3     3     5     5     5     5     5     5 
    ## 16552 16553 16554 16556 16558 16559 16560 16561 16562 16563 16564 16567 16568 
    ##     5     4     5     3     3     5     4     5     5     5     5     3     5 
    ## 16569 16570 16571 16572 16573 16574 16578 16579 16580 16581 16583 16584 16586 
    ##     5     3     5     4     3     5     3     5     5     5     5     4     3 
    ## 16587 16590 16591 16593 16594 16595 16596 16597 16598 16600 16602 16603 16605 
    ##     5     4     5     5     5     3     4     5     3     5     3     5     5 
    ## 16607 16608 16609 16610 16611 16612 16614 16615 16616 16617 16622 16623 16624 
    ##     3     5     5     4     5     4     3     5     5     4     3     4     5 
    ## 16625 16626 16627 16629 16630 16633 16634 16635 16637 16638 16639 16641 16644 
    ##     3     4     5     3     5     3     5     3     4     5     4     4     4 
    ## 16645 16646 16648 16650 16652 16653 16654 16655 16656 16657 16658 16659 16662 
    ##     5     5     5     4     5     3     3     3     5     5     5     3     5 
    ## 16663 16664 16665 16666 16667 16669 16670 16674 16675 16676 16681 16683 16685 
    ##     4     5     3     4     3     3     5     4     3     5     3     3     4 
    ## 16686 16688 16689 16690 16691 16692 16693 16694 16695 16696 16697 16698 16700 
    ##     4     5     3     3     5     5     5     5     4     4     5     4     4 
    ## 16701 16702 16703 16704 16705 16706 16707 16708 16709 16710 16711 16712 16713 
    ##     3     5     4     5     5     5     5     4     3     5     3     5     4 
    ## 16714 16717 16718 16719 16721 16724 16725 16726 16727 16729 16730 16731 16734 
    ##     3     3     3     3     5     3     4     3     5     5     4     5     3 
    ## 16736 16737 16738 16740 16743 16746 16748 16749 16750 16751 16752 16753 16755 
    ##     5     3     5     5     5     5     4     3     4     3     4     5     5 
    ## 16756 16757 16758 16759 16760 16761 16764 16765 16766 16768 16770 16771 16772 
    ##     4     4     5     5     5     4     5     5     3     4     5     3     5 
    ## 16774 16775 16777 16778 16779 16780 16781 16783 16785 16786 16788 16789 16790 
    ##     3     5     5     5     4     3     3     5     5     3     4     5     5 
    ## 16791 16792 16793 16794 16795 16796 16798 16799 16800 16801 16802 16803 16804 
    ##     5     5     4     5     5     5     4     5     4     5     5     4     3 
    ## 16807 16808 16812 16813 16814 16815 16817 16818 16820 16821 16822 16824 16827 
    ##     4     5     4     4     5     5     5     5     5     5     5     4     5 
    ## 16828 16829 16830 16831 16832 16833 16836 16837 16839 16840 16842 16844 16845 
    ##     5     5     4     4     3     5     5     4     3     5     5     5     3 
    ## 16846 16847 16848 16850 16852 16853 16855 16856 16857 16859 16860 16861 16862 
    ##     3     5     5     5     5     5     3     5     5     5     5     5     5 
    ## 16863 16865 16866 16867 16868 16870 16871 16875 16876 16877 16880 16881 16883 
    ##     3     5     3     5     5     5     4     4     3     5     4     5     5 
    ## 16885 16888 16889 16891 16892 16897 16898 16900 16901 16903 16904 16905 16906 
    ##     5     5     4     5     5     4     3     5     3     5     3     3     4 
    ## 16907 16909 16910 16911 16913 16914 16915 16916 16917 16920 16922 16924 16925 
    ##     3     4     4     5     5     3     3     5     3     3     3     4     5 
    ## 16927 16928 16929 16930 16931 16933 16934 16935 16937 16938 16939 16940 16942 
    ##     4     5     4     5     5     4     5     4     4     4     4     5     4 
    ## 16943 16946 16947 16948 16952 16954 16955 16956 16957 16958 16960 16962 16964 
    ##     5     3     3     5     4     4     3     4     5     5     4     4     4 
    ## 16966 16967 16969 16971 16972 16973 16976 16978 16980 16981 16982 16987 16988 
    ##     3     4     3     5     5     5     5     4     5     3     5     3     3 
    ## 16989 16990 16991 16992 16995 16998 17000 17002 17003 17005 17006 17008 17009 
    ##     5     3     3     5     5     5     5     5     5     3     5     3     3 
    ## 17012 17014 17015 17016 17018 17019 17020 17021 17022 17023 17024 17025 17026 
    ##     5     3     5     3     4     3     5     3     5     4     3     3     5 
    ## 17027 17028 17030 17031 17034 17035 17039 17040 17043 17045 17046 17051 17052 
    ##     3     5     4     5     3     5     3     5     4     4     3     5     5 
    ## 17055 17056 17057 17058 17059 17060 17061 17062 17063 17064 17065 17067 17068 
    ##     5     4     4     5     4     5     5     5     3     3     5     4     5 
    ## 17069 17071 17072 17073 17075 17076 17077 17080 17081 17082 17085 17088 17089 
    ##     5     5     3     4     4     3     4     3     5     4     3     4     5 
    ## 17090 17091 17092 17095 17097 17099 17101 17102 17103 17105 17107 17108 17109 
    ##     5     5     3     5     5     5     5     5     5     5     5     5     5 
    ## 17111 17113 17114 17115 17116 17117 17119 17120 17121 17122 17123 17124 17125 
    ##     5     5     4     4     4     5     5     3     3     5     5     5     5 
    ## 17126 17127 17128 17129 17131 17132 17133 17134 17135 17136 17137 17138 17139 
    ##     5     4     4     4     3     4     5     3     3     5     4     3     5 
    ## 17141 17142 17143 17144 17145 17146 17147 17148 17150 17151 17152 17156 17157 
    ##     3     3     5     5     3     5     4     5     5     5     5     5     5 
    ## 17161 17162 17164 17165 17166 17167 17169 17172 17173 17177 17178 17179 17183 
    ##     5     4     5     5     3     5     3     3     3     4     4     5     5 
    ## 17185 17187 17189 17190 17191 17192 17193 17195 17196 17200 17201 17202 17203 
    ##     5     3     3     5     5     5     5     5     5     5     5     5     5 
    ## 17205 17206 17207 17209 17212 17213 17214 17217 17220 17221 17223 17224 17225 
    ##     4     5     5     3     4     4     3     4     3     4     4     5     5 
    ## 17228 17231 17232 17233 17234 17235 17236 17237 17238 17239 17240 17242 17243 
    ##     4     5     5     4     4     5     3     5     5     4     5     4     5 
    ## 17244 17245 17248 17249 17250 17252 17255 17257 17258 17259 17261 17262 17263 
    ##     5     4     5     3     3     5     5     5     5     4     5     3     5 
    ## 17264 17267 17268 17271 17272 17273 17274 17275 17276 17277 17278 17279 17281 
    ##     5     5     4     4     5     3     3     5     5     3     4     5     5 
    ## 17282 17283 17284 17285 17288 17289 17290 17293 17294 17295 17296 17297 17300 
    ##     5     4     5     3     5     5     5     4     3     5     4     5     5 
    ## 17302 17303 17304 17305 17306 17307 17308 17309 17310 17311 17312 17313 17314 
    ##     5     4     5     5     4     5     5     5     5     5     5     3     5 
    ## 17315 17316 17317 17318 17319 17321 17323 17324 17325 17326 17327 17328 17329 
    ##     4     5     3     4     4     4     5     5     5     5     3     4     5 
    ## 17330 17331 17332 17333 17334 17336 17337 17338 17339 17340 17341 17342 17343 
    ##     5     3     5     5     5     3     4     5     5     3     5     4     4 
    ## 17344 17345 17347 17348 17349 17350 17351 17353 17355 17356 17357 17358 17360 
    ##     5     5     5     5     4     5     5     4     4     5     5     5     5 
    ## 17361 17362 17365 17366 17368 17369 17371 17372 17374 17375 17377 17378 17379 
    ##     5     5     3     4     3     5     4     5     5     5     3     4     5 
    ## 17382 17383 17384 17387 17388 17389 17390 17392 17393 17395 17396 17397 17398 
    ##     4     5     3     4     4     4     4     4     5     5     5     5     5 
    ## 17402 17404 17406 17407 17408 17409 17410 17411 17412 17413 17414 17415 17416 
    ##     5     5     4     5     5     5     5     4     5     3     3     5     4 
    ## 17419 17421 17423 17424 17425 17426 17429 17430 17431 17432 17433 17435 17436 
    ##     5     5     3     5     5     5     4     5     5     5     5     5     5 
    ## 17439 17440 17441 17442 17443 17444 17445 17447 17448 17449 17450 17452 17454 
    ##     5     5     4     3     3     5     5     5     4     5     4     5     3 
    ## 17455 17456 17457 17458 17459 17460 17461 17462 17464 17465 17466 17468 17470 
    ##     5     3     5     3     5     4     4     4     4     3     5     4     4 
    ## 17472 17473 17478 17479 17480 17481 17484 17485 17488 17489 17491 17495 17496 
    ##     5     5     5     4     3     5     5     3     5     3     4     4     5 
    ## 17497 17498 17500 17501 17502 17503 17505 17507 17509 17511 17512 17513 17514 
    ##     5     4     3     5     5     5     5     5     4     5     5     3     5 
    ## 17515 17516 17517 17519 17521 17522 17524 17525 17528 17529 17530 17531 17533 
    ##     5     5     5     5     5     5     5     5     3     3     4     5     5 
    ## 17534 17535 17536 17537 17538 17540 17541 17543 17544 17548 17549 17550 17552 
    ##     5     3     4     4     5     4     5     5     4     4     5     5     5 
    ## 17554 17555 17558 17559 17561 17562 17563 17564 17565 17566 17570 17571 17573 
    ##     5     5     5     5     5     5     3     3     5     4     4     5     5 
    ## 17574 17576 17577 17578 17579 17585 17586 17588 17589 17591 17592 17593 17594 
    ##     4     5     5     3     3     5     5     4     5     5     5     4     5 
    ## 17595 17596 17597 17598 17599 17600 17602 17603 17604 17605 17606 17609 17611 
    ##     5     4     5     5     5     5     5     4     3     5     5     4     5 
    ## 17612 17613 17615 17616 17618 17619 17620 17621 17622 17623 17624 17626 17629 
    ##     3     5     5     5     3     5     5     5     4     5     5     4     4 
    ## 17633 17634 17635 17636 17637 17640 17641 17642 17643 17645 17646 17652 17653 
    ##     5     5     5     3     4     5     5     5     5     5     5     5     5 
    ## 17656 17657 17658 17661 17662 17665 17667 17668 17670 17671 17672 17673 17674 
    ##     5     5     4     5     3     3     5     4     5     5     5     3     3 
    ## 17676 17677 17678 17679 17680 17681 17683 17684 17685 17687 17688 17689 17692 
    ##     5     4     3     5     5     3     5     5     5     5     4     4     5 
    ## 17695 17696 17697 17698 17699 17700 17701 17703 17704 17706 17707 17709 17710 
    ##     5     3     5     3     5     5     3     5     3     5     4     4     3 
    ## 17711 17712 17713 17717 17718 17720 17721 17722 17723 17724 17725 17726 17727 
    ##     3     5     3     3     5     5     3     4     4     5     3     5     5 
    ## 17728 17729 17730 17731 17732 17733 17734 17736 17737 17738 17739 17740 17741 
    ##     3     4     4     5     5     5     3     5     5     5     4     3     3 
    ## 17742 17744 17745 17746 17747 17748 17749 17750 17751 17756 17757 17758 17759 
    ##     4     5     5     3     3     3     5     4     5     5     5     5     5 
    ## 17760 17761 17762 17763 17764 17765 17766 17767 17768 17770 17774 17775 17777 
    ##     5     4     4     3     3     4     3     5     3     5     5     4     5 
    ## 17778 17779 17781 17782 17783 17784 17785 17786 17788 17789 17790 17791 17792 
    ##     5     5     4     5     5     5     5     3     5     5     5     4     4 
    ## 17793 17795 17796 17798 17799 17802 17803 17804 17805 17806 17808 17809 17810 
    ##     4     3     4     5     5     5     5     5     5     3     5     4     3 
    ## 17812 17813 17814 17815 17816 17817 17818 17819 17822 17823 17824 17827 17828 
    ##     5     3     5     4     5     3     5     5     5     4     5     5     5 
    ## 17829 17830 17831 17832 17834 17835 17837 17838 17839 17840 17842 17844 17846 
    ##     5     5     4     3     5     3     5     3     3     5     4     5     3 
    ## 17847 17848 17849 17850 17851 17852 17853 17855 17856 17857 17858 17859 17862 
    ##     5     3     3     5     4     5     3     5     5     4     5     4     4 
    ## 17863 17865 17866 17869 17870 17871 17872 17873 17876 17877 17879 17883 17884 
    ##     5     4     5     4     5     5     4     5     5     3     4     3     5 
    ## 17885 17886 17887 17888 17889 17890 17891 17893 17894 17895 17897 17898 17899 
    ##     4     5     4     5     3     5     5     3     5     5     5     4     4 
    ## 17900 17901 17904 17909 17911 17912 17914 17916 17917 17920 17921 17922 17925 
    ##     3     4     5     5     3     3     4     3     5     5     4     5     4 
    ## 17926 17927 17928 17929 17930 17931 17932 17933 17934 17935 17938 17940 17941 
    ##     4     4     5     5     4     3     3     5     5     4     5     5     5 
    ## 17942 17944 17945 17946 17947 17949 17950 17951 17953 17954 17955 17957 17958 
    ##     4     3     5     3     4     5     4     5     5     3     5     5     5 
    ## 17959 17961 17962 17965 17966 17967 17969 17971 17973 17977 17978 17979 17980 
    ##     5     5     3     5     5     5     5     4     5     5     3     5     5 
    ## 17981 17986 17988 17989 17991 17992 17994 17996 17997 17999 18000 18003 18005 
    ##     3     4     5     5     4     5     5     5     4     5     5     3     5 
    ## 18006 18009 18010 18011 18013 18017 18018 18020 18022 18026 18027 18028 18029 
    ##     4     5     5     5     5     3     5     5     5     5     3     3     5 
    ## 18030 18031 18033 18035 18036 18038 18039 18041 18044 18045 18046 18049 18050 
    ##     5     5     5     4     4     4     5     5     5     4     5     4     4 
    ## 18051 18052 18053 18054 18056 18057 18058 18059 18060 18061 18063 18065 18066 
    ##     4     4     4     4     5     5     5     4     5     4     5     5     5 
    ## 18067 18069 18070 18071 18073 18074 18075 18077 18078 18080 18081 18082 18083 
    ##     5     5     4     5     5     5     5     4     5     5     3     3     4 
    ## 18084 18085 18086 18087 18089 18090 18092 18093 18095 18096 18097 18098 18099 
    ##     4     5     5     4     4     3     3     5     3     5     4     5     3 
    ## 18100 18101 18102 18103 18105 18107 18108 18109 18110 18111 18113 18114 18115 
    ##     4     5     5     4     5     3     4     5     4     4     3     3     4 
    ## 18116 18118 18119 18120 18122 18125 18126 18127 18128 18129 18130 18131 18132 
    ##     5     3     5     5     4     3     5     4     4     5     5     4     4 
    ## 18133 18134 18135 18136 18137 18139 18140 18141 18142 18144 18146 18150 18151 
    ##     3     4     3     5     5     3     3     5     5     5     4     5     4 
    ## 18152 18154 18155 18158 18160 18161 18162 18163 18165 18166 18167 18168 18169 
    ##     3     5     5     4     5     5     3     5     5     5     5     3     4 
    ## 18171 18172 18173 18174 18175 18176 18177 18178 18179 18181 18183 18184 18185 
    ##     5     3     4     3     3     3     5     3     4     3     3     3     5 
    ## 18186 18187 18188 18189 18190 18194 18195 18196 18198 18201 18204 18205 18206 
    ##     4     5     5     5     5     5     5     5     3     3     3     4     3 
    ## 18207 18208 18209 18210 18211 18213 18214 18215 18216 18217 18219 18220 18221 
    ##     3     5     4     5     3     3     4     5     3     4     5     3     5 
    ## 18222 18223 18224 18227 18228 18229 18230 18232 18234 18235 18236 18238 18241 
    ##     5     3     3     3     3     5     5     5     5     5     5     3     4 
    ## 18242 18244 18245 18247 18248 18249 18251 18252 18254 18255 18256 18257 18258 
    ##     5     3     5     5     4     4     4     5     3     5     5     5     5 
    ## 18259 18260 18261 18262 18263 18264 18266 18267 18269 18270 18271 18273 18274 
    ##     3     5     5     4     3     5     5     3     5     5     5     5     3 
    ## 18275 18276 18278 18279 18280 18281 18282 18284 18285 18286 18288 18289 18290 
    ##     3     5     5     5     5     5     5     3     3     5     4     4     5 
    ## 18292 18293 18294 18295 18296 18297 18298 18299 18300 18301 18302 18303 18304 
    ##     5     5     3     3     4     5     3     5     4     3     5     5     5 
    ## 18305 18306 18307 18308 18309 18310 18311 18312 18313 18314 18318 18320 18323 
    ##     5     5     5     3     5     5     3     5     3     5     5     5     5 
    ## 18324 18325 18326 18328 18329 18331 18334 18337 18338 18341 18342 18343 18347 
    ##     3     4     5     5     5     3     5     5     4     5     3     4     5 
    ## 18349 18350 18351 18352 18354 18355 18356 18357 18359 18363 18364 18365 18366 
    ##     5     5     5     5     5     5     3     3     5     5     4     5     4 
    ## 18367 18368 18369 18370 18371 18373 18375 18376 18377 18378 18379 18381 18383 
    ##     5     5     4     5     3     3     5     5     4     4     5     5     5 
    ## 18384 18386 18387 18388 18391 18392 18393 18394 18395 18396 18397 18398 18399 
    ##     4     4     3     3     5     4     5     3     5     5     5     4     5 
    ## 18400 18401 18403 18404 18405 18406 18410 18411 18413 18415 18416 18417 18418 
    ##     3     3     3     4     5     5     5     5     4     5     5     4     5 
    ## 18420 18421 18422 18424 18425 18426 18428 18429 18431 18432 18433 18434 18435 
    ##     5     5     5     5     4     3     5     5     5     5     5     4     4 
    ## 18437 18438 18439 18440 18445 18446 18447 18450 18451 18454 18455 18457 18458 
    ##     5     3     3     5     5     5     3     5     5     4     3     4     4 
    ## 18459 18461 18462 18464 18466 18467 18468 18471 18472 18474 18475 18476 18477 
    ##     3     5     5     5     5     5     5     3     4     4     5     5     4 
    ## 18478 18479 18483 18484 18485 18486 18487 18489 18492 18493 18496 18498 18499 
    ##     4     5     5     4     4     3     4     5     3     5     5     5     3 
    ## 18500 18501 18503 18505 18507 18508 18510 18513 18516 18517 18518 18520 18521 
    ##     3     5     5     4     4     5     5     4     3     3     5     5     5 
    ## 18522 18523 18524 18526 18528 18529 18531 18532 18534 18535 18538 18540 18541 
    ##     5     3     5     5     3     4     3     5     5     4     3     3     4 
    ## 18542 18543 18544 18545 18546 18547 18548 18549 18551 18552 18553 18554 18556 
    ##     3     5     4     4     4     4     4     3     5     5     5     5     5 
    ## 18557 18559 18560 18561 18562 18563 18564 18565 18566 18568 18569 18570 18572 
    ##     4     4     5     5     5     4     5     5     4     3     4     3     5 
    ## 18573 18574 18575 18576 18577 18578 18579 18580 18581 18582 18583 18584 18586 
    ##     5     5     3     4     4     5     5     4     4     3     5     4     5 
    ## 18587 18590 18592 18593 18596 18598 18600 18601 18602 18605 18606 18607 18608 
    ##     5     4     3     5     3     4     4     5     3     5     3     5     4 
    ## 18609 18610 18611 18614 18616 18618 18620 18621 18622 18623 18625 18626 18627 
    ##     5     3     4     5     5     5     4     3     5     5     3     5     5 
    ## 18629 18631 18636 18637 18638 18639 18640 18641 18642 18645 18646 18647 18648 
    ##     5     3     3     4     5     5     5     3     3     3     4     4     3 
    ## 18649 18650 18653 18654 18655 18656 18658 18659 18660 18661 18665 18666 18667 
    ##     5     4     5     3     5     5     4     4     5     5     5     5     3 
    ## 18668 18670 18671 18672 18675 18676 18677 18678 18679 18681 18682 18683 18684 
    ##     4     5     5     3     3     4     5     3     5     5     5     5     4 
    ## 18686 18689 18691 18692 18693 18695 18696 18701 18703 18706 18708 18709 18710 
    ##     5     5     4     4     5     4     5     5     3     5     3     5     5 
    ## 18712 18715 18716 18718 18720 18721 18722 18723 18724 18725 18727 18728 18729 
    ##     5     5     5     5     5     5     5     3     5     5     4     5     3 
    ## 18732 18733 18734 18735 18736 18737 18739 18740 18741 18742 18744 18745 18746 
    ##     5     4     5     4     5     5     3     4     3     4     5     4     5 
    ## 18747 18748 18749 18750 18751 18752 18755 18756 18757 18759 18760 18761 18763 
    ##     5     4     5     4     5     4     4     3     5     5     3     3     5 
    ## 18764 18766 18767 18768 18770 18771 18772 18773 18775 18776 18777 18779 18780 
    ##     5     3     5     3     5     5     5     3     3     3     5     5     3 
    ## 18782 18783 18785 18787 18788 18789 18790 18791 18792 18793 18794 18795 18797 
    ##     3     3     3     4     5     3     5     3     5     3     5     4     5 
    ## 18799 18800 18801 18802 18806 18807 18808 18810 18811 18812 18813 18814 18815 
    ##     4     4     4     5     5     5     3     5     5     3     5     5     5 
    ## 18816 18817 18818 18819 18820 18821 18823 18824 18826 18827 18828 18830 18831 
    ##     5     5     4     4     5     5     5     5     5     3     5     5     3 
    ## 18832 18833 18834 18835 18836 18837 18839 18841 18843 18844 18845 18846 18847 
    ##     5     5     3     5     4     5     5     4     5     5     4     5     4 
    ## 18848 18851 18852 18854 18855 18857 18861 18864 18865 18869 18870 18874 18876 
    ##     4     3     3     5     5     5     5     3     3     5     5     5     5 
    ## 18879 18881 18882 18883 18886 18887 18888 18890 18892 18893 18895 18897 18901 
    ##     5     5     3     5     5     5     4     3     5     5     5     3     5 
    ## 18902 18903 18906 18907 18908 18909 18911 18912 18913 18914 18915 18916 18917 
    ##     5     5     5     5     5     5     3     5     3     5     5     3     3 
    ## 18918 18921 18924 18925 18926 18927 18928 18929 18931 18932 18933 18938 18940 
    ##     3     4     4     4     5     3     5     4     4     5     5     4     5 
    ## 18943 18946 18948 18949 18950 18951 18952 18953 18956 18959 18960 18962 18964 
    ##     4     4     3     5     5     5     4     3     3     5     3     5     3 
    ## 18965 18966 18967 18968 18969 18970 18972 18973 18975 18976 18977 18979 18980 
    ##     5     4     5     5     5     5     5     3     5     3     5     3     5 
    ## 18981 18982 18984 18985 18987 18988 18990 18991 18993 18995 18996 18997 19000 
    ##     5     4     5     4     4     5     5     3     4     5     3     5     3 
    ## 19001 19004 19006 19008 19009 19010 19011 19012 19014 19015 19016 19017 19018 
    ##     5     4     3     5     5     3     5     4     5     5     4     4     5 
    ## 19021 19022 19024 19025 19027 19030 19034 19035 19036 19037 19042 19043 19044 
    ##     5     4     3     5     4     3     3     5     5     5     3     5     5 
    ## 19047 19049 19051 19052 19053 19054 19055 19056 19057 19058 19059 19060 19061 
    ##     5     5     3     4     5     5     5     5     5     5     3     5     5 
    ## 19062 19063 19064 19065 19066 19069 19070 19072 19073 19074 19075 19076 19077 
    ##     5     5     4     5     3     4     5     3     5     3     5     4     4 
    ## 19079 19080 19081 19082 19083 19085 19088 19089 19090 19092 19093 19094 19095 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     3 
    ## 19096 19097 19098 19100 19103 19105 19106 19107 19109 19110 19111 19112 19113 
    ##     5     5     4     5     4     5     4     5     5     4     5     4     5 
    ## 19114 19115 19116 19117 19118 19120 19121 19122 19123 19124 19125 19127 19128 
    ##     5     5     5     3     4     5     3     3     3     5     3     4     5 
    ## 19130 19131 19132 19133 19134 19136 19138 19139 19140 19141 19142 19144 19145 
    ##     5     5     3     5     5     5     5     4     5     3     5     4     4 
    ## 19146 19147 19148 19149 19151 19152 19153 19157 19158 19159 19160 19161 19162 
    ##     5     5     4     5     4     5     5     3     3     4     5     5     5 
    ## 19163 19164 19166 19167 19169 19170 19171 19172 19174 19175 19177 19178 19179 
    ##     5     3     5     5     5     5     3     5     3     5     3     5     5 
    ## 19181 19182 19183 19184 19185 19187 19194 19197 19198 19199 19202 19203 19204 
    ##     5     3     5     5     5     4     5     5     5     4     3     3     3 
    ## 19206 19207 19208 19209 19214 19217 19218 19219 19220 19221 19222 19224 19226 
    ##     3     3     3     3     5     5     4     5     3     5     3     3     5 
    ## 19230 19232 19233 19234 19236 19237 19238 19239 19242 19243 19244 19246 19247 
    ##     5     4     5     5     5     5     3     5     5     5     4     5     3 
    ## 19248 19249 19251 19252 19256 19257 19260 19261 19262 19263 19264 19265 19267 
    ##     5     5     5     3     5     3     3     4     3     4     3     5     5 
    ## 19272 19273 19274 19275 19276 19277 19279 19280 19285 19286 19287 19289 19292 
    ##     4     5     4     5     4     5     5     5     5     5     5     5     5 
    ## 19293 19294 19295 19296 19297 19298 19299 19300 19301 19302 19303 19305 19307 
    ##     3     5     4     3     5     3     5     5     5     5     3     3     5 
    ## 19308 19310 19311 19313 19314 19315 19316 19317 19318 19319 19325 19326 19327 
    ##     5     5     4     3     5     5     5     5     5     3     3     5     3 
    ## 19328 19329 19330 19332 19335 19336 19340 19341 19342 19344 19345 19347 19348 
    ##     5     5     5     5     5     4     5     5     5     5     5     5     4 
    ## 19349 19351 19352 19353 19354 19356 19357 19358 19359 19360 19361 19362 19363 
    ##     5     3     5     3     5     4     4     5     5     5     4     5     4 
    ## 19365 19366 19369 19372 19373 19374 19375 19376 19377 19379 19380 19381 19382 
    ##     3     3     5     3     4     5     5     5     5     5     5     3     4 
    ## 19384 19385 19386 19387 19388 19389 19391 19393 19396 19398 19399 19400 19402 
    ##     3     5     4     3     5     4     5     5     5     5     5     5     5 
    ## 19403 19404 19405 19406 19407 19408 19409 19410 19414 19415 19416 19418 19420 
    ##     3     5     3     5     4     5     3     4     5     3     5     5     5 
    ## 19422 19424 19426 19429 19430 19432 19433 19435 19437 19439 19442 19443 19444 
    ##     3     3     5     3     5     5     5     3     3     5     5     5     4 
    ## 19445 19447 19448 19449 19450 19451 19452 19454 19455 19457 19458 19459 19460 
    ##     4     3     5     5     3     3     5     5     3     3     5     4     5 
    ## 19461 19462 19463 19464 19465 19467 19468 19469 19471 19474 19477 19479 19480 
    ##     4     5     5     5     5     5     5     4     5     5     3     3     3 
    ## 19481 19482 19483 19484 19485 19486 19487 19488 19489 19490 19491 19492 19494 
    ##     5     4     4     5     5     5     5     4     5     4     5     5     5 
    ## 19495 19496 19497 19498 19500 19501 19502 19503 19504 19505 19506 19507 19511 
    ##     3     5     5     5     5     5     5     5     4     5     4     5     4 
    ## 19512 19513 19515 19516 19517 19519 19520 19521 19522 19524 19525 19526 19528 
    ##     5     3     4     5     4     4     5     3     3     5     4     3     3 
    ## 19529 19532 19534 19535 19536 19538 19541 19545 19548 19549 19552 19553 19554 
    ##     3     4     5     3     3     3     5     5     4     5     3     5     3 
    ## 19555 19557 19559 19562 19563 19564 19565 19566 19567 19568 19569 19570 19572 
    ##     5     5     5     5     5     5     5     5     3     4     4     5     3 
    ## 19573 19574 19576 19578 19579 19580 19582 19583 19585 19586 19589 19590 19594 
    ##     5     5     5     5     5     4     5     3     5     5     3     4     4 
    ## 19595 19596 19597 19598 19599 19601 19603 19604 19605 19606 19607 19608 19609 
    ##     5     5     5     5     5     5     5     5     5     3     5     3     4 
    ## 19610 19612 19613 19614 19615 19617 19618 19619 19620 19621 19622 19623 19624 
    ##     5     5     3     3     5     5     5     5     5     5     5     3     5 
    ## 19625 19626 19628 19629 19630 19633 19636 19637 19638 19639 19640 19641 19644 
    ##     5     5     5     5     5     5     5     4     4     3     5     5     5 
    ## 19645 19647 19648 19650 19651 19652 19653 19654 19655 19656 19658 19659 19660 
    ##     5     3     3     3     5     5     3     4     5     3     4     3     5 
    ## 19661 19663 19664 19665 19666 19667 19668 19669 19672 19673 19675 19676 19678 
    ##     4     5     3     3     5     5     5     5     5     4     4     3     5 
    ## 19680 19682 19684 19685 19686 19687 19689 19691 19692 19694 19696 19697 19699 
    ##     5     5     3     4     5     3     3     5     5     5     4     4     3 
    ## 19700 19701 19702 19703 19704 19705 19706 19707 19709 19710 19711 19712 19713 
    ##     3     4     3     5     3     3     3     5     5     5     5     3     5 
    ## 19714 19715 19716 19717 19720 19721 19725 19727 19728 19729 19730 19731 19732 
    ##     4     5     4     3     5     5     3     4     4     5     4     3     5 
    ## 19733 19734 19735 19736 19737 19738 19739 19741 19743 19744 19748 19749 19751 
    ##     5     4     3     4     5     5     4     5     5     4     4     5     5 
    ## 19752 19753 19754 19755 19756 19757 19760 19763 19766 19767 19768 19769 19770 
    ##     3     5     3     4     5     5     3     5     5     5     3     3     4 
    ## 19771 19772 19776 19777 19778 19779 19781 19782 19784 19787 19790 19791 19794 
    ##     5     5     3     3     5     5     5     4     3     4     5     3     5 
    ## 19795 19796 19797 19803 19804 19806 19807 19808 19809 19810 19811 19812 19813 
    ##     5     3     3     5     4     4     5     5     3     4     3     5     5 
    ## 19815 19816 19817 19819 19820 19821 19822 19823 19825 19826 19827 19829 19830 
    ##     4     5     4     5     5     5     5     4     5     5     3     5     5 
    ## 19831 19833 19834 19835 19836 19838 19840 19841 19842 19843 19844 19845 19847 
    ##     4     5     5     5     4     5     5     4     4     4     5     4     5 
    ## 19848 19851 19852 19853 19854 19855 19856 19858 19860 19861 19863 19864 19865 
    ##     3     3     5     5     5     3     5     3     3     5     3     5     5 
    ## 19866 19867 19868 19869 19870 19871 19872 19874 19875 19878 19879 19880 19881 
    ##     3     3     5     3     4     5     5     3     4     5     5     5     5 
    ## 19882 19884 19885 19886 19889 19890 19893 19894 19897 19900 19901 19904 19906 
    ##     4     5     3     5     3     5     3     5     5     4     4     5     5 
    ## 19907 19909 19910 19911 19912 19913 19915 19916 19917 19918 19919 19920 19922 
    ##     4     5     3     5     3     5     5     5     5     5     4     5     5 
    ## 19924 19925 19928 19930 19932 19933 19934 19935 19937 19938 19939 19940 19941 
    ##     3     5     3     4     3     3     5     3     4     4     5     3     3 
    ## 19942 19944 19945 19946 19947 19949 19950 19951 19952 19953 19956 19957 19958 
    ##     3     5     3     3     3     5     3     5     3     4     3     4     4 
    ## 19959 19960 19961 19963 19964 19965 19968 19969 19970 19971 19974 19976 19978 
    ##     5     5     3     3     4     5     3     3     3     4     3     4     5 
    ## 19979 19980 19982 19983 19984 19985 19987 19988 19989 19990 19991 19992 19994 
    ##     3     5     3     5     5     5     3     4     5     3     3     4     5 
    ## 19995 19997 19999 20000 20002 20003 20005 20007 20008 20009 20012 20014 20015 
    ##     3     4     3     5     5     4     5     3     5     5     5     4     5 
    ## 20017 20018 20019 20022 20024 20025 20026 20027 20028 20029 20030 20033 20034 
    ##     3     5     3     5     5     5     3     5     4     3     5     3     4 
    ## 20036 20037 20038 20039 20040 20042 20043 20045 20046 20047 20048 20050 20053 
    ##     5     5     4     4     5     3     3     4     5     3     5     4     5 
    ## 20055 20057 20058 20060 20061 20062 20064 20065 20066 20067 20069 20070 20071 
    ##     5     5     5     4     5     5     4     4     5     3     5     5     5 
    ## 20072 20073 20074 20078 20080 20082 20084 20085 20086 20087 20088 20089 20090 
    ##     5     5     5     5     5     3     5     5     5     3     3     5     5 
    ## 20096 20097 20100 20102 20103 20104 20105 20107 20108 20109 20110 20112 20115 
    ##     5     4     4     5     4     5     5     4     5     3     5     3     5 
    ## 20117 20118 20119 20120 20122 20123 20124 20125 20128 20129 20130 20131 20132 
    ##     3     5     4     3     3     4     4     4     4     5     5     5     5 
    ## 20133 20134 20136 20137 20138 20139 20140 20141 20143 20145 20146 20147 20148 
    ##     5     5     5     5     3     4     3     5     5     3     4     5     5 
    ## 20149 20150 20151 20153 20154 20158 20159 20160 20161 20162 20165 20166 20167 
    ##     5     3     3     3     5     5     5     5     5     5     5     5     5 
    ## 20168 20171 20172 20173 20174 20175 20177 20178 20179 20180 20181 20182 20183 
    ##     5     3     5     5     5     3     5     5     5     4     4     5     5 
    ## 20184 20186 20187 20188 20189 20191 20192 20193 20196 20197 20198 20199 20200 
    ##     5     4     5     5     5     4     5     4     3     4     5     4     5 
    ## 20201 20204 20205 20206 20207 20209 20210 20211 20213 20214 20215 20217 20219 
    ##     5     5     5     5     5     4     5     5     5     5     5     4     3 
    ## 20220 20222 20223 20225 20226 20227 20228 20229 20232 20233 20236 20237 20238 
    ##     4     3     3     3     4     5     5     5     4     4     5     3     3 
    ## 20239 20240 20241 20243 20244 20245 20246 20247 20248 20250 20252 20254 20256 
    ##     3     4     5     5     3     5     3     5     4     5     5     3     4 
    ## 20257 20258 20260 20261 20262 20264 20265 20267 20268 20269 20270 20271 20272 
    ##     5     5     5     5     4     4     3     3     4     5     5     5     5 
    ## 20273 20274 20276 20277 20278 20279 20280 20281 20282 20283 20284 20286 20287 
    ##     3     4     5     3     5     5     3     5     3     4     5     5     4 
    ## 20288 20289 20291 20295 20296 20297 20298 20300 20301 20302 20303 20304 20305 
    ##     3     5     4     5     3     5     4     3     5     5     5     5     4 
    ## 20306 20308 20309 20310 20311 20312 20313 20314 20315 20316 20320 20321 20322 
    ##     3     3     3     5     3     3     5     5     5     5     3     3     4 
    ## 20324 20325 20326 20328 20330 20331 20332 20333 20334 20335 20336 20338 20339 
    ##     5     4     3     5     5     3     5     3     3     4     5     5     5 
    ## 20341 20342 20343 20344 20346 20350 20351 20352 20354 20355 20356 20359 20361 
    ##     3     5     5     5     4     5     5     4     4     4     4     5     3 
    ## 20363 20364 20365 20366 20367 20370 20372 20376 20379 20380 20382 20384 20386 
    ##     5     5     5     5     3     5     5     5     5     4     4     4     5 
    ## 20388 20389 20390 20391 20392 20393 20394 20395 20396 20397 20398 20400 20401 
    ##     5     5     4     5     3     3     5     5     5     5     4     3     3 
    ## 20402 20403 20404 20405 20406 20407 20409 20411 20412 20413 20414 20415 20416 
    ##     5     4     4     3     5     3     3     5     5     3     3     4     5 
    ## 20417 20419 20420 20421 20422 20423 20425 20426 20427 20430 20432 20433 20434 
    ##     5     4     4     4     5     5     5     3     3     5     3     5     5 
    ## 20437 20438 20439 20441 20443 20444 20445 20448 20449 20451 20452 20455 20456 
    ##     5     3     3     5     5     5     5     4     5     5     5     5     5 
    ## 20457 20458 20461 20462 20463 20465 20467 20468 20469 20471 20472 20474 20478 
    ##     5     4     4     3     4     5     5     5     3     5     5     3     5 
    ## 20479 20480 20481 20482 20483 20485 20486 20487 20490 20491 20492 20497 20498 
    ##     3     4     4     5     5     5     5     3     5     5     3     3     5 
    ## 20500 20501 20502 20503 20504 20505 20506 20509 20511 20514 20515 20516 20517 
    ##     4     5     4     5     5     5     4     4     5     4     4     5     5 
    ## 20518 20519 20520 20521 20522 20523 20524 20526 20528 20530 20531 20534 20535 
    ##     4     4     3     4     5     5     5     3     3     5     4     3     3 
    ## 20537 20538 20539 20540 20541 20542 20543 20544 20546 20547 20548 20549 20550 
    ##     5     5     5     5     5     3     4     5     5     5     5     5     5 
    ## 20551 20553 20554 20555 20556 20557 20558 20559 20561 20564 20565 20566 20567 
    ##     4     5     4     5     5     5     5     4     5     5     5     4     5 
    ## 20568 20570 20571 20573 20574 20575 20577 20578 20580 20581 20582 20584 20586 
    ##     5     5     3     5     4     5     5     3     3     5     5     5     5 
    ## 20587 20588 20589 20593 20594 20595 20596 20597 20598 20599 20600 20602 20603 
    ##     5     5     5     5     5     5     3     3     3     5     5     5     5 
    ## 20604 20605 20606 20608 20609 20610 20611 20612 20613 20614 20615 20617 20619 
    ##     3     3     3     5     5     5     3     4     5     4     4     5     5 
    ## 20621 20622 20623 20626 20627 20628 20629 20631 20633 20635 20636 20637 20638 
    ##     5     4     4     5     5     4     4     4     4     5     5     5     5 
    ## 20640 20641 20642 20643 20644 20645 20646 20648 20649 20650 20651 20655 20657 
    ##     5     5     5     5     4     5     5     3     5     5     5     3     3 
    ## 20658 20659 20660 20661 20663 20664 20665 20666 20667 20668 20669 20670 20671 
    ##     4     5     5     3     4     5     5     5     5     5     4     5     5 
    ## 20672 20673 20674 20675 20679 20680 20681 20683 20684 20685 20686 20687 20689 
    ##     3     5     3     5     3     3     5     5     4     4     5     5     4 
    ## 20690 20691 20692 20693 20697 20698 20700 20702 20703 20704 20705 20706 20707 
    ##     4     4     5     3     4     3     4     5     5     3     3     5     5 
    ## 20708 20709 20710 20712 20714 20715 20716 20717 20718 20720 20721 20723 20724 
    ##     5     5     5     5     4     4     3     3     3     3     5     4     5 
    ## 20725 20726 20727 20728 20730 20732 20734 20735 20736 20737 20738 20739 20740 
    ##     4     3     5     5     5     3     3     5     5     5     3     3     5 
    ## 20741 20742 20745 20746 20747 20748 20749 20751 20752 20753 20754 20755 20756 
    ##     5     5     4     5     4     4     3     5     5     5     5     3     3 
    ## 20758 20759 20760 20762 20763 20765 20766 20767 20768 20769 20770 20774 20775 
    ##     5     4     5     5     5     3     4     5     5     4     4     5     4 
    ## 20777 20778 20779 20780 20781 20782 20785 20787 20789 20791 20792 20793 20794 
    ##     4     5     5     3     5     5     3     4     4     3     5     5     5 
    ## 20795 20796 20798 20800 20801 20802 20803 20805 20806 20807 20808 20809 20810 
    ##     5     5     3     5     4     5     5     5     5     5     5     5     5 
    ## 20811 20812 20813 20814 20815 20816 20819 20821 20822 20823 20824 20825 20826 
    ##     5     5     3     4     5     5     5     5     5     4     5     4     4 
    ## 20827 20828 20830 20831 20832 20836 20837 20839 20841 20843 20844 20846 20850 
    ##     3     4     5     3     3     5     5     5     5     5     3     5     4 
    ## 20851 20854 20858 20859 20860 20861 20863 20864 20866 20867 20868 20869 20870 
    ##     5     5     4     5     4     4     5     3     5     5     5     4     4 
    ## 20871 20872 20873 20875 20878 20880 20881 20882 20884 20885 20886 20888 20890 
    ##     3     5     3     5     4     5     5     5     4     3     4     3     3 
    ## 20891 20892 20893 20894 20895 20897 20898 20899 20900 20902 20904 20906 20907 
    ##     5     5     3     3     4     5     4     4     3     5     4     3     5 
    ## 20909 20910 20912 20914 20915 20916 20918 20919 20920 20921 20922 20924 20926 
    ##     5     5     3     3     4     3     3     5     5     3     5     3     5 
    ## 20927 20928 20929 20930 20931 20934 20935 20937 20938 20939 20940 20942 20944 
    ##     3     3     5     5     3     4     4     3     5     5     4     4     3 
    ## 20946 20948 20949 20950 20952 20953 20954 20955 20956 20957 20958 20960 20961 
    ##     4     4     5     4     3     4     5     5     5     5     5     5     3 
    ## 20964 20966 20968 20969 20970 20972 20974 20975 20976 20977 20981 20982 20983 
    ##     4     3     5     3     5     3     5     5     3     5     4     5     4 
    ## 20985 20986 20988 20989 20990 20991 20992 20993 20995 20996 20997 20998 20999 
    ##     5     5     3     4     5     5     5     5     5     3     5     3     5 
    ## 21001 21004 21006 21007 21010 21011 21014 21015 21017 21018 21021 21022 21023 
    ##     4     3     5     3     5     5     5     3     5     4     5     3     3 
    ## 21026 21028 21029 21031 21032 21034 21036 21037 21038 21039 21042 21045 21046 
    ##     4     4     4     5     3     4     5     5     4     5     4     4     4 
    ## 21047 21048 21049 21050 21052 21053 21054 21056 21058 21061 21062 21063 21066 
    ##     3     3     5     3     5     5     4     5     5     5     3     5     4 
    ## 21067 21069 21070 21071 21072 21073 21074 21076 21077 21078 21083 21085 21086 
    ##     5     5     5     3     3     5     4     5     4     4     3     4     3 
    ## 21087 21089 21090 21093 21094 21095 21096 21097 21098 21099 21100 21101 21102 
    ##     3     5     5     4     3     5     3     3     3     3     4     3     3 
    ## 21104 21106 21110 21111 21112 21113 21114 21117 21118 21119 21120 21122 21123 
    ##     3     5     3     4     3     5     3     5     4     3     5     4     5 
    ## 21124 21125 21126 21128 21129 21130 21132 21133 21135 21139 21140 21141 21143 
    ##     5     5     5     3     5     5     3     5     5     3     4     5     5 
    ## 21144 21145 21147 21149 21151 21152 21153 21154 21155 21156 21157 21158 21159 
    ##     4     5     5     4     4     5     5     3     5     5     3     4     3 
    ## 21161 21162 21163 21164 21165 21166 21167 21168 21169 21170 21171 21172 21173 
    ##     5     5     3     3     5     5     4     4     4     5     5     4     3 
    ## 21174 21175 21176 21177 21180 21182 21185 21187 21190 21191 21192 21194 21195 
    ##     4     3     5     4     4     4     5     5     4     3     4     5     4 
    ## 21196 21198 21200 21201 21202 21203 21205 21209 21210 21211 21212 21214 21216 
    ##     5     5     3     5     5     5     3     5     3     4     4     4     3 
    ## 21218 21221 21222 21223 21224 21225 21226 21227 21228 21229 21234 21235 21236 
    ##     4     3     3     5     4     4     3     5     5     3     5     4     5 
    ## 21237 21242 21245 21246 21247 21248 21250 21252 21253 21254 21256 21259 21260 
    ##     5     5     4     3     3     3     5     5     3     5     4     3     3 
    ## 21261 21263 21264 21265 21266 21267 21268 21269 21270 21271 21274 21275 21277 
    ##     5     5     5     5     4     5     4     5     5     4     5     5     5 
    ## 21278 21279 21280 21282 21284 21287 21290 21291 21293 21294 21295 21296 21298 
    ##     4     3     4     5     5     5     5     3     5     5     4     5     3 
    ## 21299 21300 21301 21302 21303 21304 21306 21307 21308 21310 21312 21313 21314 
    ##     5     3     3     5     5     5     5     4     4     5     3     3     4 
    ## 21316 21318 21319 21320 21321 21322 21324 21325 21326 21327 21329 21331 21332 
    ##     3     5     4     3     5     4     4     4     4     5     3     3     4 
    ## 21333 21336 21337 21339 21341 21342 21343 21344 21345 21347 21348 21349 21350 
    ##     5     5     3     5     3     4     5     4     5     5     5     5     3 
    ## 21351 21352 21353 21354 21355 21356 21357 21358 21359 21360 21361 21362 21363 
    ##     5     3     5     5     3     3     3     5     4     5     4     5     3 
    ## 21367 21368 21369 21370 21371 21372 21373 21375 21376 21377 21379 21380 21381 
    ##     4     4     3     5     4     3     4     5     3     5     5     5     3 
    ## 21382 21383 21384 21385 21386 21387 21388 21390 21391 21392 21394 21395 21397 
    ##     5     3     3     3     4     5     4     4     5     3     4     3     3 
    ## 21398 21399 21400 21403 21407 21408 21409 21410 21412 21413 21414 21415 21416 
    ##     3     5     5     3     4     3     4     5     5     5     3     4     5 
    ## 21417 21418 21419 21420 21421 21426 21429 21430 21431 21434 21435 21436 21437 
    ##     3     5     5     4     5     5     5     3     3     4     3     3     3 
    ## 21439 21440 21441 21443 21445 21446 21447 21448 21450 21451 21452 21453 21455 
    ##     4     5     5     5     3     3     4     4     3     3     5     4     4 
    ## 21456 21458 21460 21461 21462 21463 21464 21465 21467 21468 21471 21472 21473 
    ##     3     3     3     5     3     4     5     4     4     4     5     5     3 
    ## 21474 21475 21476 21477 21478 21479 21480 21481 21482 21483 21484 21486 21487 
    ##     3     4     3     4     5     3     5     3     5     5     3     4     3 
    ## 21489 21490 21492 21495 21496 21497 21498 21500 21503 21504 21505 21506 21508 
    ##     3     3     3     3     3     3     5     5     5     5     3     5     5 
    ## 21509 21510 21511 21512 21513 21514 21515 21516 21517 21518 21520 21521 21523 
    ##     5     4     5     3     3     5     5     5     4     3     5     4     4 
    ## 21525 21526 21527 21528 21530 21531 21533 21534 21535 21536 21537 21538 21539 
    ##     3     3     5     5     3     3     4     3     4     3     4     5     3 
    ## 21540 21541 21544 21545 21546 21548 21549 21550 21551 21552 21553 21554 21555 
    ##     5     5     4     3     4     5     4     5     5     4     4     3     5 
    ## 21559 21560 21563 21564 21565 21566 21567 21569 21570 21571 21572 21573 21574 
    ##     3     5     5     4     5     3     5     4     4     3     5     4     5 
    ## 21575 21577 21582 21583 21584 21586 21587 21589 21590 21591 21592 21594 21596 
    ##     5     3     5     5     4     5     3     4     5     5     4     5     5 
    ## 21597 21599 21600 21601 21602 21603 21604 21605 21607 21608 21609 21611 21612 
    ##     3     3     5     5     3     4     3     3     4     5     5     5     4 
    ## 21614 21618 21619 21621 21622 21623 21624 21626 21627 21628 21629 21631 21632 
    ##     5     5     3     4     4     4     4     3     3     5     4     5     5 
    ## 21633 21635 21636 21637 21638 21639 21640 21641 21642 21643 21644 21645 21646 
    ##     4     5     3     5     5     4     5     5     5     5     5     5     3 
    ## 21647 21648 21651 21652 21653 21654 21655 21657 21658 21660 21661 21662 21663 
    ##     5     4     5     4     3     5     3     3     5     3     5     4     5 
    ## 21666 21667 21668 21670 21671 21674 21677 21682 21685 21686 21687 21688 21689 
    ##     3     5     3     5     5     5     3     5     4     4     3     3     5 
    ## 21696 21697 21698 21700 21701 21702 21703 21704 21706 21707 21708 21711 21714 
    ##     5     5     5     3     4     5     3     5     4     4     4     5     3 
    ## 21716 21717 21719 21720 21721 21722 21723 21724 21725 21726 21727 21728 21731 
    ##     5     5     5     3     4     5     3     4     4     4     5     4     5 
    ## 21732 21733 21735 21736 21737 21738 21739 21741 21742 21745 21748 21749 21750 
    ##     5     5     3     4     5     4     4     4     5     5     3     5     5 
    ## 21751 21752 21753 21754 21755 21756 21757 21758 21759 21760 21761 21762 21763 
    ##     5     5     5     5     5     5     5     4     3     5     5     5     5 
    ## 21764 21766 21767 21768 21769 21770 21771 21772 21773 21774 21775 21776 21777 
    ##     5     3     4     5     5     5     4     3     5     5     5     3     5 
    ## 21778 21779 21780 21782 21783 21784 21785 21786 21787 21788 21789 21790 21791 
    ##     4     4     4     5     5     5     3     4     5     3     5     5     5 
    ## 21792 21793 21794 21795 21796 21797 21799 21800 21804 21805 21806 21807 21808 
    ##     5     5     5     4     4     5     5     5     3     5     4     4     3 
    ## 21809 21810 21811 21813 21814 21816 21817 21818 21819 21820 21822 21823 21824 
    ##     3     4     3     5     3     5     3     4     4     5     4     5     4 
    ## 21825 21826 21827 21828 21829 21830 21831 21833 21835 21837 21839 21841 21842 
    ##     4     3     5     5     5     3     5     4     4     5     3     4     5 
    ## 21843 21844 21847 21848 21849 21850 21851 21852 21854 21856 21857 21858 21859 
    ##     5     3     4     5     3     5     4     5     5     4     5     3     4 
    ## 21861 21863 21864 21865 21866 21868 21869 21870 21874 21875 21876 21877 21879 
    ##     5     5     5     3     4     5     4     3     4     5     5     5     3 
    ## 21880 21881 21883 21884 21885 21886 21887 21889 21891 21892 21893 21895 21896 
    ##     3     5     3     5     4     3     4     3     5     4     4     5     5 
    ## 21897 21898 21900 21901 21902 21903 21904 21905 21909 21910 21911 21912 21913 
    ##     5     4     5     4     5     3     5     3     5     5     3     5     3 
    ## 21914 21915 21917 21918 21920 21921 21922 21923 21924 21925 21926 21929 21930 
    ##     5     5     5     3     4     5     4     5     5     5     5     5     5 
    ## 21933 21934 21935 21937 21939 21940 21941 21942 21943 21944 21945 21946 21947 
    ##     5     3     4     5     5     5     5     3     4     5     5     4     5 
    ## 21948 21949 21950 21954 21957 21959 21960 21961 21962 21966 21968 21971 21974 
    ##     4     5     5     5     3     4     3     5     4     5     5     5     3 
    ## 21980 21981 21982 21984 21985 21986 21987 21990 21991 21994 21997 21998 21999 
    ##     5     5     5     4     4     5     5     4     3     5     5     3     5 
    ## 22001 22002 22003 22004 22006 22007 22009 22011 22014 22015 22017 22019 22020 
    ##     5     5     5     3     3     4     4     5     5     4     5     5     5 
    ## 22021 22024 22025 22026 22027 22028 22029 22031 22032 22033 22034 22036 22037 
    ##     5     5     3     4     4     3     5     3     5     5     5     4     4 
    ## 22038 22040 22041 22042 22046 22047 22050 22051 22053 22056 22060 22063 22064 
    ##     5     5     5     5     3     5     3     3     4     5     5     3     4 
    ## 22065 22066 22068 22070 22075 22078 22079 22080 22081 22084 22085 22086 22088 
    ##     5     5     5     5     5     4     5     3     5     5     3     5     4 
    ## 22090 22091 22092 22093 22094 22095 22096 22097 22099 22100 22101 22103 22105 
    ##     3     3     5     3     5     5     5     5     4     5     5     5     3 
    ## 22106 22108 22109 22110 22112 22113 22114 22115 22116 22117 22118 22119 22120 
    ##     5     5     4     5     5     3     3     5     3     5     5     5     5 
    ## 22121 22122 22123 22125 22126 22128 22129 22131 22135 22136 22139 22140 22141 
    ##     3     5     4     5     5     3     3     3     5     5     4     5     4 
    ## 22142 22146 22147 22148 22151 22152 22153 22154 22155 22156 22157 22158 22160 
    ##     3     5     5     5     5     5     5     5     5     4     4     5     5 
    ## 22162 22163 22164 22166 22167 22168 22169 22171 22172 22173 22175 22176 22177 
    ##     3     5     5     4     3     3     5     3     5     5     3     5     4 
    ## 22179 22181 22182 22186 22189 22191 22192 22193 22194 22196 22198 22199 22201 
    ##     5     4     5     5     5     3     5     5     5     3     5     4     3 
    ## 22202 22203 22204 22205 22206 22207 22208 22209 22210 22211 22212 22213 22214 
    ##     5     3     5     3     3     5     5     3     3     3     5     4     5 
    ## 22215 22216 22217 22218 22219 22220 22221 22222 22223 22225 22226 22228 22230 
    ##     5     5     4     4     5     5     3     3     3     5     4     5     3 
    ## 22238 22239 22240 22241 22242 22243 22244 22245 22246 22248 22249 22251 22252 
    ##     5     5     5     4     4     5     5     3     4     5     5     5     4 
    ## 22253 22254 22255 22256 22257 22259 22260 22261 22262 22263 22265 22267 22268 
    ##     5     5     4     4     3     4     3     3     4     3     3     5     5 
    ## 22269 22270 22274 22275 22278 22279 22280 22281 22282 22283 22284 22289 22291 
    ##     5     5     5     4     5     5     4     5     3     4     5     5     4 
    ## 22294 22295 22296 22299 22300 22301 22302 22304 22306 22308 22309 22310 22312 
    ##     5     4     5     5     5     5     5     3     4     3     5     5     5 
    ## 22313 22314 22315 22317 22318 22321 22324 22325 22326 22328 22329 22330 22331 
    ##     4     5     5     5     4     4     4     3     4     4     4     5     3 
    ## 22332 22334 22336 22337 22338 22339 22340 22342 22343 22345 22346 22347 22348 
    ##     5     5     4     5     5     3     5     4     4     5     5     4     5 
    ## 22349 22354 22355 22356 22359 22361 22362 22363 22366 22367 22368 22369 22370 
    ##     5     5     5     5     5     4     5     5     5     4     5     4     5 
    ## 22371 22372 22374 22376 22378 22379 22381 22382 22384 22387 22391 22393 22396 
    ##     3     5     4     4     5     3     5     5     4     5     5     5     4 
    ## 22398 22399 22400 22401 22402 22404 22405 22406 22407 22408 22410 22411 22414 
    ##     4     5     4     4     4     3     5     5     5     5     5     4     5 
    ## 22416 22417 22418 22420 22422 22423 22424 22425 22426 22427 22428 22430 22431 
    ##     3     5     5     3     3     5     4     5     5     5     5     4     5 
    ## 22432 22435 22436 22438 22439 22440 22442 22443 22445 22447 22449 22451 22452 
    ##     4     5     5     4     5     3     3     4     5     4     3     5     5 
    ## 22454 22455 22457 22458 22459 22460 22461 22462 22463 22465 22467 22469 22471 
    ##     5     5     5     5     5     4     3     4     5     4     5     4     5 
    ## 22472 22473 22475 22476 22477 22478 22482 22483 22484 22486 22487 22488 22490 
    ##     4     5     5     3     5     3     5     5     5     3     4     5     5 
    ## 22491 22493 22494 22495 22497 22498 22499 22500 22502 22506 22507 22509 22511 
    ##     4     5     5     5     5     4     5     5     5     3     3     5     3 
    ## 22514 22516 22517 22519 22522 22523 22526 22527 22529 22530 22531 22533 22535 
    ##     4     4     5     5     4     3     5     3     4     5     3     4     5 
    ## 22537 22539 22542 22544 22545 22546 22547 22548 22549 22550 22551 22553 22554 
    ##     4     5     5     5     5     5     4     3     5     5     5     5     5 
    ## 22555 22556 22557 22558 22559 22561 22562 22565 22566 22567 22570 22572 22573 
    ##     5     4     4     3     5     5     4     5     5     3     4     5     5 
    ## 22574 22575 22576 22577 22578 22580 22582 22583 22584 22585 22586 22588 22589 
    ##     5     4     5     5     4     5     5     4     5     3     3     4     3 
    ## 22591 22592 22593 22595 22596 22597 22598 22599 22601 22603 22605 22606 22607 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 22608 22610 22613 22614 22615 22616 22617 22618 22619 22622 22624 22625 22626 
    ##     5     5     5     3     5     4     3     5     5     3     5     5     3 
    ## 22627 22628 22629 22630 22631 22632 22634 22636 22637 22638 22641 22642 22643 
    ##     5     3     5     5     3     4     5     4     3     3     5     5     5 
    ## 22644 22646 22648 22650 22651 22654 22656 22657 22658 22659 22663 22665 22667 
    ##     5     5     5     4     5     3     3     4     5     5     5     4     4 
    ## 22668 22671 22673 22676 22677 22678 22679 22680 22681 22683 22686 22687 22688 
    ##     3     5     5     4     4     4     5     5     5     5     3     5     5 
    ## 22689 22690 22692 22693 22694 22695 22696 22697 22698 22699 22700 22701 22702 
    ##     4     3     4     5     4     5     5     5     5     5     5     5     5 
    ## 22703 22705 22706 22708 22709 22710 22711 22714 22715 22718 22719 22720 22721 
    ##     5     4     5     4     5     5     3     4     3     5     4     5     4 
    ## 22722 22723 22725 22728 22730 22731 22732 22733 22734 22735 22736 22737 22738 
    ##     4     5     5     5     5     3     5     5     5     5     5     4     5 
    ## 22739 22740 22741 22743 22744 22745 22746 22747 22749 22750 22751 22752 22753 
    ##     5     5     3     4     3     5     5     3     4     5     4     5     5 
    ## 22754 22755 22757 22758 22759 22760 22761 22762 22763 22764 22765 22766 22767 
    ##     5     5     5     4     3     4     5     3     4     4     5     4     5 
    ## 22770 22771 22772 22773 22775 22778 22779 22780 22781 22782 22783 22784 22785 
    ##     5     3     3     5     5     5     5     4     5     4     5     4     5 
    ## 22786 22789 22792 22794 22795 22796 22797 22798 22799 22800 22801 22802 22803 
    ##     5     5     4     5     5     4     3     3     3     3     5     3     4 
    ## 22804 22805 22806 22809 22811 22812 22813 22814 22815 22816 22817 22820 22823 
    ##     5     5     4     4     4     3     5     3     4     5     3     4     3 
    ## 22824 22825 22826 22828 22829 22830 22832 22833 22836 22837 22838 22840 22841 
    ##     5     4     5     4     5     3     5     5     5     5     5     5     5 
    ## 22844 22845 22846 22848 22849 22850 22851 22853 22854 22855 22856 22857 22858 
    ##     5     5     5     5     4     5     4     5     3     5     5     4     5 
    ## 22859 22860 22861 22863 22864 22865 22866 22868 22871 22872 22873 22874 22876 
    ##     3     4     5     5     4     3     5     5     5     5     4     4     5 
    ## 22877 22878 22879 22880 22881 22883 22885 22886 22888 22889 22890 22891 22893 
    ##     4     5     5     5     5     5     5     5     5     5     5     5     4 
    ## 22894 22897 22900 22901 22904 22906 22909 22911 22912 22913 22914 22915 22916 
    ##     5     4     5     5     4     5     5     4     5     3     5     3     4 
    ## 22917 22918 22919 22920 22921 22922 22923 22925 22926 22928 22929 22930 22931 
    ##     3     3     5     5     3     5     5     4     4     5     3     5     4 
    ## 22932 22933 22934 22935 22936 22937 22938 22939 22940 22942 22943 22945 22946 
    ##     5     3     3     5     5     5     5     5     5     4     5     4     4 
    ## 22947 22948 22949 22950 22951 22952 22953 22955 22956 22958 22960 22961 22963 
    ##     5     5     4     5     4     5     5     4     5     4     4     5     3 
    ## 22964 22965 22967 22968 22969 22970 22975 22976 22977 22978 22980 22982 22983 
    ##     5     5     4     5     4     4     5     5     5     3     5     5     3 
    ## 22984 22985 22986 22987 22990 22996 22997 22999 23001 23002 23004 23005 23006 
    ##     3     5     3     5     5     5     4     5     5     5     5     3     3 
    ## 23007 23010 23011 23014 23015 23016 23017 23018 23019 23020 23021 23022 23023 
    ##     4     3     5     5     5     5     5     5     5     5     5     5     5 
    ## 23026 23028 23029 23030 23031 23032 23033 23034 23036 23037 23039 23041 23043 
    ##     5     5     5     4     5     5     4     5     5     4     5     5     4 
    ## 23045 23046 23047 23048 23049 23050 23051 23053 23054 23055 23057 23059 23060 
    ##     4     5     4     5     5     5     5     3     5     4     5     5     5 
    ## 23061 23064 23066 23067 23068 23069 23070 23071 23072 23073 23074 23075 23076 
    ##     5     5     5     3     3     5     4     5     3     3     5     4     5 
    ## 23077 23079 23080 23082 23083 23085 23086 23089 23093 23094 23095 23096 23098 
    ##     5     5     4     3     5     5     3     5     4     4     5     5     5 
    ## 23101 23102 23103 23105 23106 23107 23108 23110 23113 23114 23115 23116 23118 
    ##     5     5     5     5     5     5     3     3     5     5     3     3     5 
    ## 23119 23120 23121 23122 23126 23127 23128 23129 23130 23131 23133 23137 23138 
    ##     4     5     4     3     5     5     5     5     4     4     5     4     5 
    ## 23140 23141 23143 23144 23146 23147 23149 23150 23151 23152 23153 23155 23156 
    ##     5     3     5     5     5     4     4     5     5     5     3     5     4 
    ## 23160 23161 23162 23163 23166 23167 23168 23169 23170 23171 23172 23173 23174 
    ##     5     5     5     5     5     4     5     5     5     5     4     5     5 
    ## 23175 23177 23178 23179 23181 23182 23183 23184 23185 23187 23188 23190 23192 
    ##     5     5     5     4     5     5     4     5     4     5     3     5     5 
    ## 23193 23194 23195 23196 23199 23200 23201 23202 23203 23205 23206 23207 23209 
    ##     5     5     4     3     4     3     4     5     5     4     4     3     5 
    ## 23210 23212 23213 23214 23215 23216 23217 23218 23220 23222 23225 23226 23230 
    ##     5     3     5     5     5     5     5     5     5     3     5     5     3 
    ## 23232 23233 23234 23236 23237 23238 23239 23243 23244 23245 23246 23247 23249 
    ##     5     5     5     5     5     5     3     5     5     3     5     5     3 
    ## 23251 23255 23256 23257 23258 23259 23263 23264 23265 23266 23267 23268 23269 
    ##     5     5     5     5     5     4     5     5     5     5     5     5     5 
    ## 23270 23272 23273 23275 23276 23277 23278 23279 23280 23281 23282 23283 23284 
    ##     5     3     4     4     5     5     5     4     5     5     3     5     3 
    ## 23286 23287 23288 23289 23290 23293 23294 23295 23297 23298 23299 23300 23301 
    ##     5     5     5     4     3     3     5     5     4     5     5     4     5 
    ## 23302 23303 23304 23306 23309 23310 23312 23313 23314 23316 23317 23318 23319 
    ##     5     5     5     3     4     5     5     5     5     3     5     5     5 
    ## 23321 23322 23323 23324 23325 23326 23327 23330 23331 23332 23333 23335 23339 
    ##     5     3     5     3     5     5     5     5     3     5     3     3     5 
    ## 23340 23341 23342 23343 23344 23345 23346 23347 23349 23351 23352 23353 23355 
    ##     3     5     4     5     4     5     5     5     5     5     3     5     3 
    ## 23356 23357 23358 23359 23363 23366 23367 23368 23369 23370 23371 23372 23373 
    ##     5     5     5     4     5     5     5     5     5     5     4     4     5 
    ## 23375 23377 23378 23379 23380 23381 23382 23383 23384 23385 23387 23388 23389 
    ##     5     3     3     5     4     5     3     5     4     3     5     5     3 
    ## 23390 23391 23392 23393 23394 23395 23396 23398 23399 23401 23403 23404 23405 
    ##     3     5     5     5     5     3     3     3     5     4     4     3     4 
    ## 23407 23408 23411 23415 23416 23417 23418 23421 23422 23423 23426 23427 23428 
    ##     3     5     4     5     3     5     3     3     3     3     4     5     3 
    ## 23429 23430 23431 23432 23433 23434 23436 23437 23438 23439 23440 23441 23443 
    ##     5     4     5     5     3     5     3     3     5     5     5     5     4 
    ## 23445 23446 23447 23448 23449 23450 23451 23452 23455 23456 23457 23459 23460 
    ##     3     4     3     3     5     4     3     3     5     3     4     3     3 
    ## 23461 23462 23463 23466 23467 23468 23471 23473 23475 23478 23479 23480 23481 
    ##     5     4     5     3     5     4     5     5     3     3     3     3     5 
    ## 23482 23483 23485 23486 23487 23491 23492 23494 23496 23498 23500 23501 23502 
    ##     5     3     5     5     3     3     5     5     4     5     4     4     3 
    ## 23503 23504 23505 23506 23507 23509 23510 23511 23512 23515 23516 23517 23518 
    ##     4     3     5     4     5     3     4     4     5     3     5     3     5 
    ## 23520 23521 23523 23524 23525 23526 23528 23529 23530 23531 23532 23534 23535 
    ##     5     5     5     3     3     5     3     4     5     5     5     5     5 
    ## 23536 23537 23538 23539 23541 23543 23544 23545 23546 23547 23549 23550 23551 
    ##     5     3     5     5     3     5     4     5     3     5     5     5     4 
    ## 23552 23553 23554 23555 23556 23558 23561 23562 23565 23566 23568 23569 23573 
    ##     5     4     4     5     5     5     5     5     4     4     4     5     5 
    ## 23574 23575 23576 23577 23580 23581 23582 23583 23586 23588 23589 23591 23592 
    ##     3     5     5     5     5     5     5     3     4     5     5     5     5 
    ## 23593 23595 23596 23597 23598 23600 23601 23603 23604 23605 23606 23607 23608 
    ##     3     5     3     3     3     4     5     5     5     5     3     5     5 
    ## 23610 23612 23613 23614 23615 23617 23619 23623 23624 23625 23626 23627 23628 
    ##     3     4     3     4     5     3     5     5     5     5     5     3     4 
    ## 23630 23631 23632 23634 23635 23636 23637 23638 23639 23642 23643 23645 23646 
    ##     5     4     5     5     5     5     5     3     5     4     5     5     4 
    ## 23647 23648 23649 23650 23652 23653 23658 23659 23660 23661 23662 23665 23666 
    ##     5     3     4     3     5     4     5     3     5     3     5     3     3 
    ## 23667 23668 23671 23673 23674 23676 23677 23678 23679 23680 23681 23683 23684 
    ##     5     5     3     4     4     5     5     5     5     5     5     5     5 
    ## 23685 23686 23688 23691 23692 23693 23697 23699 23700 23702 23704 23705 23706 
    ##     4     3     5     5     4     4     5     3     4     3     5     5     5 
    ## 23707 23708 23709 23710 23711 23712 23713 23714 23715 23716 23717 23719 23720 
    ##     5     4     3     5     5     5     4     4     5     3     4     5     5 
    ## 23721 23722 23723 23726 23728 23729 23730 23731 23732 23733 23735 23737 23743 
    ##     5     4     5     5     5     5     3     5     5     5     5     5     5 
    ## 23745 23746 23747 23749 23750 23755 23756 23758 23759 23760 23761 23762 23763 
    ##     5     4     5     5     5     5     4     4     3     5     4     5     5 
    ## 23765 23766 23768 23769 23770 23772 23773 23776 23777 23779 23780 23781 23782 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     3 
    ## 23783 23787 23788 23790 23791 23793 23794 23795 23797 23798 23799 23800 23801 
    ##     5     4     5     5     4     3     5     5     3     5     3     3     4 
    ## 23802 23803 23804 23806 23809 23810 23811 23812 23813 23815 23816 23817 23818 
    ##     4     5     3     4     5     5     5     3     3     4     3     5     3 
    ## 23820 23821 23822 23825 23827 23828 23830 23831 23832 23833 23834 23837 23838 
    ##     3     5     5     5     3     4     3     5     5     4     5     5     4 
    ## 23839 23840 23841 23842 23844 23847 23848 23850 23851 23852 23853 23854 23857 
    ##     3     5     5     3     5     5     5     5     3     3     5     5     5 
    ## 23858 23859 23860 23862 23863 23865 23866 23867 23868 23869 23872 23873 23874 
    ##     5     5     4     5     5     4     5     3     3     5     4     5     4 
    ## 23876 23877 23879 23880 23882 23883 23884 23885 23887 23888 23889 23890 23891 
    ##     5     5     5     4     3     5     5     5     5     5     4     4     4 
    ## 23892 23893 23894 23895 23896 23899 23901 23902 23903 23904 23905 23906 23908 
    ##     4     3     5     5     5     4     5     5     5     5     3     5     5 
    ## 23910 23912 23914 23915 23916 23917 23918 23923 23924 23925 23926 23928 23929 
    ##     3     5     4     5     4     4     5     5     3     4     4     4     3 
    ## 23930 23932 23935 23938 23944 23945 23947 23948 23949 23953 23954 23959 23960 
    ##     5     3     3     4     4     5     4     4     5     4     5     4     5 
    ## 23961 23963 23964 23965 23968 23969 23970 23972 23973 23977 23978 23979 23981 
    ##     5     5     5     5     3     3     5     5     3     5     3     5     4 
    ## 23982 23985 23986 23987 23989 23990 23992 23993 23994 23995 23996 23997 23998 
    ##     3     5     4     3     3     5     4     4     4     3     3     3     5 
    ## 24000 24001 24003 24005 24006 24007 24009 24010 24011 24012 24013 24015 24016 
    ##     5     5     5     5     5     4     3     5     5     5     4     3     3 
    ## 24017 24018 24020 24021 24022 24024 24025 24026 24027 24028 24029 24030 24031 
    ##     5     4     3     4     4     5     5     5     3     5     4     4     5 
    ## 24032 24033 24034 24035 24037 24038 24039 24041 24042 24045 24046 24047 24048 
    ##     5     3     5     3     3     3     5     5     5     3     3     5     5 
    ## 24049 24050 24051 24053 24054 24055 24056 24059 24060 24061 24063 24064 24066 
    ##     3     4     3     4     4     3     5     4     5     5     3     5     5 
    ## 24067 24068 24072 24073 24074 24077 24078 24079 24080 24081 24082 24083 24085 
    ##     4     4     4     5     5     4     5     5     3     4     3     5     5 
    ## 24086 24088 24090 24093 24094 24095 24096 24097 24099 24100 24101 24102 24103 
    ##     5     5     3     3     5     4     4     5     4     5     3     5     5 
    ## 24104 24105 24106 24107 24108 24109 24110 24112 24113 24114 24115 24117 24118 
    ##     5     3     5     5     4     4     4     5     5     5     5     4     5 
    ## 24119 24123 24125 24127 24130 24131 24132 24135 24136 24137 24138 24141 24143 
    ##     4     5     5     3     5     5     5     5     4     5     5     5     5 
    ## 24146 24147 24148 24149 24151 24152 24153 24154 24156 24157 24158 24159 24160 
    ##     5     4     5     4     5     5     4     3     5     5     4     5     5 
    ## 24161 24162 24164 24165 24166 24167 24168 24169 24170 24172 24175 24177 24179 
    ##     4     5     5     5     5     5     5     3     4     4     5     5     4 
    ## 24180 24181 24182 24183 24184 24187 24188 24189 24190 24192 24194 24195 24199 
    ##     5     3     5     5     3     5     5     3     5     5     5     5     5 
    ## 24201 24202 24208 24209 24210 24211 24212 24213 24215 24216 24217 24218 24220 
    ##     5     5     3     5     4     4     5     4     5     5     3     3     5 
    ## 24221 24223 24224 24225 24227 24231 24232 24233 24236 24237 24238 24240 24241 
    ##     4     5     5     5     5     5     5     5     3     3     5     5     5 
    ## 24245 24247 24249 24250 24252 24253 24254 24255 24256 24257 24259 24261 24262 
    ##     5     3     4     5     5     5     5     3     5     3     5     5     5 
    ## 24263 24264 24269 24270 24271 24272 24273 24278 24279 24281 24282 24283 24284 
    ##     5     3     5     3     5     5     3     3     5     3     3     5     3 
    ## 24286 24289 24290 24291 24292 24293 24294 24296 24297 24299 24300 24301 24304 
    ##     5     5     5     3     5     3     5     5     4     5     3     3     5 
    ## 24305 24306 24308 24309 24312 24313 24314 24316 24319 24320 24321 24322 24323 
    ##     4     3     5     5     4     3     5     3     5     5     5     5     4 
    ## 24324 24326 24327 24328 24331 24333 24334 24335 24336 24339 24340 24341 24342 
    ##     3     3     3     5     3     4     5     5     5     4     5     5     4 
    ## 24343 24344 24345 24346 24347 24348 24349 24352 24353 24354 24355 24356 24357 
    ##     4     5     5     5     4     3     5     5     5     5     5     4     3 
    ## 24359 24360 24361 24363 24364 24365 24369 24370 24371 24372 24373 24374 24375 
    ##     5     5     5     5     5     5     5     5     4     3     5     3     5 
    ## 24377 24380 24381 24382 24383 24385 24386 24387 24390 24391 24393 24394 24395 
    ##     5     4     4     3     4     5     5     5     5     3     5     4     3 
    ## 24396 24397 24400 24401 24402 24403 24405 24406 24407 24408 24409 24413 24415 
    ##     5     4     4     5     4     5     5     5     4     4     4     5     5 
    ## 24416 24417 24418 24420 24421 24423 24424 24425 24426 24428 24431 24433 24434 
    ##     5     5     4     5     5     5     5     4     4     5     4     4     5 
    ## 24435 24436 24438 24440 24442 24446 24448 24449 24450 24451 24455 24456 24459 
    ##     3     3     5     5     5     5     5     5     5     3     4     4     3 
    ## 24460 24461 24463 24464 24465 24467 24468 24470 24471 24472 24473 24474 24475 
    ##     5     3     5     5     5     5     3     5     5     5     3     3     5 
    ## 24476 24477 24478 24479 24480 24481 24482 24483 24484 24485 24486 24487 24488 
    ##     5     4     5     3     3     5     4     4     5     5     5     5     3 
    ## 24489 24490 24492 24493 24494 24495 24496 24497 24499 24501 24504 24506 24507 
    ##     5     4     5     5     5     4     5     5     3     5     3     5     3 
    ## 24508 24510 24511 24513 24516 24517 24518 24519 24522 24523 24524 24526 24527 
    ##     5     5     5     3     5     3     5     3     5     5     5     3     5 
    ## 24528 24529 24530 24531 24532 24533 24534 24535 24536 24537 24539 24540 24541 
    ##     4     5     5     5     4     5     5     4     3     3     5     5     4 
    ## 24542 24543 24545 24547 24548 24549 24550 24551 24552 24553 24555 24557 24559 
    ##     4     4     5     5     3     5     5     5     3     5     5     5     3 
    ## 24560 24562 24563 24564 24566 24567 24568 24569 24572 24573 24575 24576 24577 
    ##     3     5     4     3     5     5     5     5     3     5     4     5     5 
    ## 24578 24579 24580 24582 24583 24584 24585 24587 24588 24589 24590 24591 24593 
    ##     5     4     5     3     3     5     3     5     5     5     5     5     5 
    ## 24594 24596 24597 24598 24599 24601 24602 24603 24604 24606 24607 24608 24609 
    ##     3     5     5     5     4     3     4     5     5     5     5     4     5 
    ## 24611 24613 24614 24616 24617 24618 24619 24620 24621 24622 24623 24624 24625 
    ##     5     3     5     4     5     3     4     4     5     3     5     5     3 
    ## 24626 24627 24628 24629 24630 24633 24635 24638 24641 24642 24644 24645 24646 
    ##     4     5     4     5     5     3     5     4     5     5     5     5     5 
    ## 24647 24649 24650 24652 24653 24654 24655 24657 24659 24661 24662 24666 24667 
    ##     4     5     3     5     4     3     3     5     4     3     5     4     5 
    ## 24668 24670 24671 24672 24673 24676 24678 24680 24681 24682 24683 24684 24685 
    ##     5     3     5     3     4     3     4     5     5     5     4     5     4 
    ## 24686 24689 24691 24692 24693 24694 24695 24697 24698 24700 24701 24702 24703 
    ##     3     5     5     4     5     5     5     5     5     5     5     5     5 
    ## 24704 24705 24706 24708 24709 24710 24711 24712 24713 24714 24715 24716 24719 
    ##     5     5     5     4     5     5     3     5     5     5     5     5     5 
    ## 24722 24724 24726 24727 24729 24730 24731 24732 24733 24734 24736 24737 24738 
    ##     5     5     3     4     3     3     5     5     5     3     5     3     4 
    ## 24740 24742 24743 24744 24745 24747 24749 24750 24751 24753 24754 24757 24758 
    ##     5     5     3     5     4     4     3     3     5     5     5     3     5 
    ## 24759 24760 24761 24763 24765 24766 24767 24768 24769 24770 24771 24772 24773 
    ##     3     4     5     5     4     3     5     4     4     5     5     5     4 
    ## 24774 24775 24776 24777 24778 24780 24781 24782 24783 24784 24785 24786 24787 
    ##     4     5     5     4     3     5     5     5     3     3     5     4     4 
    ## 24789 24793 24794 24798 24799 24800 24802 24803 24804 24805 24808 24809 24811 
    ##     4     5     4     5     5     5     5     5     5     3     4     4     5 
    ## 24812 24813 24814 24815 24816 24817 24818 24820 24821 24822 24823 24824 24825 
    ##     4     4     4     5     3     5     4     4     5     3     4     4     5 
    ## 24827 24828 24829 24830 24833 24834 24835 24836 24837 24839 24840 24841 24843 
    ##     5     5     3     5     4     5     3     5     3     5     3     3     4 
    ## 24844 24845 24847 24849 24852 24855 24857 24858 24859 24860 24864 24865 24866 
    ##     5     4     5     5     3     5     3     5     5     5     5     3     3 
    ## 24868 24869 24871 24872 24874 24875 24876 24877 24878 24879 24881 24882 24883 
    ##     3     5     5     4     5     3     5     5     5     3     5     5     3 
    ## 24884 24885 24886 24887 24888 24889 24890 24891 24892 24893 24901 24902 24904 
    ##     3     4     4     5     5     5     3     5     5     5     4     5     4 
    ## 24906 24907 24909 24910 24911 24912 24913 24914 24915 24916 24919 24920 24922 
    ##     5     3     3     5     4     5     5     5     5     3     5     3     5 
    ## 24925 24926 24928 24929 24930 24931 24934 24935 24938 24940 24941 24944 24946 
    ##     3     5     4     5     5     4     4     5     5     3     4     5     5 
    ## 24948 24949 24950 24951 24952 24953 24955 24956 24958 24960 24961 24963 24965 
    ##     4     3     5     4     3     5     4     5     4     5     5     5     3 
    ## 24966 24967 24968 24970 24973 24974 24975 24977 24979 24981 24982 24983 24984 
    ##     3     4     5     5     5     5     5     5     5     5     5     5     5 
    ## 24985 24986 24988 24990 24991 24992 24993 24994 24995 24996 24997 24999 25000 
    ##     3     3     4     4     4     3     5     5     4     5     5     5     4 
    ## 25001 25002 25004 25006 25007 25008 25009 25010 25012 25013 25014 25015 25016 
    ##     5     4     5     3     4     5     3     5     5     5     5     5     5 
    ## 25017 25018 25020 25021 25022 25023 25024 25025 25026 25028 25029 25030 25031 
    ##     5     5     4     3     5     3     5     5     5     5     5     5     5 
    ## 25032 25033 25035 25038 25040 25041 25042 25043 25045 25046 25048 25049 25052 
    ##     5     5     3     5     5     4     5     5     5     5     3     3     5 
    ## 25053 25054 25056 25057 25058 25059 25060 25061 25062 25063 25064 25066 25067 
    ##     5     4     4     5     5     3     5     4     4     5     3     5     5 
    ## 25069 25070 25071 25072 25073 25075 25076 25077 25078 25081 25083 25084 25085 
    ##     5     5     3     5     5     3     5     4     4     5     4     3     3 
    ## 25086 25087 25088 25090 25091 25092 25094 25095 25096 25097 25098 25099 25100 
    ##     5     5     4     3     5     5     5     5     5     5     5     4     4 
    ## 25103 25104 25105 25106 25107 25108 25109 25110 25111 25112 25114 25115 25116 
    ##     5     4     5     3     4     5     5     5     5     4     4     5     5 
    ## 25117 25118 25119 25120 25121 25122 25123 25124 25125 25126 25127 25128 25130 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 25131 25132 25133 25135 25136 25137 25138 25139 25140 25141 25144 25146 25147 
    ##     5     4     5     5     5     3     4     5     4     5     3     3     5 
    ## 25149 25150 25151 25152 25153 25155 25159 25161 25162 25163 25165 25166 25167 
    ##     5     5     5     5     5     3     5     5     4     5     5     5     4 
    ## 25168 25169 25170 25171 25172 25173 25174 25175 25176 25177 25179 25180 25181 
    ##     5     5     5     5     3     4     5     4     5     4     3     5     5 
    ## 25182 25184 25186 25188 25190 25191 25192 25194 25196 25198 25199 25200 25201 
    ##     4     3     5     5     5     5     5     4     5     5     4     5     5 
    ## 25202 25203 25204 25205 25206 25207 25208 25210 25211 25212 25213 25215 25221 
    ##     5     5     3     4     5     5     3     5     5     5     4     4     5 
    ## 25222 25224 25225 25226 25229 25231 25232 25233 25234 25235 25236 25238 25240 
    ##     3     3     3     5     5     5     5     5     3     5     4     5     4 
    ## 25241 25242 25243 25245 25246 25247 25249 25251 25253 25254 25255 25258 25259 
    ##     5     5     4     5     3     5     4     3     4     5     5     5     4 
    ## 25260 25261 25263 25264 25266 25267 25272 25274 25276 25277 25280 25282 25283 
    ##     5     5     4     5     3     4     5     4     5     5     5     5     5 
    ## 25284 25287 25288 25289 25291 25292 25293 25295 25296 25297 25298 25299 25300 
    ##     5     4     5     4     3     5     5     5     5     5     5     3     4 
    ## 25302 25303 25304 25305 25307 25308 25309 25311 25312 25313 25314 25316 25317 
    ##     4     5     5     3     3     5     5     5     3     5     5     4     3 
    ## 25318 25319 25320 25321 25322 25323 25325 25326 25327 25328 25329 25331 25332 
    ##     3     5     5     5     3     4     3     5     4     4     4     3     4 
    ## 25334 25335 25336 25338 25339 25340 25341 25342 25343 25344 25345 25346 25347 
    ##     5     5     4     5     4     4     3     5     5     3     5     5     4 
    ## 25348 25349 25351 25352 25354 25355 25356 25358 25361 25363 25364 25365 25366 
    ##     4     4     5     5     5     3     3     5     5     4     5     3     5 
    ## 25367 25369 25370 25371 25372 25373 25374 25375 25377 25378 25380 25381 25383 
    ##     4     5     5     5     5     5     5     5     5     5     5     3     5 
    ## 25384 25386 25388 25389 25390 25391 25392 25395 25396 25397 25398 25399 25400 
    ##     3     4     5     5     5     5     5     5     5     3     5     3     4 
    ## 25401 25402 25404 25408 25409 25412 25414 25415 25416 25418 25419 25420 25421 
    ##     5     5     4     4     5     3     5     5     4     5     5     3     4 
    ## 25424 25425 25426 25428 25430 25431 25432 25433 25434 25435 25437 25438 25439 
    ##     5     3     3     4     5     4     4     5     3     4     5     5     5 
    ## 25440 25442 25443 25445 25446 25448 25449 25451 25452 25453 25455 25456 25457 
    ##     5     5     4     5     5     5     4     4     5     4     3     3     4 
    ## 25459 25460 25461 25462 25463 25466 25467 25468 25469 25471 25474 25476 25477 
    ##     5     5     5     3     5     5     5     4     4     5     5     3     5 
    ## 25480 25481 25483 25486 25487 25488 25489 25490 25491 25492 25494 25496 25500 
    ##     4     5     4     3     4     5     3     5     4     4     5     4     4 
    ## 25504 25505 25506 25508 25509 25510 25514 25515 25516 25519 25520 25521 25522 
    ##     5     4     3     4     3     3     5     3     5     5     4     3     5 
    ## 25523 25524 25525 25526 25528 25529 25530 25531 25532 25533 25534 25535 25536 
    ##     5     3     4     3     5     5     5     5     5     3     5     4     5 
    ## 25537 25538 25539 25540 25541 25542 25543 25544 25545 25547 25548 25549 25551 
    ##     5     5     3     4     3     5     4     4     5     5     5     5     5 
    ## 25554 25555 25556 25557 25558 25559 25560 25561 25562 25563 25564 25565 25566 
    ##     5     5     4     5     5     5     5     5     5     5     5     5     4 
    ## 25568 25569 25570 25571 25572 25575 25576 25579 25581 25582 25583 25584 25586 
    ##     5     4     3     5     3     4     4     5     5     5     5     3     5 
    ## 25587 25589 25590 25591 25592 25593 25595 25597 25598 25600 25602 25603 25605 
    ##     5     3     5     3     3     5     3     5     3     3     5     5     3 
    ## 25607 25608 25610 25612 25613 25614 25615 25616 25617 25618 25619 25620 25622 
    ##     5     5     5     4     5     5     3     3     5     3     4     5     5 
    ## 25623 25624 25626 25627 25629 25630 25631 25632 25633 25634 25636 25637 25638 
    ##     3     4     4     3     5     5     5     5     5     4     5     3     5 
    ## 25640 25641 25642 25643 25644 25645 25647 25648 25650 25651 25652 25654 25656 
    ##     4     5     5     5     4     5     3     5     5     4     5     5     5 
    ## 25658 25659 25660 25661 25662 25663 25665 25669 25670 25671 25673 25675 25676 
    ##     3     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 25677 25678 25679 25680 25681 25682 25683 25684 25685 25686 25687 25688 25692 
    ##     5     5     4     3     5     5     5     3     4     5     5     4     5 
    ## 25694 25696 25697 25699 25700 25701 25703 25704 25705 25708 25710 25711 25712 
    ##     3     3     3     4     5     5     5     5     5     4     3     5     3 
    ## 25713 25714 25717 25719 25720 25721 25722 25723 25724 25725 25726 25727 25729 
    ##     5     3     5     4     4     3     4     3     5     5     4     3     3 
    ## 25730 25731 25732 25733 25735 25736 25737 25741 25743 25744 25746 25747 25748 
    ##     5     3     5     3     3     5     4     5     4     3     4     5     5 
    ## 25749 25751 25752 25753 25758 25759 25760 25761 25762 25763 25764 25766 25767 
    ##     5     3     5     3     3     5     5     3     5     5     3     5     3 
    ## 25768 25769 25771 25772 25773 25775 25776 25777 25778 25780 25784 25785 25786 
    ##     5     3     5     5     4     3     3     5     4     4     3     5     5 
    ## 25788 25789 25790 25791 25792 25793 25794 25795 25796 25797 25799 25800 25801 
    ##     3     5     4     3     3     5     5     3     4     5     3     3     4 
    ## 25804 25807 25808 25809 25810 25812 25813 25814 25815 25816 25817 25818 25819 
    ##     3     3     5     4     3     5     4     3     3     4     5     5     3 
    ## 25821 25822 25827 25828 25829 25830 25834 25836 25837 25838 25839 25840 25841 
    ##     5     4     3     5     5     5     5     3     5     5     5     4     5 
    ## 25843 25844 25845 25847 25848 25849 25850 25851 25852 25853 25854 25855 25857 
    ##     5     3     4     3     4     5     3     3     5     5     5     5     5 
    ## 25858 25859 25860 25861 25862 25863 25865 25866 25867 25868 25869 25870 25871 
    ##     3     5     5     5     5     3     5     3     5     5     4     5     3 
    ## 25872 25874 25875 25876 25877 25878 25879 25880 25881 25883 25885 25886 25887 
    ##     5     5     5     3     5     5     3     5     5     4     3     5     4 
    ## 25888 25889 25890 25893 25895 25898 25899 25900 25901 25904 25908 25911 25912 
    ##     5     5     5     5     5     5     4     5     5     3     5     5     5 
    ## 25913 25914 25916 25917 25918 25919 25922 25924 25925 25926 25930 25931 25932 
    ##     5     4     5     5     4     5     5     3     5     4     4     5     3 
    ## 25933 25935 25938 25939 25940 25941 25942 25943 25944 25946 25947 25948 25949 
    ##     4     3     5     4     4     3     3     3     4     5     4     5     4 
    ## 25951 25953 25955 25956 25957 25958 25959 25960 25961 25962 25964 25965 25966 
    ##     4     5     5     3     3     5     5     5     3     5     5     5     5 
    ## 25967 25968 25969 25972 25976 25977 25978 25979 25981 25982 25983 25985 25986 
    ##     5     5     5     3     5     4     4     5     4     3     5     3     4 
    ## 25992 25993 25994 25995 25996 25997 25999 26000 26003 26004 26007 26008 26009 
    ##     4     5     5     5     3     5     4     5     5     3     5     5     3 
    ## 26010 26011 26012 26014 26015 26016 26017 26019 26021 26023 26025 26026 26027 
    ##     4     3     4     3     3     3     5     3     4     5     4     4     5 
    ## 26028 26029 26030 26031 26033 26034 26035 26036 26037 26038 26039 26040 26041 
    ##     5     5     5     5     5     5     4     5     4     3     5     5     5 
    ## 26043 26045 26047 26048 26050 26052 26053 26055 26056 26057 26058 26059 26060 
    ##     3     4     3     5     5     5     5     5     5     4     4     5     4 
    ## 26063 26065 26067 26069 26071 26072 26074 26075 26076 26077 26078 26081 26082 
    ##     5     5     5     5     5     3     5     3     4     5     4     5     5 
    ## 26085 26087 26088 26089 26090 26091 26092 26093 26094 26095 26097 26098 26100 
    ##     3     5     5     5     3     5     4     5     5     5     3     5     5 
    ## 26101 26102 26103 26104 26105 26106 26107 26108 26109 26110 26111 26112 26114 
    ##     5     5     5     5     3     3     4     4     5     5     5     5     5 
    ## 26117 26118 26119 26120 26121 26122 26123 26124 26126 26127 26129 26132 26133 
    ##     5     5     5     3     5     4     5     5     5     5     4     5     3 
    ## 26134 26135 26136 26137 26138 26139 26142 26143 26145 26146 26147 26148 26149 
    ##     3     4     3     4     5     5     5     5     5     5     5     4     4 
    ## 26150 26151 26153 26154 26155 26156 26157 26158 26159 26160 26161 26162 26164 
    ##     4     5     5     5     5     5     5     4     5     5     3     5     5 
    ## 26165 26166 26167 26170 26171 26172 26173 26174 26175 26178 26180 26181 26182 
    ##     5     5     4     5     5     5     5     5     4     4     5     5     5 
    ## 26184 26185 26186 26188 26189 26190 26191 26193 26194 26195 26196 26197 26198 
    ##     5     5     3     5     4     5     5     5     5     4     5     4     5 
    ## 26199 26201 26203 26207 26208 26211 26212 26213 26214 26216 26217 26218 26219 
    ##     5     5     5     4     5     4     5     5     4     5     5     5     5 
    ## 26222 26223 26225 26226 26227 26228 26231 26232 26233 26235 26236 26237 26240 
    ##     3     4     5     5     4     5     5     3     4     4     3     3     5 
    ## 26241 26242 26243 26244 26245 26247 26248 26249 26250 26252 26253 26255 26256 
    ##     5     4     5     5     5     5     5     5     5     3     5     5     3 
    ## 26257 26258 26259 26261 26263 26264 26265 26266 26268 26270 26271 26273 26275 
    ##     5     4     5     5     5     4     5     5     5     5     5     5     5 
    ## 26278 26281 26282 26283 26288 26289 26290 26292 26294 26295 26296 26297 26298 
    ##     3     3     4     5     5     5     4     5     5     3     5     3     5 
    ## 26300 26301 26303 26304 26305 26306 26307 26310 26311 26312 26313 26314 26315 
    ##     5     5     5     5     5     5     5     5     5     5     5     5     5 
    ## 26316 26318 26319 26320 26324 26326 26327 26328 26329 26330 26331 26332 26335 
    ##     5     4     5     5     4     3     4     4     5     4     5     3     5 
    ## 26336 26337 26339 26340 26341 26345 26346 26347 26348 26349 26350 26351 26355 
    ##     5     5     5     5     5     5     5     5     5     3     3     5     5 
    ## 26356 26357 26358 26359 26360 26362 26363 26365 26368 26369 26370 26371 26373 
    ##     5     5     4     5     3     5     4     4     3     5     4     5     4 
    ## 26375 26376 26379 26380 26382 26383 26384 26385 26386 26387 26388 26390 26392 
    ##     4     5     5     3     5     4     5     5     5     4     3     5     4 
    ## 26393 26394 26397 26398 26399 26400 26401 26402 26404 26405 26407 26408 26409 
    ##     5     3     5     5     5     4     4     5     5     5     4     5     5 
    ## 26411 26412 26413 26416 26417 26418 26420 26421 26422 26425 26426 26427 26428 
    ##     4     3     5     3     4     5     3     3     4     4     4     5     3 
    ## 26432 26433 26434 26435 26436 26439 26440 26442 26443 26444 26445 26446 26448 
    ##     5     4     4     5     5     5     5     5     5     3     3     5     4 
    ## 26449 26451 26454 26455 26457 26458 26459 26460 26461 26462 26468 26469 26471 
    ##     3     5     3     5     5     5     3     3     5     3     5     5     3 
    ## 26472 26475 26476 26479 26480 26481 26482 26483 26484 26485 26488 26490 26491 
    ##     5     5     4     4     5     3     5     5     5     5     5     5     5 
    ## 26492 26494 26495 26496 26497 26498 26499 26501 26504 26506 26507 26508 26510 
    ##     5     5     5     3     3     5     4     5     5     4     4     5     4 
    ## 26511 26512 26513 26515 26516 26517 26518 26519 26520 26522 26526 26527 26529 
    ##     5     5     5     5     4     5     4     5     5     5     5     3     4 
    ## 26530 26533 26534 26535 26536 26538 26539 26541 26543 26544 26545 26546 26548 
    ##     3     4     5     3     4     3     5     5     5     5     5     5     3 
    ## 26549 26550 26551 26552 26553 26554 26555 26556 26557 26558 26560 26562 26563 
    ##     3     4     3     3     3     4     3     5     5     5     4     5     5 
    ## 26564 26565 26567 26568 26569 26570 26572 26573 26574 26575 26576 26577 26578 
    ##     4     3     3     3     3     4     5     3     5     3     5     5     5 
    ## 26579 26581 26582 26583 26584 26585 26587 26589 26590 26591 26592 26593 26595 
    ##     4     5     4     4     3     5     4     5     4     5     3     5     5 
    ## 26596 26597 26598 26599 26600 26605 26606 26608 26609 26610 26611 26612 26613 
    ##     5     5     4     3     5     4     5     5     4     3     5     3     3 
    ## 26614 26615 26616 26617 26619 26620 26622 26625 26628 26629 26630 26631 26632 
    ##     5     3     5     3     4     5     3     5     4     5     5     5     4 
    ## 26633 26634 26635 26636 26637 26638 26639 26640 26641 26642 26643 26645 26647 
    ##     5     5     5     5     5     5     4     4     3     5     5     5     5 
    ## 26648 26650 26651 26652 26653 26655 26657 26659 26661 26662 26663 26664 26665 
    ##     4     4     4     5     5     5     4     5     4     4     3     5     5 
    ## 26666 26667 26668 26670 26672 26673 26674 26676 26677 26678 26679 26683 26684 
    ##     5     5     5     5     5     5     5     5     4     5     5     4     5 
    ## 26685 26686 26687 26688 26689 26690 26695 26698 26699 26700 26703 26705 26706 
    ##     3     5     3     5     4     5     5     5     5     3     5     4     5 
    ## 26707 26709 26710 26711 26712 26713 26714 26716 26717 26719 26720 26722 26724 
    ##     3     5     5     3     5     5     3     4     3     4     5     4     4 
    ## 26725 26726 26727 26728 26729 26730 26731 26733 26734 26735 26736 26737 26738 
    ##     5     5     4     5     5     5     5     3     5     3     4     5     5 
    ## 26739 26740 26742 26743 26744 26745 26746 26747 26748 26750 26752 26753 26754 
    ##     5     3     5     5     5     5     4     5     5     5     3     5     5 
    ## 26755 26757 26759 26760 26761 26762 26763 26764 26765 26766 26767 26768 26769 
    ##     3     3     5     3     4     5     3     3     5     5     3     5     3 
    ## 26770 26771 26772 26773 26774 26776 26778 26779 26780 26781 26783 26784 26785 
    ##     5     5     5     4     5     5     4     4     4     3     3     3     3 
    ## 26786 26789 26790 26791 26792 26794 26795 26797 26800 26802 26804 26806 26809 
    ##     5     5     5     5     5     4     3     5     3     5     4     4     5 
    ## 26810 26811 26812 26813 26814 26816 26818 26820 26822 26823 26824 26826 26827 
    ##     4     4     4     5     5     5     5     5     5     3     3     4     5 
    ## 26829 26830 26832 26834 26836 26837 26838 26839 26840 26841 26843 26844 26845 
    ##     5     5     5     5     5     4     5     5     3     5     4     5     3 
    ## 26846 26847 26849 26850 26853 26854 26855 26856 26857 26858 26859 26860 26862 
    ##     3     5     5     4     4     5     4     5     5     5     5     5     5 
    ## 26864 26865 26866 26867 26868 26870 26871 26872 26873 26874 26875 26876 26878 
    ##     3     4     5     5     5     4     4     5     5     5     5     5     3 
    ## 26879 26880 26882 26884 26885 26886 26887 26889 26890 26891 26892 26894 26896 
    ##     5     4     4     4     4     5     3     5     5     4     5     5     5 
    ## 26898 26899 26901 26905 26907 26908 26909 26910 26913 26914 26915 26916 26917 
    ##     3     4     4     5     5     5     5     5     5     5     4     5     3 
    ## 26918 26919 26920 26921 26922 26923 26924 26925 26926 26930 26932 26933 26934 
    ##     3     5     5     4     5     4     3     3     3     3     5     5     5 
    ## 26936 26937 26938 26939 26940 26942 26943 26944 26948 26949 26950 26952 26953 
    ##     5     3     4     3     5     3     4     5     5     3     5     3     5 
    ## 26955 26956 26957 26959 26960 26966 26967 26968 26969 26970 26972 26973 26974 
    ##     5     5     5     4     5     4     5     5     5     5     5     3     4 
    ## 26976 26978 26979 26980 26983 26984 26985 26986 26988 26989 26990 26992 26993 
    ##     5     3     5     5     5     5     5     5     4     5     5     4     3 
    ## 26994 26995 26996 26997 26999 27000 27001 27002 27005 27007 27008 27009 27011 
    ##     5     3     5     5     5     5     5     5     5     5     5     5     4 
    ## 27012 27013 27014 27015 27016 27017 27019 27021 27023 27024 27025 27026 27028 
    ##     4     5     4     4     3     5     5     4     5     4     5     5     4 
    ## 27031 27032 27033 27035 27039 27040 27041 27043 27045 27046 27047 27048 27049 
    ##     5     5     5     5     4     3     3     3     5     5     5     4     5 
    ## 27051 27052 27053 27054 27055 27056 27057 27059 27060 27061 27063 27064 27066 
    ##     4     5     3     5     5     5     5     5     3     5     4     3     5 
    ## 27068 27069 27071 27072 27073 27074 27076 27077 27078 27079 27082 27083 27085 
    ##     4     4     5     5     5     5     4     5     5     3     5     5     4 
    ## 27086 27087 27088 27090 27091 27095 27097 27098 27099 27100 27101 27106 27107 
    ##     4     5     5     4     5     5     5     3     5     5     5     5     5 
    ## 27108 27109 27110 27111 27113 27114 27115 27116 27117 27118 27120 27122 27123 
    ##     5     4     5     5     3     5     4     5     5     5     5     3     4 
    ## 27126 27128 27129 27131 27132 27133 27134 27135 27136 27137 27138 27140 27141 
    ##     4     5     5     5     5     5     5     3     5     5     4     3     5 
    ## 27142 27144 27145 27146 27148 27149 27150 27152 27153 27154 27156 27157 27159 
    ##     5     4     4     3     5     5     4     5     3     3     5     3     5 
    ## 27160 27161 27163 27164 27165 27166 27167 27168 27169 27171 27172 27175 27176 
    ##     3     5     3     3     5     5     4     5     5     3     5     3     3 
    ## 27177 27178 27179 27180 27181 27182 27183 27185 27186 27187 27188 27189 27190 
    ##     4     5     5     5     5     5     4     5     4     3     3     5     3 
    ## 27192 27193 27194 27196 27197 27198 27200 27201 27202 27203 27205 27206 27207 
    ##     3     3     5     4     5     4     5     3     4     5     5     5     5 
    ## 27210 27211 27212 27213 27216 27217 27220 27221 27223 27224 27225 27226 27227 
    ##     5     5     5     5     5     5     5     5     5     4     5     3     5 
    ## 27228 27229 27230 27232 27233 27235 27236 27239 27242 27243 27244 27245 27246 
    ##     3     3     3     5     4     5     4     4     5     4     4     5     5 
    ## 27248 27250 27251 27252 27254 27259 27260 27261 27262 27263 27265 27266 27267 
    ##     5     3     4     5     4     4     5     5     3     3     4     5     4 
    ## 27268 27269 27270 27271 27272 27274 27275 27277 27278 27279 27280 27281 27282 
    ##     5     3     4     4     5     4     4     5     5     5     4     5     3 
    ## 27284 27286 27287 27288 27290 27291 27292 27293 27294 27295 27296 27297 27298 
    ##     5     3     5     5     3     3     5     5     4     5     5     5     5 
    ## 27301 27306 27307 27308 27310 27311 27312 27313 27314 27316 27318 27319 27320 
    ##     5     3     5     5     3     5     5     5     5     4     3     5     5 
    ## 27321 27323 27326 27328 27330 27331 27332 27333 27334 27335 27337 27338 27339 
    ##     5     3     4     5     3     5     5     5     5     5     3     4     3 
    ## 27340 27341 27342 27343 27346 27347 27348 27349 27350 27353 27354 27355 27359 
    ##     5     5     3     5     5     5     4     5     4     3     3     5     5 
    ## 27360 27362 27364 27366 27367 27368 27369 27370 27371 27372 27374 27376 27377 
    ##     3     3     4     5     5     5     5     5     5     3     4     5     5 
    ## 27378 27379 27380 27381 27384 27386 27388 27390 27391 27392 27393 27395 27396 
    ##     4     5     3     3     5     5     4     4     3     3     3     4     3 
    ## 27397 27398 27399 27401 27402 27404 27405 27406 27407 27408 27409 27410 27413 
    ##     5     4     5     5     3     5     5     5     4     4     5     3     5 
    ## 27414 27415 27416 27418 27419 27420 27421 27422 27423 27424 27425 27426 27428 
    ##     4     3     4     4     5     4     4     5     5     5     5     3     5 
    ## 27429 27430 27431 27433 27434 27436 27437 27438 27439 27440 27441 27442 27444 
    ##     5     3     5     5     5     4     3     3     3     4     5     4     5 
    ## 27445 27446 27447 27448 27450 27451 27453 27454 27455 27457 27458 27459 27461 
    ##     5     5     5     5     5     5     3     5     5     3     5     5     4 
    ## 27462 27463 27465 27466 27467 27468 27469 27470 27473 27474 27475 27476 27478 
    ##     3     3     5     5     5     5     5     5     3     3     4     5     5 
    ## 27479 27481 27482 27485 27486 27488 27489 27490 27491 27496 27497 27498 27499 
    ##     5     3     5     3     5     5     5     5     5     5     5     5     5 
    ## 27500 27501 27502 27503 27504 27505 27506 27508 27510 27515 27516 27517 27519 
    ##     5     3     3     5     4     5     5     4     3     5     5     5     4 
    ## 27520 27522 27523 27525 27527 27530 27532 27533 27535 27536 27537 27538 
    ##     3     5     3     4     5     3     3     3     4     5     4     5 
    ## 
    ## $call
    ## rpart(formula = Depresion_cat ~ ., data = df_entrenamiento_depresion, 
    ##     method = "class", control = rpart.control(minbucket = 2))
    ## 
    ## $terms
    ## Depresion_cat ~ Extraversion + Agreeableness + Conscientiousness + 
    ##     EmotionalStability + Openness
    ## attr(,"variables")
    ## list(Depresion_cat, Extraversion, Agreeableness, Conscientiousness, 
    ##     EmotionalStability, Openness)
    ## attr(,"factors")
    ##                    Extraversion Agreeableness Conscientiousness
    ## Depresion_cat                 0             0                 0
    ## Extraversion                  1             0                 0
    ## Agreeableness                 0             1                 0
    ## Conscientiousness             0             0                 1
    ## EmotionalStability            0             0                 0
    ## Openness                      0             0                 0
    ##                    EmotionalStability Openness
    ## Depresion_cat                       0        0
    ## Extraversion                        0        0
    ## Agreeableness                       0        0
    ## Conscientiousness                   0        0
    ## EmotionalStability                  1        0
    ## Openness                            0        1
    ## attr(,"term.labels")
    ## [1] "Extraversion"       "Agreeableness"      "Conscientiousness" 
    ## [4] "EmotionalStability" "Openness"          
    ## attr(,"order")
    ## [1] 1 1 1 1 1
    ## attr(,"intercept")
    ## [1] 1
    ## attr(,"response")
    ## [1] 1
    ## attr(,".Environment")
    ## <environment: R_GlobalEnv>
    ## attr(,"predvars")
    ## list(Depresion_cat, Extraversion, Agreeableness, Conscientiousness, 
    ##     EmotionalStability, Openness)
    ## attr(,"dataClasses")
    ##      Depresion_cat       Extraversion      Agreeableness  Conscientiousness 
    ##        "character"          "numeric"          "numeric"          "numeric" 
    ## EmotionalStability           Openness 
    ##          "numeric"          "numeric" 
    ## 
    ## $cptable
    ##           CP nsplit rel error    xerror        xstd
    ## 1 0.08023914      0 1.0000000 1.0000000 0.007281152
    ## 2 0.05821271      1 0.9197609 0.9167191 0.007249844
    ## 3 0.01000000      2 0.8615481 0.8654290 0.007205694
    ## 
    ## $method
    ## [1] "class"

    #Imprimimos el arbol
    rpart.plot(modelo_cart_depresion_mb)

![](index_files/figure-markdown_strict/modelo%20minbucket%20maxdepth-1.png)

Vemos que con maxdepth y minbucket el árbol no cambia. Veamos qué pasa
si modificamos la complejidad.

#### Variando complejidad

    # Fit
    modelo_cart_depresion_cp <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = "class", control = rpart.control(cp = 0.002))

    #Resumen del modelo
    #print(paste("Resumen del modelo para", 'Depresion'))
    #print(head(summary(modelo_cart_depresion_cp)))

    #Imprimimos el arbol
    rpart.plot(modelo_cart_depresion_cp)

![](index_files/figure-markdown_strict/modelo%20complejidad-1.png)

Vemos que aunque le dejemos crecer en complejidad, sigue prediciendo
únicamente dos de los 4 casos que tenemos, pero comienza a utilizar
algunas nuevas categorias para la predicción.

Veamos cuándo aparece nuestra nueva clase en las predicciones.

    #Fit
    modelo_cart_depresion_cp <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = "class", control = rpart.control(cp = 0.0004))

    #Resumen
    #print(paste("Resumen del modelo para", 'Depresion'))
    #print(head(summary(modelo_cart_depresion_cp)))

    #Grafico
    rpart.plot(modelo_cart_depresion_cp)

    ## Warning: labs do not fit even at cex 0.15, there may be some overplotting

![](index_files/figure-markdown_strict/Clases-1.png)

Aquí vemos que con un cp = 0.0004 aparecen nuevas clases, pero tenemos
muchos más nodos terminales y un árbol considerablemente más complejo
que el anterior, donde sacrificamos explicabilidad.

Veamos entonces qué variables son las más importantes en este modelo.

    importancia_variables <- data.frame(variable = names(modelo_cart_depresion_cp$variable.importance),
                                         importancia = modelo_cart_depresion_cp$variable.importance)

    #Ordenamos importancia
    importancia_variables <- importancia_variables[order(-importancia_variables$importancia), ]

    #Grafico de barras
    barplot(importancia_variables$importancia, names.arg = importancia_variables$variable, 
            col = "orange", main = "Importancia de Variables", 
            xlab = "Variables", ylab = "Importancia", cex.names = 0.60)

![](index_files/figure-markdown_strict/Feature%20importance-1.png)

Podemos ver que la variable más importante es EmotionalStability, le
sigue Conscientiousness y Extraversion. Esto tiene sentido ya que son
las variables que aparecieron primero en los splits del arbol.

¿Qué pasa si agregamos más variables?

#### Modelo con más variables

    variables_predictoras_mas <- c("Extraversion", "Agreeableness", "Conscientiousness", "EmotionalStability", "Openness", "education", "urban", "gender", "engnat", "hand", "religion", "orientation", "race", "voted", "married", "familysize", "Depresion_cat")


    df_entrenamiento_depresion_mas <- df_entrenamiento[variables_predictoras_mas]

    #Fit
    modelo_cart_depresion_cp_mas <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion_mas, method = "class", control = rpart.control(cp = 0.00039))

    #print(paste("Resumen del modelo para", 'Depresion'))
    #print(summary(modelo_cart_depresion_cp_mas))

    #Grafico arbol
    rpart.plot(modelo_cart_depresion_cp_mas)

    ## Warning: labs do not fit even at cex 0.15, there may be some overplotting

![](index_files/figure-markdown_strict/Mas%20variables-1.png)

    #Importancia
    importancia_variables_mas <- data.frame(variable = names(modelo_cart_depresion_cp_mas$variable.importance),
                                         importancia = modelo_cart_depresion_cp_mas$variable.importance)

    importancia_variables_mas <- importancia_variables_mas[order(-importancia_variables_mas$importancia), ]

    barplot(importancia_variables_mas$importancia,  
            col = "orange", main = "Importancia de Variables", 
            xlab = "Variables", ylab = "Importancia") 

    #Acomodamos nombres
    axis(1, at = seq_along(importancia_variables_mas$variable),
         labels = importancia_variables_mas$variable, las = 2, cex.names= 0.4)

    ## Warning in axis(1, at = seq_along(importancia_variables_mas$variable), labels =
    ## importancia_variables_mas$variable, : "cex.names" is not a graphical parameter

![](index_files/figure-markdown_strict/Mas%20variables-2.png)

Con esto podemos ver que nuestras variables más importantes siguen
siendo las de personalidad, mientras que las variables agregadas tienen
muy poca importancia. Podemos verificar en qué grado usa cada variable
en los splits.

    tabla_variables <- as.data.frame(modelo_cart_depresion_cp_mas$frame$var[modelo_cart_depresion_cp_mas$frame$var != "<leaf>"])
    names(tabla_variables) <- c("variable")
    tabla_variables %>%
      group_by(variable) %>%
      summarise(n = n()) %>%
      mutate(freq = round(n / sum(n), 2)) %>%
      arrange(-n)

    ## # A tibble: 13 × 3
    ##    variable               n  freq
    ##    <chr>              <int> <dbl>
    ##  1 EmotionalStability     9  0.19
    ##  2 Openness               8  0.17
    ##  3 Extraversion           7  0.15
    ##  4 Conscientiousness      6  0.12
    ##  5 Agreeableness          5  0.1 
    ##  6 familysize             5  0.1 
    ##  7 orientation            2  0.04
    ##  8 education              1  0.02
    ##  9 engnat                 1  0.02
    ## 10 hand                   1  0.02
    ## 11 race                   1  0.02
    ## 12 religion               1  0.02
    ## 13 urban                  1  0.02

Efectivamente podemos ver que las variables de personalidad son las más
usadas por nuestro modelo para poder predecir, y luego aparecen el
tamaño del grupo familiar, la orientación sexual, el nivel educativo
alcanzado, entre otras, pero con mucha menos frecuencia.

#### Poda del árbol

Cuando tenemos árboles que son complejos tenemos mayor posibilidad de
overfitear. Para esto, podemos realizar poda del árbol, que consiste en
eliminar ramas (nodos y hojas) para reducir su complejidad. Para nuestro
caso, se eliminan las ramas o sub-árboles que no traen diferencias
significativas, es decir que menor reducción de impureza proporcionan.

Si bien la poda puede ser Pre-Pruning o Post-Pruning, en nuestro caso
haremos Post-Pruning lo que implica partir de un árbol complejo y luego
podarlo.

    modelo_cart_depresion_cp_mas_pruned <- prune(modelo_cart_depresion_cp_mas, cp = 0.00195)

    #print(summary(modelo_cart_depresion_cp_mas_pruned))

    rpart.plot(modelo_cart_depresion_cp_mas_pruned)

![](index_files/figure-markdown_strict/Poda%20del%20arbol-1.png)

    tabla_variables_pruned <- as.data.frame(modelo_cart_depresion_cp_mas_pruned$frame$var[modelo_cart_depresion_cp_mas_pruned$frame$var != "<leaf>"])
    names(tabla_variables_pruned) <- c("variable")
    tabla_variables %>%
      group_by(variable) %>%
      summarise(n = n()) %>%
      mutate(freq = round(n / sum(n), 2)) %>%
      arrange(-n)

    ## # A tibble: 13 × 3
    ##    variable               n  freq
    ##    <chr>              <int> <dbl>
    ##  1 EmotionalStability     9  0.19
    ##  2 Openness               8  0.17
    ##  3 Extraversion           7  0.15
    ##  4 Conscientiousness      6  0.12
    ##  5 Agreeableness          5  0.1 
    ##  6 familysize             5  0.1 
    ##  7 orientation            2  0.04
    ##  8 education              1  0.02
    ##  9 engnat                 1  0.02
    ## 10 hand                   1  0.02
    ## 11 race                   1  0.02
    ## 12 religion               1  0.02
    ## 13 urban                  1  0.02

De esta manera obtenemos un árbol podado a una complejidad 0.00195

### Métricas y comparación con otros modelos

Vamos a generar modelos de Naive Bayes y Regresion Logistica Multinomial

    naive_bay <- e1071::naiveBayes(Depresion_cat ~ ., data = df_entrenamiento_depresion_mas)

    modelo_rlm <- nnet::multinom(Depresion_cat ~ ., data = df_entrenamiento_depresion_mas)

    ## # weights:  72 (51 variable)
    ## initial  value 26724.982694 
    ## iter  10 value 21704.033920
    ## iter  20 value 21296.760592
    ## iter  30 value 21187.219378
    ## iter  40 value 20599.690877
    ## iter  50 value 20363.027272
    ## iter  60 value 20199.148554
    ## iter  60 value 20199.148539
    ## iter  60 value 20199.148539
    ## final  value 20199.148539 
    ## converged

    library(pROC)

    ## Warning: package 'pROC' was built under R version 4.3.2

    ## Type 'citation("pROC")' for a citation.

    ## 
    ## Attaching package: 'pROC'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     cov, smooth, var

    #Definimos una funcion para calcular las métriacs

    calcular_metricas <- function(modelo, datos_prueba){
      
      predicciones <- predict(modelo, newdata = datos_prueba, type = "class")
      matriz_confusion <- caret::confusionMatrix(predicciones, datos_prueba$Depresion_cat)$table
      

      precision <- sum(diag(matriz_confusion)) / sum(matriz_confusion)
      sensibilidad <- matriz_confusion[2,2] / sum(matriz_confusion[2,])
      especificidad <- matriz_confusion[1,1] / sum(matriz_confusion[1,])
      f1_score <- 2* (sensibilidad * precision) / (precision + sensibilidad)
      
      # Obtener el nombre del modelo
      nombre_modelo <- deparse(substitute(modelo))
      
      # Imprimir la matriz de confusión con el nombre del modelo
      cat("Matriz de confusión para", nombre_modelo, ":\n")
      print(matriz_confusion)
     
      
      
      resultados <- data.frame(
        Modelo = deparse(substitute(modelo)),
        Precision = precision,
        Sensibilidad = sensibilidad,
        Especificidad = especificidad,
        F1_Score = f1_score
      )
      return(resultados)  
    }

    df_prueba_depresion_mas <- df_prueba[, variables_predictoras_mas]
    df_prueba_depresion_mas$Depresion_cat <- as.factor(df_prueba_depresion_mas$Depresion_cat
                                                       )
    resultados_cart <- calcular_metricas(modelo_cart_depresion_cp_mas, df_prueba_depresion_mas)

    ## Matriz de confusión para modelo_cart_depresion_cp_mas :
    ##           Reference
    ## Prediction Leve Moderado Normal Severo
    ##   Leve        1        5      5      3
    ##   Moderado   21       37     48     31
    ##   Normal    252      313   1030    396
    ##   Severo    502     1126    745   3746

    resultados_nb <- calcular_metricas(naive_bay, df_prueba_depresion_mas)

    ## Matriz de confusión para naive_bay :
    ##           Reference
    ## Prediction Leve Moderado Normal Severo
    ##   Leve        0        0      0      0
    ##   Moderado    7       16     15     26
    ##   Normal    336      411   1204    563
    ##   Severo    433     1054    609   3587

    resultados_rlm <- calcular_metricas(modelo_rlm, df_prueba_depresion_mas)

    ## Matriz de confusión para modelo_rlm :
    ##           Reference
    ## Prediction Leve Moderado Normal Severo
    ##   Leve        0        0      0      0
    ##   Moderado    0        0      0      0
    ##   Normal    261      313   1087    379
    ##   Severo    515     1168    741   3797

    resultados_cart_pruned <- calcular_metricas(modelo_cart_depresion_cp_mas_pruned, df_prueba_depresion_mas)

    ## Matriz de confusión para modelo_cart_depresion_cp_mas_pruned :
    ##           Reference
    ## Prediction Leve Moderado Normal Severo
    ##   Leve        0        0      0      0
    ##   Moderado    0        0      0      0
    ##   Normal    298      383   1115    462
    ##   Severo    478     1098    713   3714

    tabla_resultados <- rbind(resultados_cart, resultados_cart_pruned, resultados_nb, resultados_rlm)
    tabla_resultados

    ##                                Modelo Precision Sensibilidad Especificidad
    ## 1        modelo_cart_depresion_cp_mas 0.5827382     0.270073    0.07142857
    ## 2 modelo_cart_depresion_cp_mas_pruned 0.5845539          NaN           NaN
    ## 3                           naive_bay 0.5818908     0.250000           NaN
    ## 4                          modelo_rlm 0.5912117          NaN           NaN
    ##    F1_Score
    ## 1 0.3690895
    ## 2       NaN
    ## 3 0.3497399
    ## 4       NaN

Con esto podemos ver algunas cosas:

En primer lugar, ningun modelo es bueno en términos prácticos, ya que
estamos hablando de una precisión menor al 60%. Además, tenemos 3
modelos que no logran predecir todos los casos. Dos de ellos solo
predicen 2 de las clases (Normal y Severo), 1 de ellos predice también
sobre Moderado, y solo 1, el árbol sin cortar, logra predecir todas las
clases, aunque con poca precision.
