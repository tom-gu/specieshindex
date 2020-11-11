
<img src="README_files/figure-gfm/stickerfile.png" alt="hexsticker" height="250px" align="right" />

# specieshindex

`specieshindex` is a package that aims to gauge scientific influence of
different species mainly using the h-index.

## Installation

To get this package to work, make sure you have the following packages
installed.

``` r
install.packages("rscopus")
install.packages("taxize")
install.packages("XML")
install.packages("httr")
install.packages("dplyr")
install.packages("rlang")
devtools::install_github("jessicatytam/specieshindex", force = TRUE, build_vignettes = TRUE)

library(taxize)
library(httr)
library(XML)
library(rscopus)
library(specieshindex)
```

## Getting Scopus API key

To connect and download citation information from Scopus legally, you
will absolutely need an API key. Here are the steps to obtain the key.
1. Go to <https://dev.elsevier.com/> and click on the button `I want an
API key`. 2. Create an account and log in. 3. Go to the `My API Key` tab
on top of the page and click `Create API Key`. 4. Read the legal
documents and check the boxes.

## Simple example

Here is a quick demonstration of how the package works. Let’s say you
want to compare the species h-index of a few marsupials. First, you
would need to download the citation information using either
`FetchSpT()` for title only or `FetchSpTAK()` for
title+abstract+keywords. Remember to use binomial names.

``` r
Woylie <- FetchSpTAK("Bettongia", "penicillata", myAPI)
Quokka <- FetchSpTAK("Setonix", "brachyurus", myAPI)
Platypus <- FetchSpTAK("Ornithorhynchus", "anatinus", myAPI)
Koala <- FetchSpTAK("Phascolarctos", "cinereus", myAPI)
```

Now that you have the data, you can use the `Allindices()` function to
create a dataframe that shows their indices.

``` r
W <- Allindices(Woylie, genus = "Bettongia", species = "penicillata")
```

    ## Warning in if (data$citations < 1) {: the condition has length > 1 and only the
    ## first element will be used

``` r
Q <- Allindices(Quokka, genus = "Setonix", species = "brachyurus")
```

    ## Warning in if (data$citations < 1) {: the condition has length > 1 and only the
    ## first element will be used

``` r
P <- Allindices(Platypus, genus = "Ornithorhynchus", species = "anatinus")
```

    ## Warning in if (data$citations < 1) {: the condition has length > 1 and only the
    ## first element will be used

``` r
K <- Allindices(Koala, genus = "Phascolarctos", species = "cinereus")
```

    ## Warning in if (data$citations < 1) {: the condition has length > 1 and only the
    ## first element will be used

``` r
CombineSp <- rbind(W, Q, P, K) #combining the citation records
CombineSp
```

    ##              genus_species     species           genus publications citations
    ## 1    Bettongia_penicillata penicillata       Bettongia            0         0
    ## 2       Setonix_brachyurus  brachyurus         Setonix            0         0
    ## 3 Ornithorhynchus_anatinus    anatinus Ornithorhynchus            0         0
    ## 4   Phascolarctos_cinereus    cinereus   Phascolarctos            0         0
    ##   journals articles reviews years_publishing h m i10 h5
    ## 1        0        0       0               NA 0 0   0  0
    ## 2        0        0       0               NA 0 0   0  0
    ## 3        0        0       0               NA 0 0   0  0
    ## 4        0        0       0               NA 0 0   0  0

Once you are happy with your dataset, you can make some nice plots.
Using `ggplot2`, we can compare the h-index and the total citations.

``` r
library(ggplot2)
#h-index
ggplot(CombineSp, aes(x = species)) +
  geom_point(aes(y = h,
                 colour = "H-index"),
             size = 3) +
  labs(x = "Species",
       y = "Index Score",
       colour = "Index",
       title = "h-index") +
  scale_x_discrete(labels = c("Woylie", "Quokka", "Platypus", "Koala")) +
  scale_colour_manual(values = c("H-index" = "#3498DB")) +
  theme(plot.title = element_text(size = 14, face = "bold"),
        axis.title = element_text(size = 12),
        axis.text = element_text(size = 10),
        legend.position = "none")
```

<img src="README_files/figure-gfm/unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

``` r
#total citations
ggplot(CombineSp, aes(x = species)) +
geom_point(aes(y = citations,
               colour = "Citations"),
           size = 3) +
labs(x = "Species",
     y = "Total citations",
     colour = "Index",
     title = "Citations") +
scale_x_discrete(labels = c("Woylie", "Quokka", "Platypus", "Koala")) + 
scale_colour_manual(values = c("Citations"  = "#2874A6")) +
theme(plot.title = element_text(size = 14, face = "bold"),
      axis.title = element_text(size = 12),
      axis.text = element_text(size = 10),
      legend.position = "none")
```

<img src="README_files/figure-gfm/unnamed-chunk-5-2.png" style="display: block; margin: auto;" />
