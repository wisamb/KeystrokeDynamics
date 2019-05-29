# Keystroke Dynamics

<b>Programming Languages/Software:</b> R, RStudio

<b>Skills Used:</b><br>
Data Analysis<br>
Data Visualization<br>
Exploratory Analysis<br>
Statistical Models<br>
Machine Learning Algorithms<br>
Variable Importance

## Introduction

Keystroke dynamics has been a topic of research since the early 1980s.<sup>6</sup> It is a behavioral biometric that can potentially identify a user based on their typing habits. With the burgeoning market of e-commerce over the past decade, keystroke dynamics has presented itself as a potential added layer of security. The phenomenon is based on the idea that each individual possesses his or her own unique typing habits. By analyzing the way users type, password protected websites can use it in combination with passwords to authenticate a user. 

However, keystroke dynamics is not without its criticisms. As a behavioral biometric, as opposed to physiological biometrics such as fingerprints, there is an intrinsic variability associated with typing dynamics.<sup>2</sup> For example, do typing patterns change when the same password or pass-phrase is typed repeatedly? How does an injury to an individual’s hand affect typing behavior? Does age, sex, or profession influence the way people type? 

This paper will explore the possibility that typing dynamics as a repeated behavior changes over time. The data was obtained from the work performed by Killourhy and Maxion.<sup>5</sup> In their publication, timing data from a monitored keyboard was collected from 51 subjects, each typing 50 repetitions of a pre-determined password, across 8 sessions that were separated by 24 hours. The password was made at random by a publicly available password generator, using 10-characters, containing letters, numbers, and punctuation. The password that was used is:

<p align="center">.tie5Roanl</p>

This password was checked with a publicly available password-strength checker and given a rating of ‘strong’.

Speed is the crucial factor that is analyzed. More specifically, the total time to type the entire passcode will indicate if a user has developed a pattern. Two models, linear mixed-effects model and random forest, are compared for analysis. Post-hoc analysis will determine which features are most influential and if interactions between features exist.  

## Exploratory Analysis

There are no missing values in this dataset, and therefore there is not a need to imputate missing values.

Using a correlation matrix, we find there are several highly correlated columns, namely columns that correspond to “time between pressing key down to time to pressing next key down” (labeled DD) with “time between key coming up to time to pressing next key down” (labeled UD). Using a custom function, correlated columns are listed in long form. Data corresponding to DD overlaps with “the amount of time a key is held down” (labeled H). Therefore, features labeled DD will be excluded from our analysis.

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Table 1. Correlated Variables for HCC Survival Dataset
</caption>
<thead>
<tr>
<th style="text-align:left;">
</th>
<th style="text-align:left;">
row
</th>
<th style="text-align:left;">
column
</th>
<th style="text-align:right;">
cor
</th>
<th style="text-align:right;">
p
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
105
</td>
<td style="text-align:left;">
DD.five.Shift.r
</td>
<td style="text-align:left;">
UD.five.Shift.r
</td>
<td style="text-align:right;">
0.997
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
435
</td>
<td style="text-align:left;">
DD.l.Return
</td>
<td style="text-align:left;">
UD.l.Return
</td>
<td style="text-align:right;">
0.993
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
36
</td>
<td style="text-align:left;">
DD.i.e
</td>
<td style="text-align:left;">
UD.i.e
</td>
<td style="text-align:right;">
0.993
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
66
</td>
<td style="text-align:left;">
DD.e.five
</td>
<td style="text-align:left;">
UD.e.five
</td>
<td style="text-align:right;">
0.993
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
3
</td>
<td style="text-align:left;">
DD.period.t
</td>
<td style="text-align:left;">
UD.period.t
</td>
<td style="text-align:right;">
0.992
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
153
</td>
<td style="text-align:left;">
DD.Shift.r.o
</td>
<td style="text-align:left;">
UD.Shift.r.o
</td>
<td style="text-align:right;">
0.983
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
351
</td>
<td style="text-align:left;">
DD.n.l
</td>
<td style="text-align:left;">
UD.n.l
</td>
<td style="text-align:right;">
0.982
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
15
</td>
<td style="text-align:left;">
DD.t.i
</td>
<td style="text-align:left;">
UD.t.i
</td>
<td style="text-align:right;">
0.976
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
210
</td>
<td style="text-align:left;">
DD.o.a
</td>
<td style="text-align:left;">
UD.o.a
</td>
<td style="text-align:right;">
0.970
</td>
<td style="text-align:right;">
0
</td>
</tr>
<tr>
<td style="text-align:left;">
276
</td>
<td style="text-align:left;">
DD.a.n
</td>
<td style="text-align:left;">
UD.a.n
</td>
<td style="text-align:right;">
0.934
</td>
<td style="text-align:right;">
0
</td>
</tr>
</tbody>
</table>

