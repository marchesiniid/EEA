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
    #print(paste("Resumen del modelo para", 'Depresion'))
    #print(head(summary(modelo_cart_depresion_md)))

    #Imprimimos el arbol
    rpart.plot(modelo_cart_depresion_md)

![](index_files/figure-markdown_strict/modelo%20minbucket%20maxdepth-1.png)

    #Fit 2
    modelo_cart_depresion_mb <- rpart(formula = Depresion_cat ~. , data = df_entrenamiento_depresion, method = "class", control = rpart.control(minbucket = 2))

    #Resumen del modelo
    #print(paste("Resumen del modelo para", 'Depresion'))
    #print(head(summary(modelo_cart_depresion_mb)))

    #Imprimimos el arbol
    rpart.plot(modelo_cart_depresion_mb)

![](index_files/figure-markdown_strict/plot%20mb-1.png)

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
