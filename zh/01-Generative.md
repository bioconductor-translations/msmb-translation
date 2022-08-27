# Generative Models for Discrete Data

    ![ ](https://www.huber.embl.de/msmb/images/Pile_ou_face.png)

In molecular biology, many situations involve counting events: how many codons use a certain spelling, how many reads of DNA match a reference, how many CG digrams are observed in a DNA sequence. These counts give us *discrete* variables, as opposed to quantities such as mass and intensity that are measured on *continuous* scales.

If we know the rules that the mechanisms under study follow, even if the outcomes are random, we can generate the probabilities of any events we are interested in by computations and standard probability laws. This is a *top-down* approach based on deduction and our knowledge of how to manipulate probabilities. In Chapter @ref(Chap-Models), you will see how to combine this with data-driven (*bottom-up*) statistical modeling.

## Goals for this chapter

In this chapter we will:

-   Learn how to obtain the probabilities of all possible outcomes from a given model and see how we can compare the theoretical frequencies with those observed in real data.

-   Explore a complete example of how to use the Poisson distribution to analyse data on epitope detection.

-   See how we can experiment with the most useful generative models for discrete data: Poisson, binomial, multinomial.

-   Use the **R** functions for computing probabilities and counting rare events.

-   Generate random numbers from specified distributions.

## A real example

Let’s dive into a real example, where we know the probability model for the process. We are told that mutations along the genome of HIV (Human Immunodeficiency Virus) occur at random with a rate of 5 × 10<sup>−4</sup> per nucleotide per replication cycle. This means that after one cycle, the number of mutations in a genome of about 10<sup>4</sup> = 10, 000 nucleotides will follow a **Poisson** distribution[1] with rate 5. What does that tell us? This probability model predicts that the number of mutations over one replication cycle will be close to 5, and that the variability of this estimate is $\sqrt{5}$ (the standard error). We now have baseline reference values for both the number of mutations we expect to see in a typical HIV strain and its variability.

In fact, we can deduce even more detailed information. If we want to know how often 3 mutations could occur under the Poisson(5) model, we can use an R function to generate the probability of seeing *x* = 3 events, taking the value of the **rate parameter** of the Poisson distribution, called lambda (*λ*

    Greek letters such as $\lambda$ and $\mu$ often
    denote important parameters that characterize the probability
    distributions we use.

), to be 5.

    dpois(x = 3, lambda = 5)
    
    ## [1] 0.1403739

This says the chance of seeing exactly three events is around 0.14, or about 1 in 7.

If we want to generate the probabilities of all values from 0 to 12, we do not need to write a loop. We can simply set the first argument to be the **vector** of these 13 values, using R’s sequence operator, the colon “`:`”. We can see the probabilities by plotting them (Figure @ref(fig:chap1-Poisson5-1)). As with this figure, most figures in the margins of this book are created by the code shown in the text.

    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    Note how the output from R is formatted: the first line begins with the first item in the vector, hence the [1], and the second line begins with the 9th item, hence the [9]. This helps you keep track of elements in long vectors. The term vector is R parlance for an ordered list of elements of the same type (in this case, numbers).

![Figure 1.1: Probabilities of seeing 0,1,2,…,12 mutations, as modeled
by the Poisson(5) distribution. The plot shows that we will often see 4
or 5 mutations but rarely as many as 12. The distribution continues to
higher numbers (13, …), but the probabilities will be successively
smaller, and here we don’t visualize
them.](http://web.stanford.edu/class/bios221/book/figure/chap1-Poisson5-1.png)

    0:12
    
    ##  [1]  0  1  2  3  4  5  6  7  8  9 10 11 12
    
    dpois(x = 0:12, lambda = 5)
    
    ##  [1] 0.0067 0.0337 0.0842 0.1404 0.1755 0.1755 0.1462 0.1044
    ##  [9] 0.0653 0.0363 0.0181 0.0082 0.0034
    
    barplot(dpois(0:12, 5), names.arg = 0:12, col = "red")

Mathematical theory tells us that the Poisson probability of seeing the value x is given by the formula e−λλx/x!. In this book, we’ll discuss theory from time to time, but give preference to displaying concrete numeric examples and visualizations like Figure @ref(fig:chap1-Poisson5-1).

The Poisson distribution is a good model for rare events such as mutations. Other useful probability models for **discrete events** are the Bernoulli, binomial and multinomial distributions. We will explore these models in this chapter.

## Using discrete probability models

A point mutation can either occur or not; it is a binary event. The two possible outcomes (yes, no) are called the **levels** of the categorical variable.

    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    Think of a categorical variable as having different alternative values. These are the levels, similar to the different alternatives at a gene locus: alleles.

Not all events are binary. For example, the genotypes in a diploid organism can take three levels (AA, Aa, aa).

Sometimes the number of levels in a categorical variable is very large; examples include the number of different types of bacteria in a biological sample (hundreds or thousands) and the number of codons formed of 3 nucleotides (64 levels).

When we measure a categorical variable on a sample, we often want to tally the frequencies of the different levels in a vector of counts. R has a special encoding for categorical variables and calls them **factors**[2]. Here we capture the different blood genotypes for 19 subjects in a vector which we tabulate.

    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    `c()` is one of the most basic functions. It collates elements of the
    same type into a vector. In the code shown here, the elements of
    `genotype` are character strings.
    
    genotype = c("AA","AO","BB","AO","OO","AO","AA","BO","BO",
                 "AO","BB","AO","BO","AB","OO","AB","BB","AO","AO")
    table(genotype)
    
    ## genotype
    ## AA AB AO BB BO OO 
    ##  2  2  7  3  3  2

On creating a *factor*, R automatically detects the levels. You can access the levels with the `levels` function.

    genotypeF = factor(genotype)
    levels(genotypeF)
    
    ## [1] "AA" "AB" "AO" "BB" "BO" "OO"
    
    table(genotypeF)
    
    ## genotypeF
    ## AA AB AO BB BO OO 
    ##  2  2  7  3  3  2

<p class="question-begin">
► Question
</p>

What if you want to create a factor that has some levels not yet in your data?

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="solution-begin">
► Solution<span id="sol-start-1"
class="fa fa-plus-square solution-icon clickable"
onclick="toggle_visibility('sol-body-1', 'sol-start-1')"></span>
</p>

Look at the manual page of the `factor` function.

<p class="solution-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    It is not obvious from the output of the `table` function that the input
    was a factor; however if there had been another level with no instances,
    the table would also have contained that level, with a zero count.

If the order in which the data are observed doesn’t matter, we call the random variable **exchangeable**. In that case, all the information available in the factor is summarized by the counts of the factor levels. We then say that the vector of frequencies is **sufficient** to capture all the relevant information in the data, thus providing an effective way of compressing the data.

### Bernoulli trials

Tossing a coin has two possible outcomes. This simple experiment, called a Bernoulli trial, is modeled using a so-called Bernoulli random variable. Understanding this building block will take you surprisingly far. We can use it to build more complex models.

Let’s try a few experiments to see what some of these random variables look like. We use special **R** functions tailored to generate outcomes for each type of distribution. They all start with the letter `r`, followed by a specification of the model, here `rbinom`, where `binom` is the abbreviation used for binomial.

Suppose we want to simulate a sequence of 15 fair coin tosses. To get the outcome of 15 Bernoulli trials with a probability of success equal to 0.5 (a fair coin), we write

    rbinom(15, prob = 0.5, size = 1)
    
    ##  [1] 1 1 0 0 1 1 0 0 0 1 0 1 0 1 0

We use the `rbinom` function with a specific set of **parameters**[3]: the first parameter is the number of trials we want to observe; here we chose 15. We designate by `prob` the probability of success. By `size=1` we declare that each individual trial consists of just one single coin toss.

<p class="question-begin">
► Question
</p>

Repeat this function call a number of times. Why isn’t the answer always the same?

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

Success and failure can have unequal probabilities in a Bernoulli trial, as long as the probabilities sum to one[4]. To simulate twelve trials of throwing a ball into the two boxes as shown in Figure @ref(fig:BallsinBoxes2), with probability of falling in the right-hand box $\frac{2}{3}$ and in the left-hand box $\frac{1}{3}$, we write

![Figure 1.2: Two possible events with unequal probabilities. We model
this by a Bernoulli distribution with probability parameter
*p* = 2/3.](https://web.stanford.edu/class/bios221/book/images/BallsinBoxes2.jpg)

    rbinom(12, prob = 2/3, size = 1)
    
    ##  [1] 1 1 1 1 0 0 1 1 0 0 1 0

The 1 indicates success, meaning that the ball fell in the right-hand box, 0 means the ball fell in the left-hand box.

### Binomial success counts

If we only care how many balls go in the right-hand box, then the order of the throws doesn’t matter[5], and we can get this number by just taking the sum of the cells in the output vector. Therefore, instead of the binary vector we saw above, we only need to report a single number. In R, we can do this using one call to the `rbinom` function with the parameter `size` set to 12.

    Two outcomes and a size of 1 or more makes it a binomial trial.
    If the size is 1, then this is the special case of the Bernoulli trial.
    
    rbinom(1, prob = 2/3, size = 12)
    
    ## [1] 5

This output tells us how many of the twelve balls fell into the right-hand box (the outcome that has probability 2/3). We use a random two-box model when we have only two possible outcomes such as heads or tails, success or failure, CpG or non-CpG, M or F, Y = pyrimidine or R = purine, diseased or healthy, true or false. We only need the probability of “success” *p*, because “failure” (the *complementary* event) will occur with probability 1 − *p*. When looking at the result of several such trials, if they are exchangeable[6], we record only the number of successes. Therefore, SSSSSFSSSSFFFSF is summarized as (#Successes=10, \#Failures=5), or as *x* = 10, *n* = 15.

The number of successes in 15 Bernoulli trials with a probability of success of 0.3 is called a **binomial** random variable or a random variable that follows the *B*(15,0.3) distribution. To generate samples, we use a call to the `rbinom` function with the number of trials set to 15:

    set.seed(235569515)
    rbinom(1, prob = 0.3, size = 15)
    
    ## [1] 5
    
    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    What does `set.seed` do here?

<p class="question-begin">
► Question
</p>

Repeat this function call ten times. What seems to be the most common outcome?

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="solution-begin">
► Solution<span id="sol-start-2"
class="fa fa-plus-square solution-icon clickable"
onclick="toggle_visibility('sol-body-2', 'sol-start-2')"></span>
</p>

The most frequent value is 4. In fact, the theoretical proportion of times that we expect 4 to appear is the value of the probability that *X* = 4 if *X* follows *B*(15,0.3).

<p class="solution-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

The complete **probability mass distribution** is available by typing:

    probabilities = dbinom(0:15, prob = 0.3, size = 15)
    round(probabilities, 2)
    
    ##  [1] 0.00 0.03 0.09 0.17 0.22 0.21 0.15 0.08 0.03 0.01 0.00 0.00
    ## [13] 0.00 0.00 0.00 0.00
    
    We use the function `round` to keep the number of printed decimal digits
    down to 2.

We can produce a bar plot of this distribution, shown in Figure @ref(figure:chap1-binombarplot-1).

![Figure 1.3: Theoretical distribution of *B*(15,0.3). The highest bar
is at *x* = 4. We have chosen to represent theoretical values in red
throughout.](https://web.stanford.edu/class/bios221/book/figure/chap1-binombarplot-1.png)

    barplot(probabilities, names.arg = 0:15, col = "red")

The number of trials is the number we input in R as the `size` parameter and is often written *n*, while the probability of success is *p*. Mathematical theory tells us that for *X* distributed as a binomial distribution with parameters (*n*,*p*) written *X* ∼ *B*(*n*,*p*), the probability of seeing *X* = *k* successes is

$$ \begin{equation\*} \begin{aligned} P(X=k) &=& \frac{n\times (n-1)... (n-k+1)}{k\times(k-1)... 1}\\; p^k\\, (1-p)^{n-k} \\\\
&=& \frac{n!}{(n-k)!k!}\\;p^k\\, (1-p)^{n-k}\\\\ &=& { n \choose k}\\; p^k\\, (1-p)^{n-k}.\end{aligned} \end{equation\*} $$

    <img src="http://web.stanford.edu/class/bios221/book/images/devil.png" width="30%">
    Instead of $\frac{n!}{(n-k)!k!}$ we can use the special notation ${n \choose k}$ as a shortcut.

<p class="question-begin">
► Question
</p>

What is the output of the formula for *k* = 3, *p* = 2/3, *n* = 4?

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

### Poisson distributions

### A generative model for epitope detection

## Multinomial distributions: the case of DNA

### Simulating for power

## Summary of this chapter

## Further reading

## Exercises

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

REPLACE ME

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="question-begin">
Exercise
</p>

*C. elegans* genome nucleotide frequency: Is the mitochondrial sequence of *C. elegans* consistent with a model of equally likely nucleotides? **a.** Explore the nucleotide frequencies of chromosome M by using a dedicated function in the [**Biostrings**](https://bioconductor.org/packages/Biostrings/) package from Bioconductor.

**b.** Test whether the *C. elegans* data is consistent with the uniform model (all nucleotide frequencies the same) using a simulation. Hint: This is our opportunity to use Bioconductor for the first time. Since Bioconductor’s package management is more tightly controlled than CRAN’s, we need to use a special `install` function (from the [**BiocManager**](https://cran.r-project.org/web/packages/BiocManager/) package) to install Bioconductor packages.

    if (!requireNamespace("BiocManager", quietly = TRUE))
        install.packages("BiocManager")
    BiocManager::install(c("Biostrings", "BSgenome.Celegans.UCSC.ce2"))

After that, we can load the genome sequence package as we load any other R packages.

<p class="question-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

<p class="solution-begin">
► Solution
</p>

    library("BSgenome.Celegans.UCSC.ce2")
    Celegans
    
    ## Worm genome:
    ## # organism: Caenorhabditis elegans (Worm)
    ## # genome: ce2
    ## # provider: UCSC
    ## # release date: Mar. 2004
    ## # 7 sequences:
    ## #   chrI   chrII  chrIII chrIV  chrV   chrX   chrM         
    ## # (use 'seqnames()' to see all the sequence names, use the '$'
    ## # or '[[' operator to access a given sequence)
    
    seqnames(Celegans)
    
    ## [1] "chrI"   "chrII"  "chrIII" "chrIV"  "chrV"   "chrX"  
    ## [7] "chrM"
    
    Celegans$chrM
    
    ## 13794-letter DNAString object
    ## seq: CAGTAAATAGTTTAATAAAAATATAGCA...GATATATACTTTGTATATATCTATATTA
    
    class(Celegans$chrM)
    
    ## [1] "DNAString"
    ## attr(,"package")
    ## [1] "Biostrings"
    
    length(Celegans$chrM)
    
    ## [1] 13794
    
    library("Biostrings")
    lfM = letterFrequency(Celegans$chrM, letters=c("A", "C", "G", "T"))
    lfM
    
    ##    A    C    G    T 
    ## 4335 1225 2055 6179
    
    sum(lfM)
    
    ## [1] 13794
    
    lfM / sum(lfM)
    
    ##          A          C          G          T 
    ## 0.31426707 0.08880673 0.14897782 0.44794838

Create a random (each letter with equal probability) sequence of the same length as the *C. elegans* chromosome M:

    t(rmultinom(1, length(Celegans$chrM), p = rep(1/4, 4)))
    
    ##      [,1] [,2] [,3] [,4]
    ## [1,] 3409 3486 3476 3423

The expected frequencies are just

    length(Celegans$chrM) / 4
    
    ## [1] 3448.5

We’re going to compute a statistic that measures how close two multinomial outputs are to each other. We’ll take the average squared difference between expected (`e`) and observed (`o`) counts, scaled by `e`. We will call the function `oestat`.

    oestat = function(o, e) {
      sum((e-o)^2 / e)
    }
    oe = oestat(o = lfM, e = length(Celegans$chrM) / 4)
    oe
    
    ## [1] 4386.634

Is this larger than what randomness could explain? We already saw above a set of typical counts we could expect under the null model. But we need a whole set (distribution) of values. We compute these using the replicate function that evaluates a function many times. We run the following:

    B = 10000
    n = length(Celegans$chrM)
    expected = rep(n / 4, 4)
    oenull = replicate(B,
      oestat(e = expected, o = rmultinom(1, n, p = rep(1/4, 4))))
    ", toggle = FALSE)

<p class="solution-end">
<span class="fa fa-square-o solution-icon"></span>
</p>

[1] We will give more details later about this type of probability distribution.

[2] R makes sure that the factor variable will accept no other, “illegal” values, and this is useful for keeping your calculations safe.

[3] For **R** functions, parameters are also called **argument**s.

[4] We call such events **complementary**.

[5] The exchangeability property.

[6] One situation in which trials are exchangeable is if they are **independent** of each other.