### Characterization of the Response Variable 

After removing correlated columns, the sum of each row yields “total time to type passcode” (total.time). Descriptive statistics for the response variable are provided in Table 2. The mean is greater than the median and there is a large gap between the maximum value and the 3rd quantile, suggesting a very long tail. Visualization of the distribution of the total.time variable (Figure 1) validates this finding and shows a skewness to the right. Plotting the total.time variable by session (Figure 1) shows a general trend to decrease, although standard deviation is very high in this data set.

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Table 2. Summary Statistics for Total Time
</caption>
<tbody>
<tr>
<td style="text-align:left;">
Min.
</td>
<td style="text-align:right;">
1.08
</td>
</tr>
<tr>
<td style="text-align:left;">
1st Qu.
</td>
<td style="text-align:right;">
1.87
</td>
</tr>
<tr>
<td style="text-align:left;">
Median
</td>
<td style="text-align:right;">
2.30
</td>
</tr>
<tr>
<td style="text-align:left;">
Mean
</td>
<td style="text-align:right;">
2.58
</td>
</tr>
<tr>
<td style="text-align:left;">
3rd Qu.
</td>
<td style="text-align:right;">
2.94
</td>
</tr>
<tr>
<td style="text-align:left;">
Max.
</td>
<td style="text-align:right;">
36.00
</td>
</tr>
</tbody>
</table>

<figure>
  <figcaption>Figure 1. Distribution and trend over time of response variable, total.time</figcaption>
  <img src="/images/unnamed-chunk-10-1.png" style="width:80%">
</figure> 

### Detection of Outliers

Using boxplots (Figure 2), there is a large number of outliers and several extreme values. Extreme values are defined as having total.time greater than 10 seconds. Outliers will not be removed due to their high numbers; however, subjects 036 and 049 account for 31 of the 35 extreme data points (Table 3). Therefore, data from these two subjects are excluded from the analysis.

<figure>
  <figcaption>Figure 2. Boxplots to identify anomalies</figcaption>
  <img src="/images/unnamed-chunk-11-1.png" width="70%"/>
</figure> 

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Table 3. Frequency of Patients with Extreme Values 
</caption>
<thead>
<tr>
<th style="text-align:left;">
Subject
</th>
<th style="text-align:right;">
Frequency
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
s022
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
s030
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
s036
</td>
<td style="text-align:right;">
10
</td>
</tr>
<tr>
<td style="text-align:left;">
s043
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
s047
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
s049
</td>
<td style="text-align:right;">
21
</td>
</tr>
</tbody>
</table>

Figure 3 plots the distribution and trend over time of the final dataset. The distribution is still skewed right and has a long upper tail. The general trend to decrease across sessions and high standard deviation for the total.time variable are also still present.

<figure>
  <figcaption>Figure 3. Distribution and trend over time of the final dataset</figcaption>
  <img src="/images/unnamed-chunk-15-1.png">
</figure> 

## Linear Mixed-Effects Model

We run two versions of the linear mixed-effects model: using subject alone for random effect (model 1) and using subject in relation to session ID for random effect (model 2). Although the AIC of Model 1 is lower, model 2 has a higher log-likelihood. Furthermore, when plotting residuals vs fitted values (Figure 4), model 1 shows non-constant variance, which we do not see in model 2. Therefore, model 2 is the model of choice. Both models, however, report significance across sessions and highly accurate MSEs (model 1 MSE = 0.0338, model 2 MSE = 0.0215).

    model1: mean.total.time.y ~ sessionIndex.y + mean.total.time.x + (1 | subject) 
    model2: mean.total.time.y ~ sessionIndex.y + mean.total.time.x + (sessionIndex.y | subject)

Table 4. ANOVA comparison of model 1 and model 2
<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
  <tr>
    <th></th>
    <th>Df</th>
    <th>AIC</th>
    <th>logLik</th>
    <th>deviance</th>
    <th>Chisq</th>
    <th>Chi Df</th>
    <th>Pr(&gt;Chisq)</th>
  </tr>
  <tr>
    <td>model1</td>
    <td>5</td>
    <td>-13.167</td>
    <td>6.0216</td>
    <td>11.584</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>model2</td>
    <td>7</td>
    <td>-45.991</td>
    <td>-19.1266</td>
    <td>29.995</td>
    <td>36.824</td>
    <td>2</td>
    <td>1.009e-08 ***</td>
  </tr>
