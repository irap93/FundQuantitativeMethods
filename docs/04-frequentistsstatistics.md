# Frequentists Statistical Model

This is the section that most of you will find more familiar as we cover concepts typically considered "classical" statistics. These methods are tried and true, and are perhaps the most frequently seen in the literature and scientific presentations. In the best case, your data will match one of the these methods exactly. Many research projects are specifically designed to fit one of these classical techniques. Often, we may be able to transform our data or modify our model to fit a classical technique. However, this comes with the caveat that it will change the interpretability of our fitted model, and may make it more difficult to report or compare the results. But, this trade off comes with a number of positive attributes including 
- Increasing the ability to improve the computational speed and stability of our analysis
- Stable definitions of parameter estimation
- Efficient communication of analytical methods
- utilize existing R machinery

However, I strongly believe that our efforts taken build our own deterministic and statistical models engrained a deeper understanding of math and probability. These efforts should 


## General Linear Models

## Example one

## Example two

## Liklihood vs. Probability
(inspired by the following [medium article](https://medium.com/@wl8380/probability-vs-likelihood-the-most-misunderstood-duo-in-data-science-759fc4dcb730))


# Demonstrating Equivalence

 I do have some concerns with the message that is conveyed by the title and approach taken in the statistical methods. Specifically, remember the definition of a p-value is “the probability of obtaining a value of the test statistic that is as likely or more likely to reject the null hypothesis (H0) as the actual observed value of the test statistic, assuming the null hypothesis is true.” The null hypothesis tested by a general linear mixed effects model for your study is generally “Newly weaned calves limit fed a high concentrate diet perform does not alter growth, performance, and efficiency compared to calves fed an ad libitum forage based diet”,  with H0: ALF = LFC and HA: ALF ≠ LFC. A high p-value in this instance indicates a failure to reject the null hypothesis. No matter how large the p-value may be, it does not provide support for the null hypothesis. Failure to reject the null hypothesis means one must now become concerned with the power, or n, of the study, and then move forward to show sufficient effort was made to demonstrate equivalence, or conversely find differences, between treatments.  This is typically illustrated by determining an acceptable delta, (difference between treatment) and then conducting a power analysis to show how many animals would be required to obtain a significant p-value at that acceptable delta given the variation in the system.
 
If this is unnecessarily restrictive or feels difficult to communicate, more studies are moving towards a more Bayesian type of approach, which allows you to specify the prior distribution of the effect size (informed by what is biologically or economically relevant) and then calculate the likelihood of observing no meaningful effect. 
