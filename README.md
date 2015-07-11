rstanarm
========
This is an R package that emulates other R model-fitting functions but uses (R)Stan for the back-end estimation. The primary target audience is people who would be open to Bayesian inference if using
Bayesian software were easy but would use frequentist software otherwise. That said, this R package
often uses posterior means as point estimates.

Rules:
  1. The stan* (e.g. `stan_glm`) wrapper function should take (almost) all of the same arguments as the function it emulates (e.g. `glm`) but ignoring unnecessary arguments is fine
  2. After that, you can add additional arguments that often pertain to the priors used by Stan
  3. The ... is always passed to `stan` so that the user can specify the number of chains, etc.
  4. The .stan files go in the exec/ subdirectory; try to make them as abstract, numerically stable, fast, etc. as possible at the expense of readability if necessary.
  5. The man/rstanarm-internal.Rd file autogenerates the other .Rd files and creates a bunch of `stanmodel` objects that are saved as the R/sysdata.rda file, which are loaded into the rstanarm namespace and can be passed to `sampling`, `optimizing`, etc.
  6. The order of the returned parameters should be intercept(s) (if any), regression coefficients, nuisance parameters, generated quantities, anything else. Use the `pars` argument to `stan` if necessary to achieve this ordering and to avoid showing the user unnecessary things.
  7. Do not store any quantities whose number is proportional to the sample size. If necessary, generate such things inside R so that we can aggregate over them incrementally (e.g. importance sampling weighted LOO-CV).
  8. Do generate the (typically scalar, unless outcome is multivariate) sample average posterior predictive distribution of the outcome and call it `mean_PPD`. This is useful for checking.