# Advanced Subsetting




``` r
library(tidyverse) # Load tidyverse packages
library(kableExtra)
library(knitr)
```

Previously, you learned about *subsetting*, which is choosing certain columns or values in your dataframe to keep (or exclude) from your analysis. This chapter expands on this idea to show more advanced techniques, especially for working for large datasets. The large dataset used in examples throughout is called `HELPfull` (`mosaicData::HELPfull`), contains data from the **H**ealth **E**valuation and **L**inkage to **P**rimary Care study. From the dataset's description:

- "The HELP study was a clinical trial for adult inpatients recruited from a detoxification unit. Patients with no primary care physician were randomized to receive a multidisciplinary assessment and a brief motivational intervention or usual care, with the goal of linking them to primary medical care."

This data is saved in a variable called `dat`.



This dataset is ***massive***, so it is not even worth using `glimpse()` to preview it.


``` r
ncol(dat)
#> [1] 788
nrow(dat)
#> [1] 1472
```

It contains 788(!!) variables from 1472 observations!!! Throughout, as has been done previously, a `head()` call will be added at the end of most of the code just to prevent having a ***giant*** output in every block! 

<p class="text-info"> **<u>Note:</u> Using `head()` here is only to make these notes easier for you to understand. This is <u>NOT</u> something that should *always* be used in your code!!**</p>

## Advanced Selecting

### Helper Functions: Select

In the case of the `HELPfull` dataset, there are a **TON** of variables, and it would be quite annoying to have to explicitly specify all of them that you want by full name. Luckily, there are a number of **<u>helper functions</u>** that you can use with `select()` to help make selecting variables easier. What they do is select the variable names you want by *matching* them based on some specified criteria.

#### `starts_with()`

`starts_with()` will select all variables that **start** with a specified pattern.


``` r
dat %>%
  select(starts_with("RAW")) %>%
  head()
#>   RAWPF RAWRP RAWBP RAWGH RAWVT RAWSF RAWRE RAWMH RAW_RE
#> 1    28     7   9.4  21.4    13     4     4    15     33
#> 2    28     8  10.0  21.4    19     9     6    23     24
#> 3    30     8   8.2  22.4    16    10     6    27     15
#> 4    29     8  10.4  19.0    20    10     6    27     29
#> 5    21     4   8.1   7.0     9     6     3    13     34
#> 6    30     8  12.0  18.4    15     9     5    21     35
#>   RAW_AM RAW_TS RAW_ADS
#> 1     14     38       5
#> 2     12     33      NA
#> 3      8     36      NA
#> 4     13     37      NA
#> 5      8     30      26
#> 6      7     18      NA
```

#### `ends_with()`

`ends_with()` will select all variables that **end** with a specified pattern.


``` r
dat %>%
  select(ends_with("ABUSE")) %>%
  head()
#>   PHYABUSE SEXABUSE FAMABUSE STRABUSE ABUSE
#> 1        1        1        1        1     1
#> 2       NA       NA       NA       NA    NA
#> 3       NA       NA       NA       NA    NA
#> 4       NA       NA       NA       NA    NA
#> 5        1        0        1        0     1
#> 6       NA       NA       NA       NA    NA
```

#### `contains()`

`contains()` selects variables if their name **contains** a specified pattern.


``` r
dat %>%
  select(contains("RISK")) %>%
  head()
#>   DRUGRISK SEXRISK
#> 1        0       4
#> 2        0       1
#> 3        0       1
#> 4        0       3
#> 5        0       7
#> 6        0       0
```

This also works on special characters.


``` r
dat %>%
  select(contains("_")) %>% 
  select(1:7) %>%
  # Take just the first 7 columns
  # to save space
  head()
#>   NUM_INTERVALS INT_TIME1 DAYS_SINCE_BL INT_TIME2
#> 1             1  0.000000            NA  6.000000
#> 2             1  8.033333           241  8.033333
#> 3             2 15.533333           466  7.500000
#> 4             1 27.566667           827 12.033333
#> 5             1  0.000000            NA  6.000000
#> 6             1  6.366667           191  6.366667
#>   DAYS_SINCE_PREV PREV_TIME A14G_T
#> 1              NA        NA       
#> 2             241         0       
#> 3             225         6       
#> 4             361        18       
#> 5              NA        NA       
#> 6             191         0   JAIL
```

