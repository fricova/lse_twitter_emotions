# Hypotheses and variable description

### Hypotheses

The following hypotheses are tested in this paper:\
H1: Daily Twitter sentiment index predicts subsequent SP500 prices.\
H2: Prevalence of fear expressions on Twitter predicts subsequent SP500 prices.\
H3: Prevalence of trust expressions on Twitter predicts subsequent SP500 prices.\
H4: Prevalence of anticipation expressions on Twitter predicts subsequent SP500 prices.\
H5: Prevalence of joy expressions on Twitter predicts subsequent SP500 prices.\
H6: Sentiment index and/or aggregate emotions increase the forecasting accuracy of our neural network in predicting SP500 prices.\

### Preanalysis plan

To increase the transparency of my research, and to allow other researchers to exactly replicate my study, I have published a pre-analysis plan prior to testing the hypotheses. The pre-analysis plan is publicly available on the Open Science Framework website, and it can be accessed via the following link: osf.io/anonymous/viewonly.

### Variables of Interest
To overcome the small size limitations of past research, the current study uses a large sample of almost 14 million Tweets during the Covid-19 outbreaks to predict subsequent SP500 prices. I investigate the predictive power of both a unidimensional sentiment index (predictor variable 1) and multiple aggregate emotion metrics (predictor variables 2-5) by using linear, as well as non-linear techniques. 

### Predictor Variables

As outlined previously, all independent variables are standardized to z-scores. Based on my hypotheses, they are the z-scores of (1) daily sentiment; (2) fear, (3) trust, (4) joy and, finally, (5) anticipation all expressed on Twitter. All the z-score variables have an approximately 0 mean and a standard deviation of 1, closely following the characteristics of a normally distributed series. However, the sentiment index and anticipation series have a noticeable negative skew, implying that the series have positive sentiment and high anticipation on most days, but there are some outliers with very negative sentiment/low anticipation. Moreover, all the series are leptokurtic, meaning that they have heavy fat tails. And especially the
trust series appears to have extreme outliers in its values. Trust levels skyrocketed in October 2020, at the time when multiple vaccines were successfully completing phases 2 and 3 of trials.

### Outcome Variable

My outcome of interest concerns the S&P500 price series. When testing the properties of S&P500 raw prices, I find evidence of unit root non-stationarity in the series (please see Table A.3 in this folder for more information). This has no implications for the neural network model I am using, since neural networks do not pose any limitations on variable distributions. And I, therefore, use raw S&P500 prices as outcome variable in the neural network models. On the other hand, the linear Granger causality method requires that the variables are unit root stationary [Wooldridge, 2015]. To overcome this issue and prevent any potential bias in the Granger causality results which unit root price series could introduce, I compute the day-to-day changes of the price series. By taking the day-to-day price changes, I eliminate any unit root non-stationarity. I, therefore, use day-to-day changes in S&P500 prices as
my dependent variable in the Granger causality model. The descriptive statistics of the S&P 500 prices illustrate that there is an overall rising price trend. The negative skew suggests that despite the general positive trend, there are some very substantial price crashes in the series.

# Empirical Strategy

## Linear Granger Causality

I use linear Granger causality analysis to evaluate Hypotheses 1-5 as well as to provide partial evidence for Hypothesis 6. In general,
Granger causality technique is based on the assumption that if a variable X causes variable Y, then changes in X will systematically precede changes in Y. Therefore, lagged values of X must be significantly correlation with present values of Y. It must be pointed out, however, that correlation does not imply causation and I am not testing whether one time series causes the other, but merely whether one time series has predictive information about the other.

I run two types of linear models to test our hypotheses: \
(1) anautoregressive model of past changes in S&P 500 prices,\
(2) an autoregressive model which additionally incorporates one of the four different emotions or the unidimensional
Twitter sentiment index.

Both Model (1) and Model (2) are nested Auto Regressive Distributed Lag models. The nested structure allows me to conduct likelihood ratio F-tests. More specifically, I am testing whether the specific emotions, or the unidimensional Twitter index expressed in variable X have predictive content for S&P500 above and beyond that contained in past changes of S&P 500 prices
(Kirchgassner, 2012).

Granger causality test statistics are very sensitive to the lag length chosen for the underlying VAR model and too high selected number of lags (i.e. overfitting) increases the probability of false positive results (Bruns et al. 2019). Therefore, I choose the exact lag length prior to testing. Given that previous research by Bollen et al. (2011) incorporated 1-7 lags in their linear model specifications and found that some emotions Granger cause DJIA values for up to 6 days worth of lags, I also consider up to 6 day-lags for the Granger causality analysis. But out of the potential 1-6 day lag specifications, I only select the one lag specification that minimises both the AIC (Akaike, 1974) and the BIC (Schwarz, 1978) prior to analysis. In case that AIC and BIC yield different results, I use the model which minimises AIC - as recommended by Thornton and Batten (1985), as well as Ozcicek and Mcmillin (1999). To illustrate, in a Monte Carlo simulation study by Ozcicek and Mcmillin (1999), the AIC criterion selects the true lags more frequently than BIC, and also more often than all other considered criteria in symmetric VAR models.

## Fully Connected Neural Network

