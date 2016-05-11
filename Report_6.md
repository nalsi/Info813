Info813 Report 6: logit choice model
====================================

Problem statement
-----------------

Data description
----------------

The dataset is about the choices of contraception measures by married
women in Indonesia and other social profiles of them and their families.
It is a subset of the 1987 National Indonesia Contraceptive Prevalence
Survey, when the data were collected through interview method. The
dataset is available in [Machine Learning Repository at University of
California,
Irvine](http://archive.ics.uci.edu/ml/datasets/Contraceptive+Method+Choice).
(Lichman, 2013)

The dataset is composed of 10 variables of 1,473 observations. The 10
variables include:

<table style="width:78%;">
<colgroup>
<col width="13%" />
<col width="30%" />
<col width="20%" />
<col width="12%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Variable</th>
<th align="left">name in the dataset</th>
<th align="left">Type of data</th>
<th align="left">Values</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">1. Wife's age</td>
<td align="left">age</td>
<td align="left">numerical</td>
</tr>
<tr class="even">
<td align="left">2. Wife's education</td>
<td align="left">education_w</td>
<td align="left">categorical</td>
<td align="left">1=low, 2, 3, 4=high</td>
</tr>
<tr class="odd">
<td align="left">3. Husband's education</td>
<td align="left">education_h</td>
<td align="left">categorical</td>
<td align="left">1=low, 2, 3, 4=high</td>
</tr>
<tr class="even">
<td align="left">4. Number of children ever born</td>
<td align="left">child</td>
<td align="left">numerical</td>
</tr>
<tr class="odd">
<td align="left">5. Wife's religion</td>
<td align="left">religion</td>
<td align="left">categorical</td>
<td align="left">0=Non-Islam, 1=Islam</td>
</tr>
<tr class="even">
<td align="left">6. Wife's now working?</td>
<td align="left">working</td>
<td align="left">categorical</td>
<td align="left">0=Yes, 1=No</td>
</tr>
<tr class="odd">
<td align="left">7. Husband's occupation</td>
<td align="left">occupation_h</td>
<td align="left">categorical</td>
<td align="left">1, 2, 3, 4</td>
</tr>
<tr class="even">
<td align="left">8. Standard-of-living index</td>
<td align="left">SII</td>
<td align="left">categorical</td>
<td align="left">1=low, 2, 3, 4=high</td>
</tr>
<tr class="odd">
<td align="left">9. Media exposure</td>
<td align="left">media</td>
<td align="left">categorical</td>
<td align="left">0=Good, 1=Not good</td>
</tr>
<tr class="even">
<td align="left"><strong>10. Contraceptive method used</strong></td>
<td align="left">method</td>
<td align="left">categorical</td>
<td align="left">1=No-use, 2=Long-term, 3=Short-term</td>
</tr>
</tbody>
</table>

Below is the head of this dataset.

    head(output)

    ##   age education_w education_h child religion working occupation_h SII
    ## 1  24           2           3     3        1       1            2   3
    ## 2  45           1           3    10        1       1            3   4
    ## 3  43           2           3     7        1       1            3   4
    ## 4  42           3           2     9        1       1            3   3
    ## 5  36           3           3     8        1       1            3   2
    ## 6  19           4           4     0        1       1            3   3
    ##   media method
    ## 1     0      1
    ## 2     0      1
    ## 3     0      1
    ## 4     0      1
    ## 5     0      1
    ## 6     0      1

Ane below is a summary of the dataset by the dependent variable.

    describeBy(output, group = output$method)

    ## group: 1
    ##              vars   n  mean   sd median trimmed   mad min max range  skew
    ## age             1 629 33.42 9.12     32   33.29 11.86  16  49    33  0.15
    ## education_w     2 629  2.67 1.05      3    2.71  1.48   1   4     3 -0.16
    ## education_h     3 629  3.28 0.90      4    3.41  0.00   1   4     3 -0.98
    ## child           4 629  2.93 2.66      2    2.58  1.48   0  12    12  1.13
    ## religion        5 629  0.88 0.32      1    0.97  0.00   0   1     1 -2.34
    ## working         6 629  0.73 0.44      1    0.79  0.00   0   1     1 -1.03
    ## occupation_h    7 629  2.20 0.84      2    2.22  1.48   1   4     3 -0.18
    ## SII             8 629  2.95 1.04      3    3.07  1.48   1   4     3 -0.58
    ## media           9 629  0.12 0.32      0    0.02  0.00   0   1     1  2.37
    ## method         10 629  1.00 0.00      1    1.00  0.00   1   1     0   NaN
    ##              kurtosis   se
    ## age             -1.24 0.36
    ## education_w     -1.20 0.04
    ## education_h     -0.13 0.04
    ## child            0.80 0.11
    ## religion         3.50 0.01
    ## working         -0.94 0.02
    ## occupation_h    -1.18 0.03
    ## SII             -0.91 0.04
    ## media            3.61 0.01
    ## method            NaN 0.00
    ## -------------------------------------------------------- 
    ## group: 2
    ##              vars   n  mean   sd median trimmed  mad min max range  skew
    ## age             1 333 34.38 7.45     35   34.41 8.90  17  49    32 -0.04
    ## education_w     2 333  3.46 0.80      4    3.60 0.00   1   4     3 -1.32
    ## education_h     3 333  3.66 0.71      4    3.84 0.00   1   4     3 -2.27
    ## child           4 333  3.74 2.10      3    3.54 1.48   1  13    12  0.95
    ## religion        5 333  0.77 0.42      1    0.84 0.00   0   1     1 -1.29
    ## working         6 333  0.73 0.44      1    0.79 0.00   0   1     1 -1.05
    ## occupation_h    7 333  1.84 0.89      2    1.78 1.48   1   4     3  0.44
    ## SII             8 333  3.47 0.77      4    3.62 0.00   1   4     3 -1.37
    ## media           9 333  0.03 0.17      0    0.00 0.00   0   1     1  5.48
    ## method         10 333  2.00 0.00      2    2.00 0.00   2   2     0   NaN
    ##              kurtosis   se
    ## age             -0.89 0.41
    ## education_w      0.85 0.04
    ## education_h      4.64 0.04
    ## child            1.18 0.12
    ## religion        -0.34 0.02
    ## working         -0.91 0.02
    ## occupation_h    -1.27 0.05
    ## SII              1.22 0.04
    ## media           28.14 0.01
    ## method            NaN 0.00
    ## -------------------------------------------------------- 
    ## group: 3
    ##              vars   n  mean   sd median trimmed  mad min max range  skew
    ## age             1 511 30.24 6.94     29   29.84 7.41  16  49    33  0.45
    ## education_w     2 511  2.99 0.97      3    3.08 1.48   1   4     3 -0.50
    ## education_h     3 511  3.46 0.73      4    3.58 0.00   1   4     3 -1.04
    ## child           4 511  3.35 2.05      3    3.11 1.48   0  16    16  1.49
    ## religion        5 511  0.86 0.34      1    0.96 0.00   0   1     1 -2.13
    ## working         6 511  0.78 0.41      1    0.86 0.00   0   1     1 -1.38
    ## occupation_h    7 511  2.25 0.84      2    2.30 1.48   1   4     3 -0.33
    ## SII             8 511  3.14 0.95      3    3.27 1.48   1   4     3 -0.82
    ## media           9 511  0.05 0.22      0    0.00 0.00   0   1     1  4.17
    ## method         10 511  3.00 0.00      3    3.00 0.00   3   3     0   NaN
    ##              kurtosis   se
    ## age             -0.39 0.31
    ## education_w     -0.88 0.04
    ## education_h     -0.03 0.03
    ## child            4.11 0.09
    ## religion         2.54 0.02
    ## working         -0.09 0.02
    ## occupation_h    -1.19 0.04
    ## SII             -0.40 0.04
    ## media           15.42 0.01
    ## method            NaN 0.00

    output[,2] <- as.factor(output[,2])
    output[,3] <- as.factor(output[,3])
    output[,5] <- as.factor(output[,5])
    output[,6] <- as.factor(output[,6])
    output[,7] <- as.factor(output[,7])
    output[,8] <- as.factor(output[,8])
    output[,9] <- as.factor(output[,9])
    output[,10] <- as.factor(output[,10])
    output$child <- as.numeric(output$child)

Research method
---------------

Multinominal logit model is used to finish this report, by using package
*mlogit* (version 0.2-4) of R.

Results
-------

#### Assumptions

The following assumptions are tested for multinominal logistic
regression test:

1.  Independence of observations
2.  No multicollinearity
3.  Independence of irrelevant alternatives

###### Multicollinearity

It seems that the multicollinearity hypothesis is met because the GVIF
(generalized variance inflation factor) values of all the independent
variables in the comprehensive model are lower than 3.

    lm.model <- glm(method ~ age + child + religion + education_w + education_h + working + media,
                    data = output,
                    family=binomial(link="logit"))
    vif(lm.model)

    ##                 GVIF Df GVIF^(1/(2*Df))
    ## age         1.758286  1        1.326004
    ## child       1.785492  1        1.336223
    ## religion    1.109876  1        1.053507
    ## education_w 2.094412  3        1.131124
    ## education_h 1.815890  3        1.104540
    ## working     1.029517  1        1.014651
    ## media       1.174874  1        1.083916

###### Independence of irrelevant alternatives hypothesis

However, the IIA hypothesis is not met using the comprehensive model and
the model based on a subset of the data.

    mldata <- mlogit.data(data = output, 
                          choice = "method", 
                          shape = "wide")

    model <- mlogit(method ~ 1 | age + child + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt")

    model_new <- mlogit(method ~ 1 | age + child + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    alt.subset = c("1", "2"))

    mlogit::hmftest(model, model_new)

    ## 
    ##  Hausman-McFadden test
    ## 
    ## data:  mldata
    ## chisq = -6.9046, df = 12, p-value = 1
    ## alternative hypothesis: IIA is rejected

###### Correlation between independent variables

It is not a part of the assumption test. But correlations between
nominal variables are tested using Cramer's V test (using function
cramersV from package **lsr** version 0.5). The results (some of which
might be problematic) indicate that there might be a pretty strong
correlation between the education levels of wife and husband. But all
the other variables might just have relatively week correlation.

    list <- c(2, 3, 5, 6, 7, 8, 9, 10)
    cramer_table <- data.frame()
    for (i in 1:length(list)) {
      for  (j in 1:length(list)) {
        cramer_table[i, j] <- cramersV(table(output[,list[i]], output[,list[j]]))
        colnames(cramer_table)[i] <- colnames(output)[list[i]]
        rownames(cramer_table)[i] <- colnames(output)[list[i]]
      }
    }

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    ## Warning in chisq.test(...): Chi-squared approximation may be incorrect

    cramer_table <- round(cramer_table, digits = 2)
    cramer_table

    ##              education_w education_h religion working occupation_h  SII
    ## education_w         1.00        0.40     0.24    0.08         0.26 0.22
    ## education_h         0.40        1.00     0.19    0.03         0.24 0.22
    ## religion            0.24        0.19     1.00    0.07         0.12 0.21
    ## working             0.08        0.03     0.07    1.00         0.07 0.09
    ## occupation_h        0.26        0.24     0.12    0.07         1.00 0.18
    ## SII                 0.22        0.22     0.21    0.09         0.18 1.00
    ## media               0.40        0.30     0.06    0.00         0.13 0.26
    ## method              0.22        0.16     0.12    0.06         0.15 0.15
    ##              media method
    ## education_w   0.40   0.22
    ## education_h   0.30   0.16
    ## religion      0.06   0.12
    ## working       0.00   0.06
    ## occupation_h  0.13   0.15
    ## SII           0.26   0.15
    ## media         1.00   0.15
    ## method        0.15   1.00

#### Model performance

The rejection of the IIA hypothesis, and the fact that the three
selections in the dataset can be combined together suggest that a nested
model is a better choice. So a nested logit model is established.
Package mlogit makes the distinction between alternative/individual
variables. There are three sections in the model using this package, the
first and third sections contain alternative variables (with and without
specific coefficient, respectively), and the second section contains
individual variables. In this case, all the independent variables are
individual variables rather than choice variables. The method of this
report is called "multinominal logit model" in the sense that all the
independent variables are individual variables.

    model_1 <- mlogit(method ~ 0 | age + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    nests = list(positive = c("2", "3"), negative = c("1")),,
                    reflevel = "1",
                    un.nest.el = T)

Below is the results of the comprehensive model using Choice 1 (Not
using any contraceptive method) as the reference level. McFadden
R-squared value is 0.07, suggesting the model's relatively poor
predictive power.

The strongest independent variable when comparing choice 1 and choices 2
and 3 is **women's education**, it has an obvious positive impact on the
women's choices, judged by the increasing coefficients when the level of
education increases. However, education doesn't make women more likely
to choose short-term methods. **Media exposure** (which is in a
different order from other independent variables) and **working status**
also have positive impacts on their choices; while **age** has a
negative impacts. (Admittedly, many independent variables should have
correlation with each other, which cannot be tested given the nature of
the data.)

    mldata <- mlogit.data(data = output, 
                          choice = "method", 
                          shape = "wide")

    model_1 <- mlogit(method ~ 0 | age + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    nests = list(positive = c("2", "3"), negative = c("1")),,
                    reflevel = "1",
                    un.nest.el = T)

    summary(model_1)

    ## 
    ## Call:
    ## mlogit(formula = method ~ 0 | age + religion + education_w + 
    ##     education_h + working + media, data = mldata, reflevel = "1", 
    ##     nests = list(positive = c("2", "3"), negative = c("1")), 
    ##     un.nest.el = T, shape = "long", alt.var = "alt")
    ## 
    ## Frequencies of alternatives:
    ##       1       2       3 
    ## 0.42702 0.22607 0.34691 
    ## 
    ## bfgs method
    ## 23 iterations, 0h:0m:1s 
    ## g'(-H)^-1g = -0.0261 
    ## last step couldn't find higher value 
    ## 
    ## Coefficients :
    ##                  Estimate Std. Error t-value  Pr(>|t|)    
    ## 2:(intercept)  -0.2008004  0.5661658 -0.3547  0.722839    
    ## 3:(intercept)  -0.1390056  0.5124064 -0.2713  0.786176    
    ## 2:age          -0.0197842  0.0084389 -2.3444  0.019057 *  
    ## 3:age          -0.0227037  0.0076559 -2.9655  0.003022 ** 
    ## 2:religion1    -0.2195235  0.1628865 -1.3477  0.177752    
    ## 3:religion1    -0.2135037  0.1627038 -1.3122  0.189445    
    ## 2:education_w2  0.3372187  0.2402112  1.4038  0.160366    
    ## 3:education_w2  0.3013928  0.2298170  1.3114  0.189707    
    ## 2:education_w3  0.7448188  0.2663140  2.7968  0.005162 ** 
    ## 3:education_w3  0.6856160  0.2418625  2.8347  0.004586 ** 
    ## 2:education_w4  1.3861230  0.2913012  4.7584 1.951e-06 ***
    ## 3:education_w4  1.3105063  0.2614915  5.0117 5.396e-07 ***
    ## 2:education_h2  0.3736395  0.4121376  0.9066  0.364624    
    ## 3:education_h2  0.4800381  0.4240498  1.1320  0.257621    
    ## 2:education_h3  0.4351547  0.4140776  1.0509  0.293304    
    ## 3:education_h3  0.5518778  0.4231338  1.3043  0.192144    
    ## 2:education_h4  0.2596813  0.4059037  0.6398  0.522328    
    ## 3:education_h4  0.3602980  0.4205118  0.8568  0.391551    
    ## 2:working1      0.2651204  0.1272014  2.0843  0.037137 *  
    ## 3:working1      0.2687451  0.1268525  2.1186  0.034127 *  
    ## 2:media1       -0.5341396  0.2434929 -2.1937  0.028260 *  
    ## 3:media1       -0.5340204  0.2439275 -2.1893  0.028578 *  
    ## iv              0.0407293  0.1139705  0.3574  0.720817    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Log-Likelihood: -1450.7
    ## McFadden R^2:  0.076768 
    ## Likelihood ratio test : chisq = 241.26 (p.value = < 2.22e-16)

Below is the same model using choice 2 (long-term method) as the
reference level. It is shown in the results below that none of the
independent variable actually has significant impact on the women's
choices as compared with choice 3 (short-term method).

    model_1a <- mlogit(method ~ 0 | age + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    nests = list(positive = c("2", "3"), negative = c("1")),
                    reflevel = "2",
                    un.nest.el = T)

    summary(model_1a)

    ## 
    ## Call:
    ## mlogit(formula = method ~ 0 | age + religion + education_w + 
    ##     education_h + working + media, data = mldata, reflevel = "2", 
    ##     nests = list(positive = c("2", "3"), negative = c("1")), 
    ##     un.nest.el = T, shape = "long", alt.var = "alt")
    ## 
    ## Frequencies of alternatives:
    ##       2       1       3 
    ## 0.22607 0.42702 0.34691 
    ## 
    ## bfgs method
    ## 29 iterations, 0h:0m:2s 
    ## g'(-H)^-1g =  11.3 
    ## last step couldn't find higher value 
    ## 
    ## Coefficients :
    ##                   Estimate  Std. Error t-value  Pr(>|t|)    
    ## 1:(intercept)   0.16826717  0.50942930  0.3303  0.741169    
    ## 3:(intercept)   0.03936388  0.03877126  1.0153  0.309970    
    ## 1:age           0.02043839  0.00697686  2.9295  0.003396 ** 
    ## 3:age          -0.00187508  0.00149388 -1.2552  0.209416    
    ## 1:religion1     0.21854911  0.16262129  1.3439  0.178976    
    ## 3:religion1     0.00377425  0.00610888  0.6178  0.536687    
    ## 1:education_w2 -0.32850951  0.22850488 -1.4376  0.150534    
    ## 3:education_w2 -0.02203070  0.02134071 -1.0323  0.301916    
    ## 1:education_w3 -0.72990238  0.23846148 -3.0609  0.002207 ** 
    ## 3:education_w3 -0.03682884  0.03167883 -1.1626  0.245004    
    ## 1:education_w4 -1.36813116  0.25452513 -5.3752 7.648e-08 ***
    ## 3:education_w4 -0.04713668  0.03925026 -1.2009  0.229780    
    ## 1:education_h2 -0.39151905  0.39152050 -1.0000  0.317312    
    ## 3:education_h2  0.06758317  0.05715909  1.1824  0.237059    
    ## 1:education_h3 -0.45524414  0.38622320 -1.1787  0.238515    
    ## 3:education_h3  0.07412860  0.06227870  1.1903  0.233939    
    ## 1:education_h4 -0.27569642  0.38901500 -0.7087  0.478508    
    ## 3:education_h4  0.06383700  0.05452221  1.1708  0.241662    
    ## 1:working1     -0.26620024  0.12693641 -2.0971  0.035983 *  
    ## 3:working1      0.00212293  0.00491426  0.4320  0.665746    
    ## 1:media1        0.53613816  0.24341595  2.2026  0.027626 *  
    ## 3:media1        0.00082437  0.01054991  0.0781  0.937717    
    ## iv              0.02590753  0.02039077  1.2706  0.203888    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Log-Likelihood: -1450.7
    ## McFadden R^2:  0.076773 
    ## Likelihood ratio test : chisq = 241.28 (p.value = < 2.22e-16)

Based on the first model, **religion** and **education of husband** are
removed from the model. Thus the new model is compared with the old one.
The results of the new model (using choice 1 as reference) seem to
suggest that the new model is a worse mode, even though the difference
is not significant. All the impacts are still available, but their
significance is weaker than in the original model.

    model_2 <- mlogit(method ~ 0 | age + education_w + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    nests = list(positive = c("2", "3"), negative = c("1")),
                    reflevel = "1",
                    un.nest.el = T)

    summary(model_2)

    ## 
    ## Call:
    ## mlogit(formula = method ~ 0 | age + education_w + working + media, 
    ##     data = mldata, reflevel = "1", nests = list(positive = c("2", 
    ##         "3"), negative = c("1")), un.nest.el = T, shape = "long", 
    ##     alt.var = "alt")
    ## 
    ## Frequencies of alternatives:
    ##       1       2       3 
    ## 0.42702 0.22607 0.34691 
    ## 
    ## bfgs method
    ## 12 iterations, 0h:0m:0s 
    ## g'(-H)^-1g = 7.59E-07 
    ## gradient close to zero 
    ## 
    ## Coefficients :
    ##                   Estimate  Std. Error t-value Pr(>|t|)  
    ## 2:(intercept)  -1.59819244  2.27485095 -0.7025  0.48234  
    ## 3:(intercept)   0.36336944  0.59393883  0.6118  0.54067  
    ## 2:age           0.00093168  0.03054129  0.0305  0.97566  
    ## 3:age          -0.03401744  0.02034649 -1.6719  0.09454 .
    ## 2:education_w2  0.58538759  0.42095104  1.3906  0.16434  
    ## 3:education_w2  0.26103484  0.26335908  0.9912  0.32160  
    ## 2:education_w3  1.16533754  0.64995859  1.7929  0.07298 .
    ## 3:education_w3  0.55946189  0.33472690  1.6714  0.09464 .
    ## 2:education_w4  1.91183303  0.85283004  2.2418  0.02498 *
    ## 3:education_w4  1.02394477  0.47709025  2.1462  0.03185 *
    ## 2:working1      0.20543153  0.15589719  1.3177  0.18759  
    ## 3:working1      0.27958958  0.13298498  2.1024  0.03552 *
    ## 2:media1       -0.49964288  0.27824433 -1.7957  0.07254 .
    ## 3:media1       -0.54268412  0.24824005 -2.1861  0.02881 *
    ## iv              0.50935003  0.71770606  0.7097  0.47790  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Log-Likelihood: -1463.5
    ## McFadden R^2:  0.068636 
    ## Likelihood ratio test : chisq = 215.7 (p.value = < 2.22e-16)

So a likelihood ratio test was conducted to compare the two models. The
results indicate that the first model is a significantly better one on
the 0.001 level.

    lrtest(model_1, model_2)

    ## Likelihood ratio test
    ## 
    ## Model 1: method ~ 0 | age + religion + education_w + education_h + working + 
    ##     media
    ## Model 2: method ~ 0 | age + education_w + working + media
    ##   #Df  LogLik Df  Chisq Pr(>Chisq)   
    ## 1  23 -1450.7                        
    ## 2  15 -1463.5 -8 25.557    0.00125 **
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

#### So what if the number of children is added to the formula?

Adding the number of children to the formula could result a iv value
higher than 1, which suggests that the nest may not exist.

    model_3 <- mlogit(method ~ 0 | age + child + religion + education_w + education_h + working + media,
                    data = mldata,
                    shape = "long", 
                    alt.var = "alt",
                    nests = list(positive = c("2", "3"), negative = c("1")),,
                    reflevel = "1",
                    un.nest.el = T)

    summary(model_3)

    ## 
    ## Call:
    ## mlogit(formula = method ~ 0 | age + child + religion + education_w + 
    ##     education_h + working + media, data = mldata, reflevel = "1", 
    ##     nests = list(positive = c("2", "3"), negative = c("1")), 
    ##     un.nest.el = T, shape = "long", alt.var = "alt")
    ## 
    ## Frequencies of alternatives:
    ##       1       2       3 
    ## 0.42702 0.22607 0.34691 
    ## 
    ## bfgs method
    ## 7 iterations, 0h:0m:0s 
    ## g'(-H)^-1g = 1.98E-07 
    ## gradient close to zero 
    ## 
    ## Coefficients :
    ##                 Estimate Std. Error t-value  Pr(>|t|)    
    ## 2:(intercept)  -1.742281   1.416645 -1.2299 0.2187479    
    ## 3:(intercept)   0.163212   0.840715  0.1941 0.8460704    
    ## 2:age          -0.030747   0.026070 -1.1794 0.2382413    
    ## 3:age          -0.109741   0.019680 -5.5764 2.456e-08 ***
    ## 2:child         0.350643   0.047603  7.3660 1.759e-13 ***
    ## 3:child         0.346990   0.038940  8.9110 < 2.2e-16 ***
    ## 2:religion1    -0.544785   0.222913 -2.4439 0.0145280 *  
    ## 3:religion1    -0.389610   0.211669 -1.8407 0.0656721 .  
    ## 2:education_w2  1.187589   0.612562  1.9387 0.0525349 .  
    ## 3:education_w2 -0.044816   0.299749 -0.1495 0.8811505    
    ## 2:education_w3  2.077280   0.772680  2.6884 0.0071794 ** 
    ## 3:education_w3  0.226748   0.361391  0.6274 0.5303761    
    ## 2:education_w4  3.212375   0.918134  3.4988 0.0004673 ***
    ## 3:education_w4  0.801143   0.468369  1.7105 0.0871744 .  
    ## 2:education_h2 -1.395753   1.012452 -1.3786 0.1680223    
    ## 3:education_h2  1.838981   0.897935  2.0480 0.0405588 *  
    ## 2:education_h3 -1.162879   0.944388 -1.2314 0.2181893    
    ## 3:education_h3  2.025974   0.907390  2.2327 0.0255655 *  
    ## 2:education_h4 -0.929407   0.825051 -1.1265 0.2599604    
    ## 3:education_h4  1.824294   0.873033  2.0896 0.0366532 *  
    ## 2:working1     -0.032841   0.195507 -0.1680 0.8665998    
    ## 3:working1      0.164748   0.161276  1.0215 0.3070051    
    ## 2:media1       -0.702463   0.420567 -1.6703 0.0948647 .  
    ## 3:media1       -0.638858   0.290282 -2.2008 0.0277486 *  
    ## iv              1.217062   0.641126  1.8983 0.0576543 .  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Log-Likelihood: -1392.3
    ## McFadden R^2:  0.11393 
    ## Likelihood ratio test : chisq = 358.04 (p.value = < 2.22e-16)

A t-test is conducted for this hypothesis. The resulting t-value is
smaller than the critical t-value at 5% level. So the hypothesis that
the nest exists can be rejected.

    (coef(model_3)['iv']-1)/sqrt(vcov(model_3)['iv', 'iv'])

    ##        iv 
    ## 0.3385628

However, according to the result of the likeliness-ratio test, the last
model including the number of children, is a better model than our
initial model without this variable, despite of the fact that the nest
probably doesn't exist, which leaves clue for developing a better model.

    lrtest(model_1, model_3)

    ## Likelihood ratio test
    ## 
    ## Model 1: method ~ 0 | age + religion + education_w + education_h + working + 
    ##     media
    ## Model 2: method ~ 0 | age + child + religion + education_w + education_h + 
    ##     working + media
    ##   #Df  LogLik Df  Chisq Pr(>Chisq)    
    ## 1  23 -1450.7                         
    ## 2  25 -1392.3  2 116.77  < 2.2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Conclusions
-----------

According to our analysis, depending on the model established, different
independent variables can be related with women's decisions to adopt
contraceptive methods, but the level of education women receive seems to
be the strongest one that explains.

Besides other factors, number of children seems to be another strong
factor connected with women's decisions to adopte both long-term and
short-term methods. And the resulting model is significantly better than
the models without this variable.

Reference:
----------

Lichman, M. (2013). UCI Machine Learning Repository
\[<http://archive.ics.uci.edu/ml>\]. Irvine, CA: University of
California, School of Information and Computer Science.
