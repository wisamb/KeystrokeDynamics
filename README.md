# Keystroke Dynamics

<b>Programming Languages/Software:</b> R, RStudio

<b>Skills Used:</b><br>
Statistical Analysis<br>
Exploratory Analysis<br>
Statistical Models<br>
Machine Learning Algorithms<br>
Variable Importance

## Introduction

Keystroke dynamics has been a topic of research since the early 1980s.<sup>[6]</sup> It is a behavioral biometric that can potentially identify a user based on their typing habits. With the burgeoning market of e-commerce over the past decade, keystroke dynamics has presented itself as a potential added layer of security. The phenomenon is based on the idea that each individual possesses his or her own unique typing habits. By analyzing the way users type, password protected websites can use it in combination with passwords to authenticate a user. 

However, keystroke dynamics is not without its criticisms. As a behavioral biometric, as opposed to physiological biometrics such as fingerprints, there is an intrinsic variability associated with typing dynamics.<sup>[2]</sup> For example, do typing patterns change when the same password or pass-phrase is typed repeatedly? How does an injury to an individual’s hand affect typing behavior? Does age, sex, or profession influence the way people type? 

This paper will explore the possibility that typing dynamics as a repeated behavior changes over time. The data was obtained from the work performed by Killourhy and Maxion.<sup>[5]</sup> In their publication, timing data from a monitored keyboard was collected from 51 subjects, each typing 50 repetitions of a pre-determined password, across 8 sessions that were separated by 24 hours. The password was made at random by a publicly available password generator, using 10-characters, containing letters, numbers, and punctuation. The password that was used is:

<p align="center">.tie5Roanl</p>

This password was checked with a publicly available password-strength checker and given a rating of ‘strong’.

Speed is the crucial factor that is analyzed. More specifically, the total time to type the entire passcode will indicate if a user has developed a pattern. Two models, linear mixed-effects model and random forest, are compared for analysis. Post-hoc analysis will determine which features are most influential and if interactions between features exist.  

## Exploratory Analysis

Using the built-in `is.na` function to look for missing values, we find there are 0 missing values, and therefore do not need to imputate missing values.

    ## [1] 0

Using a correlation matrix, we find there are several highly correlated columns, namely columns that correspond to “time between pressing key down to time to pressing next key down” (labeled DD) with “time between key coming up to time to pressing next key down” (labeled UD). Using a custom function, correlated columns are listed in long form. Data corresponding to DD overlaps with “the amount of time a key is held down” (labeled H). Therefore, features labeled DD will be excluded from our analysis.

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Correlated Variables for HCC Survival Dataset
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

After removing correlated columns, the sum of each row yields “total time to type passcode” (total.time). Descriptive statistics for this variable are provided in Table 1. The mean is greater than the median and there is a large gap between the maximum value and the 3rd quantile, suggesting a very long tail. Visualization of the distribution of the total.time variable (Figure 1) validates this finding and shows a skewness to the right. Plotting the total.time variable by session (Figure 1) shows a general trend to decrease, although standard deviation is very high in this data set.

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Summary Statistics for Total Time
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

Using boxplots (Figure 2), we find a large number of outliers and several extreme values. Extreme values are defined as having total.time greater than 10 seconds. Outliers will not be removed due to their high numbers; however, subjects 036 and 049 account for 31 of the 35 extreme data points (Table 2). Therefore, data from these two subjects are excluded from the analysis.

<figure>
  <figcaption>Figure 2. Boxplots to identify anomalies</figcaption>
  <img src="/images/unnamed-chunk-11-1.png" style="width:60%">
</figure> 

<table class="table" style="width: auto !important; margin-left: auto; margin-right: auto;">
<caption>
Patients with Extreme Values (total.time greater than 10 seconds)
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
  <img src="/images/unnamed-chunk-15-1.png" style="width:80%">
</figure> 

## Linear Mixed-Effects Model

We run two versions of the linear mixed-effects model: using subject alone for random effect (model 1) and using subject in relation to session ID for random effect (model 2). Although the AIC of Model 1 is lower, model 2 has a higher log-likelihood. Furthermore, when plotting residuals vs fitted values (Figure 4), model 1 shows non-constant variance, which we do not see in model 2. Therefore, model 2 is the model of choice. Both models, however, report significance across sessions and highly accurate MSEs (model 1 MSE = 0.0338, model 2 MSE = 0.0215).

ANOVA comparison of model 1 and model 2 <br>

    ## Models: <br>
    ## model1: mean.total.time.y ~ sessionIndex.y + mean.total.time.x + (1 | subject) <br>
    ## model2: mean.total.time.y ~ sessionIndex.y + mean.total.time.x + (sessionIndex.y | subject) <br>
    
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
    <div class="row">
      <div class="column">
        
      </div>
      <div class="column">
        
      </div>
    </div>
</figure>

<table>
  <tr>
    <th><img src="/images/unnamed-chunk-20-1.png" style="width:40%"></th>
    <th><img src="/images/unnamed-chunk-20-2.png" style="width:40%"></th>
  </tr>
</table> 
