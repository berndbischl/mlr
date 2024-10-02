# mlr 2.19.2

- Make tests run with suggested packages absent.
- Remove bartMachine learner (#2851)

# mlr 2.19.1

## Bug fixes

- Adjust behavior of `"positive"` arg for `classif.logreg` (#2846)
- Consistent naming for dummy feature encoding of variables with different levels count (#2847)
- Remove {nodeHarvest} learners (#2841)
- Remove {rknn} learner (#2842)
- Remove all {DiscriMiner} learners (#2840)
- Remove {extraTrees} learner (#2839)
- Remove depcrecated {rrlda} learner
- Resolve some {ggplot} deprecation warnings
- Fixed `information.gain` filter calculation.
  Before, `chi.squared` was calculated even though `information.gain` was requested due to a glitch in the filter naming (#2816, @jokokojote)
- Make `helpLearnerParam()`'s HTML parsing more robust (#2843)
- Add HTML5 support for help pages

# mlr 2.19.0

- Add filter `FSelectoRcpp::relief()`. This C++ based implementation of the RelieF filter algorithm is way faster than the Java based one from the {FSelector} package (#2804)
- Fix S3 print method for `FilterWrapper` objects
- Make ibrier measure work with survival tasks (#2789)
- Switch to testthat v3 (#2796)
- Enable parallel tests (#2796)
- Replace package PMCMR by PMCMRplus (#2796)
- Remove CoxBoost learner due to CRAN removal
- Warning if `fix.factors.prediction = TRUE` causes the generation of NAs for new factor levels in prediction (@jakob-r, #2794)
- Clear error message if prediction of wrapped learner has not the same length as `newdata` (@jakob-r, #2794)

# mlr 2.18.0

- Many praznik filters are now also able to deal with regression tasks (#2790, @bommert)
- `praznik_MRMR`: Remove handling of survival tasks (#2790, @bommert)
- xgboost: update `objective` default from `reg:linear` (deprecated) to `reg:squarederror`
- issue a warning if `blocking` was set in the Task but `blocking.cv` was not set within `makeResampleDesc() (#2788)
- Fix order of learners in `generateLearningCurveData()` (#2768)
- `getFeatureImportance()`: Account for feature importance weight of linear xgboost models
- Fix learner note for learner glmnet (the default of param `s` did not match the learner note) (#2747)
- Remove dependency {hrbrthemes} used in `createSpatialResamplingPlots()`. The package caused issues on R-devel. In addition users should set custom themes by themselves.
- Explicitly return value in `getNestedTuneResultsOptPathDf()` (#2754)

# mlr 2.17.1

## Learners - bugfixes

- remove `regr_slim` learner due to pkg (flare) being orphaned on CRAN

## Measures - bugixes

- remove measure `clValid::dunn` and its tests (package orphaned) (#2742)
- Bugfix: `tuneThreshold()` now accounts for the direction of the measure.
  Beforehand, the performance measure was always minimized (#2732).
- Remove adjusted Rsq measure (arsq), fixes #2711

## Filters - bugfixes

- Fixed an issue which caused the random forest minimal depth filter to only return NA values when using thresholding.
  NAs should only be returned for features below the given threshold. (@annette987, #2710)
- Fixed problem which prevented passing filter options via argument `more.args` for simple filters (@annette987, #2709)

## Feature selection - bugfixes

- Fix `print.FeatSelResult()` when bits.to.features is used in `selectFeatures()` (#2721)
- Return a long DF for `getFeatureImportance()` (#2708)

## Misc

- pkgdown: Move changelog to Appendix
- Account for {checkmate} v2.0.0 update (#2734)

- Refactor function calls from packages (`<pkg::fun>`) within ParamSets (#2730) to avoid errors in `listLearners()` if those pkgs are not installed
- `listLearners()` should not fail if a package is not installed (#2717)

# mlr 2.17.0.9003

- remove measure `clValid::dunn` and its tests (package orphaned) (#2742)
- Refactor function calls from packages (`<pkg::fun>`) within ParamSets (#2730) to avoid errors in `listLearners()` if those pkgs are not installed
- remove `regr_slim` learner due to pkg (flare) being orphaned on CRAN

# mlr 2.17.0.9002

- Bugfix: `tuneThreshold()` now accounts for the direction of the measure.
  Beforehand, the performance measure was always minimized (#2732).
- pkgdown: Move changelog to Appendix
- Account for {checkmate} v2.0.0 update (#2734)

# mlr 2.17.0.9001

- Fix `print.FeatSelResult()` when bits.to.features is used in `selectFeatures()` (#2721)
- Return a long DF for `getFeatureImportance()` (#2708)
- Remove adjusted Rsq measure (arsq), fixes #2711
- `listLearners()` should not fail if a package is not installed (#2717)

# mlr 2.17.0.9000

## filters - bugfixes

- Fixed an issue which caused the random forest minimal depth filter to only return NA values when using thresholding.
  NAs should only be returned for features below the given threshold. (@annette987, #2710)
- Fixed problem which prevented passing filter options via argument `more.args` for simple filters (@annette987, #2709)

# mlr 2.17.0

## plotting

- `n.show` argument had no effect in `plotFilterValues()`. Thanks @albersonmiranda. (#2689)

## Functional Data

PR: #2638 (@pfistl)

- Added several learners for regression and classification on functional data

  - classif.classiFunc.(kernel|knn) (knn/kernel using various semi-metrics)
  - (classif|regr).fgam (Functional generalized additive models)
  - (classif|regr).FDboost (Boosted functional generalized additive models)

- Added preprocessing steps for feature extraction from functional data

  - extractFDAFourier (Fourier transform)
  - extractFDAWavelets (Wavelet features)
  - extractFDAFPCA (Principal components)
  - extractFDATsfeatures (Time-Series features from tsfeatures package)
  - extractFDADTWKernel (Dynamic Time-Warping Kernel)
  - extractFDAMultiResFeatures (Compute features at multiple resolutions)

- Fixed a bug where multiclass to binaryclass reduction techniques did not work
  with functional data.

- Several other minor bug fixes and code improvements
- Extended and clarified documentation for several fda components.

## learners - general

- xgboost: added options 'auto', 'approx' and 'gpu_hist' to param `tree_method` (@albersonmiranda, #2701)
- `getFeatureImportance()` now returns a long data.frame with columns `variable` and `importance`.
  Beforehand, a wide data.frame was returned with each variable representing a column (@pat-s, #1755).

## filters - general

- Allow a custom threholding function to be passed to filterFeatures and makeFilterWrapper (@annette987, #2686)
- Allow ensemble filters to include multiple base filters of the same type (@annette987, #2688)

## filters - bugfixes

- `filterFeatures()`: Arg `thresh` was not working correctly when applied to ensemble filters. (@annette987, #2699)
- Fixed incorrect ranking of ensemble filters. Thanks @annette987 (#2698)

# mlr 2.16.0

## package infrastructure

- There is now a reference grouping for all functions on the pkgdown site (https://mlr.mlr-org.com/reference/index.html)
- CI testing now only on Circle CI (previously Travis CI)

## learners - general

- fixed a bug in `classif.xgboost` which prevented passing a watchlist for binary tasks. This was caused by a suboptimal internal label inversion approach. Thanks to @001ben for reporting (#32) (@mllg)
- update `fda.usc` learners to work with package version >=2.0
- update `glmnet` learners to upstream package version 3.0.0
- update `xgboost` learners to upstream version 0.90.2 (@pat-s & @be-marc, #2681)
- Updated ParamSet for learners `classif.gbm` and `regr.gbm`. Specifically, param `shrinkage` now defaults to 0.1 instead of 0.001. Also more choices for param `distribution` have been added. Internal parallelization by the package is now suppressed (param `n.cores`). (@pat-s, #2651)
- Update parameters for `h2o.deeplearning` learners (@albersonmiranda, #2668)

## misc

- Add `configureMlr()` to `.onLoad()`, possibly fixing some edge cases (#2585) (@pat-s, #2637)

## learners - bugfixes

- `h2o.gbm` learners were not running until `wcol` was passed somehow due to an internal bug. In addition, this bug caused another issue during prediction where the prediction `data.frame` was somehow formatted as a character rather a numeric. Thanks to @nagdevAmruthnath for bringing this up in #2630.

## filters - general

- Bugfix: Allow `method = "vh"` for filter `randomForestSRC_var.select` and return informative error message for not supported values. Also argument `conservative` can now be passed. See #2646 and #2639 for more information (@pat-s, #2649)
- Bugfix: Allow `method = "md"` of filter `randomForestSRC_var.select` to set the value returned for features below its threshold to NA (Issue #2687)

* Bugfix: With the new _praznik_ v7.0.0 release filter `praznik_CMIM` does no longer return a result for logical features. See https://gitlab.com/mbq/praznik/issues/19 for more information

# mlr 2.15.0

## Breaking

- Instead of a wide `data.frame` filter values are now returned in a long (tidy) `tibble`. This makes it easier to apply post-processing methods (like `group_by()`, etc) (@pat-s, #2456)
- `benchmark()` does not store the tuning results (`$extract` slot) anymore by default.
  If you want to keep this slot (e.g. for post tuning analysis), set `keep.extract = TRUE`.
  This change originated from the fact that the size of `BenchmarkResult` objects with extensive tuning got very large (~ GB) which can cause memory problems during runtime if multiple `benchmark()` calls are executed on HPCs.
- `benchmark()` does not store the created models (`$models` slot) anymore by default.
  The reason is the same as for the `$extract` slot above.
  Storing can be enabled using `models = TRUE`.

## functions - general

- `generateFeatureImportanceData()` gains argument `show.info` which shows the name of the current feature being calculated, its index in the queue and the elapsed time for each feature (@pat-s, #26222)

## learners - general

- `classif.liquidSVM` and `regr.liquidSVM` have been removed because `liquidSVM` has been removed from CRAN.
- fixed a bug that caused an incorrect aggregation of probabilities in some cases. The bug existed since quite some time and was exposed due to the change of `data.table`s default in `rbindlist()`. See #2578 for more information. (@mllg, #2579)
- `regr.randomForest` gains three new methods to estimate the standard error:
  - `se.method = "jackknife"`
  - `se.method = "bootstrap"`
  - `se.method = "sd"`
    See `?regr.randomForest` for more details.
    `regr.ranger` relies on the functions provided by the package ("jackknife" and "infjackknife" (default))
    (@jakob-r, #1784)
- `regr.gbm` now supports `quantile distribution` (@bthieurmel, #2603)
- `classif.plsdaCaret` now supports multiclass classification (@GegznaV, #2621)

## functions - general

- `getClassWeightParam()` now also works for Wrapper\* Models and ensemble models (@ja-thomas, #891)
- added `getLearnerNote()` to query the "Note" slot of a learner (@alona-sydorova, #2086)
- `e1071::svm()` now only uses the formula interface if factors are present. This change is supposed to prevent from "stack overflow" issues some users encountered when using large datasets. See #1738 for more information. (@mb706, #1740)

## learners - new

- add learner `cluster.MiniBatchKmeans` from package _ClusterR_ (@Prasiddhi, #2554)

## function - general

- `plotHyperParsEffect()` now supports facet visualization of hyperparam effects for nested cv (@MasonGallo, #1653)
- fixed a bug that caused an incorrect aggregation of probabilities in some cases. The bug existed since quite some time and was exposed due to the change of `data.table`s default in `rbindlist()`. See #2578 for more information. (@mllg, #2579)
- fixed a bug in which `options(on.learner.error)` was not respected in `benchmark()`. This caused `benchmark()` to stop even if it should have continued including `FailureModels` in the result (@dagola, #1984)
- `getClassWeightParam()` now also works for Wrapper\* Models and ensemble models (@ja-thomas, #891)
- added `getLearnerNote()` to query the "Note" slot of a learner (@alona-sydorova, #2086)

## filters - general

- Filter `praznik_mrmr` also supports `regr` and `surv` tasks
- `plotFilterValues()` got a bit "smarter" and easier now regarding the ordering of multiple facets. (@pat-s, #2456)
- `filterFeatures()`, `generateFilterValuesData()` and `makeFilterWrapper()` gained new examples. (@pat-s, #2456)

## filters - new

- Ensemble features are now supported. These filters combine multiple single filters to create a final ranking based on certain statistical operations. All new filters are listed in a dedicated section "ensemble filters" in the [tutorial](https://mlr.mlr-org.com/articles/tutorial/filter_methods.html).
  Tuning of simple features is not supported yet because of a [missing feature](https://github.com/mlr-org/ParamHelpers/pull/206) in _ParamHelpers_. (@pat-s, #2456)

# mlr 2.14.0

## general

- add option to use fully predefined indices in resampling (`makeResampleDesc(fixed = TRUE)`) (@pat-s, #2412).
- `Task` help pages are now split into separate ones, e.g. `RegrTask`, `ClassifTask` (@pat-s, #2564)

## functions - new

- `deleteCacheDir()`: Clear the default mlr cache directory (@pat-s, #2463)
- `getCacheDir()`: Return the default mlr cache directory (@pat-s, #2463)

## functions - general

- `getResamplingIndices(inner = TRUE)` now correctly returns the inner indices (before inner indices referred to the subset of the respective outer level train set) (@pat-s, #2413).

## filter - general

- Caching is now used when generating filter values.
  This means that filter values are only computed once for a specific setting and the stored cache is used in subsequent iterations.
  This change inherits a significant speed-up when tuning `fw.perc`, `fw.abs` or `fw.threshold`.
  It can be triggered with the new `cache` argument in `makeFilterWrapper()` or `filterFeatures()` (@pat-s, #2463).

## filter - new

- praznik_JMI
- praznik_DISR
- praznik_JMIM
- praznik_MIM
- praznik_NJMIM
- praznik_MRMR
- praznik_CMIM
- FSelectorRcpp_gain.ratio
- FSelectorRcpp_information.gain
- FSelectorRcpp_symuncert

Additionally, filter names have been harmonized using the following scheme: <pkgname>\_<filtername>.
Exeptions are filters included in base R packages.
In this case, the package name is omitted.

## filter - general

- Added filters `FSelectorRcpp_gain.ratio`, `FSelectorRcpp_information.gain` and `FSelectorRcpp_symmetrical.uncertainty` from package `FSelectorRcpp`.
  These filters are ~ 100 times faster than the implementation of the `FSelector` pkg.
  Please note that both implementations do things slightly different internally and the `FSelectorRcpp` methods should not be seen as direct replacement for the `FSelector` pkg.
- filter names have been harmonized using the following scheme: <pkgname>\_<filtername>. (@pat-s, #2533)

  - `information.gain` -> `FSelector_information.gain`
  - `gain.ratio` -> `FSelector_gain.ratio`
  - `symmetrical.uncertainty` -> `FSelector_symmetrical.uncertainty`
  - `chi.squared` -> `FSelector_chi.squared`
  - `relief` -> `FSelector_relief`
  - `oneR` -> `FSelector_oneR`
  - `randomForestSRC.rfsrc` -> `randomForestSRC_importance`
  - `randomForestSRC.var.select` -> `randomForestSRC_var.select`
  - `randomForest.importance` -> `randomForest_importance`

- fixed a bug related to the loading of namespaces for required filter packages (@pat-s, #2483)

## learners - new

- classif.liquidSVM (@PhilippPro, #2428)
- regr.liquidSVM (@PhilippPro, #2428)

## learners - general

- regr.h2o.gbm: Various parameters added, `"h2o.use.data.table" = TRUE` is now the default (@j-hartshorn, #2508)
- h2o learners now support getting feature importance (@markusdumke, #2434)

## learners - fixes

- In some cases the optimized hyperparameters were not applied in the performance level of a nested CV (@berndbischl, #2479)

## featSel - general

- The FeatSelResult object now contains an additional slot `x.bit.names` that stores the optimal bits
- The slot `x` now always contains the real feature names and not the bit.names
- This fixes a bug and makes `makeFeatSelWrapper` usable with custom `bit.names`.
- Fixed a bug due to which `sffs` crashed in some cases (@bmihaljevic, #2486)

# mlr 2.13:

## general

- Disabled unit tests for CRAN, we test on travis only now
- Suppress messages with show.learner.output = FALSE

## functions - general

- plotHyperParsEffect: add colors

## functions - new

- getResamplingIndices
- createSpatialResamplingPlots

## learners - general

- regr.nnet: Removed unneeded params linout, entropy, softmax and censored
- regr.ranger: Add weight handling

## learners - removed

- {classif,regr}.blackboost: broke API with new release
- regr.elmNN : package was removed from CRAN
- classif.lqa : package was removed from CRAN

# mlr 2.12:

## general

- Support for functional data (fda) using matrix columns has been added.
- Relaxed the way wrappers can be nested -- the only explicitly forbidden
  combination is to wrap a tuning wrapper around another optimization wrapper
- Refactored the resample progress messages to give a better overview and
  distinguish between train and test measures better
- calculateROCMeasures now returns absolute instead of relative values
- Added support for spatial data by providing spatial partitioning methods "SpCV" and "SpRepCV".
- Added new spatial.task classification task.
- Added new spam.task classification task.
- Classification tasks now store the class distribution in the
  class.distribution member.
- mlr now predicts NA for data that contains NA and learners that do not support
  missing values.
- Tasks are now subsetted in the "train" function and the factor levels (for
  classification tasks) based on this subset. This means that the factor level
  distribution is not necessarily the same as for the entire task, and that the
  task descriptions of models in resampling reflect the respective subset, while
  the task description of resample predictions reflect the entire task and not
  necessarily the task of any individual model.
- Added support for growing and fixed window cross-validation for forecasting
  through new resample methods "GrowingWindowCV" and "FixedWindowCV".

## functions - general

- generatePartialDependenceData: depends now on the "mmpf" package,
  removed parameter: "center", "resample", "fmin", "fmax" and "gridsize"
  added parameter: "uniform" and "n" to configure the grid for the partial dependence plot
- batchmark: allow resample instances and reduction of partial results
- resample, performance: new flag "na.rm" to remove NAs during aggregation
- plotTuneMultiCritResultGGVIS: new parameters "point.info" and "point.trafo" to
  control interactivity
- calculateConfusionMatrix: new parameter "set" to specify whether confusion
  matrix should be computed for "train", "test", or "both" (default)
- PlotBMRSummary: Add parameter "shape"
- plotROCCurves: Add faceting argument
- PreprocWrapperCaret: Add param "ppc.corr", "ppc.zv", "ppc.nzv", "ppc.n.comp", "ppc.cutoff", "ppc.freqCut", "ppc.uniqueCut"

## functions - new

- makeClassificationViaRegressionWrapper
- getPredictionTaskDesc
- helpLearner, helpLearnerParam: open the help for a learner or get a
  description of its parameters
- setMeasurePars
- makeFunctionalData
- hasFunctionalFeatures
- extractFDAFeatures, reextractFDAFeatures
- extractFDAFourier, extractFDAFPCA, extractFDAMultiResFeatures, extractFDAWavelets
- makeExtractFDAFeatMethod
- makeExtractFDAFeatsWrapper
- getTuneResultOptPath
- makeTuneMultiCritControlMBO: Allows model based multi-critera / multi-objective optimization using mlrMBO

## functions - removed

- Removed plotViperCharts

## measures - general

- measure "arsq" now has ID "arsq"
- measure "measureMultiLabelF1" was renamed to "measureMultilabelF1" for consistency

## measures - new

- measureBER, measureRMSLE, measureF1
- cindex.uno, iauc.uno

## learners - general

- unified {classif,regr,surv}.penalized{ridge,lasso,fusedlasso} into {classif,regr,surv}.penalized
- fixed a bug where surv.cforest gave wrong risk predictions (#1833)
- fixed bug where classif.xgboost returned NA predictions with multi:softmax
- classif.lda learner: add 'prior' hyperparameter
- ranger: update hyperpar 'respect.unordered.factors', add 'extratrees' and 'num.random.splits'
- h20deeplearning: Rename hyperpar 'MeanSquare' to 'Quadratic'
- h20\*: Add support for "missings"

## learners - new

- classif.adaboostm1
- classif.fdaknn
- classif.fdakernel
- classif.fdanp
- classif.fdaglm
- classif.mxff
- regr.fdaFDboost
- regr.mxff

## learners - removed

- {classif,regr}.bdk: broke our API, stability issues
- {classif,regr}.xyf: broke our API, stability issues
- classif.hdrda: package removed from CRAN
- surv.penalized: stability issues

## aggregations - new

- testgroup.sd

## filter - new

- auc
- ranger.permutation, ranger.impurity

# mlr 2.11:

## general

- The internal class naming of the task descriptions have been changed causing probable incompatibilities with tasks generated under old versions.
- New option on.error.dump to include dumps that can be inspected with the
  debugger with errors
- mlr now supports tuning with Bayesian optimization with mlrMBO

## functions - general

- tuneParams: fixed a small and obscure bug in logging for extremely large ParamSets
- getBMR-operators: now support "drop" argument that simplifies the resulting list
- configureMlr: added option "on.measure.not.applicable" to handle situations where performance
  cannot be calculated and one wants NA instead of an error - useful in, e.g., larger benchmarks
- tuneParams, selectFeatures: removed memory stats from default output for
  performance reasons (can be restored by using a control object with "log.fun"
  = "memory")
- listLearners: change check.packages default to FALSE
- tuneParams and tuneParamsMultiCrit: new parameter `resample.fun` to specify a custom resampling function to use.
- Deprecated: getTaskDescription, getBMRTaskDescriptions, getRRTaskDescription.
  New names: getTaskDesc, getBMRTaskDescs, getRRTaskDesc.

## functions - new

- getOOBPreds: get out-of-bag predictions from trained models for learners that store them -- these learners have the new "oobpreds" property
- listTaskTypes, listLearnerProperties
- getMeasureProperties, hasMeasureProperties, listMeasureProperties
- makeDummyFeaturesWrapper: fuse a learner with a dummy feature creator
- simplifyMeasureNames: shorten measure names to the actual measure, e.g.
  mmce.test.mean -> mmce
- getFailureModelDump, getPredictionDump, getRRDump: get error dumps
- batchmark: Function to run benchmarks with the batchtools package on high performance computing clusters
- makeTuneControlMBO: allows Bayesian optimization

## measures - new

- kendalltau, spearmanrho

## learners - general

- classif.plsdaCaret: added parameter "method".
- regr.randomForest: refactored se-estimation code, improved docs and default is now se.method = "jackknife".
- regr.xgboost, classif.xgboost: removed "factors" property as these learners do not handle categorical features
  -- factors are silently converted to integers internally, which may misinterpret the structure of the data
- glmnet: control parameters are reset to factory settings before applying
  custom settings and training and set back to factory afterwards

## learners - removed

- {classif,regr}.avNNet: no longer necessary, mlr contains a bagging wrapper

# mlr 2.10:

## functions - general

- fixed bug in resample when using predict = "train" (issue #1284)
- update to irace 2.0 -- there are algorithmic changes in irace that may affect
  performance
- generateFilterValuesData: fixed a bug wrt feature ordering
- imputeLearner: fixed a bug when data actually contained no NAs
- print.Learner: if a learner hyperpar was set to value "NA" this was not
  displayed in printer
- makeLearner, setHyperPars: if you mistype a learner or hyperpar name, mlr
  uses fuzzy matching to suggest the 3 closest names in the message
- tuneParams: tuning with irace is now also parallelized, i.e., different
  learner configs are evaluated in parallel.
- benchmark: mini fix, arg 'learners' now also accepts class strings
- object printers: some mlr printers show head previews of data.frames.
  these now also print info on the total nr of rows and cols and are less confusing
- aggregations: have better properties now, they know whether they require training or
  test set evals
- the filter methods have better R docs
- filter randomForestSRC.var.select: new arg "method"
- filter mrmr: fixed some smaller bugs and updated properties
- generateLearningCurveData: also accepts single learner, does not require a list
- plotThreshVsPerf: added "measures" arg
- plotPartialDependence: can create tile plots with joint partial dependence
  on two features for multiclass classification by facetting across the classes
- generatePartialDependenceData and generateFunctionalANOVAData: expanded
  "fun" argument to allow for calculation of weights
- new "?mlrFamilies" manual page which lists all families and the functions
  belonging to it
- we are converging on data.table as a standard internally, this should not
  change any API behavior on the outside, though
- generateHyperParsEffectData and plotHyperParsEffect now support more than 2
  hyperparameters
- linear.correlation, rank.correlation, anova.test: use Rfast instead of
  FSelector/custom implementation now, performance should be much better
- use of our own colAUC function instead of the ROCR package for AUC calculation
  to improve performance
- we output resample performance messages for every iteration now
- performance improvements for the auc measure
- createDummyFeatures supports vectors now
- removed the pretty.names argument from plotHyperParsEffect -- labels can be set
  though normal ggplot2 functions on the returned object
- Fixed a bad bug in resample, the slot "runtime" or a ResampleResult,
  when the runtime was measured not in seconds but e.g. mins. R measures then potentially in mins,
  but mlr claimed it would be seconds.
- New "dummy" learners (that disregard features completely) can be fitted now for baseline comparisons,
  see "featureless" learners below.

## functions - new

- filter: randomForest.importance
- generateFeatureImportanceData: permutation-based feature importance and local
  importance
- getFeatureImportanceLearner: new Learner API function
- getFeatureImportance: top level function to extract feature importance
  information
- calculateROCMeasures
- calculateConfusionMatrix: new confusion-matrix like function that calculates
  and tables many receiver operator measures
- makeLearners: create multiple learners at once
- getLearnerId, getLearnerType, getLearnerPredictType, getLearnerPackages
- getLearnerParamSet, getLearnerParVals
- getRRPredictionList
- addRRMeasure
- plotResiduals
- getLearnerShortName
- mergeBenchmarkResults

## functions - renamed

- Renamed rf.importance filter (now deprecated) to randomForestSRC.var.rfsrc
- Renamed rf.min.depth filter (now deprecated) to randomForestSRC.var.select
- Renamed getConfMatrix (now deprecated) to calculateConfusionMatrix
- Renamed setId (now deprecated) to setLearnerId

## functions - removed

- mergeBenchmarkResultLearner, mergeBenchmarkResultTask

## learners - general

- classif.ada: fixed some param problem with rpart.control params
- classif.cforest, regr.cforest, surv.cforest:
  removed parameters "minprob", "pvalue", "randomsplits"
  as these are set internally and cannot be changed by the user
- regr.GPfit: some more params for correlation kernel
- classif.xgboost, regr.xgboost: can now properly handle NAs (property was missing and other problems), added "colsample_bylevel" parameter
- adapted {classif,regr,surv}.ranger parameters for new ranger version

## learners - new

- multilabel.cforest
- surv.gbm
- regr.cvglmnet
- {classif,regr,surv}.gamboost
- classif.earth
- {classif,regr}.evtree
- {classif,regr}.evtree

## learners - removed

- classif.randomForestSRCSyn, regr.randomForestSRCSyn: due to continued stability issues

## measures - new

- ssr, qsr, lsr
- rrse, rae, mape
- kappa, wkappa
- msle, rmsle

# mlr 2.9:

## functions - general

- various cleanups that removed unused code
- subsetTask, getTaskData: arg "features" now also accepts logical and integer
- removeConstantFeatures now also operates on data.frames and
  makeRemoveConstantFeaturesWrapper can be used to augment a learner with this
  preprocessing step.
- normalizeFeatures, createDummyFeatures: arg 'exclude' was replaced by 'cols'
- normalizeFeatures is now S3 and can be called also on data.frames
- SMOTEWrapper: fix a bug where "sw.nn" was not correctly passed down
- fixed a bug that caused hyperparameters to be not passed on correctly in the
  ModelMultiplexer in some cases
- fix bug with NoFeaturesModel and ModelMultiplexer
- fix small bug in DownsampleWrapper when trained with weights
- getNestedTuneResultsOptPathDf: added new arg "trafo"
- improve documentation for permutation.importance filter and perform slight
  argument renaming to fix potential name clashes
- plotPartialDependence can plot classification tasks with more than one
  interacted features now
- generateFilterValuesData: added argument 'more.args'
- add pretty.names arguments to plots that show learner short names instead of IDs
- addition of 'data' argument to plotPartialDependence which adds the training
  data to the graph
- added new arguments "facet.wrap.nrow" and "facet.wrap.ncol" which enable
  arrangement of facets in
  rows and columns to plotting functions

## functions - new

- generateHyperParsEffectData, plotHyperParsEffect
- makeMultilabelClassifierChainsWrapper, makeMultilabelDBRWrapper
  makeMultilabelNestedStackingWrapper, makeMultilabelStackingWrapper
- makeConstantClassWrapper
- generateFunctionalANOVAData

## functions - removed

- getParamSet generic (now in ParamHelpers package)

## functions - renamed

- generatePartialPrediction to generatePartialDependence
- plotPartialPrediction to plotPartialDependence
- plotPartialPredictionGGVIS to plotPartialDependenceGGVIS

## learners - general

- fixed weight handling and weight tag for some learners
- remove unnecessary linear.output parameter for classif.neuralnet
- remove unsupported KSVM parameter value stringdot
- fix some bartMachine compatibility issues
- classif.ranger, regr.ranger and surv.ranger: now respect unordered factors by
  default
- clean up randomForestSRC and randomForestSRCSyn learners
- the "penalized" learner were restructured and improved (params were added), also
  see below.
- add stability.nugget parameter for "regr.km"
- classif.blackboost, regr.blackboost: made sure that arg "stump" is passed on
  correctly
- fixed parameter values for WEKA learners IBk, J48, PART, EM, SimpleKMeans, XMeans
- classif.glmboost, regr.glmboost: add parameters stopintern and trace

## learners - new

- classif.C50
- classif.gausspr
- classif.penalized.fusedlasso
- classif.penalized.lasso
- classif.penalized.ridge
- classif.h2o.deeplearning
- classif.h2o.gbm
- classif.h2o.glm
- classif.h2o.randomForest
- classif.rrf
- regr.penalized.fusedlasso
- regr.gausspr
- regr.glm
- regr.GPfit
- regr.h2o.deeplearning
- regr.h2o.gbm
- regr.h2o.glm
- regr.h2o.randomForest
- regr.rrf
- surv.cv.CoxBoost
- surv.penalized.fusedlasso
- surv.penalized.lasso
- surv.penalized.ridge
- cluster.kkmeans
- multilabel.randomforestSRC

## learners - removed

- surv.optimCoxBoostPenalty
- surv.penalized (split up, see new learners above)

## measures - general

- updated gmean measure and unit test, added reference to formula of gmean
- makeCostMeasure: removed arg "task", names of cost matrix are checked on measure
  calculation

## measures - new

- multiclass.brier
- brier.scaled
- logloss
- multilabel.subset01, multilabel.f1, multilabel.acc, multilabel.ppv,
  multilabel.tpr
- multiclass.au1p, multiclass.au1u, multiclass.aunp, multiclass.aunu

## measures - renamed

- multiclass.auc to multiclass.au1u
- hamloss to multilabel.hamloss

# mlr 2.8:

- Feature filter "univariate" had a bad name, was deprecated and is now called
  "univariate.model.score". The new one also has better defaults.
- (generate/plot)PartialPrediction: added new arg "geom" for tile plots
- small fix for plotBMRSummary
- the ModelMultiplexer inherits its predict.type from the base learners now
- check that learners in an ensemble have the same predict.type
- new function getBMRModels to extract stored models from a benchmark result
- Fixed a bug where several learners from the LiblineaR package
  ("classif.LiblineaRL2LogReg", "classif.LiblineaRL2SVC", "regr.LiblineaRL2L2SVR")
  were calling the wrong value for "type" (0) and thus training the wrong model.
- Fixed a bug where the resampling objects hout, cv2, cv3, cv5, cv10 were not
  documented in the ResampleDesc help page
- regr.xgboost, classif.xgboost: add feval param
- fixed a bug in irace tuning interface with unamed discrete values
- Fixed bugs in "jackknife" and "bootstrap" se estimators for regr.randomForest.
- Added "sd" estimator for regr.randomForest.
- Fixed a mini bug in ModelMultiplexer where hyperpars that are only needed in
  predict were not passed down correctly
- Fixed a bug where the function capLargeValues wasn't working if you passed a
  task.
- capLargeValues now has a new argument "target", to prevent from capping response
  values.
- classif.gbm, regr.gbm: Updated possible 'distribution' settings a bit.
- oversample, undersample, makeOversampleWrapper, makeUndersampleWrapper,
  makeOverBaggingWrapper:
  Added arguments to specifically select the sampled class.

## API changes

- listLearners now returns a data frame with properties of the learners if
  create is false

## new functions

- getBMRModels

## removed functions

- generateROCRCurvesData, plotROCRCurves, plotROCRCurvesGGVIS

## new learners

- classif.randomForestSRCSyn
- classif.cvglmnet
- regr.randomForestSRCSyn
- cluster.dbscan

## new measures

- rsq, arsq, expvar

# mlr 2.7:

- New argument "models" for function benchmark
- fixed a bug where 'keep.pred' was ignored in the benchmark function
- some of the very new functions for benchmark plots had to be refactored and/or
  renamed.
  these names are gone from the API:
  plotBenchmarkResult, generateRankMatrixAsBarData, plotRankMatrixAsBar, generateBenchmarkSummaryData, plotBenchmarkSummary,
  this is the new API:
  plotBMRSummary, plotBMRBoxplots, plotBMRRanksAsBarChart

# mlr 2.6:

- cluster.kmeans: added support for fuzzy clustering (property "prob")
- regr.lm: removed some erroneous param settings
- regr.glmnet: added 'family' param and allowed 'gaussian', but also 'poisson'
- disabled plotViperCharts unit tests as VC seems to be offline currently
- multilabel: improve few task getter functions, especially getTaskFormula is
  now correct

## new learners

- regr.glmboost
- cluster.Cobweb

# mlr 2.5:

- fixed a bug that caused performance() to return incorrect values with
  ResamplePredictions
- we have (somewhat experimental) support for multilabel classification.
  so we now have a task, a new baselearner (rFerns),
  and a generic reduction-to-binary algorithm (MultilabelWrapper)
- tuning: added 'budget' parameter in makeTuneControl* (single-objective)
  and makeTuneMultiCritControl* (multi-objective scenarios), allowing to define
  a maximum "number of evaluations" budget for tuning algorithms
- tuning: added 'budget' parameter in makeTuneMultiCritControl\*, allowing to
  define a maximum "number of evaluations" budget for tuning algorithms
  in the single-objective case
- makeTuneControlGenSA: optimized function will be considered non-smooth
  per default (change via ... args)
- classif.svm, regr.svm: added 'scale' param
- ksvm: added 'cache' param
- plotFilterValuesGGVIS: sort and n_show are interactive, interactive flag removed
- renamed getProbabilities to getPredictionProbabilities and deprecated
  getProbabilities
- plots now use long names for measures where possible
- there was a nasty bug in measure "mcc". fixed and unit tested. and apologies.
- removed getTaskFormulaAsString and improved getTaskFormula so the former is
  not needed anymore
- aggregations now have a 'name' property, which is a long name
- generateLearningCurveData and generateThreshVsPerfData now append the
  aggregation id to the output column name if the measure ids are the same
- plotLearningCurve, plotLearningCurveGGVIS, plotThreshVsPerf,
  plotThreshVsPerfGGVIS now have an argument
  'pretty.names' which plots the 'name' element of the measures instead of the 'id'.
- makeCustomResampledMeasure now has arguments 'measure.id' and 'aggregation.id'
  instead of only 'id' which corresponded to the measure. Also, 'name' and note (corresponding to the measure)
  as well as 'aggregation.name' have been added.
- makeCostMeasure now has arguments 'name' and 'id'.
- classification learner now can have a property 'class.weights', supported by
  'class.weights.param'. The latter indicates which of the parameters provides
  that class weights information to the learner.
- class weights integrated in the learner will be used as default for 'wcw.param'
  in 'makeWeightedClassesWrapper'
- listLearners with create = FALSE does not load packages anymore and is
  therefore faster and more reliable; it also supports the additional parameter
  check.packages now that will check whether required packages are installed
  without loading them
- many new functions for statistical benchmark comparisons are added, see below
- rename hasProperties, getProperties to hasLearnerProperties and
  getLearnerProperties
- Learner properties are now implemented object oriented as a state of a Learner.
  Only RLearners have the properties stored in a slot.
  For each class the getter can be overwritten.
- The hill climbing algorithm for stacking (Caruana 04) is implemented as method
  'hill.climb' in 'makeStackedLearner' to select models from base learners, which
  is equivalent to weighted average.
- The model compression algorithm for stacking (Caruana 06) is implemented as
  method 'compress' in 'makeStackedLearner' to first select models from base
  learners and then mimic the behaviour with a super learner. The default super
  learner is neural network.
- relativeOverfitting provides a way to estimate how much a model overfits to
  the training data according to a measure.
- restructured the LiblineaR learners to a more convenient format. These old ones
  were removed:
  classif.LiblineaRBinary, classif.LiblineaRLogReg, classif.LiblineaRMultiClass.
  For the new ones, see below.
- Added some commonly used ResampleDesc description objects, to save typing in
  resample experiments:
  hout, cv2, cv3, cv5, cv10.
- regr.randomForest: changed default nodesize to 5 (according to randomForest
  defaults)

## new functions

- getDefaultMeasure
- getTaskClassLevels
- getPredictionTruth, getPredictionResponse, getPredictionSE
- convertMLBenchObjToTask
- getBMRLearners, getBMRMeasures, getBMRMeasureIds
- makeMultilabelTask, makeMultilabelWrapper, getMultilabelBinaryPerformances
- generatePartialPredictionData, plotPartialPrediction, and
  plotPartialPredictionGGVIS
- getClassWeightParam
- plotBenchmarkResult, convertBMRToRankMatrix, generateRankMatrixAsBarData,
  plotRankMatrixAsBar, generateBenchmarkSummaryData, plotBenchmarkSummary,
  friedmanTestBMR, friedmanPostHocTestBMR, generateCritDifferencesData,
  plotCritDifferences
- getCaretParamSet
- generateCalibrationData and plotCalibration
- relativeOverfitting
- plotROCCurves

## new measures

- hamloss

## new learners

- multilabel.rFerns
- classif.avNNet
- classif.neuralnet
- regr.avNNet
- classif.clusterSVM
- classif.dcSVM
- classif.gaterSVM
- classif.mlp
- classif.saeDNN
- classif.dbnDNN
- classif.nnTrain
- classif.rknn
- regr.rknn
- classif.xgboost
- regr.xgboost
- classif.rotationForest
- classif.LiblineaRL1L2SVC
- classif.LiblineaRL1LogReg
- classif.LiblineaRL2L1SVC
- classif.LiblineaRL2LogReg
- classif.LiblineaRL1LMultiClassSVC
- regr.LiblineaRL2L1SVR
- regr.LiblineaRL2L2SVR
- classif.ranger
- regr.ranger
- surv.ranger

## new filters

- permutation.importance

## removed functions

- setProperties, addProperties, removeProperties

# mlr 2.4:

- WrappedModel printer was slightly improved
- ReampleResult now stores the runtime it took to resample in a slot
- getTaskFormula / getTaskFormulaAsString have new argument 'explicit.features'
- getTaskData now has recodeY = "drop.levels" which drops empty factor levels
- option fix.factors in makeLearner was renamed to fix.factors.prediction for
  clarity
- showHyperPars was removed. getParamSet does exactly the same thing
- 'resample' and 'benchmark' got the argument keep.pred,
  setting it to FALSE allows to discard the prediction objects to save memory
- we had to slightly change how the mem usage is reported in tuning and feature
  selection
  See TuneControl and FeatSelControl where it is documented what is done now.
- tuneIrace: allows to set the precision / digits within irace (using the argument
  'digits' in makeTuneControlIrace); default is maximum precision
- for plotting in general we try to introduce a "data layer", so the data can be
  generated independently of the plotting first, into well-defined objects;
  these can then be plotted with mlr or custom code;
  the naming scheme is always generate<Foo>Data and plot<Foo>
- getFilterValues is deprecated in favor of generateFilterValuesData
- plotFilterValues can now plot multiple filter methods using facetting
- plotROCRCurves has been rewritten to use ggplot2
- classif.ada: added "loss" hyperpar
- add missings properties to all ctree and cforest methods:
  regr/classif for ctree, regr/classif/surv for cforest, and regr/classif for blackboost
- learner xgboost was removed, because the package is not on CRAN anymore,
  unfortunately
- reg.km: added param 'iso'
- classif.mda: added param 'start.method' and changed its default to 'lvq', added
  params 'sub.df', 'tot.df' and 'criterion'
- classif.randomForest: 'sampsize' can now be an int vector (instead of a scalar)
- plotThreshVsPerf and plotLearningCurve now have param 'facet'

## new functions

- getTaskSize
- getNestedTuneResultsX, getNestedTuneResultsOptPathDf
- tuneDesign
- generateROCRCurvesData, generateFilterValuesData, generateLearningCurveData,
  plotLearningCurve, generateThreshVsPerfData, plotThreshVsPerf,
- generateThreshVsPerfData accepts Prediction, ResampleResult, lists of
  ResampleResult, and BenchmarkResult objects.
- experimental ggvis functions: plotROCRCurvesGGVIS, plotLearningCurveGGVIS,
  plotTuneMultiCritResultGGVIS, plotThreshVsPerfGGVIS, and plotFilterValuesGGVIS

## new learners:

- classif.bst
- classif.hdrda
- classif.nodeHarvest
- classif.pamr
- classif.rFerns
- classif.sparseLDA
- regr.bst
- regr.frbs
- regr.nodeHarvest
- regr.slim

## new measures:

- brier

# mlr 2.3:

- resample now returns an object of class ResampleResult (downward compatible)
  to allow for a print method.
- resampling on features now supported for an arbitrary number of factor features
- mlr supports ViperCharts plots now
- ROC plot via ROCR can now be created automatically, before you had to call
  asROCRPrediction,
  then construct the plots via ROCR your self. See plotROCRCurves
- all mlr measures now have slots "name" and "note"
- exported a few very simple "getters" for tasks, see below
- in makeLearner a probability predict.threshold can be set for classifiers, also
  see setPredictThreshold
- in the control objects for tuning and feature selection, the user can now enable
  threshold tuning
- in the control objects for tuning and feature selection, the user can now define
  his own logging function
- default console logging for tuneParams and selectFeatures is more informative,
  it displays time and memory info
- updated some properties of some learners
- Default arguments of classif.bartMachine, classif.randomForestSRC,
  regr.randomForestSRC and sur.randomForestSRC
  have been changed to allow missing data support with default settings.
- externalized measure functions to be used on vectors.
- some minor bug fixes
- required basic learner packages are not loaded into the global namespace
  anymore, requireNamespace
  is used internally instead. this ensures less name clashes and name shadowing
- resample passes dot arguments to the learner hyperpars
- new option "on.par.out.of.bounds" to disable out-of-bound checks for model
  parameters
- measures were slightly internally changed. they expose more properties (check
  ?Measure) and some now unnecessary object slots were removed
- classif.lda and classif.qda now have hyperpar "predict.method"
- filterFeatures and makeFilterWrapper gain an argument for mandatory features
- plotLearnerPrediction has new option "err.size"
- classif.plsDA and cluster.DBscan for now removed because of problems with the
  underlying learning algorithm
- new aggregation test.join
- the following models now can handle factors and ordereds by extra dummy or int
  encoding:
  classif.glmnet, regr.glmnet, surv.glmnet, surv.cvglmnet, surv.penalized,
  surv.optimCoxBoostPenalty, surv.glmboost, surv.CoxBoost

## new functions

- getTaskType, getTaskId, getTaskTargetNames
- plotROCRCurves
- plotViperCharts
- measureSSE, measureMSE, measureRMSE, measureMEDSE, ...
- PreprocWrapperCaret
- setPredictThreshold

## new learners:

- classif.bdk
- classif.binomial
- classif.extraTrees
- classif.probit
- classif.xgboost
- classif.xyf
- regr.bartMachine
- regr.bcart
- regr.bdk
- regr.bgp
- regr.bgpllm
- regr.blm
- regr.brnn
- regr.btgp
- regr.btgpllm
- regr.btlm
- regr.cubist
- regr.elmNN
- regr.extraTrees
- regr.laGP
- regr.xgboost
- regr.xyf
- surv.rpart

# mlr 2.2:

- The web tutorial was MUCH improved!
- more example tasks and data sets
- Learners and tasks now support ordered factors as features.
  The task description knows whether ordered factors are present and it is checked
  whether the learner supports such a feature. We have set this property 'ordered'
  very conservatively, so very few learners have it, where we are sure ordered
  inputs are handled correctly during training.
  If you know of more models that support this, please inform us.
- basic R learners now have new slots: name (a descriptive name of the algorithm),
  short.name (abbreviation that can be used in plots and tables) and note
  (notes regarding slight changes for the mlr integration of the learner and such).
- makeLearner now supports some options regarding learner error handling and
  output which could before only be set globally via configureMlr
- Additional arguments for imputation functions to allow a more fine-grain
  control of dummy column creation
- imputeMin and imputeMax now subtract or add a multiple of the range of
  the data from the minimum or to the maximum, respectively.
- cluster methods now have property 'prob' when they support fuzzy cluster
  membership probabilities,
  and also then support predict.type = 'prob'. Everything basically works the same
  as for posterior probabilities in classif.\* methods.
- predict preserves the rownames of the input in its output
- fixed a bug in createDummyFeatures that caused an error when the data contained
  missing values.
- plotLearnerPrediction works for clustering and allows greyscale plots (for
  printing or articles)
- the whole object-oriented structure behind feature filtering was much
  improved. Smaller changes in the signature of makeFilterWrapper and
  filterFeatures have become necessary.
- fixed a bug in filter methods of the FSelector package that caused an error when
  variable names contained accented letters
- filterFeatures can now be also applied to the result of getFilterValues
- We dropped the data.frame version of some preprocessing operations like
  mergeFactorLevelsBySize,
  joinClassLevels and removeConstantFeatures for consistency. These now always require tasks as input.
- We support a pretty generic framework for stacking / super-learning now, see
  makeStackedLearner
- imbalancy correction + smote:
  ** fix a bug in "smote" when only factor features are present
  ** change to oversampling: sample new observations only (with replacement)
  \*\* extension to smote algorithm (sampling): minority class observations in
  binary classification
  are either chosen via sampling or alternatively, each minority class observation
  is used an equal number of times
- made the getters for BenchmarkResult more consistent. These are now:
  getBMRTaskIds, getBMRLearnerIds, getBMRPredictions, getBMRPerformances,
  getBMRAggrPerformances
  getBMRTuneResults, getFeatSelResults, getBMRFilteredFeatures
  The following methods do not work for BenchmarkResult anymore: getTuneResult, getFeatSelResult
- Removed getFilterResult, because it does the same as getFilteredFeatures

## new learners:

- classif.bartMachine
- classif.lqa
- classif.randomForestSRC
- classif.sda
- regr.ctree
- regr.plsr
- regr.randomForestSRC
- cluster.cmeans
- cluster.DBScan
- cluster.kmeans
- cluster.FarthestFirst
- surv.cvglmnet
- surv.optimCoxBoostPenalty

## new filters:

- variance
- univariate
- carscore
- rf.importance, rf.min.depth
- anova.test, kruskal.test
- mrmr

## new functions

- makeMulticlassWrapper
- makeStackedLearner, getStackedBaseLearnerPredictions
- joinClassLevels
- summarizeColumns, summarizeLevels
- capLargeValues, mergeFactorLevelsBySize

# mlr 2.1:

- mlr now supports multi-criteria tuning
- mlr now supports cluster analysis (experimental)
- improve makeWeightedClassesWrapper: Hyperparams for class weighting are now
  supported, too.
- removed fix.factors option from randomForest, but added it in general to
  makeLearner, so it now works for all learners.
  Helps when feature factor levels where dropped in newdata prediction data.frames
- more consistent results for tuning algorithms and parameters with "trafos" :
  we always return the optimal settings on the transformed scale, but in the opt.path in the original scale.
- fix a bug when feature filtering resulted in a NoFeatureModel
- resample now returns a data.frame "err.mgs" or error messages that might have
  occurred during resampling
- stratified resampling for survival

## new learners:

- classif.cforest
- classif.glmnet
- classif.plsdaCaret
- regr.cforest
- regr.glmnet
- regr.svm
- surv.cforest
- cluster.SimpleKMeans
- cluster.EM
- cluster.XMeans

## new measures

- bac
- db, dunn, g1, g2, silhouette

## new functions

- makeClusterTask
- removeHyperPars
- tuneParamsMultiCrit
- makeTuneMultiCritControlGrid, makeTuneMultiCritControlRandom,
  makeTuneMultiCritControlNSGA2
- plotTuneMultiCritResult
- getFailureModelMsg

# mlr 2.0:

- mlr now supports survival analysis models (experimental)
- mlr now supports cost-sensitive learning with example-specific costs
  experimental)
- Some example tasks and data sets were added for simple access
- added FeatSelWrapper and getFeatSelResult
- performance functions now allows to compute multiple measures
- added multiclass.roc performance measure
- observation weights can now also be specified in the task
- added option on.learner.warning to configureMlr to suppress warnings in learners
- fixed a bug in stratified CV where elements where not distributed as evenly as
  possible when the split number did not divide the number of observation
- added class.weights param for classif.svm
- add fix.factors.prediction option to randomForest
- generic standard error estimation in randomForest and BaggingWrapper
- added fixup.data option to task constructors, so basic data cleanup can be
  performed
- show.info is now an option in configureMlr
- learners now support taggable properties that can be queried and changed. also
  see below.
- listLearners(forTask) was unified
- removed tuning via R' optim method (makeTuneControlOptim), as the optimizers in
  there really make no sense for tuning
- Grid search was improved so one does not have to discretize parameters manually
  anymore (although this is still possible). Instead one now passes a 'resolution' argument. Internally we
  now use ParamHelpers::generateGridDesign for this.
- toy tasks were added for convenient usage: iris.task, sonar.task, bh.task
  they also also have corresponding resampling instances, so you directly start
  working, e.g., iris.rin

## new learners:

- classif.knn
- classif.IBk
- classif.LiblineaRBinary
- classif.LiblineaRLogReg
- classif.LiblineaRMultiClass
- classif.linDA
- classif.plr
- classif.plsDA
- classif.rrlda
- regr.crs
- regr.IBk
- regr.mob
- surv.CoxBoost
- surv.coxph
- surv.glmboost
- surv.glmnet
- surv.penalized
- surv.randomForestSRC

## new measures

- multiauc
- cindex
- meancosts, mcp

## new functions

- removeConstantFeatures, normalizeFeatures, dropFeatures, createDummyFeatures
- getTaskNFeats
- hasProperties, getProperties, setProperties, addProperties, removeProperties
- showHyperPars
- setId
- listMeasures
- isFailureModel
- plotLearnerPrediction
- plotThreshVsPerf
- holdout, subsample, crossval, repcv, bootstrapOOB, bootstrapB632,
  bootstrapB632plus
- listFilterMethods, getFilterValues, filterFeatures, makeFilterWrapper,
  plotFilterValues
- benchmark
- getPerformances, getAggrPerformances, getPredictions, getFilterResult,
  getTuneResult, getFeatSelResult
- oversample, undersample, makeOversampleWrapper, makeUndersampleWrapper
- smote, makeSmoteWrapper
- downsample, makeDownsampleWrapper
- makeWeightedClassesWrapper
- makeTuneControlGenSA
- makeModelMultiplexer, makeModelMultiplexerParamSet
- makeCostSensTask, makeCostSensClassifWrapper, makeCostSensRegrWrapper,
  makeCostsSensWeightedPairsLearner
- makeSurvTask
- impute, reimpute, makeImputeWrapper, lots of impute<Method>, makeImputeMethod

# mlr 1.1-18:

- Initial release to CRAN