</table>
 
<figure>
  <figcaption>Figure 4. LME models variance comparison</figcaption>
    <table>
      <tr>
        <th><img src="/images/unnamed-chunk-20-1.png" style="width:40%"></th>
        <th><img src="/images/unnamed-chunk-20-2.png" style="width:40%"></th>
      </tr>
    </table> 
</figure>

### Appropriateness of the LME Model

A linear mixed-effects model is ideal for longitudinal, repeated measures data such this dataset, and also accounts for unseen variables, known as random effects, such as differences between subjects. Furthermore, the model is suitable when the distribution conditional on the predictors is normal. Residuals of the model are plotted across session ID in Figure 5 and show that variability of the residuals is constant as the sessions progress. Therefore, the linear mixed-effect model is appropriate for analysis on this dataset and switching to generalized estimating equations is unnecessary. 

<figure>
  <figcaption>Figure 5. LME model 2: Residuals across Session ID</figcaption>
  <img src="/images/unnamed-chunk-23-1.png" width="70%"/>
</figure>

## Random Forest

Ensemble decision trees are known to be some of the most powerful algorithms. It is of interest to analyze the data with a random forest model in addition to the linear mixed-effects model. 

To use this model, we express total.time as a response to each of the 21 variables that were measured. The complete formula for the model is as such:

<i>mean.total.time ~ H.period + UD.period.t + H.t + UD.t.i + H.i + UD.i.e + H.e + UD.e.five + H.five + UD.five.Shift.r + H.Shift.r + UD.Shift.r.o + H.o + UD.o.a + H.a + UD.a.n + H.n + UD.n.l + H.l + UD.l.Return + H.Return</i>

where mean.total.time is the mean of the repetitions for each subject per session. The model produces an MSE of 0.0247, which is comparable to the MSE of the mixed-effects model.

### Feature Analysis

Importance of the variables is measured by the mean decrease in accuracy as measured by MSE (%IncMSE). Order of importance is presented in Figure 6. Clearly, “time between key coming up to time to pressing next key down” (labeled UD) is most influential in determining total time to type entire passcode as these variables are ranked 10 of the top 11 spots in the feature analysis. This makes sense as transitioning between keys takes more time than holding down a key. Of more interest, however, we find the top 4 most important features involve transitioning between a letter key and a non-letter key (i.e., numbers, period, or return keys).

<figure>
  <figcaption>Figure 6. Order of Variable Importance</figcaption>
  <img src="/images/unnamed-chunk-26-1.png" width="70%"/>
</figure>

### Recursive Feature Elimination

Recursive feature elimination is performed to determine the extent each variable affects MSE of the random forest model. Figure 7 shows a gradual decline in RMSE as the number of variables increase. Our random forest has an MSE of 0.0247 (equivalent to an RMSE of 0.157) which corresponds to the first 20 important variables (highlighted by the solid blue circle). The least important feature corresponds to holding down the "n" key (H.n).

<figure>
  <figcaption>Figure 7. RMSE vs Variables of Random Forest Model</figcaption>
  <img src="/images/run_rfe-1.png" width="70%"/>
</figure>

Each individual feature is also plotted across sessions (Figure 8) and plots are sorted by order of importance. It becomes obvious how importance is assigned as the most important features show a negative slope and the least important features have either a zero slope or a slightly positive slope.

<figure>
  <figcaption>Figure 8. Plots of Individual Features Across Sessions</figcaption>
  <table>
  <tr>
    <td><img src="/images/unnamed-chunk-30-1a.png"></td>
    <td><img src="/images/unnamed-chunk-30-2a.png"></td>
    <td><img src="/images/unnamed-chunk-30-3a.png"></td>
    <td><img src="/images/unnamed-chunk-30-4a.png"></td>
  </tr>
  <tr>
    <td><img src="/images/unnamed-chunk-30-5a.png"></td>
    <td><img src="/images/unnamed-chunk-30-6a.png"></td>
    <td><img src="/images/unnamed-chunk-30-7a.png"></td>
    <td><img src="/images/unnamed-chunk-30-8a.png"></td>
  </tr>
  <tr>
    <td><img src="/images/unnamed-chunk-30-9a.png"></td>
    <td><img src="/images/unnamed-chunk-30-10a.png"></td>
    <td><img src="/images/unnamed-chunk-30-11a.png"></td>
    <td><img src="/images/unnamed-chunk-30-12a.png"></td>
  </tr>
  <tr>
    <td><img src="/images/unnamed-chunk-30-13a.png"></td>
    <td><img src="/images/unnamed-chunk-30-14a.png"></td>
    <td><img src="/images/unnamed-chunk-30-15a.png"></td>
    <td><img src="/images/unnamed-chunk-30-16a.png"></td>
  </tr>
  <tr>
    <td><img src="/images/unnamed-chunk-30-17a.png"></td>
    <td><img src="/images/unnamed-chunk-30-18a.png"></td>
    <td><img src="/images/unnamed-chunk-30-19a.png"></td>
    <td><img src="/images/unnamed-chunk-30-20a.png"></td>
  </tr>
