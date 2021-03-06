Circle 6: Doing Global Assignments
================

Doing Global Assignments from inside a function may have bad
consequences and therefore, is recommended to avoid global assignments
which can be done with `<<-`.

It is expected that R function would have no side effects. But a
function that make global assignments violates this expectation and
change a global variable value which is dangerous thing to happened. For
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

So the value of y got changed due to global binding inside from `fun`
function.

A particular case where global binding is useful is in memoization. This
is when the results of computations are stored so that if the same
computations is desired later then the value can be simply looked up
from the stored than simply recomputing it.

``` r
fibonacci <- local({
  memo <- c(1, 1, rep(NA, 100))
  f <- function(x) {
    if(x == 0) return(0)
    if(x < 0) return(NA)
    if(x > length(memo)) {
      stop("'x' is too big for implementations")
    }
    if(!is.na(memo[x])) return(memo[x])
    
    ans <- f(x-2) + f(x-1)
    memo[x] <<- ans
    ans
  }
})


fibonacci(6)
```

    ## [1] 8

``` r
fibonacci(12)
```

    ## [1] 144

``` r
tictoc::tic()
fibonacci(78) |> invisible()
tictoc::toc()
```

    ## 0.01 sec elapsed

``` r
tictoc::tic()
fibonacci(78) |> invisible()
tictoc::toc()
```

    ## 0.02 sec elapsed

``` r
head(get('memo', envir = environment(fibonacci)))
```

    ## [1] 1 1 2 3 5 8

Here we are hiding `memo` object inside the environment local to the
function. So `memo` is not available outside the fibonacci.

``` r
memo
```

    ## Error in eval(expr, envir, enclos): object 'memo' not found

Last thing to remember: \> R always passes by value. It never passes by
reference. But in R the passed expression in function as argument is
only evaluated if it is needed inside the function (Becuase R evaluates
object lazily)

``` r
stop("this will stop whenever get called")
```

    ## Error in eval(expr, envir, enclos): this will stop whenever get called

``` r
test <- function(x) {
  a <- 10
  a
}

test(stop("this will stop whenever get called"))
```

    ## [1] 10

`test` function runs instead of stopping and showing error message
because the `stop()` was passed by x to test and test do not use x. So
the call to `stop()` remains unevaluated.

# Extra (Using {`memoise`})

``` r
library(memoise)
```

    ## Warning: package 'memoise' was built under R version 4.2.1

``` r
fibo <- function(x) {
  if(x == 0) return(0)
  if(x == 1) {
    return(1)
  } else {
    fibo(x-2) + fibo(x-1)  
  }
}

fibo_memoized <- memoise(fibo)

tictoc::tic()
fibo(30) |> invisible()
tictoc::toc()
```

    ## 3.58 sec elapsed

``` r
tictoc::tic()
fibonacci(30) |> invisible()
tictoc::toc()
```

    ## 0 sec elapsed

``` r
tictoc::tic()
fibo_memoized(30) |> invisible()
tictoc::toc()
```

    ## 2.87 sec elapsed

``` r
tictoc::tic()
fibo_memoized(30) |> invisible()
tictoc::toc()
```

    ## 0.05 sec elapsed

So using `{memoise}` we can easily do memoization for a function.

# [Back to contents](https://github.com/shafayetShafee/R_inferno#contents)