#### `matches()`

`matches()` works similarly to `contains()` but uses *regular expressions* rather than patterns.

##### Regular Expressions {#regex}

Regular expressions (regex) are a way to select certain *kinds* of characters. When you want to refer to a class/type of character, you can select them with a regex.

Regular expressions all get wrapped in brackets. So, to use any of these, wrap them in brackets and then quotes. (i.e., they need double brackets in quotes to be used!):

- **[:punct:]** - punctuation.
- **[:alpha:]** - letters.
- **[:lower:]** - lowercase letters.
- **[:upper:]** - upperclass letters.
- **[:digit:]** - digits.
- **[:xdigit:]** - hex digits.
- **[:alnum:]** - letters and numbers.
- **[:cntrl:]** - control characters.
- **[:graph:]** - letters, numbers, and punctuation.
- **[:print:]** - letters, numbers, punctuation, and whitespace.
- **[:space:]** - space characters (basically equivalent to `\s`).
- **[:blank:]** - space and tab.


``` r
dat %>%
  select(matches("[[:digit:]]")) %>%
  select(1:7) %>%
  # Take just the first 7 columns
  # to save space
  head()
#>   INT_TIME1 INT_TIME2 A1 A9 A10 A11A A11B
#> 1  0.000000  6.000000  1  9   1    1    0
#> 2  8.033333  8.033333 NA NA   2    1    0
#> 3 15.533333  7.500000 NA NA   1    1    0
#> 4 27.566667 12.033333 NA NA   4    1    0
#> 5  0.000000  6.000000  1 12   6    1    0
#> 6  6.366667  6.366667 NA NA   6    1    0
```

<!-- #### `num_range()`

`num_range()` matches a series of columns that has a string ending with a numerical range, e.g., x01, x02, x03.


``` r
dat %>%
  select(num_range("M", 1:15)) %>%
  head()
#>   M1 M2 M3 M4 M5 M6 M7 M8 M9 M10 M11 M12 M13 M14 M15
#> 1  1  1  1  1  1  1  1  1  1   1   0   1   1   1   1
#> 2  0  1  0  0  1  0  0  0  0   0   0   1   0   0   0
#> 3  0  0  0  0  0  0  0  0  0   0   0   0   0   0   0
#> 4  0  0  0  0  3  0  1  2  0   2   0   2   2   1   0
#> 5  1  1  1  1  1  1  1  1  1   1   1   1   1   1   0
#> 6  1  0  0  0  1  0  0  0  0   0   0   0   0   0   1
```

#### `last_col()`

`last_col()` selects the last column in a df. This can be especially helpful when paired with other functions. For example, when positioning columns in mutate or relocate:


``` r
dat %>%
  mutate(newRow = "test", .after = last_col()) %>%
  select(last_col()) %>%
  head()
#>   newRow
#> 1   test
#> 2   test
#> 3   test
#> 4   test
#> 5   test
#> 6   test
```

::: {.rmdimportant}
**This creates a new variable after the last column in the df, and then selects the last column (which is now the newly created variable).**
::: -->

#### `all_of()`

`all_of()` matches variable names in a character vector. **All** names must be present, otherwise an error is thrown. In this way, `all_of()` is good when strict selection is needed and an error could have wide ranging consequences.


``` r
dat %>%
  select(all_of(c("DRUGRISK", "SEXRISK"))) %>%
  head()
#>   DRUGRISK SEXRISK
#> 1        0       4
#> 2        0       1
#> 3        0       1
#> 4        0       3
#> 5        0       7
#> 6        0       0
```


``` r
dat %>%
  select(all_of(c("DRUGRISK", "DRUGRISK2"))) %>%
  head()
#> Error in `select()`:
#> ℹ In argument: `all_of(c("DRUGRISK", "DRUGRISK2"))`.
#> Caused by error in `all_of()`:
#> ! Can't subset elements that don't exist.
#> ✖ Element `DRUGRISK2` doesn't exist.
```

::: {.rmdimportant}
**The `DRUGRISK2` variable does not exist in this dataset. Thus, the 2nd code chunk will throw an error.**
:::

#### `any_of()`

`any_of()` works the same as `all_of()`, except that **no error is thrown** for names that do not exist. It will only return the columns from the input that are found in the data and ignore the ones that are not.