</table>
</figure>

### Random Forest with Reduced Features

We run the randomForest algorithm removing the least important feature (H.n). The formula becomes:

<i>mean.total.time ~ H.period + UD.period.t + H.t + UD.t.i + H.i + UD.i.e + H.e + UD.e.five + H.five + UD.five.Shift.r + H.Shift.r + UD.Shift.r.o + H.o + UD.o.a + H.a + UD.a.n + UD.n.l + H.l + UD.l.Return + H.Return </i>

Running this algorithm reduces MSE slightly reduced (MSE=0.0236).

### Testing for Interactions

From the 20 most important variables, we explore the idea that interactions may exist when typing keys that appear sequentially in the password, resulting in an additional 18 variables. The formula including these interactions becomes:

<i>mean.total.time ~ H.period + UD.period.t + H.t + UD.t.i + H.i + UD.i.e + H.e + UD.e.five + H.five + UD.five.Shift.r + H.Shift.r + UD.Shift.r.o + H.o + UD.o.a + H.a + UD.a.n + UD.n.l + H.l + UD.l.Return + H.Return + H.period:UD.period.t + UD.period.t:H.t + H.t:UD.t.i + UD.t.i:H.i + H.i:UD.i.e + UD.i.e:H.e + H.e:UD.e.five + UD.e.five:H.five + H.five:UD.five.Shift.r + UD.five.Shift.r:H.Shift.r + H.Shift.r:UD.Shift.r.o + UD.Shift.r.o:H.o + H.o:UD.o.a + UD.o.a:H.a + H.a:UD.a.n + UD.n.l:H.l + H.l:UD.l.Return + UD.l.Return:H.Return</i>

Including these interaction terms does not improve the model’s MSE (MSE=0.0236), and with no apparent benefit, these interaction terms should not be included.

### Conclusion

Using the data from Killourhy and Maxion, we determined that typing speed does in fact decrease across sessions, implying that typing dynamics in general change as users become accustomed to typing a password. In particular, users become faster at transitioning between letter keys and a non-letter keys (i.e., numbers, period, or return keys). Recognizing this pattern, and potentially others, can lend itself to enhanced authentication methods as someone who is not accustomed to typing the password, i.e. a hacker, would show slower patterns.

In addition, it would be necessary to investigate how these patterns might change based on age, sex, profession, and possibly many other factors. Some of this type of information may be collected from users voluntarily, while other information might be more difficult to collect, such as injury to an individual’s hand. No matter how the information is collected, any enhanced authentication methods would need to reliably factor in this information. 

### References

1. Bates, D., Mächler, M., Bolker, B. M., & Walker, S. C. (2015). Fitting Linear Mixed-Effects Models Using lme4. Journal of Statistical Software, 67(1), 1-48. doi:10.18637/jss.v067.i01 
2. Bergadano, F., Gunetti, D., & Picardi, C. (november 2002). User authentication through keystroke dynamics. ACM Transactions on Information and System Security (TISSEC), 5(4), 367-397. 
3. Hothorn, T. and Everitt, B. (2014). A handbook of statistical analyses using R. 3rd ed. Boca Raton: CRC Press. 
4. “Keystroke Dynamics - Benchmark Data Set,” https://www.cs.cmu.edu/~keystroke/#sec2, accessed: 2018-11-27. 
5. S. Killourhy, Kevin & Maxion, R.A.. (2009). Comparing Anomaly-Detection Algorithms for Keystroke Dynamics. IEEE/IFIP International Conference on Dependable Systems & Networks. 125 - 134. 10.1109/DSN.2009.5270346. 
6. Teh, P. S., Teoh, A. B., Tee, C., & Ong, T. S. (2010). Keystroke dynamics in password authentication enhancement. Expert Systems with Applications, 37(12), 8618-8627.
