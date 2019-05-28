# Keystroke Dynamics

<b>Programming Languages/Software:</b> R, RStudio

<b>Skills Used:</b><br>
Statistical Analysis<br>
Exploratory Analysis<br>
Machine Learning Models<br>
Variable Importance

## Introduction

Keystroke dynamics has been a topic of research since the early 1980s.<sup>[6]</sup> It is a behavioral biometric that can potentially identify a user based on their typing habits. With the burgeoning market of e-commerce over the past decade, keystroke dynamics has presented itself as a potential added layer of security. The phenomenon is based on the idea that each individual possesses his or her own unique typing habits. By analyzing the way users type, password protected websites can use it in combination with passwords to authenticate a user. 

However, keystroke dynamics is not without its criticisms. As a behavioral biometric, as opposed to physiological biometrics such as fingerprints, there is an intrinsic variability associated with typing dynamics.<sup>[2]</sup> For example, do typing patterns change when the same password or pass-phrase is typed repeatedly? How does an injury to an individual’s hand affect typing behavior? Does age, sex, or profession influence the way people type? 

This paper will explore the possibility that typing dynamics as a repeated behavior changes over time. The data was obtained from the work performed by Killourhy and Maxion.<sup>[5]</sup> In their publication, timing data from a monitored keyboard was collected from 51 subjects, each typing 50 repetitions of a pre-determined password, across 8 sessions that were separated by 24 hours. The password was made at random by a publicly available password generator, using 10-characters, containing letters, numbers, and punctuation. The password that was used is:

<p align="center">.tie5Roanl</p>

This password was checked with a publicly available password-strength checker and given a rating of ‘strong’.

Speed is the crucial factor that is analyzed. More specifically, the total time to type the entire passcode will indicate if a user has developed a pattern. Two models, linear mixed-effects model and random forest, are compared for analysis. Post-hoc analysis will determine which features are most influential and if interactions between features exist.  

## Exploratory Analysis

Using the built-in is.na function to look for missing values, we find there are 0 missing values, and therefore do not need to imputate missing values.

    ## [1] 0

Using a correlation matrix, we find there are several highly correlated columns, namely columns that correspond to “time between pressing key down to time to pressing next key down” (labeled DD) with “time between key coming up to time to pressing next key down” (labeled UD). Using a custom function, correlated columns are listed in long form. Data corresponding to DD overlaps with “the amount of time a key is held down” (labeled H). Therefore, features labeled DD will be excluded from our analysis.

Correlated features of Killourhy and Maxion Typing Data <br>
    ##                 row          column       cor p <br>
    ## 105 DD.five.Shift.r UD.five.Shift.r 0.9965210 0 <br>
    ## 66        DD.e.five       UD.e.five 0.9933818 0 <br>
    ## 36           DD.i.e          UD.i.e 0.9930538 0 <br>
    ## 435     DD.l.Return     UD.l.Return 0.9925515 0 <br>
    ## 3       DD.period.t     UD.period.t 0.9916247 0 <br>
    ## 153    DD.Shift.r.o    UD.Shift.r.o 0.9826646 0 <br>
    ## 351          DD.n.l          UD.n.l 0.9821240 0 <br>
    ## 15           DD.t.i          UD.t.i 0.9759530 0 <br>
    ## 210          DD.o.a          UD.o.a 0.9699643 0 <br>
    ## 276          DD.a.n          UD.a.n 0.9335130 0 <br>
