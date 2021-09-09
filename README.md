
<!-- README.md is generated from README.Rmd. Please edit that file -->

# modelops

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/modelops)](https://CRAN.R-project.org/package=modelops)
[![R-CMD-check](https://github.com/juliasilge/modelops/workflows/R-CMD-check/badge.svg)](https://github.com/juliasilge/modelops/actions)
<!-- badges: end -->

**This pre-release is available for feedback and experimenting.**

The goal of modelops is to provide fluent tooling to version, share,
deploy, and monitor a trained model. Functions handle both recording and
checking the model’s input data prototype, and predicting from a remote
API endpoint. The modelops package is extensible, with generics that can
be supported by many kinds of models. For an example of how to use and
extend the modelops package, see
[deploytidymodels](https://github.com/juliasilge/deploytidymodels).

## Installation

~~You can install the released version of modelops from
[CRAN](https://CRAN.R-project.org) with:~~

``` r
install.packages("modelops") ## not yet
```

And the development version from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("juliasilge/modelops")
```

## Example

A `modelops()` object collects the information needed to store, version,
and deploy a trained model.

You can **version** and **share** your model by choosing a
[pins](https://pins.rstudio.com/dev/) “board” for it, including a local
folder, RStudio Connect, Amazon S3, and more.

``` r
library(modelops)
library(pins)

model_board <- board_temp()
cars_lm <- lm(mpg ~ ., data = mtcars)
m <- modelops(cars_lm, "cars_linear", model_board)
m
#> 
#> ── cars_linear ─ <butchered_lm> model for deployment 
#> An OLS linear regression model using 10 features
```

When you use `modelops_pin_write()`, you store and version your
deployable, trained model object.

``` r
modelops_pin_write(m)
#> Creating new version '20210909T205659Z-f1109'
```

You can **deploy** your pinned `modelops()` object via a [Plumber
API](https://www.rplumber.io/), which can be [hosted in a variety of
ways](https://www.rplumber.io/articles/hosting.html).

``` r
library(plumber)
pr() %>%
  modelops_pr_predict(m) %>%
  pr_run(port = 8088)
```

Make predictions with your deployed model at its endpoint and new data.

``` r
endpoint <- modelops_endpoint("http://127.0.0.1:8088/predict")
predict(endpoint, mtcars[4:7, -1])
#> # A tibble: 4 x 1
#>   .pred
#>   <dbl>
#> 1  21.2
#> 2  17.7
#> 3  20.4
#> 4  14.4
```

## Contributing

This project is released with a [Contributor Code of
Conduct](https://contributor-covenant.org/version/2/0/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.

-   For questions and discussions about modeling packages, modeling, and
    machine learning, please [post on RStudio
    Community](https://community.rstudio.com/new-topic?category_id=15&tags=tidymodels,question).

-   If you think you have encountered a bug, please [submit an
    issue](https://github.com/juliasilge/modelops/issues).

-   Either way, learn how to create and share a
    [reprex](https://reprex.tidyverse.org/articles/articles/learn-reprex.html)
    (a minimal, reproducible example), to clearly communicate about your
    code.