``` r
dat %>%
  select(any_of(c("DRUGRISK", "SEXRISK"))) %>%
  head()
#>   DRUGRISK SEXRISK
#> 1        0       4
#> 2        0       1
#> 3        0       1
#> 4        0       3
#> 5        0       7
#> 6        0       0
dat %>%
  select(any_of(c("DRUGRISK", "DRUGRISK2"))) %>%
  head()
#>   DRUGRISK
#> 1        0
#> 2        0
#> 3        0
#> 4        0
#> 5        0
#> 6        0
```

`any_of()` is particularly useful when removing variables from your df, since there will be no error if you are including a variable that already does not exist. In this way, it can be used to make sure a variable is truly removed.

#### `where()`

`where()` takes a function and returns all variables for which the function returns `TRUE`. For example, say you wanted to find how many quantitative variables there are in this df. As a reminder, `ncol()` returns the number of columns of the object passed to it. 


``` r
dat %>%
  select(where(is.numeric)) %>%
  ncol()
#> [1] 782
```

::: {.rmdimportant}
**This takes the `dat` dataframe, selects only the columns that are numeric, and then finds the number of columns.**
:::

<p class="text-info"> **Note: 1. You do not <u>call</u> the function by including parentheses after it, you just *NAME* the function. 2. The `ncol()` here is only used to make these notes easier for you to understand (and not print hundreds of columns). It is <u>NOT</u> necessary when using `where()`.**</p>

<button class="btn btn-primary" data-toggle="collapse" data-target="#BlockName"> How the Syntax Works</button>  
<div id="BlockName" class="collapse">  

Under the hood of `where()`, it just needs a function. This means it can take functions that are designed on the spot. 

The long form version of the code above would be the following:


``` r
dat %>%
  select(where(function(x) is.numeric(x))) %>%
  ncol()
#> [1] 782
```

Where you define the function that calls `is.numeric()` inline. A slightly condensed version of this inline defining looks like this:


``` r
dat %>%
  select(where(~ is.numeric(.x))) %>%
  ncol()
#> [1] 782
```

You can replace the `function(x)` part with a `~`. Note that you need `.` before the `x` here though! 

This inline shorthand is really useful because it allows you to string multiple functions together using logic statements. For example, you could first select all the abuse columns, then only the columns that are numeric and have a mean larger than 1.


``` r
dat %>% 
  drop_na(contains("ABUSE")) %>%
  select(contains("ABUSE")) %>%
  select(where(~ is.numeric(.x) && mean(.x) > 1)) %>%
  head()
#>   ABUSE2 ABUSE3
#> 1      3      2
#> 2      1      1
#> 3      1      1
#> 4      3      2
#> 5      0      0
#> 6      0      0
```

Breaking this down line by line:

- `dat %>%` take the `dat` dataframe
- `drop_na(contains("ABUSE")) %>%` get rid of the `NA` values in all the columns used, because it will mess things up otherwise (note that you can use the selection helper functions here too!).
- `select(contains("ABUSE")) %>%` select only the columns that contain "ABUSE"
- `select(where(~ is.numeric(.x) && mean(.x) > 1)) %>%` select only the columns that are numeric AND have a mean greater than 1
- `head()` show just the first 5 observations

<p class="text-info"> **NOTE: You are not looking for the mean of the column, the output *IS* the column that has a mean greater than 1. Remember, selection helper functions are to help select certain columns. You can just get VERY specific with which columns you want in this way!**</p>
</div>

<br>

### Selecting & Logical operators

Logical operators work as expected and can be used to create tests with multiple helper functions for even more specified selecting!


