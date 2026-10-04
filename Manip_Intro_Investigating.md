# (PART\*) Data Manipulation {-}




``` r
library(tidyverse)
```

So far in this class, we have learned the basics of how to code in R and we have learned about how to make graphs and visualizations. The next step is *data manipulation*, also known as data tidying, data wrangling, data cleaning, etc. The skills you learn in this unit will help you take a large, messy, and/or unwieldy dataset and make it more manageable, more graphable, and, crucially, more analyzable!

# Tidy Data and Tidyverse

In this class, you already encountered the `tidyverse` package. In this unit, we'll learn a lot more about this package and it's many useful functions. Before getting to that, we first need to talk about what form of data is easiest to work with. What should be the end goal of any data manipulation that we do? The answer is: *tidy data*.

## Tidy Data {#Tidy-Data}

You can represent the same data in many different ways. In almost all cases, the best way to do so is to make sure your data is **tidy**. In tidy data, each row corresponds to a unique observation, each column is a variable, and each cell contains the value for a particular observation and variable.

The series of illustrations below helps explain the concept of tidy data and why it is useful!

What is tidy data?

![](figures/tidydata_1.jpeg){width=100%}

Tidy data are like families.

![](figures/tidydata_2.jpeg){width=100%}

Since you know all sets of tidy data will have the same structure, the same tools can be used across different datasets.

![](figures/tidydata_3.jpeg){width=100%}

This enables universality with the tools being used, rather than all different people trying to accomplish the same task in different ways.

![](figures/tidydata_4.jpeg){width=100%}

This makes your own life better by making it easier for automation & iteration across your projects and datasets.

![](figures/tidydata_5.jpeg){width=100%}

It also makes all other tidy datasets seem more welcoming!

![](figures/tidydata_6.jpeg){width=100%}

![](figures/tidydata_7.jpeg){width=100%}
<p style="font-size:6pt">Illustrations from the Openscapes blog Tidy Data for reproducibility, efficiency, and collaboration by Julia Lowndes and Allison Horst.</p>

## Tidyverse

`tidyverse` is a collection of packages that all "share an underlying design philosophy, grammar, and data structure" of tidy data. The tidyverse packages and functions are the tools you will use to fill your R workbench, and they will help with every step of your workflow. Maybe of the most helpful functions that we will learn this unit come from the package `dplyr`.

![](figures/environmental-data-science-r4ds-general.png){width=100%}
<p style="font-size:6pt"> Updated from Grolemund & Wickham's classis R4DS schematic, envisioned by Dr. Julia Lowndes for her 2019 useR! keynote talk and illustrated by Allison Horst.</p>

This illustrates the Social/Data Science workflow that the tidyverse suite of packages are designed to help you accomplish:

* **Import** -- get data into R

* **Tidy** -- clean and format the data

* **Transform** -- select variables, create new ones, group and summarize

* **Visualize** -- look at the data in different ways

* **Model** -- ~~answer questions about the data~~
  + *we will cover this is the next unit on statistics*

* **Communicate** -- write reproducible research reports

This unit will build your arsenal with tidyverse tools to help manage unruly raw data, as it will almost certainly be the case that the data you get initially will be very messy and need lots of cleaning and prepping!

![](figures/unrulyData.jpeg){width=100%}
<p style="font-size:6pt">Artwork by @allison_horst</p>

## Investigating Data

One of the first things you should do when working with a new dataset is to actually look at it. This might seem obvious and simple, but it is very important because it allows you to get a sense of what type of wrangling and manipulation you may need to apply to be able to work with that data.

### View Full

You can view a data object by using the `View()` function. To inspect the `penguins` object you can run `View(penguins)` or click on the object itself in your RStudio's global environment panel.

### glimpse

Often times datasets are very large and it is impractical to try and comb through the whole file. It would be much more helpful if there was a way to quickly glimpse at your data to get an overall impression of it. There is a very appropriately named function for this: `glimpse()`!


``` r
dplyr::glimpse(penguins)
#> Rows: 344
#> Columns: 8
#> $ species     <fct> Adelie, Adelie, Adelie, Adelie, Adelie…
#> $ island      <fct> Torgersen, Torgersen, Torgersen, Torge…
#> $ bill_len    <dbl> 39.1, 39.5, 40.3, NA, 36.7, 39.3, 38.9…
#> $ bill_dep    <dbl> 18.7, 17.4, 18.0, NA, 19.3, 20.6, 17.8…
#> $ flipper_len <int> 181, 186, 195, NA, 193, 190, 181, 195,…
#> $ body_mass   <int> 3750, 3800, 3250, NA, 3450, 3650, 3625…
#> $ sex         <fct> male, female, female, NA, female, male…
#> $ year        <int> 2007, 2007, 2007, 2007, 2007, 2007, 20…
```

From this one function alone, you can learn a lot about your data:

1. The names of the variables (columns), which you also can get with `names()`.

2. The number of observations (rows, 344) and variables (columns, 8). You can also get this information with the `nrow()` and `ncol()` functions.

3. These variables are saved as either "fct" (factors), "dbl" (double-precision), or "int" (integers).

<!-- <div class="panel panel-success"> -->
<!--   <div class="panel-heading">**EXERCISE 6**</div> -->
<!--   <div class="panel-body"> -->
<!--   1. Use `glimpse()` to exam the `msleep` dataset.<br> -->
<!--   2. What information have you learned about this dataset from the output?</div> -->
<!-- </div> -->

### head

`glimpse()` gives you a high level snapshot of your data, but it can also be useful to look at some actual rows of data. Looking at all of them at once is silly though. You really just want to look at a few observations to see if you can recognize anything that may need to be corrected. The `head()` function can be used for this. It will print the first few rows of data in the argument you pass it to. 


``` r
head(penguins)
#>   species    island bill_len bill_dep flipper_len body_mass
#> 1  Adelie Torgersen     39.1     18.7         181      3750
#> 2  Adelie Torgersen     39.5     17.4         186      3800
#> 3  Adelie Torgersen     40.3     18.0         195      3250
#> 4  Adelie Torgersen       NA       NA          NA        NA
#> 5  Adelie Torgersen     36.7     19.3         193      3450
#> 6  Adelie Torgersen     39.3     20.6         190      3650
#>      sex year
#> 1   male 2007
#> 2 female 2007
#> 3 female 2007
#> 4   <NA> 2007
#> 5 female 2007
#> 6   male 2007
```

You can specify the exact amount of rows you want to print by passing a second argument to `head()` that specifies the number of rows. E.g.,


``` r
head(penguins, 10)
#>    species    island bill_len bill_dep flipper_len
#> 1   Adelie Torgersen     39.1     18.7         181
#> 2   Adelie Torgersen     39.5     17.4         186
#> 3   Adelie Torgersen     40.3     18.0         195
#> 4   Adelie Torgersen       NA       NA          NA
#> 5   Adelie Torgersen     36.7     19.3         193
#> 6   Adelie Torgersen     39.3     20.6         190
#> 7   Adelie Torgersen     38.9     17.8         181
#> 8   Adelie Torgersen     39.2     19.6         195
#> 9   Adelie Torgersen     34.1     18.1         193
#> 10  Adelie Torgersen     42.0     20.2         190
#>    body_mass    sex year
#> 1       3750   male 2007
#> 2       3800 female 2007
#> 3       3250 female 2007
#> 4         NA   <NA> 2007
#> 5       3450 female 2007
#> 6       3650   male 2007
#> 7       3625 female 2007
#> 8       4675   male 2007
#> 9       3475   <NA> 2007
#> 10      4250   <NA> 2007
```

