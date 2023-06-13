# Week3 HW6
Sarah Tareen
2023-04-25

## Section 1: Improving analysis code by writing functions

> A Improve this regular R code by abstracting the main activities in
> your own new function. Note, we will go through this example together
> in the formal lecture. The main steps should entail running through
> the code to see if it works, simplifying to a core working code
> snippet, reducing any calculation duplication, and finally
> transferring your new streamlined code into a more useful function for
> you.

``` r
# (A. Can you improve this analysis code?
df <- data.frame(a=1:10, b=seq(200,400,length=10),c=11:20,d=NA)

df$a <- (df$a - min(df$a)) / (max(df$a) - min(df$a))

df$b <- (df$b - min(df$a)) / (max(df$b) - min(df$b))

df$c <- (df$c - min(df$c)) / (max(df$c) - min(df$c))

df$d <- (df$d - min(df$d)) / (max(df$a) - min(df$d))
```

There are some copy paste errors that we need to fix:

``` r
df <- data.frame(a=1:10, b=seq(200,400,length=10),c=11:20,d=NA)

df$a <- (df$a - min(df$a)) / (max(df$a) - min(df$a))

df$b <- (df$b - min(df$b)) / (max(df$b) - min(df$b))

df$c <- (df$c - min(df$c)) / (max(df$c) - min(df$c))

df$d <- (df$d - min(df$d)) / (max(df$d) - min(df$d))
```

In order to write a function, the first step is to write a working code
snippet. We can simplify the code to work with a generic vector.

``` r
x <- 1:10
result <- (x - min(x)) / (max(x) - min(x))
result
```

     [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
     [8] 0.7777778 0.8888889 1.0000000

Since we call the `min()` function twice, we can make this more
efficient by calling `range()` one time.

``` r
rng <- range(x)
result <- (x - rng[1] / (rng[2] - rng[1]))
result
```

     [1] 0.8888889 1.8888889 2.8888889 3.8888889 4.8888889 5.8888889 6.8888889
     [8] 7.8888889 8.8888889 9.8888889

Since the code snippet works, we can turn it into a function.

``` r
rescale <- function(x) {
  rng <- range(x)
  (x - rng[1]) / (rng[2] - rng[1])
}

## Test the function on a small vector.
x <- 1:10
rescale(x)
```

     [1] 0.0000000 0.1111111 0.2222222 0.3333333 0.4444444 0.5555556 0.6666667
     [8] 0.7777778 0.8888889 1.0000000

Now we need to improve this function so it works with NA elements.

``` r
rescale <- function(x) {
  rng <- range(x, na.rm=TRUE)
  (x - rng[1]) / (rng[2] - rng[1])
}

## Test the function on a small vector.
y <- c(1, 2, NA, 4, 5)
rescale(y)
```

    [1] 0.00 0.25   NA 0.75 1.00

> B Next improve the below example code for the analysis of protein drug
> interactions by abstracting the main activities in your own new
> function. Then answer questions 1 to 6 below. It is recommended that
> you start a new Project in RStudio in a new directory and then install
> the bio3d package noted in the R code below (N.B. you can use the
> command install.packages(“bio3d”) or the RStudio interface to do
> this). Then run through the code to see if it works, fix any
> copy/paste errors before simplifying to a core working code snippet,
> reducing any calculation duplication, and finally transferring it into
> a more useful function for you.

> Q1 What type of object is returned from the read.pdb() function? The
> type of object returned is a pdb object.

> Q2 What does the trim.pdb() function do? The trim.pdb() function
> creates a new PDB object based on the selection of atoms given in the
> arguments.

> Q3 What input parameter would turn off the marginal black and grey
> rectangles in the plots and what do they represent in this case? The
> input parameter sse would turn off the marginal black and grey
> rectangles. In this case, it represents which residues have chain A.

> Q4 What would be a better plot to compare across the different
> proteins? A better plot could be a scatterplot of the different
> proteins that has smooth lines so its easier to compare them side by
> side.

> Q5 Which proteins are more similar to each other in their B-factor
> trends. How could you quantify this? HINT: try the rbind(), dist() and
> hclust() functions together with a resulting dendrogram plot. Look up
> the documentation to see what each of these functions does. - rbind()
> combines data structured by columns or rows. - dist() find the
> distances between the rows of a data matrix - hclust() makes a
> dendrogram plot of the dissimilarities

``` r
## We can use these PDB files for the proteins 
library(bio3d)
s1 <- read.pdb("4AKE") # kinase with drug
```

      Note: Accessing on-line PDB file

``` r
s2 <- read.pdb("1AKE") # kinase no drug
```

      Note: Accessing on-line PDB file
       PDB has ALT records, taking A only, rm.alt=TRUE

``` r
s3 <- read.pdb("1E4Y") # kinase with drug
```

      Note: Accessing on-line PDB file

``` r
s1.chainA <- trim.pdb(s1, chain="A", elety="CA")
s2.chainA <- trim.pdb(s2, chain="A", elety="CA")
## changed the s1 to s3
s3.chainA <- trim.pdb(s3, chain="A", elety="CA")
s1.b <- s1.chainA$atom$b
s2.b <- s2.chainA$atom$b
s3.b <- s3.chainA$atom$b

hc <- hclust( dist( rbind(s1.b, s2.b, s3.b) ) )
plot(hc)
```

![](week3hw6function_files/figure-commonmark/unnamed-chunk-7-1.png)

Since s2.b and s3.b are more closely related then the proteins “1AKE”
and “1E4Y” are more related to each other.
