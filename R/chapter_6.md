Circle 6: Doing Global Assignments
================

Doing Global Assignments from inside a function may have bad
consequences and therefore, is recommended to avoid global assignments
which can be done with `<<-`.

It is expected that R function would have no side effects. But a
function that make global assignments violates this expectation and
change a global variable value which is dangerous thing to happend. For
example,

``` r
x <- 1
y <- 1

fun <- function() {
  x <- 101
  y <<- 102
}

fun()

x
```

    ## [1] 1

``` r
y
```

    ## [1] 102