``` r
dat %>%
select(starts_with("RAW") | starts_with("ABUSE")) %>%
  head()
#>   RAWPF RAWRP RAWBP RAWGH RAWVT RAWSF RAWRE RAWMH RAW_RE
#> 1    28     7   9.4  21.4    13     4     4    15     33
#> 2    28     8  10.0  21.4    19     9     6    23     24
#> 3    30     8   8.2  22.4    16    10     6    27     15
#> 4    29     8  10.4  19.0    20    10     6    27     29
#> 5    21     4   8.1   7.0     9     6     3    13     34
#> 6    30     8  12.0  18.4    15     9     5    21     35
#>   RAW_AM RAW_TS RAW_ADS ABUSE2 ABUSE3 ABUSE
#> 1     14     38       5      3      2     1
#> 2     12     33      NA     NA     NA    NA
#> 3      8     36      NA     NA     NA    NA
#> 4     13     37      NA     NA     NA    NA
#> 5      8     30      26      1      1     1
#> 6      7     18      NA     NA     NA    NA
dat %>%
  select(starts_with("A") & ends_with("C")) %>%
  head()
#>   A11C A14C A15C A16C A17C A12B_REC
#> 1    1    0    0    0    0        1
#> 2    1    0    0   NA    0       NA
#> 3    1    0    0   NA    0       NA
#> 4    1    0    0   NA    0       NA
#> 5    1    0   12   49    0        1
#> 6    1    0  178   NA    0       NA
```

Selection helper functions can be negated with a `!`

Below, only the number of columns is shown to save space, but note the difference! 

First look at how many variables there are total:


``` r
dat %>% 
  ncol()
#> [1] 788
```

Then see how many there are that start with "RAW":


``` r
dat %>%
  select(starts_with("RAW")) %>%
  ncol()
#> [1] 12
```

Then see how many there are after selecting *NOT* the columns that start with "RAW":


``` r
dat %>%
  select(!starts_with("RAW")) %>%
  ncol()
#> [1] 776
```

The total number of columns has decreased by exactly the number of columns that start with "RAW"!

## Advanced Filtering

Remember that filtering subsets the data by rows or observations instead of columns. In this section, we will use the `mtcars` dataset because it's a little bit more managable than the dataset we used above.




### %in%

Stringing together multiple tests of text patterns would be very cumbersome. Fortunately, there is an alternative with the `%in%` operator. As a reminder, this will check to see if the value of one variable is in a vector of possible values. Test conditions using the `%in%` operator are used the same way as all test conditions using all other operators: you must specify the variable to search within and what values to return (those in the vector).


``` r
mtcars2 %>%
  filter(model %in% c("Lotus Europa", "Ferrari Dino", "Volvo 142E"))
```



<table class="kable_wrapper">
<tbody>
  <tr>
   <td> 

