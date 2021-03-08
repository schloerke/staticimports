
<!-- README.md is generated from README.Rmd. Please edit that file -->

# staticimports

<!-- badges: start -->
<!-- badges: end -->

**staticimports** provides utility functions that R programmers can use
in their packages or other projects. The “static” part means that the
functions are statically “imported” – that is they copied into the
project as text, instead of being loaded from a package at run time.

The benefits of doing things this way:

-   A package/project that uses staticimports will not take a run-time
    dependency staticimports.
-   Any changes to a function in **staticimports** will not affect a
    project that uses them, until the author decides to re-import them.
-   You can specify the individual functions to import. This is faster
    and lighter-weight than loading a separate package, especially if
    the package has much more functionality than you need.

Instead of copying and pasting utility functions from project to
project, the utility functions can be centralized in **staticimports**,
where they can be vetted and tested.

The functions in **staticimports** are designed to be:

-   Fast
-   Simple
-   Have no external dependencies

If your project imports a function from **staticimports**, and that
function changes in a way that has a negative impact on your project,
you can simply stop importing it from **staticimports**, and copy the
old version to a separate file.

## Installation

You can install the development version of staticimports with:

``` r
remotes::install_github("wch/staticimports")
```

## Example

To statically import functions into your package use `import()`. By
default this will write to a file `R/staticimports.R` in your project.

``` r
library(staticimports)

# This will write to R/staticimports.R
import(c("os_name", "walk"))
```

You examine the output by writing to `stdout()` instead of
`R/staticimports.R`. Notice how importing `os_name` automatically brings
in `is_windows`, `is_mac`, and `is_linux`.

``` r
import(c("os_name", "walk"), stdout())
#> # Generated by staticimports; do not edit by hand.
#> 
#> os_name <- function() {
#>   if (is_windows()) {
#>     "win"
#>   } else if (is_mac()) {
#>     "mac"
#>   } else if (is_linux()) {
#>     "linux"
#>   } else if (.Platform$OS.type == "unix") {
#>     "unix"
#>   } else {
#>     "unknown"
#>   }
#> }
#> 
#> walk <- function(.x, .f, ...) {
#>   for (i in seq_along(.x)) {
#>     .f(.x[[i]], ...)
#>   }
#>   NULL
#> }
#> 
#> is_linux <- function() Sys.info()[['sysname']] == 'Linux'
#> 
#> is_mac <- function() Sys.info()[['sysname']] == 'Darwin'
#> 
#> is_windows <- function() .Platform$OS.type == "windows"
```

## Some conventions

Note: This is currently just a dumping ground for ideas.

### Don’t use `force()`

Two reasons:

-   Error reporting
-   Missing args

``` r
with_option <- function(x, y) {
  force(x)
}

with_option(a = 123)
#> Error in with_option(a = 123): unused argument (a = 123)

with_option2 <- function(x, y) {
  x
}

with_option2(a = 123)
#> Error in with_option2(a = 123): unused argument (a = 123)

err <- function() { stop("asdf") }

f()
#> Error in f(): could not find function "f"
```

### Don’t use `match.arg()`

-   `match.arg()` is slow, at least if values are inferred
    automatically.