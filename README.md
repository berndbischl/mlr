
![mlr](https://mlr-org.github.io/mlr-tutorial/images/mlrLogo_blue_141x64.png): Machine Learning in R


Updates to MLR for Forecasting: October 31st, 2016
==========================

The goal of this project is to develop:

1. Forecasting time series models in the MLR framework
2. Measures for evaluating forecast models
3. Resampling methods that work for time based data
4. Automated Preprocessing for lag and difference features

As a basic introduction we will simulate data from an arima process and place it into an xts object.
```{r}
library(xts)
library(lubridate)
set.seed(1234)
dat = arima.sim(model = list(ar = c(.5,.2), ma = c(.4), order = c(2,0,1)), n = 200)
times = (as.POSIXlt("1992-01-14")) + lubridate::days(1:200)
dat = xts(dat,order.by = times)
colnames(dat) = c("arma_test")
```

# Forecast Regression Tasks

Just like with `makeRegrTask()` we will use `makeForecastRegrTask()` to create a task for forecasting. The main difference between `Forecast` tasks and the normal tasks is that our data must be an xts object.


```{r}
Timeregr.task = makeForecastRegrTask(id = "test", data = dat, target = "arma_test",
                                     frequency = 7L)
Timeregr.task
# Task: test
# Type: fcregr
# Observations: 200
# Dates:
#  Start: 1992-01-15 
# End: 1992-08-01
# Frequency: 7
# Features:
# numerics  factors  ordered 
#        1        0        0 
# Missings: FALSE
# Has weights: FALSE
# Has blocking: FALSE
```

 While `xts` is robust, it assumes all dates are continuous, unique, and have a frequency of one. While this gives a robust structure, many of the methods in forecast are dependent on the data's frequency. As such we manually set the frequency we desire in the task. Frequency can be throught of as the seasonality of the time series. A frequency of seven with daily data would represent a weekly seasonality. A frequency of 52 on weekly data would represent yearly seasonality.


## Making Learners for Forecasting

We create an arima model from the package `forecast` using `makeLearner()` by calling the learner class `fcregr.Arima`. An important parameter is the `h` parameter, which is used to specify that we are forecasting 10 periods ahead.

```{r}
arm = makeLearner("fcregr.Arima", order = c(2L,0L,1L), h = 10L, include.mean = FALSE)
arm
# Learner fcregr.Arima from package forecast
# Type: fcregr
# Name: AutoRegressive Integrated Moving Average; Short name: Arima
# Class: fcregr.Arima
# Properties: numerics,ts,quantile
# Predict-Type: response
# Hyperparameters: order=2,0,1,h=10,include.mean=FALSE
```

## Resampling

We now have two new cross validation resampling strategies, `GrowingCV` and `FixedCV`. They are both rolling forecasting origin techniques established in [Hyndman and Athanasopoulos (2013)](https://www.otexts.org/fpp/2/5) and first widely available for machine learning in R by the `caret` package's `createTimeSlices()` function. We specify:

1. horizon - the number of periods to forecast
2. initial.window - The proportion of data that will be used in the initial window
3. size - The numberof rows in the training set
4. skip - the proportion of windows to skip over, which can be used to save time
```{r}
resamp_desc = makeResampleDesc("GrowingCV", horizon = 10L,
                               initial.window = .90,
                               size = nrow(dat), skip = .01)
resamp_desc
# Window description:
#  growing with 6 iterations:
#  100 observations in initial window and 10 horizon.
# Predict: test
# Stratification: FALSE
```

Note that we should stratification, as it does not really make sense in the context of time series to stratify our data (unless we can somehow use this for panel data). The wonderful graphic posted below comes from the `caret` website and gives an intuitive idea of the sliding windows for both the growth and fixed options.

![Build Status](http://topepo.github.io/caret/main_files/figure-html/Split_time-1.png)

Taking our model, task, resampling strategy, and an additonal parameter for scoring our model, we use `resample()` to train our model.
```{r}
resamp_arm = resample(arm,Timeregr.task, resamp_desc, measures = mase)
resamp_arm
# Resample Result
# Task: test
# Learner: fcregr.Arima
# Aggr perf: mase.test.mean=0.0631
# Runtime: 0.137119
```
## Tuning

The forecasting features fully integrate into mlr, allowing us to also make a parameter set to tune over. Here we make a very small parameter space and will use F1-racing to tune our parameters.
```{r}

par_set = makeParamSet(
  makeIntegerVectorParam(id = "order",
                         len = 3L,
                         lower = 0,
                         upper = 2,
                         tunable = TRUE),
  makeLogicalParam(id = "include.mean", default = FALSE, tunable = TRUE)
  )

#Specify tune by grid estimation
ctrl = makeTuneControlIrace(maxExperiments = 180L)

configureMlr(on.learner.error = "warn")
res = tuneParams(makeLearner("fcregr.Arima",h=10), task = Timeregr.task, resampling = resamp_desc, par.set = par_set, control = ctrl, measures = mase)

```
Note that we have to do something very odd when specifying `h`. We specify the upper bound of `h` as 11 as irace will not work if the lower and upper bound of a parameter is the same value, even if the parameter has been specified to `tune = FALSE`. What this means is that inside of `makePrediction.TaskDescForecastRegr` we have to do a weird thing where, even though our prediction will at times be length 11, we cut it by the length of the truth variable `y[1:length(truth)]`. This is only going to complicate things in the future I'm sure. But irace works.

It is interesting to note that Arima does not select our sample data's original underlying process and instead selects a (2,0,0) model.
```{r}
res

# Tune result:
# Op. pars: order=2,0,0; include.mean=FALSE; h=10
# mase.test.mean=0.0623
```

This may be due to how small the data set is. Note that the original process still performed very well.
```{r}
as.data.frame(res$opt.path)[140,]
#     order1 order2 order3 include.mean  h mase.test.mean dob eol error.message exec.time
# 140      2      0      1        FALSE 10      0.0630864  20  NA          <NA>     0.115
```

We can now use our learned model with tuning to pass over the entire data set and give our final prediction.

```{r}
lrn = setHyperPars(makeLearner("fcregr.Arima"), par.vals = res$x)
m = train(lrn, Timeregr.task)
m
# Model for learner.id=fcregr.Arima; learner.class=fcregr.Arima
# Trained on: task.id = test; obs = 200; features = 0
# Hyperparameters: order=2,0,0,include.mean=FALSE,h=10

predict(m, task = Timeregr.task)
# Prediction: 10 observations
# predict.type: response
# threshold: 
# time: 0.01
#                      response
# 1992-08-01 23:59:41 1.3139165
# 1992-08-02 23:59:23 0.9143794
# 1992-08-03 23:59:05 0.6571046
# 1992-08-04 23:58:47 0.4790978
# 1992-08-05 23:58:29 0.3515189
# 1992-08-06 23:58:11 0.2586106
```


# Pre-processing

A new function to create arbitrary lags and differences `createLagDiffFeatures()` has also been added. Notice that we get a weird doubel date thing, but our column names look nice

```{r}
Timeregr.task.lag = createLagDiffFeatures(Timeregr.task,lag = 2L:4L, difference = 1L, 
                                          seasonal.lag = 1L:2L)
tail(Timeregr.task.lag$env$data)
```
<!-- html table generated in R 3.3.1 by xtable 1.8-2 package -->
<!-- Mon Oct 31 20:48:43 2016 -->
<table border=1>
<tr> <th>  </th> <th> arma_test </th> <th> arma_test_lag2_diff1 </th> <th> arma_test_lag3_diff1 </th> <th> arma_test_lag4_diff1 </th> <th> arma_test_lag7_diff0 </th> <th> arma_test_lag14_diff0 </th>  </tr>
  <tr> <td align="right"> 1992-07-27 </td> <td align="right"> 0.89 </td> <td align="right"> -0.20 </td> <td align="right"> -0.60 </td> <td align="right"> -0.08 </td> <td align="right"> 0.02 </td> <td align="right"> 3.16 </td> </tr>
  <tr> <td align="right"> 1992-07-28 </td> <td align="right"> 1.44 </td> <td align="right"> 0.84 </td> <td align="right"> -0.20 </td> <td align="right"> -0.60 </td> <td align="right"> -0.68 </td> <td align="right"> 3.75 </td> </tr>
  <tr> <td align="right"> 1992-07-29 </td> <td align="right"> 3.44 </td> <td align="right"> 1.02 </td> <td align="right"> 0.84 </td> <td align="right"> -0.20 </td> <td align="right"> -0.08 </td> <td align="right"> 2.98 </td> </tr>
  <tr> <td align="right"> 1992-07-30 </td> <td align="right"> 4.09 </td> <td align="right"> 0.55 </td> <td align="right"> 1.02 </td> <td align="right"> 0.84 </td> <td align="right"> -0.16 </td> <td align="right"> 1.14 </td> </tr>
  <tr> <td align="right"> 1992-07-31 </td> <td align="right"> 3.49 </td> <td align="right"> 2.00 </td> <td align="right"> 0.55 </td> <td align="right"> 1.02 </td> <td align="right"> -0.76 </td> <td align="right"> 1.14 </td> </tr>
  <tr> <td align="right"> 1992-08-01 </td> <td align="right"> 2.02 </td> <td align="right"> 0.65 </td> <td align="right"> 2.00 </td> <td align="right"> 0.55 </td> <td align="right"> -0.96 </td> <td align="right"> 0.56 </td> </tr>
   </table>
   
A new preprocessing wrapper `makePreprocWrapperLambert()` has been added. This function uses the
`LambertW` package's `Guassianize()` function to help remove skewness and kurtosis from the data.

```{r}
lrn = makePreprocWrapperLambert("classif.lda", type = "h")
print(lrn)
# Learner classif.lda.preproc from package MASS
# Type: classif
# Name: ; Short name: 
# Class: PreprocWrapperLambert
# Properties: numerics,factors,prob,twoclass,multiclass
# Predict-Type: response
# Hyperparameters: type=h,methods=IGMM,verbose=FALSE

train(lrn, iris.task)
# Model for learner.id=classif.lda.preproc; learner.class=PreprocWrapperLambert
# Trained on: task.id = iris-example; obs = 150; features = 4
# Hyperparameters: type=h,methods=IGMM,verbose=FALSE
```

The lambert W transform is a bijective function, but the current preprocessing wrapper does not allow us to invert our predictions back to the actual values when we make new predictions. This would be helpful if we wanted to use LambertW on our target, then get back answers that match with our real values instead of the transformed values.

# Models

Several new models have been included from forecast:

1. Exponential smoothing state space model with Box-Cox transformation (bats)
2. Exponential smoothing state space model with Box-Cox transformation, ARMA errors, Trend and Seasonal Fourier components (tbats)
3. Exponential smoothing state space model (ets)
4. Neural Network Autoregressive model (nnetar)
5. Automated Arima (auto.arima)
6. General Autoregressive Conditional Heteroskedasticity models (GARCH)

The below code will run with the `Timeregr.task` we state above.
```{r}
batMod = makeLearner("fcregr.bats", h = 10)
m = train(batMod, Timeregr.task)
predict(m, task = Timeregr.task)

#fc.tbats
tBatsMod = makeLearner("fcregr.tbats", h = 10)
m = train(tBatsMod, Timeregr.task)
predict(m, task = Timeregr.task)

#fc.ets
etsMod = makeLearner("fcregr.ets", h = 10)
m = train(etsMod, Timeregr.task)
predict(m, task = Timeregr.task)

#fc.nnetar
nnetarMod = makeLearner("fcregr.nnetar", h = 10)
nnetarMod
m = train(nnetarMod, Timeregr.task)
predict(m, task = Timeregr.task)
```
The GARCH models come from the package `rugarch`, which has lists of parameter controls. 

```{r}
garchMod = makeLearner("fcregr.garch", model = "sGARCH",
                       garchOrder = c(1,1), n.ahead = 10,
                       armaOrder = c(2, 1))
m = train(garchMod, Timeregr.task)
predict(m,task = Timeregr.task)
```

### Tests

There are now tests for each of the forecasting models implimented here. However ets fails with a strange error from the `forecast` package's namespace

```{r}
devtools::test(filter = "fcregr")
# Loading mlr
# Testing mlr
# fcregr_arfima: .....
# fcregr_Arima: .....
# fcregr_bats: .....
# fcregr_ets: 1
# fcregr_garch: .....
# fcregr_tbats: .....
# learners_all_fcregr: ..............2Timing stopped at: 0.003 0 0.003 
# Failed --------------------------------------------------------------------------------------------------
# 1. Error: fcregr_ets (@test_fcregr_ets.R#20)  ------------------------------------------------------------
# "etsTargetFunctionInit" not resolved from current namespace (forecast)
# ...
# 2. Error: learners work: fcregr  (@test_learners_all_fcregr.R#21) ---------------------------------------
# "etsTargetFunctionInit" not resolved from current namespace (forecast)
```

I've posted an issue on `forecasts` github page and will email Dr. Hyndman (author) if he does not respond soon. The strangest thing about this error is that it only happens during testing. With a fresh restart of R you can use the ets functions just fine.

We also have tests for general forecasting and `createLagDiffFeatures`. The test for Lambert W pre-processing is available in the general forecasting tests.
```{r}
devtools::test(filter = "forecast")
Loading mlr
Testing mlr
forecast: .......

DONE ====================================================================================================

devtools::test(filter = "createLagDiffFeatures")
Loading mlr
Testing mlr
createLagDiffFeatures: ....

DONE ====================================================================================================

```

## Updating Models

A new function `updateModel()` has been implimented that is a sort of frankenstein between `train()` and `predict()`.

```{r}
Timeregr.task = makeForecastRegrTask(id = "test", data = dat[1:190,], target = "arma_test",
                                     frequency = 7L)
arm = makeLearner("fcregr.Arima", order = c(2L,0L,1L), h = 10L, include.mean = FALSE)
arm
armMod = train(arm, Timeregr.task)
updateArmMod = updateModel(armMod, Timeregr.task, newdata = dat[192:200,])
updateArmMod
# Model for learner.id=fcregr.Arima; learner.class=fcregr.Arima
# Trained on: task.id = test; obs = 9; features = 0
# Hyperparameters: order=2,0,1,h=10,include.mean=FALSE
```

This works by making a call to `updateLearner.fcregr.Arima()` and updating the model and task data with `newdata`. `predict()` works as it would on a normal model.

```{r}
predict(updateArmMod, task = Timeregr.task)
# Prediction: 10 observations
# predict.type: response
# threshold: 
# time: 0.01
#                      response
# 1992-07-22 23:59:40 1.3631309
# 1992-07-23 23:59:21 1.0226897
# 1992-07-24 23:59:02 0.7818986
# 1992-07-25 23:58:43 0.5996956
# 1992-07-26 23:58:24 0.4601914
# 1992-07-27 23:58:05 0.3531699
# ... (10 rows, 1 cols)
```

Other models with update functions include `ets`, `bats`, and `tbats`.

## Using ML models in forecasting

To use ML models for forecasting we have to create our autoregressive features using `createLagDiffFeatures`.
```{r}

daty = arima.sim(model = list(ar = c(.5,.2), ma = c(.4), order = c(2,0,1)), n = 200)
datx = daty + arima.sim(model = list(ar = c(.6,.1), ma = c(.3), order = c(2,0,1)), n = 200)
times = (as.POSIXlt("1992-01-14")) + lubridate::days(1:200)
dat = xts(data.frame(dat_y = daty,dat_x = datx),order.by = times)

train.dat = dat[1:190,]
test.dat = dat[191:200,]

Timeregr.task = makeForecastRegrTask(id = "test", data = train.dat,
                                     target = "dat_y", frequency = 7L)

Timeregr.ml = createLagDiffFeatures(Timeregr.task, lag = 5:12L, na.pad = FALSE, 
return.nonlag = FALSE, cols = NULL)
Timeregr.ml
# Task: test
# Type: regr
# Observations: 178
# Dates:
#  Start: 1992-01-27
#  End:   1992-07-22
# Frequency: 7
# Features:
# numerics  factors  ordered 
#       9        0        0 
# Missings: FALSE
# Has weights: FALSE
# Has blocking: FALSE

```

There are two important things to note here.

1. We are forecasting 5 periods ahead and so we start our lags 5 periods in the past.
2. Our type is now `regr`.

(1) happens because we want to forecast 5 periods ahead. The regular schema for forecasting models when you have only one variable is to use prediction `y_{t+1}` to predict `y_{t+2}` ,etc. This is reasonable when your target's forecast is only decided by past forecasts, however when you have multiple variables predicting your target this becomes very difficult. Take for example estimating `y_{t+2}` with `y_{t+1}` and `x_{1,t+1}` and `x_{2,t+1}` when you are at time `t`. Below we do this by using a forecasting model that treats all of the data as endogeneous and then stacking an ML model on top of our forecasts.

(2) happens because we did not select any particular columns to lag. If we selected no columns to lag we lag our target variable, which is not something that happens inside of `fcregr` models. If we had other variables and specified them in cols then we would receive back a `fcregr` task type.

Now, just as with a regular regression, we specify our learner and train it.

```{r}
regrGbm <- makeLearner("regr.gbm", par.vals = list(n.trees = 2000,
interaction.depth = 8,
distribution = "laplace"))
gbmMod = train(regrGbm, Timeregr.ml)
```

We now want to predict our new data, but notice that our lags are from 5 through 50 and our testing data is only 10 periods in length. If we did 50 lags we would receive back a bunch of `NA` columns. So to get around this we will do the following.

```{r}
Timeregr.gbm.update = updateLagDiff(Timeregr.ml,test.dat)
# Task: test
# Type: regr
# Observations: 188
# Dates:
#  Start: 1992-01-27 
#  End:   1992-08-01
# Frequency: 7
# Features:
# numerics  factors  ordered 
#       16        0        0 
# Missings: FALSE
# Has weights: FALSE
# Has blocking: FALSE
```

The function `updateData()` passes a task that was modified by `createLagDiffFeatures()` and the new data we would like to include. So now to make a prediction of the next 5 we simply subset our task by the last five observations. Here our last observation is 188 because we had an original data set of size 200 with 12 lag. 
```{r}
predict(gbmMod, task = Timeregr.gbm.update, subset = 184:188)
# Prediction: 5 observations
# predict.type: response
# threshold: 
# time: 0.00
#             id       truth    response
# 1992-07-28 184 -1.45613899  0.47721792
# 1992-07-29 185  0.09614877 -0.01412844
# 1992-07-30 186  0.90837420 -0.34565917
# 1992-07-31 187  0.39279490 -0.23385251
# 1992-08-01 188  0.29235931 -0.17127450
```

#### Ensemble's of Forecasts

It's known that ensembles of forecasts tend to outperform standard forecasting techniques. Here we use mlr's stacked modeling functionality to ensemble multiple forecast techniques by a super learner.

```{r}
library(xts)
library(lubridate)
dat = arima.sim(model = list(ar = c(.5,.2), ma = c(.4), order = c(2,0,1)), n = 500)
times = (as.POSIXlt("1992-01-14")) + lubridate::days(1:500)
dat = xts(dat,order.by = times)
colnames(dat) = c("arma_test")

dat.train = dat[1:490,]
dat.test  = dat[491:500,]

timeregr.task = makeForecastRegrTask(id = "test", data = dat.train, target = "arma_test",
                                     frequency = 7L)
                                     
resamp.sub = makeResampleDesc("GrowingCV",
                          horizon = 10L,
                          initial.window = .90,
                          size = nrow(getTaskData(timeregr.task)),
                          skip = .01
                          )
                          
resamp.super = makeResampleDesc("CV", iters = 5)

base = c("fcregr.tbats", "fcregr.bats")
lrns = lapply(base, makeLearner)
lrns = lapply(lrns, setPredictType, "response")

super.ps = makeParamSet(
  makeIntegerParam("penalty", lower = 1, upper = 2)
)
# Making a tune wrapped super learner lets us tune the super learner as well
super.mod = makeTuneWrapper(makeLearner("regr.earth"), resampling = resamp.super,
                            par.set = super.ps, control = makeTuneControlGrid() )
                            
stack.forecast = makeStackedLearner(base.learners = lrns,
                       predict.type = "response",
                       method = "growing.cv",
                       super.learner = super.mod,
                       resampling = resamp.sub)

ps = makeParamSet(
  makeDiscreteParam("fcregr.tbats.h", values = 10),
  makeDiscreteParam("fcregr.bats.h", values = 10)
)

## tuning
fore.tune = tuneParams(stack.forecast, timeregr.task, resampling = resamp.sub,
                   par.set = ps, control = makeTuneControlGrid(),
                   measures = mase)

stack.forecast.tune  = setHyperPars2(stack.forecast,fore.tune$x)
stack.forecast.tune
# Learner stack from package forecast
# Type: fcregr
# Name: ; Short name: 
# Class: StackedLearner
# Properties: numerics,quantile
# Predict-Type: response
# Hyperparameters: fcregr.tbats.h=10,fcregr.bats.h=10
stack.forecast.mod = train(stack.forecast.tune,timeregr.task)
stack.forecast.pred = predict(stack.forecast.mod, newdata = dat.test)
stack.forecast.pred
# Prediction: 10 observations
# predict.type: response
# threshold: 
# time: 0.01
#                  truth  response
# 1993-05-19 -1.01871981 1.0345043
# 1993-05-20 -0.46402313 1.0345043
# 1993-05-21 -1.35142909 1.0345043
# 1993-05-22 -1.36870018 1.0345043
# 1993-05-23 -0.06109219 0.4907984
# 1993-05-24 -0.02565932 0.8070644
# ... (10 rows, 2 cols)
performance(stack.forecast.pred, mase, task = timeregr.task)
#       mase 
# 0.03487724 
```


## Multivariate Forecasting

One common problem with forecasting is that it is difficult to use additional explanatory variables. If we are at time `t` and want to forecast 10 periods in the future, we need to know the values of the explanatory variables at time `t+10`, which is often not possible. A new set of models which treats explanatory variables endogenously instead of exogenously allows us to forecast not only our target, but addititional explanatory variables. This is done by treating all the variables as targets.

```{r}
library(lubridate)
library(xts)
data("EuStockMarkets")
stock.times = date_decimal(as.numeric(time(EuStockMarkets)))
stock.data  = xts(as.data.frame(EuStockMarkets), order.by = stock.times)
stock.data.train = stock.data[1:1850,]
stock.data.test  = stock.data[1851:1855,]
multfore.task = makeMultiForecastRegrTask(id = "bigvar", data = stock.data.train, target = "all")
multfore.task
# Supervised task: bigvar
# Type: mfcregr
# Target: DAX,SMI,CAC,FTSE
# Observations: 1850
# Features:
# numerics  factors  ordered 
#        0        0        0 
# Missings: FALSE
# Has weights: FALSE
# Has blocking: FALSE

bigvar.learn = makeLearner("mfcregr.BigVAR", p = 4, struct = "Basic", gran = c(25, 10),
                    h = 5, n.ahead = 5)
multi.train = train(bigvar.learn, multfore.task)
pred  = predict(multi.train, newdata = stock.data.test)
pred
```
<!-- html table generated in R 3.3.1 by xtable 1.8-2 package -->
<!-- Sat Oct 29 19:51:43 2016 -->
<table border=1>
<tr> <th>  </th> <th> truth.DAX </th> <th> truth.SMI </th> <th> truth.CAC </th> <th> truth.FTSE </th> <th> response.DAX </th> <th> response.SMI </th> <th> response.CAC </th> <th> response.FTSE </th>  </tr>
  <tr> <td align="right"> 1998-08-12 05:04:36 </td> <td align="right"> 5774.38 </td> <td align="right"> 8139.20 </td> <td align="right"> 4095.00 </td> <td align="right"> 5809.70 </td> <td align="right"> 5826.03 </td> <td align="right"> 8233.48 </td> <td align="right"> 4117.87 </td> <td align="right"> 5937.16 </td> </tr>
  <tr> <td align="right"> 1998-08-13 14:46:09 </td> <td align="right"> 5718.70 </td> <td align="right"> 8170.20 </td> <td align="right"> 4047.90 </td> <td align="right"> 5736.10 </td> <td align="right"> 5799.61 </td> <td align="right"> 8228.52 </td> <td align="right"> 4072.32 </td> <td align="right"> 6013.65 </td> </tr>
  <tr> <td align="right"> 1998-08-15 00:27:41 </td> <td align="right"> 5614.77 </td> <td align="right"> 7943.20 </td> <td align="right"> 3976.40 </td> <td align="right"> 5632.50 </td> <td align="right"> 5787.13 </td> <td align="right"> 8229.91 </td> <td align="right"> 4037.78 </td> <td align="right"> 6075.88 </td> </tr>
  <tr> <td align="right"> 1998-08-16 10:09:13 </td> <td align="right"> 5528.12 </td> <td align="right"> 7846.20 </td> <td align="right"> 3968.60 </td> <td align="right"> 5594.10 </td> <td align="right"> 5777.30 </td> <td align="right"> 8233.20 </td> <td align="right"> 4008.40 </td> <td align="right"> 6127.79 </td> </tr>
  <tr> <td align="right"> 1998-08-17 19:50:46 </td> <td align="right"> 5598.32 </td> <td align="right"> 7952.90 </td> <td align="right"> 4041.90 </td> <td align="right"> 5680.40 </td> <td align="right"> 5767.11 </td> <td align="right"> 8235.93 </td> <td align="right"> 3981.94 </td> <td align="right"> 6171.05 </td> </tr>
   </table>
   
```{r}
performance(pred, multivar.mase, task = test)
# multivar.mase 
#   0.02458401 
```

### Multivariate Forecasting with ML stacking

Now that we have a multivariate form of forecasting, we can use these forecasts stacked with a machine learning super learner.

```{r}
resamp.sub = makeResampleDesc("GrowingCV",
                              horizon = 5L,
                              initial.window = .97,
                              size = nrow(getTaskData(multfore.task)),
                              skip = .01
)

resamp.super = makeResampleDesc("CV", iters = 3)

base = c("mfcregr.BigVAR")
lrns = lapply(base, makeLearner)
lrns = lapply(lrns, setPredictType, "response")
stack.forecast = makeStackedLearner(base.learners = lrns,
                                    predict.type = "response",
                                    super.learner = makeLearner("regr.earth", penalty = 2),
                                    method = "growing.cv",
                                    resampling = resamp.sub)

ps = makeParamSet(
  makeDiscreteParam("mfcregr.BigVAR.p", values = 5),
  makeDiscreteParam("mfcregr.BigVAR.struct", values = "Basic"),
  makeNumericVectorParam("mfcregr.BigVAR.gran", len = 2L, lower = 25, upper = 26),
  makeDiscreteParam("mfcregr.BigVAR.h", values = 5),
  makeDiscreteParam("mfcregr.BigVAR.n.ahead", values = 5)
)

## tuning

multfore.task = makeMultiForecastRegrTask(id = "bigvar", data = stock.data.train, target = "FTSE")

multfore.tune = tuneParams(stack.forecast, multfore.task, resampling = resamp,
                   par.set = ps, control = makeTuneControlGrid(),
                   measures = multivar.mase)
multfore.tune
# Tune result:
# Op. pars: mfcregr.BigVAR.p=5; mfcregr.BigVAR.struct=Basic; mfcregr.BigVAR.gran=26,25; 
# mfcregr.BigVAR.h=5; mfcregr.BigVAR.n.ahead=5
# multivar.mase.test.mean=0.0183

stack.forecast.f  = setHyperPars2(stack.forecast,multfore.tune$x)
multfore.train = train(stack.forecast.f,multfore.task)
multfore.train
# Model for learner.id=stack; learner.class=StackedLearner
# Trained on: task.id = bigvar; obs = 1850; features = 3
# Hyperparameters: mfcregr.BigVAR.p=5,mfcregr.BigVAR.struct=Basic,mfcregr.BigVAR.gran=26,25,mfcregr.
# BigVAR.h=5,mfcregr.BigVAR.n.ahead=5

multfore.pred = predict(multfore.train, newdata = stock.data.test)
multfore.pred
# Prediction: 5 observations
# predict.type: response
# threshold: 
# time: 0.01
#                      truth response
# 1998-08-12 05:04:36 5809.7 6024.293
# 1998-08-13 14:46:09 5736.1 6022.679
# 1998-08-15 00:27:41 5632.5 6022.640
# 1998-08-16 10:09:13 5594.1 6024.083
# 1998-08-17 19:50:46 5680.4 6025.666

performance(multfore.pred, mase, task = multfore.task)
#      mase 
# 0.04215817 
```