<table class=" lightable-paper table table-striped table-hover table-condensed table-responsive" style='font-family: "Arial Narrow", arial, helvetica, sans-serif; width: auto !important; margin-left: auto; margin-right: auto; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> model </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> mpg </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> cyl </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> disp </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> hp </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> drat </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> wt </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> qsec </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> vs </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> am </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> gear </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> carb </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;color: white !important;"> Mazda RX4 </td>
   <td style="text-align:right;color: white !important;"> 21.0 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 160.0 </td>
   <td style="text-align:right;color: white !important;"> 110 </td>
   <td style="text-align:right;color: white !important;"> 3.90 </td>
   <td style="text-align:right;color: white !important;"> 2.620 </td>
   <td style="text-align:right;color: white !important;"> 16.46 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Mazda RX4 Wag </td>
   <td style="text-align:right;color: white !important;"> 21.0 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 160.0 </td>
   <td style="text-align:right;color: white !important;"> 110 </td>
   <td style="text-align:right;color: white !important;"> 3.90 </td>
   <td style="text-align:right;color: white !important;"> 2.875 </td>
   <td style="text-align:right;color: white !important;"> 17.02 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Datsun 710 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 22.8 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 108.0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 93 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.85 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2.320 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 18.61 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Hornet 4 Drive </td>
   <td style="text-align:right;color: white !important;"> 21.4 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 258.0 </td>
   <td style="text-align:right;color: white !important;"> 110 </td>
   <td style="text-align:right;color: white !important;"> 3.08 </td>
   <td style="text-align:right;color: white !important;"> 3.215 </td>
   <td style="text-align:right;color: white !important;"> 19.44 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Hornet Sportabout </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 18.7 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 8 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 360.0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 175 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.15 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.440 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 17.02 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Valiant </td>
   <td style="text-align:right;color: white !important;"> 18.1 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 225.0 </td>
   <td style="text-align:right;color: white !important;"> 105 </td>
   <td style="text-align:right;color: white !important;"> 2.76 </td>
   <td style="text-align:right;color: white !important;"> 3.460 </td>
   <td style="text-align:right;color: white !important;"> 20.22 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Duster 360 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 14.3 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 8 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 360.0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 245 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.21 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.570 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 15.84 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 240D </td>
   <td style="text-align:right;color: white !important;"> 24.4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 146.7 </td>
   <td style="text-align:right;color: white !important;"> 62 </td>
   <td style="text-align:right;color: white !important;"> 3.69 </td>
   <td style="text-align:right;color: white !important;"> 3.190 </td>
   <td style="text-align:right;color: white !important;"> 20.00 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 230 </td>
   <td style="text-align:right;color: white !important;"> 22.8 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 140.8 </td>
   <td style="text-align:right;color: white !important;"> 95 </td>
   <td style="text-align:right;color: white !important;"> 3.92 </td>
   <td style="text-align:right;color: white !important;"> 3.150 </td>
   <td style="text-align:right;color: white !important;"> 22.90 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 280 </td>
   <td style="text-align:right;color: white !important;"> 19.2 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 167.6 </td>
   <td style="text-align:right;color: white !important;"> 123 </td>
   <td style="text-align:right;color: white !important;"> 3.92 </td>
   <td style="text-align:right;color: white !important;"> 3.440 </td>
   <td style="text-align:right;color: white !important;"> 18.30 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 280C </td>
   <td style="text-align:right;color: white !important;"> 17.8 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 167.6 </td>
   <td style="text-align:right;color: white !important;"> 123 </td>
   <td style="text-align:right;color: white !important;"> 3.92 </td>
   <td style="text-align:right;color: white !important;"> 3.440 </td>
   <td style="text-align:right;color: white !important;"> 18.90 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 450SE </td>
   <td style="text-align:right;color: white !important;"> 16.4 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 275.8 </td>
   <td style="text-align:right;color: white !important;"> 180 </td>
   <td style="text-align:right;color: white !important;"> 3.07 </td>
   <td style="text-align:right;color: white !important;"> 4.070 </td>
   <td style="text-align:right;color: white !important;"> 17.40 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 450SL </td>
   <td style="text-align:right;color: white !important;"> 17.3 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 275.8 </td>
   <td style="text-align:right;color: white !important;"> 180 </td>
   <td style="text-align:right;color: white !important;"> 3.07 </td>
   <td style="text-align:right;color: white !important;"> 3.730 </td>
   <td style="text-align:right;color: white !important;"> 17.60 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Merc 450SLC </td>
   <td style="text-align:right;color: white !important;"> 15.2 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 275.8 </td>
   <td style="text-align:right;color: white !important;"> 180 </td>
   <td style="text-align:right;color: white !important;"> 3.07 </td>
   <td style="text-align:right;color: white !important;"> 3.780 </td>
   <td style="text-align:right;color: white !important;"> 18.00 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Cadillac Fleetwood </td>
   <td style="text-align:right;color: white !important;"> 10.4 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 472.0 </td>
   <td style="text-align:right;color: white !important;"> 205 </td>
   <td style="text-align:right;color: white !important;"> 2.93 </td>
   <td style="text-align:right;color: white !important;"> 5.250 </td>
   <td style="text-align:right;color: white !important;"> 17.98 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Lincoln Continental </td>
   <td style="text-align:right;color: white !important;"> 10.4 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 460.0 </td>
   <td style="text-align:right;color: white !important;"> 215 </td>
   <td style="text-align:right;color: white !important;"> 3.00 </td>
   <td style="text-align:right;color: white !important;"> 5.424 </td>
   <td style="text-align:right;color: white !important;"> 17.82 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Chrysler Imperial </td>
   <td style="text-align:right;color: white !important;"> 14.7 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 440.0 </td>
   <td style="text-align:right;color: white !important;"> 230 </td>
   <td style="text-align:right;color: white !important;"> 3.23 </td>
   <td style="text-align:right;color: white !important;"> 5.345 </td>
   <td style="text-align:right;color: white !important;"> 17.42 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Fiat 128 </td>
   <td style="text-align:right;color: white !important;"> 32.4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 78.7 </td>
   <td style="text-align:right;color: white !important;"> 66 </td>
   <td style="text-align:right;color: white !important;"> 4.08 </td>
   <td style="text-align:right;color: white !important;"> 2.200 </td>
   <td style="text-align:right;color: white !important;"> 19.47 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Honda Civic </td>
   <td style="text-align:right;color: white !important;"> 30.4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 75.7 </td>
   <td style="text-align:right;color: white !important;"> 52 </td>
   <td style="text-align:right;color: white !important;"> 4.93 </td>
   <td style="text-align:right;color: white !important;"> 1.615 </td>
   <td style="text-align:right;color: white !important;"> 18.52 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Toyota Corolla </td>
   <td style="text-align:right;color: white !important;"> 33.9 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 71.1 </td>
   <td style="text-align:right;color: white !important;"> 65 </td>
   <td style="text-align:right;color: white !important;"> 4.22 </td>
   <td style="text-align:right;color: white !important;"> 1.835 </td>
   <td style="text-align:right;color: white !important;"> 19.90 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Toyota Corona </td>
   <td style="text-align:right;color: white !important;"> 21.5 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 120.1 </td>
   <td style="text-align:right;color: white !important;"> 97 </td>
   <td style="text-align:right;color: white !important;"> 3.70 </td>
   <td style="text-align:right;color: white !important;"> 2.465 </td>
   <td style="text-align:right;color: white !important;"> 20.01 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Dodge Challenger </td>
   <td style="text-align:right;color: white !important;"> 15.5 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 318.0 </td>
   <td style="text-align:right;color: white !important;"> 150 </td>
   <td style="text-align:right;color: white !important;"> 2.76 </td>
   <td style="text-align:right;color: white !important;"> 3.520 </td>
   <td style="text-align:right;color: white !important;"> 16.87 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> AMC Javelin </td>
   <td style="text-align:right;color: white !important;"> 15.2 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 304.0 </td>
   <td style="text-align:right;color: white !important;"> 150 </td>
   <td style="text-align:right;color: white !important;"> 3.15 </td>
   <td style="text-align:right;color: white !important;"> 3.435 </td>
   <td style="text-align:right;color: white !important;"> 17.30 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Camaro Z28 </td>
   <td style="text-align:right;color: white !important;"> 13.3 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 350.0 </td>
   <td style="text-align:right;color: white !important;"> 245 </td>
   <td style="text-align:right;color: white !important;"> 3.73 </td>
   <td style="text-align:right;color: white !important;"> 3.840 </td>
   <td style="text-align:right;color: white !important;"> 15.41 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Pontiac Firebird </td>
   <td style="text-align:right;color: white !important;"> 19.2 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 400.0 </td>
   <td style="text-align:right;color: white !important;"> 175 </td>
   <td style="text-align:right;color: white !important;"> 3.08 </td>
   <td style="text-align:right;color: white !important;"> 3.845 </td>
   <td style="text-align:right;color: white !important;"> 17.05 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 3 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Fiat X1-9 </td>
   <td style="text-align:right;color: white !important;"> 27.3 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 79.0 </td>
   <td style="text-align:right;color: white !important;"> 66 </td>
   <td style="text-align:right;color: white !important;"> 4.08 </td>
   <td style="text-align:right;color: white !important;"> 1.935 </td>
   <td style="text-align:right;color: white !important;"> 18.90 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Porsche 914-2 </td>
   <td style="text-align:right;color: white !important;"> 26.0 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 120.3 </td>
   <td style="text-align:right;color: white !important;"> 91 </td>
   <td style="text-align:right;color: white !important;"> 4.43 </td>
   <td style="text-align:right;color: white !important;"> 2.140 </td>
   <td style="text-align:right;color: white !important;"> 16.70 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 5 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Lotus Europa </td>
   <td style="text-align:right;color: white !important;"> 30.4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 95.1 </td>
   <td style="text-align:right;color: white !important;"> 113 </td>
   <td style="text-align:right;color: white !important;"> 3.77 </td>
   <td style="text-align:right;color: white !important;"> 1.513 </td>
   <td style="text-align:right;color: white !important;"> 16.90 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 5 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Ford Pantera L </td>
   <td style="text-align:right;color: white !important;"> 15.8 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 351.0 </td>
   <td style="text-align:right;color: white !important;"> 264 </td>
   <td style="text-align:right;color: white !important;"> 4.22 </td>
   <td style="text-align:right;color: white !important;"> 3.170 </td>
   <td style="text-align:right;color: white !important;"> 14.50 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 5 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Ferrari Dino </td>
   <td style="text-align:right;color: white !important;"> 19.7 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
   <td style="text-align:right;color: white !important;"> 145.0 </td>
   <td style="text-align:right;color: white !important;"> 175 </td>
   <td style="text-align:right;color: white !important;"> 3.62 </td>
   <td style="text-align:right;color: white !important;"> 2.770 </td>
   <td style="text-align:right;color: white !important;"> 15.50 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 5 </td>
   <td style="text-align:right;color: white !important;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Maserati Bora </td>
   <td style="text-align:right;color: white !important;"> 15.0 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
   <td style="text-align:right;color: white !important;"> 301.0 </td>
   <td style="text-align:right;color: white !important;"> 335 </td>
   <td style="text-align:right;color: white !important;"> 3.54 </td>
   <td style="text-align:right;color: white !important;"> 3.570 </td>
   <td style="text-align:right;color: white !important;"> 14.60 </td>
   <td style="text-align:right;color: white !important;"> 0 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 5 </td>
   <td style="text-align:right;color: white !important;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;"> Volvo 142E </td>
   <td style="text-align:right;color: white !important;"> 21.4 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 121.0 </td>
   <td style="text-align:right;color: white !important;"> 109 </td>
   <td style="text-align:right;color: white !important;"> 4.11 </td>
   <td style="text-align:right;color: white !important;"> 2.780 </td>
   <td style="text-align:right;color: white !important;"> 18.60 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 1 </td>
   <td style="text-align:right;color: white !important;"> 4 </td>
   <td style="text-align:right;color: white !important;"> 2 </td>
  </tr>
