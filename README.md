# Combinations-using-expand.grid

Introduction
==============
It seems that there is no base R function to generate exhaustive combinations of two identical vectors, sometimes desired as function inputs to mapply/vapply. The ‘combn’ function from the ‘utils’ package is required.

‘combn’ outputs the unique set of combinations, so for the example below where the first 8 letters of the alphabet are used, the combination {a, b} appears, but {b, a} does not. Similarly the cases where the two elements are identical such as {a, a} also do not feature. It can be seen that there are 28 (or 8 choose 2) unique combinations for the vector of length 8.

          x <- letters[1:8]
          xlen <- length(x)

          combn <- utils::combn(x, 2)
          combn
               [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
          [1,] "a"  "a"  "a"  "a"  "a"  "a"  "a"  "b"  "b"  "b"   "b"   "b"  
          [2,] "b"  "c"  "d"  "e"  "f"  "g"  "h"  "c"  "d"  "e"   "f"   "g"  
               [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22]
          [1,] "b"   "c"   "c"   "c"   "c"   "c"   "d"   "d"   "d"   "d"  
          [2,] "h"   "d"   "e"   "f"   "g"   "h"   "e"   "f"   "g"   "h"  
               [,23] [,24] [,25] [,26] [,27] [,28]
          [1,] "e"   "e"   "e"   "f"   "f"   "g"  
          [2,] "f"   "g"   "h"   "g"   "h"   "h"  

          expand.grid

‘expand.grid’ from the base package is a useful function in its own right, most well-known perhaps for its use in generating hyperparameter tuning grids in machine learning models.

‘expand.grid’ produces a data frame in columns rather than a matrix in rows like ‘combn’. Hence just for demonstration purposes to compare like-for-like, a bit of manipulation is done below to make the output exactly the same format. In real world usage the output of expand.grid can just be used ‘as is’.

          grid <- expand.grid(x, x, KEEP.OUT.ATTRS = FALSE, stringsAsFactors = FALSE)
          grid <- t(as.matrix(grid))
          grid <- rbind(grid[2,], grid[1,])
          rownames(grid) <- NULL
          grid
               [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
          [1,] "a"  "a"  "a"  "a"  "a"  "a"  "a"  "a"  "b"  "b"   "b"   "b"  
          [2,] "a"  "b"  "c"  "d"  "e"  "f"  "g"  "h"  "a"  "b"   "c"   "d"  
               [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22]
          [1,] "b"   "b"   "b"   "b"   "c"   "c"   "c"   "c"   "c"   "c"  
          [2,] "e"   "f"   "g"   "h"   "a"   "b"   "c"   "d"   "e"   "f"  
               [,23] [,24] [,25] [,26] [,27] [,28] [,29] [,30] [,31] [,32]
          [1,] "c"   "c"   "d"   "d"   "d"   "d"   "d"   "d"   "d"   "d"  
          [2,] "g"   "h"   "a"   "b"   "c"   "d"   "e"   "f"   "g"   "h"  
               [,33] [,34] [,35] [,36] [,37] [,38] [,39] [,40] [,41] [,42]
          [1,] "e"   "e"   "e"   "e"   "e"   "e"   "e"   "e"   "f"   "f"  
          [2,] "a"   "b"   "c"   "d"   "e"   "f"   "g"   "h"   "a"   "b"  
               [,43] [,44] [,45] [,46] [,47] [,48] [,49] [,50] [,51] [,52]
          [1,] "f"   "f"   "f"   "f"   "f"   "f"   "g"   "g"   "g"   "g"  
          [2,] "c"   "d"   "e"   "f"   "g"   "h"   "a"   "b"   "c"   "d"  
               [,53] [,54] [,55] [,56] [,57] [,58] [,59] [,60] [,61] [,62]
          [1,] "g"   "g"   "g"   "g"   "h"   "h"   "h"   "h"   "h"   "h"  
          [2,] "e"   "f"   "g"   "h"   "a"   "b"   "c"   "d"   "e"   "f"  
               [,63] [,64]
          [1,] "h"   "h"  
          [2,] "g"   "h"  

It can be seen that the output of ‘expand.grid’ is simply all combinations, of which there are 8^2 = 64 in total.
ichimoku::duplicate

So how to get from the output of ‘expand.grid’ to that of ‘combn’? Well, with the help of a simple algorithm, which has been coded into the ‘duplicate’ function from the ‘ichimoku’ package, expressly for this purpose.

From the function documentation: ‘create a vector of element positions of duplicates in the output of expand.grid on 2 identical vectors’.

Feel free to inspect the code behind the function, but it is simply a case of codifying the sequence of duplicates into a formula.

Using the function as per the below, ‘grid1’ contains all unique combinations and also those where the two elements are identical. This is sometimes the desired output if two of the same elements is still considered a unique combination, and simply that the order does not matter.

          library(ichimoku)

          grid1 <- grid[, -duplicate(xlen)]
          grid1
               [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
          [1,] "a"  "a"  "a"  "a"  "a"  "a"  "a"  "a"  "b"  "b"   "b"   "b"  
          [2,] "a"  "b"  "c"  "d"  "e"  "f"  "g"  "h"  "b"  "c"   "d"   "e"  
               [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22]
          [1,] "b"   "b"   "b"   "c"   "c"   "c"   "c"   "c"   "c"   "d"  
          [2,] "f"   "g"   "h"   "c"   "d"   "e"   "f"   "g"   "h"   "d"  
               [,23] [,24] [,25] [,26] [,27] [,28] [,29] [,30] [,31] [,32]
          [1,] "d"   "d"   "d"   "d"   "e"   "e"   "e"   "e"   "f"   "f"  
          [2,] "e"   "f"   "g"   "h"   "e"   "f"   "g"   "h"   "f"   "g"  
               [,33] [,34] [,35] [,36]
          [1,] "f"   "g"   "g"   "h"  
          [2,] "h"   "g"   "h"   "h"  

If the ‘identical = TRUE’ argument is set for ‘duplicate’, identical elements are also removed. ‘grid2’ should then be the same as ‘combn’ obtained above.

Indeed it can be seen that both ‘identical’ and ‘all.equal’ return TRUE.

          grid2 <- grid[, -duplicate(xlen, identical = TRUE)]
          grid2
               [,1] [,2] [,3] [,4] [,5] [,6] [,7] [,8] [,9] [,10] [,11] [,12]
          [1,] "a"  "a"  "a"  "a"  "a"  "a"  "a"  "b"  "b"  "b"   "b"   "b"  
          [2,] "b"  "c"  "d"  "e"  "f"  "g"  "h"  "c"  "d"  "e"   "f"   "g"  
               [,13] [,14] [,15] [,16] [,17] [,18] [,19] [,20] [,21] [,22]
          [1,] "b"   "c"   "c"   "c"   "c"   "c"   "d"   "d"   "d"   "d"  
          [2,] "h"   "d"   "e"   "f"   "g"   "h"   "e"   "f"   "g"   "h"  
               [,23] [,24] [,25] [,26] [,27] [,28]
          [1,] "e"   "e"   "e"   "f"   "f"   "g"  
          [2,] "f"   "g"   "h"   "g"   "h"   "h"  
          identical(combn, grid2)
          [1] TRUE
          all.equal(combn, grid2)
          [1] TRUE

Benchmarking the results

‘Microbenchmark’ can be used to benchmark the performance, where it is usual practice to compare median values.

For small vector lengths, expand.grid is not as performant. This is somewhat to be expected given the overhead of working with data frames rather than matrices. However the absolute times are also small so any difference would not matter as much.

When the vector length reaches 16, the custom algorithm using expand.grid/duplicate starts to outperform.

By the time the vector length reaches 1,000, this implies total unique combinations of 499,500 and the custom algorithm is already over 7x faster.

It should be noted that the custom algorithm is tailored for the special case of combn(x, m) where m = 2 and that is most likely why there can be such an outperformance.

          fn_combn <- function(x) {
            utils::combn(x, 2)
          }

          fn_grid <- function(x) {
            expand.grid(x, x, KEEP.OUT.ATTRS = FALSE,
                        stringsAsFactors = FALSE)[-duplicate(length(x), identical = TRUE), ]
          }

          microbenchmark::microbenchmark(fn_combn(1:16), fn_grid(1:16))
          Unit: microseconds
                     expr    min     lq     mean  median     uq     max neval
           fn_combn(1:16) 69.181 71.549 78.14286 73.9480 78.735 164.560   100
            fn_grid(1:16) 56.708 59.507 65.52700 61.4205 65.811 198.252   100
          microbenchmark::microbenchmark(fn_combn(1:1000), fn_grid(1:1000))
          Unit: milliseconds
                       expr       min       lq      mean    median        uq
           fn_combn(1:1000) 284.20095 287.9277 294.38977 290.50384 294.67032
            fn_grid(1:1000)  35.41869  38.0489  42.11251  39.15508  40.94597
                max neval
           367.7571   100
           113.5726   100

Use case: mapply and vapply

This type of output is suitable for feeding into functions such as ‘mapply’ or ‘vapply’.

A standard use for mapply is when multiple arguments have to be mapped into a function. Here ‘simplify = FALSE’ is set to have mapply return a list, and fed into ‘do.call’ with ‘c’ to create a vector. This is a safer and more performant method to create a vector than relying on the built-in simplification.

          do.call(c, mapply(function(x, y) paste0(x, "&", y), 
                            grid2[1, ], grid2[2, ],
                            SIMPLIFY = FALSE, USE.NAMES = FALSE))
           [1] "a&b" "a&c" "a&d" "a&e" "a&f" "a&g" "a&h" "b&c" "b&d" "b&e" "b&f"
          [12] "b&g" "b&h" "c&d" "c&e" "c&f" "c&g" "c&h" "d&e" "d&f" "d&g" "d&h"
          [23] "e&f" "e&g" "e&h" "f&g" "f&h" "g&h"

An equivalent example using ‘vapply’ is given below. ‘vapply’ is also a safe choice for programming as an output template is explicitly specified, here ‘character(1L)’, hence the returned values are all expected to be of type ‘character’ of length ‘1’ otherwise an error is thrown.

          vapply(seq_along(grid2[1L, ]),
                 function(i) paste0(grid2[1L, i], "&", grid2[2L, i]),
                 character(1L), USE.NAMES = FALSE)
           [1] "a&b" "a&c" "a&d" "a&e" "a&f" "a&g" "a&h" "b&c" "b&d" "b&e" "b&f"
          [12] "b&g" "b&h" "c&d" "c&e" "c&f" "c&g" "c&h" "d&e" "d&f" "d&g" "d&h"
          [23] "e&f" "e&g" "e&h" "f&g" "f&h" "g&h"