To address non-linear effects that are indisputably present in the actual data-generating process, and to evaluate Hypothesis 6, I use fully connected neural network models. My decision to use fully connected networks, rather than Self-Organizing Fuzzy neural networks, as was performed in Bollen et al. (2011), has its empirical grounding. Since the publishing of Bollen et al. paper, the non-linear modelling literature has diverted away from fuzzy logic towards modern neural networks, which require fewer assumptions prior to the main analysis. Put differently, fewer parameters must be explicitly pre-specified in fully connected neural network models relative to fuzzy neural networks, which prevents the potentiality of incorrect assumption setting
(Goodfellow et al., 2016).

Moreover, other neural network models besides fully connected neural networks are being increasingly applied to stock market prediction,
including long short-term memory networks and convolutional neural
networks [@fischer2018deep; @chen2018stock]. However, both long
short-term memory networks and convolutional neural network models have
a well-known tendency to overfit small datasets by memorizing inputs
rather than training, which leaves them arguably not well-suited for our
analysis of small scale dataset
[@hochreiter1997lstm; @lecun1989backpropagation].

We run 2 fully connected neural network models - (1) model that takes as
inputs only past values of S&P500 prices, and (2) model that takes as
inputs S&P500 prices, emotions and sentiment of the past days. To
predict the S&P500 value on day t, the inputs of our fully connected
neural network (2) include lagged values of S&P500 prices, emotions and
the overall sentiment of the past n days. The selected number of
incorporated lags - n, is determined through the process of
hyper-parameter tuning [@claesen2015hyperparameter]. This tuning
procedure selects the best non-linear naive/baseline model (i.e., model
with just S&P500 prices) that we can obtain given our sample size
constraints. In consequence, the tuning procedure ensures that the
goodness-of-fit comparison between our baseline model and the model that
additionally includes emotions and sentiment is fair and does not
inflate our contribution.

The sentiment and emotion inputs are linearly scaled to \[0,1\], in
order to ensure that the mood inputs have similar initial weights in the
model. However, since we are interested in our outcome variable of
S&P500 prices, we do not linearly scale the S&P500 price values prior to
analysis. This, in consequence, determines the selection of our
activation function. The standard ReLU activation function works best
when all input variables are linearly scaled. Since we do not linearly
scale the lagged prices in our model, we instead use the Leaky ReLU
activation function, which is well-suited for negative input values
[@nwankpa2018activation]. The Leaky ReLU function is detailed in
Appendix Figure A.1.

Our fully connected neural network model has 3 layers. The decision to
choose 3 layers stems from our limited sample size on one hand and the
need to have at least 1 inner layer on the other hand. By choosing the
relatively low number of layers, we are limiting the risk of model
overfitting, while also ensuring that we provide some opportunity for
the model to learn.

In mathematical terms, given that $\sigma$ is the Leaky Relu function,
$x$ being the input, $a$ being the desired output,$M_1$, $M_2$, $M_3$,
$b_1$, $b_2$, $b_3$ being the trainable parameters for the neural
network and $L$ being the error, we obtain that our neural network can
be expressed as:

::: ceqn
$$\begin{aligned}
y_1 = \sigma(\mathbf{M_1} * x + b_1) \\
y_2 = \sigma(\mathbf{M_2} * y_1 + b_2) \\
y = \sigma(\mathbf{M_3} * y_2 + b_3) \\
L = (a - y)^2
\end{aligned}$$
:::

Having calculated the loss for a given input and desired output, given
$\alpha$ being the learning rate, we then update the trainable
parameters using a variation of the following expression:

::: ceqn
$$\begin{aligned}
M_n^{new} = M_n^{old} - \alpha\frac{\partial L}{\partial M_n^{old}} \\
b_n^{new} = b_n^{old} - \alpha\frac{\partial L}{\partial b_n^{old}} \\
\end{aligned}$$
:::

Given our small sample size, we divide the sample into a training set
and a testing set by using a k-Fold Cross-Validation, rather than
pre-specified training and testing periods. Guided by past research, we
select 10 folds (k=10) in our k-Fold Cross-Validation procedure.
According to @james2013introduction, k=10 provides a good compromise
between computational cost and bias of model performance estimates.
Forecasting accuracy is then measured in terms of the average Mean
Absolute Percentage Error (MAPE) and the direction accuracy (up or down)
during the test period selected via k-Fold Cross-Validation procedure.
The choice of MAPE and direction accuracy as key performance indicators
was driven by past research (Bollen et al., 2011).

Akaike, H., 1974. A new look at the statistical model identification. IEEE transactions on automatic control, 19(6), pp.716-723.
Bollen, J., Mao, H., & Zeng, X. (2011). Twitter mood predicts the stock market. Journal of computational science, 2 (1), 1–8.
Bruns, S.B. and Stern, D.I., 2019. Lag length selection and p-hacking in Granger causality testing: prevalence and performance of meta-regression models. Empirical Economics, 56, pp.797-830.
Goodfellow, I., Bengio, Y. and Courville, A., 2016. Deep learning. MIT press.
Kirchgässner, G., Wolters, J. and Hassler, U., 2012. Introduction to modern time series analysis. Springer Science & Business Media.
Ozcicek, O. and Douglas Mcmillin, W., 1999. Lag length selection in vector autoregressive models: symmetric and asymmetric lags. Applied Economics, 31(4), pp.517-524.
Schwarz, G., 1978. Estimating the dimension of a model. The annals of statistics, pp.461-464.
Thornton, Daniel L., and Dallas S. Batten. "Lag-length selection and tests of Granger causality between money and income." Journal of Money, credit and Banking 17, no. 2 (1985): 164-178.