</tbody>
</table>

 </td>
   <td> 

<table class=" lightable-paper" style='font-family: "Arial Narrow", arial, helvetica, sans-serif; width: auto !important; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;font-size: 0px;"> test </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;">  <img src="figures/blank.png" width="64" height="32">
</td>
  </tr>
  <tr>
   <td style="text-align:left;">  <img src="figures/blank.png" width="64" height="32">
</td>
  </tr>
  <tr>
   <td style="text-align:left;">  <img src="figures/blank.png" width="64" height="32">
</td>
  </tr>
  <tr>
   <td style="text-align:left;">  <img src="figures/arrow.png" width="64" height="32">
</td>
  </tr>
</tbody>
</table>

 </td>
   <td> 

<table class=" lightable-paper table table-striped table-hover table-condensed table-responsive" style='font-family: "Arial Narrow", arial, helvetica, sans-serif; width: auto !important; margin-left: auto; margin-right: auto; margin-left: auto; margin-right: auto;'>
 <thead>
  <tr>
   <th style="text-align:left;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> model </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> mpg </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> cyl </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> disp </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> hp </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> drat </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> wt </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> qsec </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> vs </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> am </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> gear </th>
   <th style="text-align:right;font-weight: bold;text-decoration: underline;color: white !important;text-align: center;"> carb </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Lotus Europa </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 30.4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 95.1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 113 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.77 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1.513 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 16.9 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 5 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Ferrari Dino </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 19.7 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 6 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 145.0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 175 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 3.62 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2.770 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 15.5 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 5 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 6 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: goldenrod !important;"> Volvo 142E </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 21.4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 121.0 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 109 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4.11 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2.780 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 18.6 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 1 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 4 </td>
   <td style="text-align:right;color: white !important;background-color: goldenrod !important;"> 2 </td>
  </tr>
