Info813 report week 5: Discriminant analysis on iris
====================================================

Problem statement
-----------------

Two questions will be answered in this report:

1.  Do the three species differ with respect to the four characteristics
    collected?
2.  How well is the model between the specimen and their attributes?

Data description
----------------

Iris dataset collected by Fisher is used, including 150 observations of
iris concerning the following 4 variables:

-   Length of sepal
-   Width of sepal
-   Length of petal
-   Width of petal

![](https://raw.githubusercontent.com/nalsi/Info813/master/perianth_segments2.jpg)

The observations are composed of 50 observations in each of the three
species. No name of the species is specified in the dataset.

Below is the head of this dataset.

    ##   species sepal_length sepal_width petal_length petal_width
    ## 1       1          5.1         3.5          1.4         0.2
    ## 2       1          4.9         3.0          1.4         0.2
    ## 3       1          4.7         3.2          1.3         0.2
    ## 4       1          4.6         3.1          1.5         0.2
    ## 5       1          5.0         3.6          1.4         0.2
    ## 6       1          5.4         3.9          1.7         0.4

Research method
---------------

Linear discriminant analysis method and a set of R packages connected
with this method are used in this report.

Results
-------

#### Distances between iris categories

The distance between the three categories in the dataset can be best
presented by the scatterplot matrices, which is plotted below. Blue dots
belong to catetory 1, red 2, and black 3. It is shown by this plot that
group 1 is almost always different than the other two groups on any
combination of two variables. Category 2 and 3 have strong similarities
in the two sepal-related variables. (In the following chart, **blue** is
group 1, **red** is group 2, and **black** is group 3.)

![](Report_5_files/figure-markdown_strict/unnamed-chunk-2-1.png)<!-- -->

#### Test of assumptions of the original dataset

The following four assumptions of linear discriminant analysis are
tested before the analysis:

1.  Outlier
2.  Homogeneity of variance
3.  Univariate normality
4.  Multivariate normality

###### 1. Outliers

According to the scatterplot matrice, there is no obvious outliers that
can be identified in the dataset. Even though category 1 is relatively
distant from the other two groups.

###### 2. Homogeneity of variance

Homogeneity of variance is tested using **test of variance homogeneity
of correlated variances** that is included the MVT package (version
0.3). However, since the p-value approximates 0, the null hypothesis
that the variances are homogeneous can be rejected. Thus, it could be a
problem to conduct linear discriminant analysis method.

    ## 
    ## Likelihood ratio test for equality of variances 
    ## 
    ## data: output 
    ## LRT statistic = 970.4401, df = 3, p-value = 0
    ## alternative hypothesis: true variances are not equal.
    ## 
    ## sample estimate:
    ##              sepal_length sepal_width petal_length petal_width
    ## sepal_length  0.68564773                                      
    ## sepal_width  -0.04508295   0.18933964                         
    ## petal_length  1.27895734  -0.33427897  3.13303165             
    ## petal_width   0.51869129  -0.12348804  1.30319975   0.58389557

###### 3. Univariate normality

uniNorm function in MVN package (version 4.0) is used to test the
univariate normality of the dataset. The core of this function are a
descriptive analysis as well as the Shapiro-wilk's Normality test.
According to the latter, all variables but sepal\_width fail to pass
this test.

    ## $`Descriptive Statistics`
    ##                n  Mean Std.Dev Median Min Max 25th 75th   Skew Kurtosis
    ## sepal_length 150 5.843   0.828   5.80 4.3 7.9  5.1  6.4  0.309   -0.606
    ## sepal_width  150 3.057   0.436   3.00 2.0 4.4  2.8  3.3  0.313    0.139
    ## petal_length 150 3.758   1.765   4.35 1.0 6.9  1.6  5.1 -0.269   -1.417
    ## petal_width  150 1.199   0.762   1.30 0.1 2.5  0.3  1.8 -0.101   -1.358
    ## 
    ## $`Shapiro-Wilk's Normality Test`
    ##       Variable Statistic   p-value Normality
    ## 1 sepal_length    0.9761    0.0102    NO    
    ## 2 sepal_width     0.9849    0.1012    YES   
    ## 3 petal_length    0.8763    0.0000    NO    
    ## 4 petal_width     0.9018    0.0000    NO

Among the four variables, two petal-related variables are highly
unevenly distributed, as plotted in the histograms below:

![](Report_5_files/figure-markdown_strict/unnamed-chunk-5-1.png)<!-- -->

###### 4. Multivariate normality

Mardia’s Multivariate Normality Test as included in the MVN package is
used. Similarly, the multivariate normality test is not passed as well.

    ##    Mardia's Multivariate Normality Test 
    ## --------------------------------------- 
    ##    data : output[, 2:5] 
    ## 
    ##    g1p            : 2.69722 
    ##    chi.skew       : 67.43051 
    ##    p.value.skew   : 4.757998e-07 
    ## 
    ##    g2p            : 23.73966 
    ##    z.kurtosis     : -0.2301121 
    ##    p.value.kurt   : 0.8180047 
    ## 
    ##    chi.small.skew : 69.33009 
    ##    p.value.small  : 2.342108e-07 
    ## 
    ##    Result          : Data are not multivariate normal. 
    ## ---------------------------------------

#### Transformed dataset and assumption test

Two types of transformation were done to address to the problems
identified above. First, to mitigate violation to the univariate
normality, transformation was conducted to both petal-related variables
using **the absolute value of the difference between observation and the
mean value of the variable**, because of the bimodality in both
variables. For the two sepal variables, **square root** is applied.

    output$petal_length_tran <- abs(output$petal_length - mean(output$petal_length))

Moreover, after the first transformation, Box-Cox power transformation
is applied to the transformed variables. The three sets of variables
will be compared in the later sections.

    ## bcPower Transformations to Multinormality 
    ## 
    ##                    Est.Power Std.Err. Wald Lower Bound Wald Upper Bound
    ## sepal_length_trans    0.6997   0.8705          -1.0064           2.4059
    ## sepal_width_trans     1.3020   0.7400          -0.1484           2.7525
    ## petal_length_trans    0.8423   0.0710           0.7030           0.9815
    ## petal_width_trans     0.7109   0.0550           0.6032           0.8186
    ## 
    ## Likelihood ratio tests about transformation parameters
    ##                                           LRT df         pval
    ## LR test, lambda = (0 0 0 0)       478.8533492  4 0.000000e+00
    ## LR test, lambda = (1 1 1 1)        24.3582817  4 6.769451e-05
    ## LR test, lambda = (1 1 0.84 0.71)   0.2777058  4 9.912075e-01

    ##                                         LRT df      pval
    ## LR test, lambda = (1 1 0.84 0.71) 0.2827988  4 0.9908973

And then, the assumption tests were conducted again using the 2 sets of
new variables.

###### 1. Outliers

Similar with the original data, no outlier is identified in the dataset.

![](Report_5_files/figure-markdown_strict/unnamed-chunk-10-1.png)<!-- -->![](Report_5_files/figure-markdown_strict/unnamed-chunk-10-2.png)<!-- -->

###### 2. Homogeneity of variance

However, even after both transformations, the variance still cannot pass
the homogeneity test.

    ## 
    ## Likelihood ratio test for equality of variances 
    ## 
    ## data: output 
    ## LRT statistic = 902.4816, df = 3, p-value = 0
    ## alternative hypothesis: true variances are not equal.
    ## 
    ## sample estimate:
    ##                    sepal_length_trans sepal_width_trans petal_length_trans
    ## sepal_length_trans  0.028905005                                           
    ## sepal_width_trans  -0.002495366        0.015408241                        
    ## petal_length_trans -0.014388366        0.061318098       0.656853206      
    ## petal_width_trans  -0.008796273        0.027164911       0.262571384      
    ##                    petal_width_trans
    ## sepal_length_trans                  
    ## sepal_width_trans                   
    ## petal_length_trans                  
    ## petal_width_trans   0.145035332

    ## 
    ## Likelihood ratio test for equality of variances 
    ## 
    ## data: output 
    ## LRT statistic = 805.4957, df = 3, p-value = 0
    ## alternative hypothesis: true variances are not equal.
    ## 
    ## sample estimate:
    ##                 sepal_length_bt sepal_width_bt petal_length_bt
    ## sepal_length_bt  0.68337403                                   
    ## sepal_width_bt  -0.04356672      0.18887060                   
    ## petal_length_bt  1.05000681     -0.28252629     2.15158351    
    ## petal_width_bt   0.52488776     -0.13730683     1.10745469    
    ##                 petal_width_bt
    ## sepal_length_bt               
    ## sepal_width_bt                
    ## petal_length_bt               
    ## petal_width_bt   0.60492265

###### 3. Univariate normality

Similarly, the univariate normality test was also not passed by both
sets of variables, even though the latter set got better results.

    ## $`Descriptive Statistics`
    ##                      n  Mean Std.Dev Median   Min   Max  25th  75th   Skew
    ## petal_length_tran  150 1.563   0.811  1.792 0.042 3.142 0.842 2.258 -0.278
    ## sepal_length_trans 150 2.411   0.171  2.408 2.074 2.811 2.258 2.530  0.174
    ## sepal_width_trans  150 1.744   0.124  1.732 1.414 2.098 1.673 1.817  0.096
    ## petal_length_trans 150 1.563   0.811  1.792 0.042 3.142 0.842 2.258 -0.278
    ##                    Kurtosis
    ## petal_length_tran    -1.236
    ## sepal_length_trans   -0.718
    ## sepal_width_trans     0.028
    ## petal_length_trans   -1.236
    ## 
    ## $`Shapiro-Wilk's Normality Test`
    ##             Variable Statistic   p-value Normality
    ## 1 petal_length_tran     0.9304    0.0000    NO    
    ## 2 sepal_length_trans    0.9806    0.0328    NO    
    ## 3 sepal_width_trans     0.9896    0.3355    YES   
    ## 4 petal_length_trans    0.9304    0.0000    NO

    ## $`Descriptive Statistics`
    ##                     n  Mean Std.Dev Median   Min   Max  25th  75th   Skew
    ## petal_width_trans 150 0.658   0.381  0.799 0.001 1.301 0.301 0.999 -0.287
    ## sepal_length_bt   150 4.843   0.828  4.800 3.300 6.900 4.100 5.400  0.309
    ## sepal_width_bt    150 2.057   0.436  2.000 1.000 3.400 1.800 2.300  0.313
    ## petal_length_bt   150 2.373   1.467  2.908 0.000 4.853 0.577 3.496 -0.327
    ##                   Kurtosis
    ## petal_width_trans   -1.379
    ## sepal_length_bt     -0.606
    ## sepal_width_bt       0.139
    ## petal_length_bt     -1.436
    ## 
    ## $`Shapiro-Wilk's Normality Test`
    ##            Variable Statistic   p-value Normality
    ## 1 petal_width_trans    0.9011    0.0000    NO    
    ## 2  sepal_length_bt     0.9761    0.0102    NO    
    ## 3  sepal_width_bt      0.9849    0.1012    YES   
    ## 4  petal_length_bt     0.8677    0.0000    NO

###### 4. Multivariate normality

Last, Mardia’s Multivariate Normality Test was also not met by both sets
of variables.

    ##    Mardia's Multivariate Normality Test 
    ## --------------------------------------- 
    ##    data : output[, 2:5] 
    ## 
    ##    g1p            : 2.69722 
    ##    chi.skew       : 67.43051 
    ##    p.value.skew   : 4.757998e-07 
    ## 
    ##    g2p            : 23.73966 
    ##    z.kurtosis     : -0.2301121 
    ##    p.value.kurt   : 0.8180047 
    ## 
    ##    chi.small.skew : 69.33009 
    ##    p.value.small  : 2.342108e-07 
    ## 
    ##    Result          : Data are not multivariate normal. 
    ## ---------------------------------------

    ##    Mardia's Multivariate Normality Test 
    ## --------------------------------------- 
    ##    data : output[, 2:5] 
    ## 
    ##    g1p            : 2.69722 
    ##    chi.skew       : 67.43051 
    ##    p.value.skew   : 4.757998e-07 
    ## 
    ##    g2p            : 23.73966 
    ##    z.kurtosis     : -0.2301121 
    ##    p.value.kurt   : 0.8180047 
    ## 
    ##    chi.small.skew : 69.33009 
    ##    p.value.small  : 2.342108e-07 
    ## 
    ##    Result          : Data are not multivariate normal. 
    ## ---------------------------------------

So even though eventually the dataset failed to pass the assumption
test, the descriminant analysis was still conducted over the three sets
of variables to compare the results. Even though there is a high risk
that the results might not be accurate.

#### Results

Function "lda" was used in the "MASS" package to establish the linear
discriminant analysis model. Two similar models were established using
the two transformed set of variables.

    lda.model_1 <- lda(species ~ sepal_length + sepal_width + petal_length + petal_width, 
                       data = output, CV = T)

Below is the summary information of model 1. It shows the coefficients
of the four variables on the two linear discriminants. It also suggests
that the first linear discriminant can explain the majority of the
variance in the data.

    ## $class
    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ##  [36] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ##  [71] 3 2 2 2 2 2 2 2 2 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 3 3 3 3
    ## [106] 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 2 3 3 3 3 3 3
    ## [141] 3 3 3 3 3 3 3 3 3 3
    ## Levels: 1 2 3
    ## 
    ## $posterior
    ##                1            2            3
    ## 1   1.000000e+00 5.087494e-22 4.385241e-42
    ## 2   1.000000e+00 9.588256e-18 8.888069e-37
    ## 3   1.000000e+00 1.983745e-19 8.606982e-39
    ## 4   1.000000e+00 1.505573e-16 5.101765e-35
    ## 5   1.000000e+00 2.075670e-22 1.739832e-42
    ## 6   1.000000e+00 5.332271e-21 8.674906e-40
    ## 7   1.000000e+00 1.498839e-18 3.999205e-37
    ## 8   1.000000e+00 5.268133e-20 1.983027e-39
    ## 9   1.000000e+00 2.280729e-15 1.293376e-33
    ## 10  1.000000e+00 1.504085e-18 5.037348e-38
    ## 11  1.000000e+00 1.296140e-23 4.023338e-44
    ## 12  1.000000e+00 2.171874e-18 3.223111e-37
    ## 13  1.000000e+00 1.996136e-18 6.109118e-38
    ## 14  1.000000e+00 1.604055e-19 2.549802e-39
    ## 15  1.000000e+00 2.843397e-31 1.593594e-54
    ## 16  1.000000e+00 2.330545e-28 3.074132e-49
    ## 17  1.000000e+00 5.136116e-25 3.269819e-45
    ## 18  1.000000e+00 5.747697e-21 2.253825e-40
    ## 19  1.000000e+00 2.187125e-22 4.069438e-42
    ## 20  1.000000e+00 3.297882e-22 9.802494e-42
    ## 21  1.000000e+00 1.757286e-19 8.150916e-39
    ## 22  1.000000e+00 2.027767e-20 3.730752e-39
    ## 23  1.000000e+00 5.650696e-25 6.509776e-46
    ## 24  1.000000e+00 8.618517e-15 7.014744e-32
    ## 25  1.000000e+00 1.520334e-15 1.857885e-33
    ## 26  1.000000e+00 2.936141e-16 8.159510e-35
    ## 27  1.000000e+00 4.557392e-17 5.510803e-35
    ## 28  1.000000e+00 2.079675e-21 2.831513e-41
    ## 29  1.000000e+00 1.232321e-21 1.082692e-41
    ## 30  1.000000e+00 1.153050e-16 4.267126e-35
    ## 31  1.000000e+00 2.584595e-16 9.537258e-35
    ## 32  1.000000e+00 2.878754e-19 5.473623e-38
    ## 33  1.000000e+00 2.247070e-27 4.047137e-49
    ## 34  1.000000e+00 2.620949e-29 1.970538e-51
    ## 35  1.000000e+00 1.493279e-17 2.047516e-36
    ## 36  1.000000e+00 2.146308e-21 1.550216e-41
    ## 37  1.000000e+00 1.673983e-24 1.322398e-45
    ## 38  1.000000e+00 3.810942e-23 9.131835e-44
    ## 39  1.000000e+00 5.423320e-17 1.146137e-35
    ## 40  1.000000e+00 2.414191e-20 6.552342e-40
    ## 41  1.000000e+00 1.417602e-21 3.569675e-41
    ## 42  1.000000e+00 8.956712e-11 4.968454e-28
    ## 43  1.000000e+00 2.125837e-18 2.395462e-37
    ## 44  1.000000e+00 1.101293e-15 1.403899e-32
    ## 45  1.000000e+00 2.285363e-17 5.214629e-35
    ## 46  1.000000e+00 2.087086e-16 1.027948e-34
    ## 47  1.000000e+00 2.588201e-22 3.634491e-42
    ## 48  1.000000e+00 3.643000e-18 4.504970e-37
    ## 49  1.000000e+00 3.000767e-23 1.346233e-43
    ## 50  1.000000e+00 3.171862e-20 7.860312e-40
    ## 51  3.157725e-18 9.998716e-01 1.284247e-04
    ## 52  1.753919e-19 9.991816e-01 8.184018e-04
    ## 53  2.551962e-22 9.951044e-01 4.895626e-03
    ## 54  2.742687e-22 9.995996e-01 4.004477e-04
    ## 55  4.854978e-23 9.951404e-01 4.859638e-03
    ## 56  9.575747e-23 9.982973e-01 1.702702e-03
    ## 57  4.467689e-22 9.838631e-01 1.613691e-02
    ## 58  5.922943e-14 9.999999e-01 8.584221e-08
    ## 59  8.088509e-20 9.998655e-01 1.344590e-04
    ## 60  1.767441e-20 9.994314e-01 5.686054e-04
    ## 61  3.330661e-18 9.999987e-01 1.314516e-06
    ## 62  8.331100e-20 9.991631e-01 8.369389e-04
    ## 63  4.614428e-18 9.999989e-01 1.117671e-06
    ## 64  1.290071e-23 9.939163e-01 6.083745e-03
    ## 65  5.229707e-14 9.999984e-01 1.593028e-06
    ## 66  3.393529e-17 9.999528e-01 4.721492e-05
    ## 67  7.983370e-24 9.763990e-01 2.360097e-02
    ## 68  3.119288e-16 9.999991e-01 8.659241e-07
    ## 69  3.847473e-28 9.390462e-01 6.095377e-02
    ## 70  1.678698e-17 9.999966e-01 3.360127e-06
    ## 71  1.302246e-28 1.772727e-01 8.227273e-01
    ## 72  1.113263e-16 9.999902e-01 9.801197e-06
    ## 73  1.634947e-29 7.868347e-01 2.131653e-01
    ## 74  3.331093e-22 9.995073e-01 4.926830e-04
    ## 75  1.013127e-17 9.999741e-01 2.594176e-05
    ## 76  2.949236e-18 9.999081e-01 9.193549e-05
    ## 77  7.224891e-23 9.979459e-01 2.054146e-03
    ## 78  2.386376e-27 6.569495e-01 3.430505e-01
    ## 79  4.473658e-23 9.922840e-01 7.716012e-03
    ## 80  7.145460e-12 1.000000e+00 1.241414e-08
    ## 81  1.333306e-17 9.999970e-01 3.044209e-06
    ## 82  1.119894e-15 9.999997e-01 2.916503e-07
    ## 83  1.748156e-16 9.999961e-01 3.876682e-06
    ## 84  1.125494e-33 9.924153e-02 9.007585e-01
    ## 85  1.191672e-24 9.474667e-01 5.253333e-02
    ## 86  1.983291e-20 9.924721e-01 7.527887e-03
    ## 87  4.531906e-21 9.980100e-01 1.989996e-03
    ## 88  2.035626e-23 9.993358e-01 6.642410e-04
    ## 89  7.813451e-18 9.999440e-01 5.603286e-05
    ## 90  8.212308e-21 9.998033e-01 1.967487e-04
    ## 91  6.631189e-23 9.992802e-01 7.197827e-04
    ## 92  7.049062e-22 9.979525e-01 2.047473e-03
    ## 93  4.490728e-18 9.999881e-01 1.188058e-05
    ## 94  2.600275e-14 9.999999e-01 8.745690e-08
    ## 95  6.422939e-21 9.996751e-01 3.248823e-04
    ## 96  2.159263e-17 9.999804e-01 1.956029e-05
    ## 97  3.823305e-19 9.998801e-01 1.199041e-04
    ## 98  2.089502e-18 9.999504e-01 4.963639e-05
    ## 99  9.013113e-11 1.000000e+00 9.943306e-09
    ## 100 6.167377e-19 9.999219e-01 7.813051e-05
    ## 101 1.335977e-53 3.188548e-09 1.000000e+00
    ## 102 9.949508e-38 1.209398e-03 9.987906e-01
    ## 103 1.950796e-42 2.774428e-05 9.999723e-01
    ## 104 3.081602e-38 1.232592e-03 9.987674e-01
    ## 105 5.411117e-46 1.807449e-06 9.999982e-01
    ## 106 5.887455e-50 5.662591e-07 9.999994e-01
    ## 107 1.203272e-32 8.794800e-02 9.120520e-01
    ## 108 1.774038e-42 1.735541e-04 9.998264e-01
    ## 109 1.924345e-42 2.617818e-04 9.997382e-01
    ## 110 1.851248e-46 1.352651e-07 9.999999e-01
    ## 111 4.379051e-32 1.446014e-02 9.855399e-01
    ## 112 2.052671e-37 1.776421e-03 9.982236e-01
    ## 113 9.704392e-39 2.172029e-04 9.997828e-01
    ## 114 2.386650e-40 2.251253e-04 9.997749e-01
    ## 115 8.048237e-46 8.410965e-07 9.999992e-01
    ## 116 1.008588e-39 2.840103e-05 9.999716e-01
    ## 117 2.811294e-35 6.595206e-03 9.934048e-01
    ## 118 7.282186e-45 1.296566e-06 9.999987e-01
    ## 119 1.004644e-64 2.647509e-10 1.000000e+00
    ## 120 3.160887e-33 3.033047e-01 6.966953e-01
    ## 121 1.719583e-42 6.688965e-06 9.999933e-01
    ## 122 6.252717e-37 9.870164e-04 9.990130e-01
    ## 123 2.627103e-51 7.704580e-07 9.999992e-01
    ## 124 1.504499e-31 1.070121e-01 8.929879e-01
    ## 125 3.688147e-39 9.571422e-05 9.999043e-01
    ## 126 2.426533e-36 3.398007e-03 9.966020e-01
    ## 127 3.865436e-30 2.055755e-01 7.944245e-01
    ## 128 3.606381e-30 1.437670e-01 8.562330e-01
    ## 129 8.371636e-44 1.376281e-05 9.999862e-01
    ## 130 2.937738e-32 1.589920e-01 8.410080e-01
    ## 131 6.294581e-42 1.714027e-04 9.998286e-01
    ## 132 5.466934e-36 7.736441e-04 9.992264e-01
    ## 133 1.208158e-45 3.051435e-06 9.999969e-01
    ## 134 5.464475e-29 7.876238e-01 2.123762e-01
    ## 135 9.884011e-35 1.578198e-01 8.421802e-01
    ## 136 6.515088e-46 1.990735e-06 9.999980e-01
    ## 137 2.840394e-44 7.895048e-07 9.999992e-01
    ## 138 7.160822e-35 7.053731e-03 9.929463e-01
    ## 139 1.782247e-29 2.122042e-01 7.877958e-01
    ## 140 3.640914e-36 9.289807e-04 9.990710e-01
    ## 141 5.881132e-45 1.108009e-06 9.999989e-01
    ## 142 2.122304e-35 6.157433e-04 9.993843e-01
    ## 143 9.949508e-38 1.209398e-03 9.987906e-01
    ## 144 9.585800e-46 9.978596e-07 9.999990e-01
    ## 145 2.206003e-46 2.038879e-07 9.999998e-01
    ## 146 1.133074e-38 8.851900e-05 9.999115e-01
    ## 147 8.781586e-36 7.084468e-03 9.929155e-01
    ## 148 7.108984e-35 3.342993e-03 9.966570e-01
    ## 149 3.096565e-40 1.338572e-05 9.999866e-01
    ## 150 3.585667e-33 2.058806e-02 9.794119e-01
    ## 
    ## $terms
    ## species ~ sepal_length + sepal_width + petal_length + petal_width
    ## attr(,"variables")
    ## list(species, sepal_length, sepal_width, petal_length, petal_width)
    ## attr(,"factors")
    ##              sepal_length sepal_width petal_length petal_width
    ## species                 0           0            0           0
    ## sepal_length            1           0            0           0
    ## sepal_width             0           1            0           0
    ## petal_length            0           0            1           0
    ## petal_width             0           0            0           1
    ## attr(,"term.labels")
    ## [1] "sepal_length" "sepal_width"  "petal_length" "petal_width" 
    ## attr(,"order")
    ## [1] 1 1 1 1
    ## attr(,"intercept")
    ## [1] 1
    ## attr(,"response")
    ## [1] 1
    ## attr(,".Environment")
    ## <environment: R_GlobalEnv>
    ## attr(,"predvars")
    ## list(species, sepal_length, sepal_width, petal_length, petal_width)
    ## attr(,"dataClasses")
    ##      species sepal_length  sepal_width petal_length  petal_width 
    ##     "factor"    "numeric"    "numeric"    "numeric"    "numeric" 
    ## 
    ## $call
    ## lda(formula = species ~ sepal_length + sepal_width + petal_length + 
    ##     petal_width, data = output, CV = T)
    ## 
    ## $xlevels
    ## named list()

Below is the scatterplot of the original dataset on the two linear
discriminants.

    ##         1         2         3         4         5         6         7 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##         8         9        10        11        12        13        14 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        15        16        17        18        19        20        21 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        22        23        24        25        26        27        28 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        29        30        31        32        33        34        35 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        36        37        38        39        40        41        42 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        43        44        45        46        47        48        49 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        50        51        52        53        54        55        56 
    ## 1.0000000 0.9998894 0.9992575 0.9958069 0.9996423 0.9955903 0.9985020 
    ##        57        58        59        60        61        62        63 
    ## 0.9858346 0.9999999 0.9998781 0.9995027 0.9999986 0.9992294 0.9999988 
    ##        64        65        66        67        68        69        70 
    ## 0.9943267 0.9999984 0.9999573 0.9806471 0.9999991 0.9595735 0.9999967 
    ##        71        72        73        74        75        76        77 
    ## 0.7467718 0.9999907 0.8155328 0.9995723 0.9999758 0.9999171 0.9982541 
    ##        78        79        80        81        82        83        84 
    ## 0.6892131 0.9925169 1.0000000 0.9999970 0.9999997 0.9999962 0.8566081 
    ##        85        86        87        88        89        90        91 
    ## 0.9635576 0.9940401 0.9982223 0.9994557 0.9999486 0.9998183 0.9993856 
    ##        92        93        94        95        96        97        98 
    ## 0.9980934 0.9999887 0.9999999 0.9996980 0.9999817 0.9998892 0.9999536 
    ##        99       100       101       102       103       104       105 
    ## 1.0000000 0.9999269 1.0000000 0.9989217 0.9999741 0.9989319 0.9999982 
    ##       106       107       108       109       110       111       112 
    ## 0.9999993 0.9513797 0.9998605 0.9997765 0.9999998 0.9869465 0.9983261 
    ##       113       114       115       116       117       118       119 
    ## 0.9997994 0.9998051 0.9999990 0.9999739 0.9939164 0.9999985 1.0000000 
    ##       120       121       122       123       124       125       126 
    ## 0.7792010 0.9999935 0.9991727 0.9999990 0.9028806 0.9999116 0.9973207 
    ##       127       128       129       130       131       132       133 
    ## 0.8116325 0.8657569 0.9999870 0.8963177 0.9998558 0.9994802 0.9999970 
    ##       134       135       136       137       138       139       140 
    ## 0.7293881 0.9339775 0.9999978 0.9999991 0.9938344 0.8074738 0.9991709 
    ##       141       142       143       144       145       146       147 
    ## 0.9999988 0.9995724 0.9989217 0.9999990 0.9999997 0.9999253 0.9941012 
    ##       148       149       150 
    ## 0.9968541 0.9999874 0.9824577

    ##         1         2         3         4         5         6         7 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##         8         9        10        11        12        13        14 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        15        16        17        18        19        20        21 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 0.9999575 1.0000000 0.9999380 
    ##        22        23        24        25        26        27        28 
    ## 1.0000000 1.0000000 0.9999999 1.0000000 0.9999988 1.0000000 1.0000000 
    ##        29        30        31        32        33        34        35 
    ## 1.0000000 1.0000000 1.0000000 0.9999995 1.0000000 1.0000000 1.0000000 
    ##        36        37        38        39        40        41        42 
    ## 1.0000000 0.9999999 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        43        44        45        46        47        48        49 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        50        51        52        53        54        55        56 
    ## 1.0000000 0.9995655 0.9990207 0.9903194 0.9999742 0.9966978 0.9997454 
    ##        57        58        59        60        61        62        63 
    ## 0.9841015 0.9990794 0.9998650 0.9999592 0.9995685 0.9996754 0.9999328 
    ##        64        65        66        67        68        69        70 
    ## 0.9978382 0.9999956 0.9999072 0.9961890 0.9999296 0.9929292 0.9999923 
    ##        71        72        73        74        75        76        77 
    ## 0.5360497 0.9999952 0.9462561 0.9998602 0.9999781 0.9998765 0.9978824 
    ##        78        79        80        81        82        83        84 
    ## 0.6512070 0.9973489 0.9999464 0.9999947 0.9999747 0.9999990 0.5003332 
    ##        85        86        87        88        89        90        91 
    ## 0.9950063 0.9949022 0.9968669 0.9998578 0.9999868 0.9999821 0.9999449 
    ##        92        93        94        95        96        97        98 
    ## 0.9990787 0.9999977 0.9990405 0.9999564 0.9999946 0.9999728 0.9999718 
    ##        99       100       101       102       103       104       105 
    ## 0.9989319 0.9999838 0.9997536 0.9902696 0.9999818 0.9964257 0.9999959 
    ##       106       107       108       109       110       111       112 
    ## 0.9999997 0.6965835 0.9999024 0.9992532 1.0000000 0.9873005 0.9946645 
    ##       113       114       115       116       117       118       119 
    ## 0.9997998 0.9971880 0.9999919 0.9999636 0.9892772 0.9999942 1.0000000 
    ##       120       121       122       123       124       125       126 
    ## 0.7755223 0.9999957 0.9917879 0.9999995 0.7581668 0.9999280 0.9988860 
    ##       127       128       129       130       131       132       133 
    ## 0.6010725 0.7100011 0.9999561 0.9444469 0.9999139 0.9999784 0.9999897 
    ##       134       135       136       137       138       139       140 
    ## 0.8819523 0.6415832 0.9999995 0.9999962 0.9888810 0.5830679 0.9994705 
    ##       141       142       143       144       145       146       147 
    ## 0.9999987 0.9997713 0.9902696 0.9999990 0.9999998 0.9999235 0.9743459 
    ##       148       149       150 
    ## 0.9953409 0.9999794 0.9265025

    ##         1         2         3         4         5         6         7 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##         8         9        10        11        12        13        14 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        15        16        17        18        19        20        21 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        22        23        24        25        26        27        28 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        29        30        31        32        33        34        35 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        36        37        38        39        40        41        42 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        43        44        45        46        47        48        49 
    ## 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000 
    ##        50        51        52        53        54        55        56 
    ## 1.0000000 0.9997153 0.9984829 0.9912248 0.9993094 0.9918236 0.9962963 
    ##        57        58        59        60        61        62        63 
    ## 0.9756110 0.9999998 0.9996724 0.9991198 0.9999973 0.9986185 0.9999969 
    ##        64        65        66        67        68        69        70 
    ## 0.9872032 0.9999967 0.9998983 0.9645254 0.9999973 0.9392070 0.9999918 
    ##        71        72        73        74        75        76        77 
    ## 0.7536759 0.9999787 0.7185302 0.9987513 0.9999383 0.9998088 0.9959954 
    ##        78        79        80        81        82        83        84 
    ## 0.6277134 0.9862651 1.0000000 0.9999930 0.9999992 0.9999909 0.8978322 
    ##        85        86        87        88        89        90        91 
    ## 0.9347282 0.9897537 0.9963445 0.9988102 0.9998758 0.9996292 0.9984081 
    ##        92        93        94        95        96        97        98 
    ## 0.9955992 0.9999728 0.9999998 0.9993125 0.9999486 0.9997321 0.9998828 
    ##        99       100       101       102       103       104       105 
    ## 1.0000000 0.9998321 0.9999999 0.9984411 0.9999362 0.9988670 0.9999938 
    ##       106       107       108       109       110       111       112 
    ## 0.9999981 0.9521935 0.9998377 0.9997322 0.9999985 0.9789768 0.9976282 
    ##       113       114       115       116       117       118       119 
    ## 0.9995285 0.9996017 0.9999931 0.9998884 0.9938542 0.9999951 1.0000000 
    ##       120       121       122       123       124       125       126 
    ## 0.8474864 0.9999718 0.9984346 0.9999978 0.8957798 0.9998030 0.9973165 
    ##       127       128       129       130       131       132       133 
    ## 0.8023334 0.8651894 0.9999666 0.9318329 0.9997863 0.9991798 0.9999894 
    ##       134       135       136       137       138       139       140 
    ## 0.5886112 0.9665035 0.9999897 0.9999947 0.9939119 0.8053242 0.9981333 
    ##       141       142       143       144       145       146       147 
    ## 0.9999925 0.9981783 0.9984411 0.9999953 0.9999978 0.9996683 0.9912219 
    ##       148       149       150 
    ## 0.9945982 0.9999482 0.9822313

![](Report_5_files/figure-markdown_strict/unnamed-chunk-17-1.png)<!-- -->

Another way to look at the distance between the three categories is
through histogram on the linear discriminant. Below is the historgram of
the three categories on the two discriminants base on model 1.

![](Report_5_files/figure-markdown_strict/unnamed-chunk-18-1.png)<!-- -->![](Report_5_files/figure-markdown_strict/unnamed-chunk-18-2.png)<!-- -->

Quite similar with the visual clue, model 1 and model 3 have the same
accuracy rate, which is higher than that of model 2.

    ## 
    ## ------------------
    ##  Model   Accuracy 
    ## ------- ----------
    ## model 1    0.98   
    ## 
    ## model 2    0.96   
    ## 
    ## model 3    0.98   
    ## ------------------
    ## 
    ## Table: Table of accuracy rate comparison

Conclusions
-----------

It can be concluded that even though the dataset might not be
appropriate for discriminant analysis, the original model is quite
accurate. And transforming the data doesn't help to increase the
performance of the model.

And group 2 and group 3 are more similar with each other than with group
1.
