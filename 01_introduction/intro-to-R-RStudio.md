Workshop \#1: introduction to R and RStudio
================
BEPHIG
2020-11-01

# Getting started

## Environment

Your current working directory can be found by typing the function
`getwd()` into your console. It is important to know the location of
your working directory when importing and exporting data to and from R.
You can set a new working directory with the function `setwd()`.  
But before we do that, now is as good a time as any to talk about
functions. Both `getwd()` and `setwd()` are functions. If you want to
learn more about a function (e.g., description; usage; arguments), add a
question mark before the function to bring up any relevant documentation
in your Help pane.

Give these a try:

``` r
?getwd()
getwd()
```

Based on the information included in the documentation, we can use
`setwd()` to change our working directory to another folder. Before we
do that, it might be a good idea to assign (`<-`) the current working
directory to a variable in case we need to revert back to it in the
future.

``` r
original_directory <- getwd() 
# alternatively
assign(x = "original_directory", value = getwd())
```

Now, we can change the working directory, allowing us to keep all the
files relevant to our analysis in a single folder named ‘tmp’.

``` r
setwd(dir = "/tmp")
```

We can then use `getwd()` to see if the change in working directory has
been successful.

``` r
getwd()
```

We can restore the original working directory by using the value stored
in the variable `original_directory`.

``` r
setwd(original_directory)
```

Now try `getwd()` again.

``` r
getwd()
```

Notice that `getwd()` will now return the original working directory. It
is worth pointing out that the `dir =` portion can be omitted from
`setwd(...)` because the function only accepts one argument.  
We can also omit certain arguments from functions if there are default
values. For example, bring up the documentation for `rnorm()`.

``` r
?rnorm()
```

This function generates `n` random values from a normal distribution. If
we don’t specify the mean and standard deviation (sd) of the normal
distribution and provide a single number as an argument, then that
number will be treated as the argument for `n` and the mean and sd will
default to 0 and 1, respectively.

``` r
set.seed(1) # to ensure that we get the same values
rnorm(10)
```

    ##  [1] -0.6264538  0.1836433 -0.8356286  1.5952808  0.3295078 -0.8204684
    ##  [7]  0.4874291  0.7383247  0.5757814 -0.3053884

But what if we were interested in generating 10 values from a normal
distribution with mean of 0 and sd of 10?

``` r
set.seed(1)
rnorm(10, 10)
```

    ##  [1]  9.373546 10.183643  9.164371 11.595281 10.329508  9.179532 10.487429
    ##  [8] 10.738325 10.575781  9.694612

Judging from the output, we can already tell that these ten values were
drawn from a normal distribution with a mean of 10. In this example,
where the arguments are ambiguous, the first `10` is interpreted as the
first argument (`n`) and the second `10` is interpreted as the second
argument (`mean` instead of `sd`). Thus, we have to be more specific.

``` r
set.seed(1)
rnorm(10, sd = 10)
```

    ##  [1] -6.264538  1.836433 -8.356286 15.952808  3.295078 -8.204684  4.874291
    ##  [8]  7.383247  5.757814 -3.053884

Bringing the focus back to the topic of working directories, I would
recommend the use of RStudio projects. I find it helpful in
compartmentalize my analyses. It also plays nicely with git if you are
ever in need of version control. Detailed information about Rstudio
projects can be found on [this
page](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects).