</tbody>
</table>

 </td>
  </tr>
</tbody>
</table>



::: {.rmdimportant}
**This filters the dataframe to return only rows where the value of `model` is "Lotus Europa", "Ferrari Dino", or "Volvo 142E".**
:::


As `%in%` is an operator, it can also be combined with other operators!


``` r
mtcars2 %>%
  filter(hp > 300 | model %in% c("Lotus Europa", "Ferrari Dino", "Volvo 142E"))
#>           model  mpg cyl  disp  hp drat    wt qsec vs am
#> 1  Lotus Europa 30.4   4  95.1 113 3.77 1.513 16.9  1  1
#> 2  Ferrari Dino 19.7   6 145.0 175 3.62 2.770 15.5  0  1
#> 3 Maserati Bora 15.0   8 301.0 335 3.54 3.570 14.6  0  1
#> 4    Volvo 142E 21.4   4 121.0 109 4.11 2.780 18.6  1  1
#>   gear carb
#> 1    5    2
#> 2    5    6
#> 3    5    8
#> 4    4    2
```

::: {.rmdimportant}
**This filters the dataframe to return only rows where the value of `hp` is greater than 300 OR the value of `model` is "Lotus Europa", "Ferrari Dino", or "Volvo 142E".**
:::

### Pattern Matching

