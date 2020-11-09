Workshop \#2: Data Wrangling with dplyr
================
BEPHIG
2020-11-09

Please note that there is overlap between this workshop and the
materials found in the first
[workshop](https://github.com/bephig/bephig/blob/main/01_introduction/intro-to-R-RStudio.md)
because of time constraints. Some parts have been re-worked in an
attempt to streamline the workshop.

# Getting started

If you haven’t done so already, install `dplyr` and `readr` (or
preferrably, `tidyverse`) by using the `installed.packages()`. This will
allow you to attach the packages.

``` r
library(readr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

Notice the output in the console when attaching `dplyr`. This is simply
stating that some functions that would have been available before have
been masked with functions of the same name included in the `dplyr`
package. If you do need to use a function that has been masked, you can
explicitly use these functions by specifying the package to which the
masked function belongs. For example, you can use `stats::filter()` to
use the original `filter()` function.

# Downloading data from the internet

If you’re working on your own research projects, then it will be
unlikely that you’d ever need to download your data set. It is more
likely that you’d read in the data from your local disk. Nonetheless,
please download the following csv file containing data about measles
vaccination rates from [rfordatascience’s tidytuesday GitHub
project](https://github.com/rfordatascience/tidytuesday). The original
source of these data is [The Wall Street
Journal](https://github.com/WSJ/measles-data).

``` r
measles <- read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-02-25/measles.csv')
```

    ## Parsed with column specification:
    ## cols(
    ##   index = col_double(),
    ##   state = col_character(),
    ##   year = col_character(),
    ##   name = col_character(),
    ##   type = col_character(),
    ##   city = col_character(),
    ##   county = col_character(),
    ##   district = col_logical(),
    ##   enroll = col_double(),
    ##   mmr = col_double(),
    ##   overall = col_double(),
    ##   xrel = col_logical(),
    ##   xmed = col_double(),
    ##   xper = col_double(),
    ##   lat = col_double(),
    ##   lng = col_double()
    ## )

Ignore the console output for now. Notice that you had assigned the
value of `read_csv(...)` to an object named “measles”. This is an
important step because the data set must be available in the environment
in order for you to work with it.

What exactly is “measles”? Use the `str()` function to inspect the
internal structure of the object.

``` r
str(measles)
```

    ## tibble [66,113 × 16] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ index   : num [1:66113] 1 2 3 4 5 6 7 8 9 10 ...
    ##  $ state   : chr [1:66113] "Arizona" "Arizona" "Arizona" "Arizona" ...
    ##  $ year    : chr [1:66113] "2018-19" "2018-19" "2018-19" "2018-19" ...
    ##  $ name    : chr [1:66113] "A J Mitchell Elementary" "Academy Del Sol" "Academy Del Sol - Hope" "Academy Of Mathematics And Science South" ...
    ##  $ type    : chr [1:66113] "Public" "Charter" "Charter" "Charter" ...
    ##  $ city    : chr [1:66113] "Nogales" "Tucson" "Tucson" "Phoenix" ...
    ##  $ county  : chr [1:66113] "Santa Cruz" "Pima" "Pima" "Maricopa" ...
    ##  $ district: logi [1:66113] NA NA NA NA NA NA ...
    ##  $ enroll  : num [1:66113] 51 22 85 60 43 36 24 22 26 78 ...
    ##  $ mmr     : num [1:66113] 100 100 100 100 100 100 100 100 100 100 ...
    ##  $ overall : num [1:66113] -1 -1 -1 -1 -1 -1 -1 -1 -1 -1 ...
    ##  $ xrel    : logi [1:66113] NA NA NA NA NA NA ...
    ##  $ xmed    : num [1:66113] NA NA NA NA 2.33 NA NA NA NA NA ...
    ##  $ xper    : num [1:66113] NA NA NA NA 2.33 NA 4.17 NA NA NA ...
    ##  $ lat     : num [1:66113] 31.3 32.2 32.1 33.5 33.5 ...
    ##  $ lng     : num [1:66113] -111 -111 -111 -112 -112 ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   index = col_double(),
    ##   ..   state = col_character(),
    ##   ..   year = col_character(),
    ##   ..   name = col_character(),
    ##   ..   type = col_character(),
    ##   ..   city = col_character(),
    ##   ..   county = col_character(),
    ##   ..   district = col_logical(),
    ##   ..   enroll = col_double(),
    ##   ..   mmr = col_double(),
    ##   ..   overall = col_double(),
    ##   ..   xrel = col_logical(),
    ##   ..   xmed = col_double(),
    ##   ..   xper = col_double(),
    ##   ..   lat = col_double(),
    ##   ..   lng = col_double()
    ##   .. )

Based on the first line of this output, you can safely state that you
are looking at something called a tibble. Moreover, this tibble has
dimensions of 66,113 x 16. The next sixteen lines of your output all
start with a dollar sign. Though it may or may not be immediately
obvious, each of these corresponds to a column in the “measles” object.
That would mean that 66,113 refers to the number of rows.

Use `View()` to take a look at the object.

``` r
View(measles)
```

You should see a new tab appear with what looks to be a spreadsheet of
values. This confirms much of the earlier speculation concerning the
output of `str()`.

For now, you can think of a tibble as a fancier data frame (see
[here](https://r4ds.had.co.nz/tibbles.html#tibbles-vs.-data.frame) for a
more detailed discussion). But you could be wondering what exactly is a
data frame. Now would be a good time to take a step back and talk about
data types and structures in R.

# Data types

You were briefly introduced to numeric (or “double”) and logical data
types in the previous workshop.

``` r
str(1)
```

    ##  num 1

``` r
str(TRUE)
```

    ##  logi TRUE

Don’t get too preoccupied with integers for the time being.

``` r
str(1L)
```

    ##  int 1

``` r
1 == 1L
```

    ## [1] TRUE

As you can see, numeric values and integers are functionally equivalent
in most contexts (more information, should you be interested, is
available [here](https://r4ds.had.co.nz/vectors.html)). The final data
type that you will routinely encounter in everyday practice is character
data.

``` r
str("hello")
```

    ##  chr "hello"

So far, you have only looked at one value at a time. In terms of
character data, you have only looked at one string at a time. To prove
this point, make use the function `length()`.

``` r
length(1)
```

    ## [1] 1

``` r
length(1L)
```

    ## [1] 1

``` r
length(TRUE)
```

    ## [1] 1

``` r
length("hello")
```

    ## [1] 1

# Data structures

What if you wanted to have an object hold multiple values? You would
need a data structure like a vector. You can use `c()` to create vectors
holding more than one value at a time.

``` r
c(1, 2, 3)
```

    ## [1] 1 2 3

``` r
c(TRUE, F, FALSE, T)
```

    ## [1]  TRUE FALSE FALSE  TRUE

``` r
c("hello", "world")
```

    ## [1] "hello" "world"

Sometimes, you won’t even need `c()` to create vectors. For example, the
operator `:` generates regular sequences.

``` r
1:5
```

    ## [1] 1 2 3 4 5

But what if you were to combine different data types in a single vector?

``` r
c(1, TRUE, "hello")
```

    ## [1] "1"     "TRUE"  "hello"

This doesn’t appear to cause problems, but take a closer look at the
internal structure.

``` r
str(c(1, TRUE, "hello")) # could have assigned vector to an object to avoid re-typing it
```

    ##  chr [1:3] "1" "TRUE" "hello"

You can see that this vector contains character data. This is by design.
Vectors can only hold data of the same type. R has defaulted to a
character vector because strings can contain an arbitrary amount of
data.

That said, you can have R default to a different vector type if no
strings are included.

``` r
str(c(1, TRUE))
```

    ##  num [1:2] 1 1

Remember how TRUE can be represented with 1 (and FALSE with 0)? The
internal structure of `c(1, TRUE)` can therefore be a numeric vector
instead of a character vector.

Lastly, what if you were interested in accessing one value or a subset
of values from a vector? You can do so with square brackets. Let’s
explore using the character vector `LETTERS`.

``` r
LETTERS
```

    ##  [1] "A" "B" "C" "D" "E" "F" "G" "H" "I" "J" "K" "L" "M" "N" "O" "P" "Q" "R" "S"
    ## [20] "T" "U" "V" "W" "X" "Y" "Z"

``` r
LETTERS[5] # fifth letter of the alphabet
```

    ## [1] "E"

As you can see, appending `[5]` to the end of `LETTERS` allows you to
access the fifth letter of the alphabet. You can access multiple values
at the same time if you supply a vector instead.

``` r
LETTERS[c(1, 4, 5)] # first, fourth, and fifth letters
```

    ## [1] "A" "D" "E"

You can also access values based on criterion or criteria. This may be
more easily illustrated with numbers instead of letters.

``` r
set.seed(1)
NUMBERS <- rnorm(100,0,50)
NUMBERS
```

    ##   [1]  -31.32269054    9.18216621  -41.78143062   79.76404011   16.47538859
    ##   [6]  -41.02341921   24.37145262   36.91623526   28.78906758  -15.26941936
    ##  [11]   75.58905842   19.49216182  -31.06202903 -110.73499436   56.24654591
    ##  [16]   -2.24668045   -0.80951315   47.19181053   41.06105975   29.69506606
    ##  [21]   45.94886858   39.10681504    3.72824917  -99.46758479   30.99128739
    ##  [26]   -2.80643698   -7.78977534  -73.53761919  -23.90750276   20.89707801
    ##  [31]   67.93397758   -5.13938637   19.38358058   -2.69025203  -68.85297784
    ##  [36]  -20.74972816  -19.71449769   -2.96566984   55.00126860   38.15878742
    ##  [41]   -8.22617981  -12.66808401   34.84816877   27.83315993  -34.43778473
    ##  [46]  -35.37475785   18.22909811   38.42664623   -5.61731061   44.05538632
    ##  [51]   19.90529402  -30.60131966   17.05598457  -56.46815480   71.65118509
    ##  [56]   99.01999493  -18.36107382  -52.20673132   28.48598137   -6.75273019
    ##  [61]  120.08088803   -1.96200014   34.48696812    1.40010794  -37.16366044
    ##  [66]    9.43961498  -90.24793144   73.27774308    7.66266691  108.63058352
    ##  [71]   23.77547644  -35.49732155   30.53631767  -46.70488158  -62.68167001
    ##  [76]   14.57231178  -22.16459366    0.05526758    3.71706621  -29.47604731
    ##  [81]  -28.43343664   -6.75893076   58.90434983  -76.17834002   29.69730938
    ##  [86]   16.64751856   53.15499186  -15.20919618   18.50094050   13.35493954
    ##  [91]  -27.12600155   60.39339030   58.02013078   35.01068248   79.34167273
    ##  [96]   27.92432128  -63.82961042  -28.66327071  -61.23063074  -23.67003182

The output includes the 100 values drawn from a normal distribution with
mean of 0 and standard deviation of 50. What if you only wanted values
greater than zero?

``` r
NUMBERS[NUMBERS>0]
```

    ##  [1]   9.18216621  79.76404011  16.47538859  24.37145262  36.91623526
    ##  [6]  28.78906758  75.58905842  19.49216182  56.24654591  47.19181053
    ## [11]  41.06105975  29.69506606  45.94886858  39.10681504   3.72824917
    ## [16]  30.99128739  20.89707801  67.93397758  19.38358058  55.00126860
    ## [21]  38.15878742  34.84816877  27.83315993  18.22909811  38.42664623
    ## [26]  44.05538632  19.90529402  17.05598457  71.65118509  99.01999493
    ## [31]  28.48598137 120.08088803  34.48696812   1.40010794   9.43961498
    ## [36]  73.27774308   7.66266691 108.63058352  23.77547644  30.53631767
    ## [41]  14.57231178   0.05526758   3.71706621  58.90434983  29.69730938
    ## [46]  16.64751856  53.15499186  18.50094050  13.35493954  60.39339030
    ## [51]  58.02013078  35.01068248  79.34167273  27.92432128

The following would have also worked

``` r
NUMBERS[!(NUMBERS<=0)]
```

    ##  [1]   9.18216621  79.76404011  16.47538859  24.37145262  36.91623526
    ##  [6]  28.78906758  75.58905842  19.49216182  56.24654591  47.19181053
    ## [11]  41.06105975  29.69506606  45.94886858  39.10681504   3.72824917
    ## [16]  30.99128739  20.89707801  67.93397758  19.38358058  55.00126860
    ## [21]  38.15878742  34.84816877  27.83315993  18.22909811  38.42664623
    ## [26]  44.05538632  19.90529402  17.05598457  71.65118509  99.01999493
    ## [31]  28.48598137 120.08088803  34.48696812   1.40010794   9.43961498
    ## [36]  73.27774308   7.66266691 108.63058352  23.77547644  30.53631767
    ## [41]  14.57231178   0.05526758   3.71706621  58.90434983  29.69730938
    ## [46]  16.64751856  53.15499186  18.50094050  13.35493954  60.39339030
    ## [51]  58.02013078  35.01068248  79.34167273  27.92432128

Why does this work? It works because you are supplying a logical vector
that is the same length as the original. The only values of that vector
that will be accessed are those that are TRUE.

One peculiarity of note is that a shorter logical vector could also be
used.

``` r
NUMBERS[c(TRUE, FALSE)]
```

    ##  [1] -31.3226905 -41.7814306  16.4753886  24.3714526  28.7890676  75.5890584
    ##  [7] -31.0620290  56.2465459  -0.8095132  41.0610598  45.9488686   3.7282492
    ## [13]  30.9912874  -7.7897753 -23.9075028  67.9339776  19.3835806 -68.8529778
    ## [19] -19.7144977  55.0012686  -8.2261798  34.8481688 -34.4377847  18.2290981
    ## [25]  -5.6173106  19.9052940  17.0559846  71.6511851 -18.3610738  28.4859814
    ## [31] 120.0808880  34.4869681 -37.1636604 -90.2479314   7.6626669  23.7754764
    ## [37]  30.5363177 -62.6816700 -22.1645937   3.7170662 -28.4334366  58.9043498
    ## [43]  29.6973094  53.1549919  18.5009405 -27.1260015  58.0201308  79.3416727
    ## [49] -63.8296104 -61.2306307

This is an unusual way to go about it, but will give you all of the
values in odd numbered positions (i.e., the first, third, fifth,
seventh, …, ninety-ninth values). This works because many functions in R
are vectorized. What does this mean? If the length of the longer vector
is a multiple of the length of the shorter vector, R will loop through
the shorter vector as needed.

``` r
c(1, 30) + c(10, 84, 0, -1)
```

    ## [1]  11 114   1  29

In the example above, you are attempting to add together two vectors.
The value in the first position of each vector were added together.
Then, the value in the second position of each vector were added
together. Normally, the value in the third position of each vector would
be added together. However, there is no third position in the first
vector.

Since the length of the second vector (4) is a multiple of that of the
first (2), R will loop through the shorter vector. The value in the
first position of the first vector will be added to the value in the
third position of the second vector. Finally, the value in the second
position of the first vector will be added to the value in the fourth
position of the second vector.

You will run into problems if the longer vector is not a multiple of the
shorter vector.

``` r
c(1, 30) + c(10, 84, 0)
```

    ## Warning in c(1, 30) + c(10, 84, 0): longer object length is not a multiple of
    ## shorter object length

    ## [1]  11 114   1

Hopefully, by now, you should have also noticed something else when
using the function `str()` on a vector.

``` r
str(1:5)
```

    ##  int [1:5] 1 2 3 4 5

Does the output remind you of something else that you saw earlier when
examining the “measles” tibble?

``` r
str(measles)
```

    ## tibble [66,113 × 16] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
    ##  $ index   : num [1:66113] 1 2 3 4 5 6 7 8 9 10 ...
    ##  $ state   : chr [1:66113] "Arizona" "Arizona" "Arizona" "Arizona" ...
    ##  $ year    : chr [1:66113] "2018-19" "2018-19" "2018-19" "2018-19" ...
    ##  $ name    : chr [1:66113] "A J Mitchell Elementary" "Academy Del Sol" "Academy Del Sol - Hope" "Academy Of Mathematics And Science South" ...
    ##  $ type    : chr [1:66113] "Public" "Charter" "Charter" "Charter" ...
    ##  $ city    : chr [1:66113] "Nogales" "Tucson" "Tucson" "Phoenix" ...
    ##  $ county  : chr [1:66113] "Santa Cruz" "Pima" "Pima" "Maricopa" ...
    ##  $ district: logi [1:66113] NA NA NA NA NA NA ...
    ##  $ enroll  : num [1:66113] 51 22 85 60 43 36 24 22 26 78 ...
    ##  $ mmr     : num [1:66113] 100 100 100 100 100 100 100 100 100 100 ...
    ##  $ overall : num [1:66113] -1 -1 -1 -1 -1 -1 -1 -1 -1 -1 ...
    ##  $ xrel    : logi [1:66113] NA NA NA NA NA NA ...
    ##  $ xmed    : num [1:66113] NA NA NA NA 2.33 NA NA NA NA NA ...
    ##  $ xper    : num [1:66113] NA NA NA NA 2.33 NA 4.17 NA NA NA ...
    ##  $ lat     : num [1:66113] 31.3 32.2 32.1 33.5 33.5 ...
    ##  $ lng     : num [1:66113] -111 -111 -111 -112 -112 ...
    ##  - attr(*, "spec")=
    ##   .. cols(
    ##   ..   index = col_double(),
    ##   ..   state = col_character(),
    ##   ..   year = col_character(),
    ##   ..   name = col_character(),
    ##   ..   type = col_character(),
    ##   ..   city = col_character(),
    ##   ..   county = col_character(),
    ##   ..   district = col_logical(),
    ##   ..   enroll = col_double(),
    ##   ..   mmr = col_double(),
    ##   ..   overall = col_double(),
    ##   ..   xrel = col_logical(),
    ##   ..   xmed = col_double(),
    ##   ..   xper = col_double(),
    ##   ..   lat = col_double(),
    ##   ..   lng = col_double()
    ##   .. )

Notice the resemblance to each of the sixteen lines starting with a
dollar sign? Hopefully, it should now be clear that a tibble or a data
frame is composed of vectors arranged vertically in columns. In fact,
you can create your own data frame from vectors as follows:

``` r
numeric_v <- 1:6
character_v <- c("a", "b", "c", "d", "e", "f")
logical_v <- c(T, T, F)
integer_v <- 1L

sample_df <- data.frame(
  numeric = numeric_v,
  character = character_v,
  logical = logical_v,
  integer = integer_v
  )

sample_df
```

    ##   numeric character logical integer
    ## 1       1         a    TRUE       1
    ## 2       2         b    TRUE       1
    ## 3       3         c   FALSE       1
    ## 4       4         d    TRUE       1
    ## 5       5         e    TRUE       1
    ## 6       6         f   FALSE       1

``` r
str(sample_df)
```

    ## 'data.frame':    6 obs. of  4 variables:
    ##  $ numeric  : int  1 2 3 4 5 6
    ##  $ character: chr  "a" "b" "c" "d" ...
    ##  $ logical  : logi  TRUE TRUE FALSE TRUE TRUE FALSE
    ##  $ integer  : int  1 1 1 1 1 1

And to, once again, show you that a tibble is just a fancy data frame,
coerce the object using the function `as_tibble()`:

``` r
as_tibble(sample_df)
```

    ## # A tibble: 6 x 4
    ##   numeric character logical integer
    ##     <int> <chr>     <lgl>     <int>
    ## 1       1 a         TRUE          1
    ## 2       2 b         TRUE          1
    ## 3       3 c         FALSE         1
    ## 4       4 d         TRUE          1
    ## 5       5 e         TRUE          1
    ## 6       6 f         FALSE         1

``` r
str(as_tibble(sample_df))
```

    ## tibble [6 × 4] (S3: tbl_df/tbl/data.frame)
    ##  $ numeric  : int [1:6] 1 2 3 4 5 6
    ##  $ character: chr [1:6] "a" "b" "c" "d" ...
    ##  $ logical  : logi [1:6] TRUE TRUE FALSE TRUE TRUE FALSE
    ##  $ integer  : int [1:6] 1 1 1 1 1 1

You may have noticed that vectorization was deliberately used in
constructing this data frame. Interestingly, `tibble()` only allows for
the recycling of values if the supplied length is one (see
`?tibble::tibble` for documentation). This probably makes more sense
with cases where you’re adding a column of constant values. You would
rarely make use of vectorization if each row of data is supposed to
correspond to a different observation. It would also be rare for you to
prepare an entire data frame in R because it is oftentimes easier for
you to save a spreadsheet as a csv file and import it that way.

You can summarize the data in a tibble or data frame with the
`summary()` function:

``` r
summary(sample_df)
```

    ##     numeric      character          logical           integer 
    ##  Min.   :1.00   Length:6           Mode :logical   Min.   :1  
    ##  1st Qu.:2.25   Class :character   FALSE:2         1st Qu.:1  
    ##  Median :3.50   Mode  :character   TRUE :4         Median :1  
    ##  Mean   :3.50                                      Mean   :1  
    ##  3rd Qu.:4.75                                      3rd Qu.:1  
    ##  Max.   :6.00                                      Max.   :1

You can go from data frame to individual vectors in several ways:

``` r
sample_df$numeric # by naming the column
```

    ## [1] 1 2 3 4 5 6

``` r
sample_df[,1] # by specifying the column number
```

    ## [1] 1 2 3 4 5 6

Analogous to the previous example where you had accessed certain values
in your vector by specifying the positions (in square brackets), you can
access certain columns or rows by specifying their positions (in square
brackets). The value(s) before the comma indicate the position of rows
to be accessed. The value(s) after the comma indicate the position of
columns to be accessed.

To illustrate, you can determine which rows meet a specific criterion,
then supply the positions of these rows in square brackets.

``` r
which(sample_df$logical == TRUE)
```

    ## [1] 1 2 4 5

Alternatively, you can then filter the data frame directly:

``` r
sample_df[which(sample_df$logical == TRUE),] # functionally the same as sample_df[c(1, 2, 4, 5),]
```

    ##   numeric character logical integer
    ## 1       1         a    TRUE       1
    ## 2       2         b    TRUE       1
    ## 4       4         d    TRUE       1
    ## 5       5         e    TRUE       1

In essence, you have accessed rows 1, 2, 4, and 5 (those meeting the
criterion) by supplying these row numbers within the square brackets. If
you were interested in the character values of observations that are
TRUE, you can combine these two criteria as follows:

``` r
sample_df[which(sample_df$logical == TRUE), c("character", "logical")]
```

    ##   character logical
    ## 1         a    TRUE
    ## 2         b    TRUE
    ## 4         d    TRUE
    ## 5         e    TRUE

Instead of finding the position where these columns can be found (using
something like `which(colnames(sample_df) %in% c("character",
"logical"))`), the names of the columns can be supplied within a
character vector and placed after the comma. This also works if you
wanted to access certain rows within a data frame while maintaining the
data structure.

``` r
sample_df["numeric"] # by naming the column, but this time within square brackets and with quotations
```

    ##   numeric
    ## 1       1
    ## 2       2
    ## 3       3
    ## 4       4
    ## 5       5
    ## 6       6

``` r
sample_df[c("numeric", "character")]
```

    ##   numeric character
    ## 1       1         a
    ## 2       2         b
    ## 3       3         c
    ## 4       4         d
    ## 5       5         e
    ## 6       6         f

While there are several more data structures, one last structure that
you may routinely encounter is a list. It is unlikely that you’d
routinely use a list in routine data analyses, but they can be used to
store different data types and data structures.

``` r
sample_list <- list() # create an empty list
sample_list$character <- character_v
sample_list$numeric <- list(integer = integer_v, numeric = numeric_v)
sample_list$df <- sample_df
sample_list
```

    ## $character
    ## [1] "a" "b" "c" "d" "e" "f"
    ## 
    ## $numeric
    ## $numeric$integer
    ## [1] 1
    ## 
    ## $numeric$numeric
    ## [1] 1 2 3 4 5 6
    ## 
    ## 
    ## $df
    ##   numeric character logical integer
    ## 1       1         a    TRUE       1
    ## 2       2         b    TRUE       1
    ## 3       3         c   FALSE       1
    ## 4       4         d    TRUE       1
    ## 5       5         e    TRUE       1
    ## 6       6         f   FALSE       1

The list includes a character vector, a sublist with an integer and a
numeric vector, and a data frame. Notice how they are not of the same
length or even data structure.

You can access individual components within the framework of a list with
square brackets.

``` r
sample_list[1]
```

    ## $character
    ## [1] "a" "b" "c" "d" "e" "f"

``` r
sample_list["df"]
```

    ## $df
    ##   numeric character logical integer
    ## 1       1         a    TRUE       1
    ## 2       2         b    TRUE       1
    ## 3       3         c   FALSE       1
    ## 4       4         d    TRUE       1
    ## 5       5         e    TRUE       1
    ## 6       6         f   FALSE       1

You can also access these components in their original data structures
with a dollar sign or with square brackets.

``` r
sample_list$character
```

    ## [1] "a" "b" "c" "d" "e" "f"

``` r
sample_list[[3]]
```

    ##   numeric character logical integer
    ## 1       1         a    TRUE       1
    ## 2       2         b    TRUE       1
    ## 3       3         c   FALSE       1
    ## 4       4         d    TRUE       1
    ## 5       5         e    TRUE       1
    ## 6       6         f   FALSE       1

``` r
sample_list[["numeric"]]
```

    ## $integer
    ## [1] 1
    ## 
    ## $numeric
    ## [1] 1 2 3 4 5 6

The idea of accessing sublists is the same as lists (with extra steps).

``` r
sample_list$numeric[1] # as a list
```

    ## $integer
    ## [1] 1

``` r
sample_list[["numeric"]]$integer # as a vector
```

    ## [1] 1

# dplyr

Now that you have seen how to work with data using base R functions, it
is time to turn your attention to data wrangling with the `dplyr`
package. Let’s take a quick peak at the measles data frame.

``` r
measles
```

    ## # A tibble: 66,113 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Ariz… 2018… A J … Publ… Noga… Santa… NA           51   100      -1
    ##  2     2 Ariz… 2018… Acad… Char… Tucs… Pima   NA           22   100      -1
    ##  3     3 Ariz… 2018… Acad… Char… Tucs… Pima   NA           85   100      -1
    ##  4     4 Ariz… 2018… Acad… Char… Phoe… Maric… NA           60   100      -1
    ##  5     5 Ariz… 2018… Accl… Char… Phoe… Maric… NA           43   100      -1
    ##  6     6 Ariz… 2018… Alfr… Publ… Phoe… Maric… NA           36   100      -1
    ##  7     7 Ariz… 2018… All … Char… Phoe… Maric… NA           24   100      -1
    ##  8     8 Ariz… 2018… Amer… Char… Yuma  Yuma   NA           22   100      -1
    ##  9     9 Ariz… 2018… Ariz… Priv… Phoe… Maric… NA           26   100      -1
    ## 10    10 Ariz… 2018… Ariz… Publ… San … Yuma   NA           78   100      -1
    ## # … with 66,103 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

On first glance, you can already see that there are a lot of values that
are missing (NA). Just how many NAs are there under each column?

``` r
apply(measles, 2, function(x) sum(is.na(x)))
```

    ##    index    state     year     name     type     city   county district 
    ##        0        0        0        0    36622    20071     6254    66113 
    ##   enroll      mmr  overall     xrel     xmed     xper      lat      lng 
    ##    16260        0        0    66004    45122    57560     1549     1549

The `apply()` function returns a vector of values obtained by applying a
function to a margin (in this scenario: columns) of an array (in this
scenario: a tibble). Don’t worry too much about the anonymous function
used here.

Knowing that there are no NAs in some of these columns is helpful, but
what is actually contained within these columns? The data dictionary is
available
[here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-02-25/readme.md).

There is obvious a lot of data here. Let’s focus on only a couple of
states and filter out the rest. First, identify the unique names under
the column “State”.

``` r
unique(measles$state)
```

    ##  [1] "Arizona"        "Arkansas"       "California"     "Colorado"      
    ##  [5] "Connecticut"    "Florida"        "Idaho"          "Illinois"      
    ##  [9] "Iowa"           "Maine"          "Massachusetts"  "Michigan"      
    ## [13] "Minnesota"      "Missouri"       "Montana"        "New Jersey"    
    ## [17] "New York"       "North Carolina" "North Dakota"   "Oklahoma"      
    ## [21] "Ohio"           "Oregon"         "Pennsylvania"   "Rhode Island"  
    ## [25] "South Dakota"   "Tennessee"      "Texas"          "Utah"          
    ## [29] "Vermont"        "Virginia"       "Washington"     "Wisconsin"

Then, use `filter()` to keep only California, Michigan, New York, and
Utah (these were chosen at a whim). Be sure to assign it to another
object to keep the original measles data frame in the environment.

``` r
CAMINYUT <- filter(measles, state == "California" | state == "Michigan" | state == "New York" | state == "Utah")
```

Let’s take a look at the unique names under the column “state” again.

``` r
unique(CAMINYUT$state)
```

    ## [1] "California" "Michigan"   "New York"   "Utah"

Success\! A cleaner way of filtering the data may have looked something
like this:

``` r
filter(measles, state %in% c("California", "Michigan", "New York", "Utah"))
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

A quick glance at the dimensions of CAMINYUT and the console output
indicates that the two resulting data frames share the same dimensions.
If you are interested in arranging the data by enrollment, you can use
the `arrange()` function:

``` r
arrange(CAMINYUT, enroll)
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1  6550 Cali… 2018… All … Priv… San … Marin  NA            1    -1      -1
    ##  2  6557 Cali… 2018… Almo… Priv… Oran… Sacra… NA            1    -1      -1
    ##  3  6564 Cali… 2018… Amaa… Priv… Anah… Orange NA            1    -1      -1
    ##  4  6567 Cali… 2018… Anit… Priv… Duar… Los A… NA            1    -1      -1
    ##  5  6610 Cali… 2018… Beac… Priv… Mont… Shasta NA            1    -1      -1
    ##  6  6617 Cali… 2018… Bere… Priv… Ange… San B… NA            1    -1      -1
    ##  7  6634 Cali… 2018… Bibl… Priv… Mary… Yuba   NA            1    -1      -1
    ##  8  6643 Cali… 2018… Biol… Priv… La M… Los A… NA            1    -1      -1
    ##  9  6645 Cali… 2018… Blai… Priv… El D… El Do… NA            1    -1      -1
    ## 10  6667 Cali… 2018… Brid… Priv… Perr… River… NA            1    -1      -1
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

``` r
arrange(CAMINYUT, desc(enroll))
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1   482 Utah  2017… Trav… Publ… Lehi  Utah   NA         1246  93.5      -1
    ##  2   535 Utah  2017… Vine… Publ… Orem  Utah   NA         1202  91.6      -1
    ##  3   432 Utah  2017… Bluf… Publ… Bluf… Salt … NA         1179  94.7      -1
    ##  4   468 Utah  2017… Fox … Publ… Lehi  Utah   NA         1115  93.8      -1
    ##  5   281 Utah  2017… Foot… Publ… Rive… Salt … NA         1056  96.6      -1
    ##  6   520 Utah  2017… Eagl… Publ… Lehi  Utah   NA         1056  92.2      -1
    ##  7   326 Utah  2017… Sout… Publ… Sout… Salt … NA         1046  96.0      -1
    ##  8   117 Utah  2017… Sand… Publ… Layt… Davis  NA         1042  98.3      -1
    ##  9   499 Utah  2017… Free… Publ… High… Utah   NA         1032  92.9      -1
    ## 10   499 Utah  2017… Free… Publ… High… Utah   NA         1032  92.9      -1
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

For most analysis, the ordering of your data is unlikely to make a
difference, but it can be helpful if you wanted to take a quick glance
at the data.

If you decide that you no longer need certain columns, you can remove
them with `select()`. Let’s remove lat and lng. You could explicitly
name all other columns.

``` r
select(CAMINYUT, "index", "state", "year", "name", "type", "city", "county", "district", "enroll", "mmr", "overall", "xrel", "xmed", "xper")
```

    ## # A tibble: 23,328 x 14
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 3 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>

Alternatively, you can take advantage of the fact that lat and lng are
the last two columns and keep all columns between index and xper
instead.

``` r
select(CAMINYUT, "index":"xper")
```

    ## # A tibble: 23,328 x 14
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 3 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>

Another method is to state the names of columns you no longer need, but
add a subtraction or a minus symbol before it.

``` r
select(CAMINYUT, -"lat", -"lng")
```

    ## # A tibble: 23,328 x 14
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 3 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>

What if you wanted to rearrange the columns. For example, how would you
move the mmr column immediately to the right of index? You could use
`select()`.

``` r
select(CAMINYUT, index, mmr, everything())
```

    ## # A tibble: 23,328 x 16
    ##    index   mmr state year  name  type  city  county district enroll overall
    ##    <dbl> <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl>   <dbl>
    ##  1     1    99 Cali… 2018… Abby… Publ… Teme… River… NA          137      96
    ##  2     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  3     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  4     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  5     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  6     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  7     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  8     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ##  9     2    99 Cali… 2018… Abra… Publ… Sant… Orange NA          135      99
    ## 10     3    99 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116      99
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

You could also use `relocate()` to move several columns at once.

``` r
relocate(CAMINYUT, enroll:overall, .after = index)
```

    ## # A tibble: 23,328 x 16
    ##    index enroll   mmr overall state year  name  type  city  county district
    ##    <dbl>  <dbl> <dbl>   <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>   
    ##  1     1    137    99      96 Cali… 2018… Abby… Publ… Teme… River… NA      
    ##  2     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  3     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  4     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  5     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  6     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  7     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  8     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ##  9     2    135    99      99 Cali… 2018… Abra… Publ… Sant… Orange NA      
    ## 10     3    116    99      99 Cali… 2018… Achi… Publ… Oakl… Alame… NA      
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

What if you wanted to rename columns? For example, you may want to
rename overall to overall\_rate to more clearly indicate that this
column reflects percent vaccination. You can do that using the
`rename()` function.

``` r
rename(CAMINYUT, overall_rate = overall)
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall_rate
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>        <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99           96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99           99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99           99
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

What if you wanted to change the values of a column? Perhaps you want
decimal proportions rather than percentages. Give `mutate()` a try.

``` r
mutate(CAMINYUT, overall = overall/100)
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99    0.96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99    0.99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99    0.99
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

Perhaps you want to shorten Public and Private to Pub and Priv,
respectively, but keep all other labels under type the same:

``` r
mutate(CAMINYUT, type = ifelse(type == "Public", "Pub", 
                               ifelse(type == "Private", "Priv", type)))
```

    ## # A tibble: 23,328 x 16
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Pub   Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Pub   Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Pub   Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 5 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>, lat <dbl>, lng <dbl>

You can also use `mutate()` to create new columns.

``` r
CAMINYUT %>% 
  mutate(overall_rate = overall/100) %>% 
  select(index:overall, overall_rate)
```

    ## # A tibble: 23,328 x 12
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 1 more variable: overall_rate <dbl>

Notice the pipe operator `%>%` (keyboard shortcut: Ctrl+Shift+M or
Cmd+Shift+M) introduced in the above lines of code. You can think of the
pipe as feeding whatever is on the left of the operator into the
function as the first argument. This works nicely for dplyr because the
first argument is invariably the data structure.

If you want to pipe something into a function as a second or a third
argument, you would have to do something like this:

``` r
mean_val <- 10
set.seed(1)
mean_val %>% rnorm(5, ., 1)
```

    ## [1]  9.373546 10.183643  9.164371 11.595281 10.329508

`.` denotes the position to which you would pipe.

Why use the pipe? The pipe makes for easier to read code. You could have
done something like this instead, but it is harder to read at a glance:

``` r
select(mutate(CAMINYUT, overall_rate = overall/100), index:overall, overall_rate)
```

    ## # A tibble: 23,328 x 12
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Cali… 2018… Achi… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows, and 1 more variable: overall_rate <dbl>

There are many other functions included in `dplyr`. The last ones that
you should try out today are `group_by()` and `summarise()`. Give this a
try:

``` r
CAMINYUT %>% 
  group_by(state) %>% 
  summarise(mean_mmr = mean(mmr))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

    ## # A tibble: 4 x 2
    ##   state      mean_mmr
    ##   <chr>         <dbl>
    ## 1 California     85.0
    ## 2 Michigan       -1  
    ## 3 New York       93.4
    ## 4 Utah           95.0

The output shows the average MMR vaccination rate for the different
states. Perhaps it would have been a smart idea to filter out all states
without MMR data (like Michigan).

Let’s end by combining two separate different frames with one column in
common. First, split up CAMINYUT, but keep enough information to link
the two separate data frames.

``` r
CAMINYUT1 <- CAMINYUT %>% 
  select(index:district, "enroll", "mmr", "overall")

CAMINYUT2 <- CAMINYUT %>% 
  select(index:district, "xrel", "xmed", "xper")

CAMINYUT1
```

    ## # A tibble: 23,328 x 11
    ##    index state  year   name     type  city  county district enroll   mmr overall
    ##    <dbl> <chr>  <chr>  <chr>    <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Calif… 2018-… Abby Re… Publ… Teme… River… NA          137    99      96
    ##  2     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Calif… 2018-… Abraham… Publ… Sant… Orange NA          135    99      99
    ## 10     3 Calif… 2018-… Achieve… Publ… Oakl… Alame… NA          116    99      99
    ## # … with 23,318 more rows

``` r
CAMINYUT2
```

    ## # A tibble: 23,328 x 11
    ##    index state   year   name       type  city  county district xrel   xmed  xper
    ##    <dbl> <chr>   <chr>  <chr>      <chr> <chr> <chr>  <lgl>    <lgl> <dbl> <dbl>
    ##  1     1 Califo… 2018-… Abby Rein… Publ… Teme… River… NA       NA        2    NA
    ##  2     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  3     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  4     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  5     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  6     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  7     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  8     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ##  9     2 Califo… 2018-… Abraham L… Publ… Sant… Orange NA       NA        1    NA
    ## 10     3 Califo… 2018-… Achieve A… Publ… Oakl… Alame… NA       NA        1    NA
    ## # … with 23,318 more rows

Perhaps you started working with two data frames. One with the actual
vaccination data and one with the percentage of students exempt from
vaccinations for religious, medical, or personal reasons. Let’s combine
these two data frames using join functions. In this case, it seems
appropriate to use `left_join()`.

``` r
left_join(CAMINYUT1, CAMINYUT2)
```

    ## Joining, by = c("index", "state", "year", "name", "type", "city", "county", "district")

    ## # A tibble: 138,274 x 14
    ##    index state year  name  type  city  county district enroll   mmr overall
    ##    <dbl> <chr> <chr> <chr> <chr> <chr> <chr>  <lgl>     <dbl> <dbl>   <dbl>
    ##  1     1 Cali… 2018… Abby… Publ… Teme… River… NA          137    99      96
    ##  2     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  3     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  4     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  5     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  6     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  7     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  8     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ##  9     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## 10     2 Cali… 2018… Abra… Publ… Sant… Orange NA          135    99      99
    ## # … with 138,264 more rows, and 3 more variables: xrel <lgl>, xmed <dbl>,
    ## #   xper <dbl>

Most of the time, the function will be able to piece together which
columns are common between two data frames. This will inform R how best
to combine the two data frames into one. If you have columns with
slightly different names, you will have to specify additional arguments
(see `?left_join()`).

# Wrapping up by exporting data

This more or less wraps up the second workshop.

Let’s review how to export data.

``` r
write_csv(measles, path = "measles.csv") # the specified path will create a data folder within your current working directory  
```

To make sure that this was exported correctly, import the csv file, this
time from your local disk, then compare it to the version that still
exists in your environment

``` r
measles_local <- read_csv("measles.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   index = col_double(),
    ##   state = col_character(),
    ##   year = col_character(),
    ##   name = col_character(),
    ##   type = col_character(),
    ##   city = col_character(),
    ##   county = col_character(),
    ##   district = col_logical(),
    ##   enroll = col_double(),
    ##   mmr = col_double(),
    ##   overall = col_double(),
    ##   xrel = col_logical(),
    ##   xmed = col_double(),
    ##   xper = col_double(),
    ##   lat = col_double(),
    ##   lng = col_double()
    ## )

``` r
identical(measles_local, measles)
```

    ## [1] TRUE

Everything checks out\! Hope you learned something new.

See you next time\!
