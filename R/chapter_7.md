Tripping on Object Orientation
================

## S3 methods

S3 methods make the `class` attribute the driver of a OO system and S3
method is only effective for an object if that object has the `class`
attribute defined.

There are some function that are generic (e.g. print, plot, summary, lm
etc.). Generic functions look for whether its first argument has a class
attribute and then look for a method of that generic function that
matches up the name of the class of the first argument. If such a method
exits, then that method function is used. But if no method is found or
the argument does not have a class attribute, then the default method of
generic function is used.

### Generic functions

say, we want to see the source code of `median` function.

``` r
median
```

    ## function (x, na.rm = FALSE, ...) 
    ## UseMethod("median")
    ## <bytecode: 0x0000021bdbcc51b8>
    ## <environment: namespace:stats>

Actually to get the default method for median is

``` r
getS3method('median', 'default')
```

    ## function (x, na.rm = FALSE, ...) 
    ## {
    ##     if (is.factor(x) || is.data.frame(x)) 
    ##         stop("need numeric data")
    ##     if (length(names(x))) 
    ##         names(x) <- NULL
    ##     if (na.rm) 
    ##         x <- x[!is.na(x)]
    ##     else if (any(is.na(x))) 
    ##         return(x[NA_integer_])
    ##     n <- length(x)
    ##     if (n == 0L) 
    ##         return(x[NA_integer_])
    ##     half <- (n + 1L)%/%2L
    ##     if (n%%2L == 1L) 
    ##         sort(x, partial = half)[half]
    ##     else mean(sort(x, partial = half + 0L:1L)[half + 0L:1L])
    ## }
    ## <bytecode: 0x0000021bde0e14c0>
    ## <environment: namespace:stats>

### Methods

-   `methods` list the methods of a generic function.
-   Also given a class, `methods` lists all the generic function that
    have methods for this class.

``` r
methods(median) # here input is a generic function
```

    ## [1] median.default  median.quosure*
    ## see '?methods' for accessing help and source code

``` r
methods(class = "factor") # here input is a class
```

    ##  [1] [             [[            [[<-          [<-           all.equal    
    ##  [6] as.character  as.data.frame as.Date       as.list       as.logical   
    ## [11] as.POSIXlt    as.vector     c             coerce        droplevels   
    ## [16] format        initialize    is.na<-       length<-      levels<-     
    ## [21] Math          Ops           plot          print         relevel      
    ## [26] relist        rep           show          slotsFromS3   summary      
    ## [31] Summary       xtfrm        
    ## see '?methods' for accessing help and source code

## S4 methods

### multiple dispatch

One feature of S4 methods that is missing from S3 methods is the
`multiple dispatch`

-   `UseMethod` creates an S3 generic function
-   `StandardGeneric` creates S4 generic function

### S4 structure

-   The pieces of S4 object is called slots and accessed with `@` sign
-   S4 methods are also driven by `class`. But in S3 the decision of
    what method will be used for that generic function call is made in
    real time when the function is called. But in S4 the decision is
    made when the code is loaded in R session.

## Namespaces

A namespace exports one or more objects so that they are visible but may
have more objects that are private.

To get an object from a particular namespace we can use `::` operator
(Be warned that **the operator `::` only works if that object is
exported from that namespace**.)

``` r
stats::coef
```

    ## function (object, ...) 
    ## UseMethod("coef")
    ## <bytecode: 0x0000021bdd4be7e0>
    ## <environment: namespace:stats>

``` r
stats::coef.default
```

    ## Error: 'coef.default' is not an exported object from 'namespace:stats'

To get the unexported object from a namespace we can use `:::` operator
or `getAnywhere` function

``` r
stats:::coef.default
```

    ## function (object, complete = TRUE, ...) 
    ## {
    ##     cf <- object$coefficients
    ##     if (complete) 
    ##         cf
    ##     else cf[!is.na(cf)]
    ## }
    ## <bytecode: 0x0000021bdbaf6908>
    ## <environment: namespace:stats>

``` r
getAnywhere("coef.default")
```

    ## A single object matching 'coef.default' was found
    ## It was found in the following places
    ##   registered S3 method for coef from namespace stats
    ##   namespace:stats
    ## with value
    ## 
    ## function (object, complete = TRUE, ...) 
    ## {
    ##     cf <- object$coefficients
    ##     if (complete) 
    ##         cf
    ##     else cf[!is.na(cf)]
    ## }
    ## <bytecode: 0x0000021bdbaf6908>
    ## <environment: namespace:stats>

There can be problem if we want to modify a function that is in a
namespace. Functions `assignInNamespace` and `unlockBinding` can be
useful in this regard.

# [Back to contents](https://github.com/shafayetShafee/R_inferno#contents)