For any number of reasons there may be times you do not want to search for an exact string match. Rather, you want to filter based on whether a value *contains* a certain string. This can be accomplished with `grepl()`, which takes the form:

`grepl(pattern_to_search_for, where_to_search)`


``` r
mtcars2 %>%
  filter(grepl("Porsche", model))
#>           model mpg cyl  disp hp drat   wt qsec vs am gear
#> 1 Porsche 914-2  26   4 120.3 91 4.43 2.14 16.7  0  1    5
#>   carb
#> 1    2
```

::: {.rmdimportant}
**This filters the dataframe to return only rows where `model` contains the string "Porsche" in its value.**
:::

### Helper Functions: Filter

There are two `if_*()` functions which take a selection of columns and apply the same test function to each. The results from each column are combined into individual logical vectors (a vector of `TRUE` and `FALSE`). **Since you are dealing with logical vectors, this is good for *filtering*!**

#### `if_any()` 

Returns `TRUE` when the test evaluates to `TRUE` for *any* of the selected columns.

`filter(if_any(selection_of_columns, filter test to apply to each))`

Since the first argument of `if_any()` is a selection of columns, you can use the same selection helper functions introduced above!


``` r
# Show the number of rows and columns in the full df
dim(dat)
#> [1] 1472  788
# Show the number of rows and columns for
# All observations where the value of at least one of the selected
# Columns is greater than or equal to 1:
dat %>%
  filter(if_any(starts_with("K"), ~ . >= 1)) %>%
  dim()
#> [1] 1462  788
```

::: {.rmdimportant}
**From `dat`, return only the observations where the value of <u>ANY</u> column that starts with "K" is greater than or equal to 1.**
:::

<p class="text-info"> **<u>Note:</u> `dim()` was added here only to save space and make these notes easier for you to understand. Without `dim()`, this code will just output the actual rows of data like any other filter call would (which is what you want!). `dim()` is NOT something that should *always* be used in your code.**</p>

Notice how the number of columns (second value) stays the same, but the number of rows (first value) differs! This is because, even though you are selecting some rows to apply the test to, you are ultimately filtering rows to return, not selecting columns to return.

To break down the unique syntax here:

* It is filtering for observations where any of the columns that start with "K" have a score/value greater than or equal to 1.

So what is a use case for this? Maybe you have a suicidality questionnaire in your dataset and a participant who responds greater than $X$ on any of the questions needs to be contacted for emergency intervention. There are many questions on your suicidality questionnaire (the columns that start with "K" here), and this is a way to quickly filter for the people who responded with 1 or greater on **ANY** of those questions!


#### `if_all()`

`if_all()` is similar to `if_any()`, except it returns `TRUE` **only** when the test evaluates to `TRUE` for **<u>ALL</u>** of the selected columns.


``` r
# Show the number of rows and columns for
# the observations where ALL of the selected
# columns have a value greater than or equal to 1:
dat %>%
  filter(if_all(starts_with("K"), ~ . >= 1)) %>%
  dim()
#> [1] 728 788
```

::: {.rmdimportant}
**From `dat`, return only the observations where the values of <u>ALL</u> columns that start with "K" are greater than or equal to 1.**
:::

Importantly, these functions will not give you an error if you do something incorrect (whereas using `select()` would). Since you are dealing with logical vectors and tests, having nothing return is not inherently indicative of a problem! 

Look what happens when we try this on columns that we know do not exist (there are no columns that start with "Y").


``` r
dat %>%
  select(starts_with("Y"))
#> data frame with 0 columns and 1472 rows
dat %>%
  filter(if_all(starts_with("Y"), ~ . > 75)) %>%
  dim()
#> [1] 1472  788
dat %>%
  dim()
#> [1] 1472  788
```

## Extra Resources

* [Select Vignette](https://dplyr.tidyverse.org/reference/select.html)
