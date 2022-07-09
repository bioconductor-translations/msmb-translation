# Introduction

The two instances of *modern* in the title of this book reflect the two major recent revolutions in biological data analyses:

    ![](https://web.stanford.edu/class/bios221/book/images/WringingFlood_web.png)

-   Biology, formerly a science with sparse, often only qualitative data has turned into a field whose production of quantitative data is on par with high energy physics or astronomy, and whose data are wildly more heterogeneous and complex.

-   Statistics, a field that in the 20th century had become an application ground for probability theory and calculus, often taught loaded with notation and perceived with a heavy emphasis on hypothesis testing, has been transformed by the ubiquity of computers and of data in machine-readable form. Exploratory data analysis, visualization, resampling, simulations, pragmatic hybridizations of Bayesian ideas and methods with frequentist data analysis have become part of the toolset.

The aim of this book is to enable scientists working in biological research to quickly learn many of the important ideas and methods that they need to make the best of their experiments and of other available data. The book takes a hands-on approach. The narrative in each chapter is driven by classes of questions, or by certain data types. Methods and theory are introduced on a need-to-know basis. We don’t try to systematically deduce from first principles. The book will often throw readers into the pool and hope they can swim in spite of so many missing details.

By no means this book will replace systematic training in underlying theory: probability, linear algebra, computer science, databases, multivariate statistics. Such training takes many semesters of coursework. Perhaps the book will whet your appetite to engage more deeply with one of these fields.

## The challenge: heterogeneity

Any biological system or organism is composed of tens of thousands of components, which can be in different states and interact in multiple ways. Modern biology aims to understand such systems by acquiring comprehensive –and this means high-dimensional– data in their temporal and spatial context, with multiple covariates and interactions. Dealing with this complexity will be our primary challenge. This includes real, biological complexity as well as the complexities and heterogeneities of the data we are able to acquire with our always imperfect instruments.

Biological data come in all sorts of shapes: nucleic acid and protein sequences, rectangular tables of counts, multiple tables, continuous variables, batch factors, phenotypic images, spatial coordinates. Besides data measured in lab experiments, there are clinical data, longitudinal information, environmental measurements, networks, lineage trees, annotation from biological databases in free text or controlled vocabularies, …

> “Homogeneous data are all alike; all heterogeneous data are heterogeneous in their own way.” The Anna Karenina principle.

It is this heterogeneity that motivates our choice of R and Bioconductor as the computational platform for this book – some more on this below.

## What’s in this book?
