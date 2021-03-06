# Magic Functions to Obtain Results from for Loops in R
Koji MAKIYAMA (@hoxo_m)  

<!-- README.md is generated from README.Rmd. Please edit that file -->



[![Travis-CI Build Status](https://travis-ci.org/hoxo-m/magicfor.svg?branch=master)](https://travis-ci.org/hoxo-m/magicfor)
[![Coverage Status](https://coveralls.io/repos/github/hoxo-m/magicfor/badge.svg?branch=master)](https://coveralls.io/github/hoxo-m/magicfor?branch=master)

## 1. Overview

`for()` is one of the most popular functions in R.
As you know, it is used to create loops.

For example, let's calculate squared values for 1 to 3.


```r
for (i in 1:3) {
  squared <- i ^ 2
  print(squared)
}
#> [1] 1
#> [1] 4
#> [1] 9
```

It is very easy.

However, it becomes too much hassle to change such codes to store the result.
You must prepare some containers with correct length for storing the result and change `print()` to assignment statements.


```r
result <- vector("numeric", 3) # prepare a container
for (i in 1:3) {
  squared <- i ^ 2
  result[i] <- squared         # change to assignment
}
result
#> [1] 1 4 9
```

Moreover, you may want to store the result as a data.frame with the iteration numbers.


```r
result <- data.frame(matrix(nrow = 3, ncol = 2))
colnames(result) <- c("i", "squared")
for (i in 1:3) {
  squared <- i ^ 2
  result[i, 1] <- i
  result[i, 2] <- squared
}
result
#>   i squared
#> 1 1       1
#> 2 2       4
#> 3 3       9
```

What a bother!

In such or more troublesome situations like that you have to store many variables, the code will grow more complex soon.

The **magicfor** package makes to resolve the problem being kept readability.

You just add two lines before the for loop.
First, load the library. Second, call `magic_for()`.
Notice that the main for loop is kept intact.


```r
library(magicfor)               # load library
magic_for(print, silent = TRUE) # call magic_for()

for (i in 1:3) {
  squared <- i ^ 2
  print(squared)
}

magic_result_as_dataframe()     # get the result
#>   i squared
#> 1 1       1
#> 2 2       4
#> 3 3       9
```

`magic_for()` takes a function name, and then reconstructs `for()` to remember values passed to the specified function in for loops.
We call it "magicalization".
Once you call `magic_for()`, as you just run `for()` as usual, the result will be stored in memory automatically.

Here, we are using `magic_result_as_dataframe()` in order to get the stored values.
It is one of the functions to obtain results from "magicalized for loops", and means to take out the results as a data.frame.

Even if the number of observed variables increases, you can do it the same way.


```r
magic_for(silent = TRUE)

for (i in 1:3) {
  squared <- i ^ 2
  cubed <- i ^ 3
  put(squared, cubed)
}

magic_result_as_dataframe()
#>   i squared cubed
#> 1 1       1     1
#> 2 2       4     8
#> 3 3       9    27
```

`put()` is the default function to store values in magicalized for loops.
It allows to take any number of variables and can display them.

## 2. Installation

You can install the package from GitHub.


```r
install.packages("devtools") # if you have not installed "devtools" package
devtools::install_github("hoxo-m/magicfor")
```

You can also use **githubinstall** package to easy install from GitHub.


```r
install.packages("githubinstall") # if you have not installed "githubinstall" package
githubinstall::githubinstall("magicfor")
```

The source code for **magicfor** package is available on GitHub at

- https://github.com/hoxo-m/magicfor.

## 3. Details

The **magicfor** package provides the functions as follows:

- `magic_for()`: Magicalize for.
- `magic_free()`: Free magicalization.
- Get results:
    - `magic_result()`: as a list.
    - `magic_result_as_vetor()`: as a vector.
    - `magic_result_as_dataframe()`: as a data.frame.
- `put()`: Display values.

In the following, we assume that the library is loaded to use the functions.


```r
library(magicfor)
```

### 3.1 Basics

The main function `magic_for()` magicalize for loops.
"Magicalize" means to change the behavior of `for()` to store values outputted via target functions.


```r
magic_for()

for (i in 1:3) {
  squared <- i ^ 2
  put(squared)
}
#> The loop is magicalized with put().
#> squared: 1
#> squared: 4
#> squared: 9
```

The default target function is `put()`. It displays input values, for example:


```r
x <- 1
put(x)
#> x: 1
```

You can take out stored values using `magic_result_**()` when for loops have  finished.


```r
magic_result_as_vector()
#> [1] 1 4 9
```

### 3.2 `magic_for()`

`magic_for()` has several options.

Specify the first argument `func`, you can change target functions.


```r
magic_for(cat)

for (i in 1:3) {
  squared <- i ^ 2
  cat(squared, " ")
}
#> The loop is magicalized with cat().
#> 1  4  9
```

If `progress = TRUE`, show progress bar.


```r
magic_for(progress = TRUE)

for (i in 1:3) {
  squared <- i ^ 2
  put(squared)
}
```

```
#> |=================================================================| 100%
```

If you set `test` a number, the iteration is limited to that number of times.


```r
magic_for(test = 2)

for (i in 1:100) {
  squared <- i ^ 2
  put(squared)
}
#> The loop is magicalized with put().
#> squared: 1
#> squared: 4
```

If `silent = TRUE`, target function will be not executed but only the values will be stored.

If `temp = TRUE`, the effect of magicalization will be lost after once execution of for loop.


```r
magic_for(temp = TRUE)
is_magicalized()
#> [1] TRUE

for (i in 1:3) {
  squared <- i ^ 2
  put(squared)
}
#> The loop is temporary magicalized with put().
#> squared: 1
#> squared: 4
#> squared: 9

is_magicalized()
#> [1] FALSE
```

### 3.3 `magic_free()`

You can use `magic_free()` to cancel magicalization.


```r
magic_for()
is_magicalized()
#> [1] TRUE

magic_free()
is_magicalized()
#> [1] FALSE
```

The function also clear the stored values.


```r
magic_for(silent = TRUE)

for (i in 1:3) {
  squared <- i ^ 2
  put(squared)
}

magic_result_as_vector()
#> [1] 1 4 9

magic_free()
magic_result_as_vector()
#> NULL
```

### 3.4 `magic_result_**()`

You can use `magic_result_**()` to obtain results from magicalized for loops.


```r
magic_for(silent = TRUE)

for (i in 1:3) {
  squared <- i ^ 2
  put(squared)
}
```

`magic_result()` returns results as a list.


```r
magic_result()
#> $squared
#> $squared[[1]]
#> [1] 1
#> 
#> $squared[[2]]
#> [1] 4
#> 
#> $squared[[3]]
#> [1] 9
```

`magic_result_as_vector()` returns results as a vector.


```r
magic_result_as_vector()
#> [1] 1 4 9
```

`magic_result_as_dataframe()` returns results as a data.frame.


```r
magic_result_as_dataframe()
#>   i squared
#> 1 1       1
#> 2 2       4
#> 3 3       9
```

### 3.5 `put()`

`put()` displays input values with high flexibility.


```r
x <- 2
y <- 3
put(x)
#> x: 2
put(x, y)
#> x: 2, y: 3
put(x, x ^ 2, x ^ 3)
#> x: 2, x^2: 4, x^3: 8
put(x, squared = x ^ 2, cubed = x ^ 3)
#> x: 2, squared: 4, cubed: 8
```

It is very useful for **magicfor**.


```r
magic_for()

for (i in 1:3) {
  put(x = i, squared = i ^ 2, cubed = i ^ 3)
}
#> The loop is magicalized with put().
#> x: 1, squared: 1, cubed: 1
#> x: 2, squared: 4, cubed: 8
#> x: 3, squared: 9, cubed: 27

magic_result_as_dataframe(F)
#>   x squared cubed
#> 1 1       1     1
#> 2 2       4     8
#> 3 3       9    27
```

## 4. Miscellaneous

Whenever you put just variables in magicalized for loops, their values will be stored regardless of target functions.


```r
magic_for()

for (i in 1:3) {
  squared <- i ^ 2
  squared
}
#> The loop is magicalized with put().

magic_result_as_vector()
#> [1] 1 4 9
```

When you write trarget functions inside of if statements without else, `NA` will be inserted to represent missing.


```r
magic_for()

for (i in 1:3) {
  squared <- i ^ 2
  if(i == 3) put(squared)
}
#> The loop is magicalized with put().
#> squared: 9

magic_result_as_vector()
#> [1] NA NA  9
```

Target functions work only top level lines or inside of if statements in magicalized for loops.
For example, it does not work inside nested for loops.


```r
magic_for()

for (i in 1:2) {
  for (j in 1:2) {
    put(i, j, i * j)
  }
}
#> The loop is magicalized with put().
#> i: 1, j: 1, i*j: 1
#> i: 1, j: 2, i*j: 2
#> i: 2, j: 1, i*j: 2
#> i: 2, j: 2, i*j: 4

magic_result_as_vector()
#> list()
```

## 5. Related Work

- WANTED
