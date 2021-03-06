% Analytics with R

What we're going to cover
-------------------------

1.  R: what it is, what it isn't.
2.  R and SAS code comparison.
3.  R shortfalls.


R is a programming language
---------------------------

. . .

Defined by a specification:

[http://cran.r-project.org/doc/manuals/r-release/R-lang.pdf](http://cran.r-project.org/doc/manuals/r-release/R-lang.pdf)

![](gifs/r-spec.gif)


R can be executed with an interpreter
-----------------------

Save

```r
print("Hello World!")
```

as `hello_world.R`. Then...

. . .


```bash
$ Rscript hello_world.R
```


R can be executed with an interpreter
-----------------------

Save

```r
print("Hello World!")
```

as `hello_world.R`. Then...

```bash
$ Rscript hello_world.R
[1] "Hello World!"
$
```


R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
```


R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
>
```


R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
> 2 + 3
```


R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
> 2 + 3
[1] 5
```


R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
> 2 + 3
[1] 5
> t.test(mtcars$mpg)
```

R can be executed with a Read Evaluate Print Loop (REPL)
------------------------------------------

```r
$ R
> 2 + 3
[1] 5
> t.test(mtcars$mpg)

    One Sample t-test

data:  mtcars$mpg
t = 18.8569, df = 31, p-value < 2.2e-16
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 17.91768 22.26357
sample estimates:
mean of x
 20.09062
```

R can be executed with an Integrated Development Environment (IDE)
----------------------------------------------------

Debugger, autocomplete, environment inspection, source control integration, plot display, build tool integration...

. . .

![](gifs/RStudio-Screenshot.jpg)


Reading a CSV file in SAS
-------------------------

. . .

```sas
data ds;
    %let _EFIERR_ = 0; /* set the ERROR detection macro variable */
    infile "C:\path\to\file.csv" delimiter = ',' MISSOVER DSD lrecl=32767 firstobs=2;
       informat ID $10.;
       informat DOB yymmdd10.;
       informat Gender $1.;
       informat Age best32.;
       informat Address $127.;
       informat Postcode $4.;
       informat Height best32.;
       format ID $10.;
       format DOB yymmdd10.;
       format Gender $1.;
       format Age best32.;
       format Address $127.;
       format Postcode $4.;
       format Height best32.;
    input
        ID $
        DOB
        Gender $
        Age
        Address $
        Postcode $
        Height
        ;
    if _ERROR_ then call symputx('_EFIERR_',1);  /* set ERROR detection macro variable */
run;
```


Reading a CSV file in SAS
-------------------------

This hurts when

*   Data gets resupplied
*   Data is split over multiple files


Reading a CSV file in SAS
-------------------------

![](gifs/sas-read-csv.gif)


Reading a CSV file in R
-----------------------

```r
df = read.csv("C:\\path\\to\\file.csv", header=TRUE, stringsAsFactors=FALSE)
```


Reading a CSV file in R
-----------------------

![](gifs/ecstatic.gif)


Quiz Time
---------

```SAS
proc sort data=ds_1; by var; run;
```


Quiz Time
---------

```SAS
proc sort data=ds_1; by var; run;
proc sort data=ds_2; by var; run;
```


Quiz Time
---------

```SAS
proc sort data=ds_1; by var; run;
proc sort data=ds_2; by var; run;

data merged;
    merge ds_1 (in=a) ds_2 (in=b);
    by var;
    if a=1 and b=0 then output;
run;
```


Quiz Time
---------

```R
merged = anti_join(ds_1, ds_2, by="var")
```


Flow control based on data in SAS
---------------------------------

Let's print a message if a column `var` takes on the value `'foo'` at least once.

. . .

```SAS
%macro find_foo(in_ds);
  proc sql;
      select count(*) into :num
      from &in_ds.
      where var='foo'
      ;
  quit;

  %if &num > 0 %then %put 'foo' present;
%mend;

%find_foo(ds);
```


Flow control based on data in R
-------------------------------

Let's print a message if a column `var` takes on the value `'foo'` at least once.

. . .

```r
num = sum(df$var == 'foo')
if (num > 0) print("'foo' present")
```


(why is this so hard in SAS?)
-----------------------------

Many reasons, but mainly:

*   SAS has two variable types: datasets & macro strings.
*   Only macro strings are involved in control flow.
    ```sas
    %sysfunc(countw(&str.))
    %scan(&str., &i.)
    %eval(&str. + 1)
    ```
*   Translating between datasets and macro variables is painful!


(why is this so hard in SAS?)
-----------------------------

R has

*   boolean, int, double, strings, dates, complex number types
*   Vectors
*   Lists
*   Dataframes, which are made up of vectors, so can reuse code:
    ```r
    # Which entries of a vector of strings are equal to 'foo'?
    vec == 'foo'
    # Which entries of a dataframe string column are equal to 'foo'?
    df$col == 'foo'
    ```
*   Arbitrary, user-defined datatypes
*   All of these can be used in control flow statements



What about analysing datasets stored on a network drive?
--------------------------------------------------------

*   SAS rarely caches datasets: almost always have to read the dataset off the
    network.

*   R will load datasets into RAM *once* and then process them from there

*   Bandwidth of DDR3 RAM ~20 GB/s vs local network ~100MB/s



No RAM, no R
------------

*   If it doesn't fit into memory, you're going to have to get creative.
*   This means datasets bigger than 5 GB (if that).


Core R is... weird
------------------

*   **3** different object-oriented programming systems

*   Application Programming Interface (API) is all over the shop:
    *   3 datetime classes: `POSIXct`, `POSIClt` and both are subclasses of
        `POSIXt`, and a separate date class `Date`
    *   `sys` vs `Sys` vs `System`
    *   bad default behaviour for many core functions

*   Documentation is often verbose and long-winded


Use (Hadley Wickham's) packages!
--------------------------------

*   `dplyr` for basic data manipulation (subsetting rows & columns,
    aggregation, variable transformations)
*   `ggplot2` for plotting
*   `lubridate` for date munging
*   `stringr` for string munging
*   `devtools` for writing packages
*   and more...

. . .

Don't bother learning the equivalent core R functions until you
absolutely need to.
