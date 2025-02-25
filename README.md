
<img src="README_files/figure-gfm/stickerfile.png" alt="hexsticker" height="250px" align="right" />

# specieshindex

[![CRAN
status](https://www.r-pkg.org/badges/version/specieshindex)](https://CRAN.R-project.org/package=specieshindex)
![R-CMD-check](https://github.com/jessicatytam/specieshindex/workflows/CI/badge.svg)
[![codecov](https://codecov.io/gh/jessicatytam/specieshindex/branch/master/graph/badge.svg?token=Y8N1QW0I1C)](https://codecov.io/gh/jessicatytam/specieshindex)
[![Github All
Releases](https://img.shields.io/github/downloads/jessicatytam/specieshindex/total.svg)]()

`specieshindex` is a package that aims to gauge scientific influence of
different species mainly using the *h*-index.

## Installation

To get this package to work, make sure you have the following packages
installed.

``` r
#Installation from GitHub
install.packages("rscopus")
install.packages("wosr")
install.packages("rbace")
install.packages("taxize")
install.packages("XML")
install.packages("jsonlite")
install.packages("httr")
install.packages("dplyr")
install.packages("data.table")
install.packages("tidyr")
devtools::install_github("jessicatytam/specieshindex", force = TRUE, build_vignettes = FALSE)

#Load the library
library(specieshindex)
```

You can find the vignette
[here](https://github.com/jessicatytam/specieshindex/blob/master/vignettes/vignette.pdf)
for more detailed instructions and the full list of functions
[here](https://github.com/jessicatytam/specieshindex/blob/master/specieshindex_0.1.1.pdf).

## Before you start

### :dart: Additional keywords

The Count and Fetch functions allow the addition of keywords using
Boolean operators to restrict the domain of the search. Although you can
simply use keywords such as “conservation”, you will find that using
“conserv\*” will yield more results. The “\*” (or wildcard) used here
searches for any words with the prefix “conserv”, e.g. conservation,
conserve, conservatory, etc. Find out more about search language
[here](https://guides.library.illinois.edu/c.php?g=980380&p=7089537) and
[here](http://schema.elsevier.com/dtds/document/bkapi/search/SCOPUSSearchTips.htm).

### :boar: Synonyms

Some species have had their classification changed in the past,
resulting in multiple binomial names and synonyms. Synonyms can be added
to the search strings to get the maximum hits. If you have more than 1
synonym, you can parse a list (the list should be named “synonyms”) into
the argument.

### :mega: Connecting to Scopus

**Make sure you are connected to the internet via institutional access
or acquire a VPN from your institution if you are working from home.**
Alternatively, if you are a subscriber of Scopus already, you can ignore
this step.

To connect and download citation information from Scopus legally, you
will **absolutely need** an API key. Here are the steps to obtain the
key.

1.  Go to <https://dev.elsevier.com/> and click on the button
    `I want an API key`.
2.  Create an account and log in.
3.  Go to the `My API Key` tab on top of the page and click
    `Create API Key`.
4.  Read the legal documents and check the boxes.

### :mega: Connecting to Web of Science

You are required to be at your institution for this to work since the
API is accessed via the IP address.

### :mega: Connecting to BASE

It is recommended that you have your IP address whitelisted. You can do
it [here](https://www.base-search.net/about/en/contact.php).

## Examples

Here is a quick demonstration of how the package works.

### :jigsaw: Setting up keys

You will need to set up your API key / session ID before gaining access
to the databases. Run the following lines of code to do so.

``` r
#Scopus
apikey <- "your_scopus_api_key"
#Web of Science
sid <- auth(username = NULL, password = NULL)
```

You won’t have to set this again until your next session. You are
required to be at your institution for this to work since the API is
accessed via the IP address.

### :pencil2: Count and Fetch Syntax

Multiple databases have been incorporated into `specieshindex`,
including Scopus, Web of Science, and Lens. To differentiate between
them, the suffix of the Count and Fetch functions have been labeled with
the database’s name, with the exception of Scopus.

``` r
#Scopus requests
CountSpT(db = "scopus", genus = "Bettongia", species = "penicillata")
FetchSpT(db = "scopus", genus = "Bettongia", species = "penicillata")
CountGenusT(db = "scopus", genus = "Bettongia")
FetchGenusT(db = "scopus", genus = "Bettongia")

#Web of science requests
#No tokens or api keys needed if session ID has been set as shown previously
CountSpT(db = "wos", genus = "Bettongia", species = "penicillata")
FetchSpT(db = "wos", genus = "Bettongia", species = "penicillata")
CountGenusT(db = "wos", genus = "Bettongia")
FetchGenusT(db = "wos", genus = "Bettongia")

#BASE requests
#Fetch functions are not available for BASE
CountSpT(db = "base", genus = "Bettongia", species = "penicillata")
CountGenusT(db = "base", genus = "Bettongia")
```

### :abacus: Counting citation records

If you are only interested in knowing how many publications there are on
Scopus, you can run the Count functions. Use `CountSpT()` for title only
or `CountSpTAK()` for title+abstract+keywords.

``` r
#Count citation data
CountSpT("scopus", "Bettongia", "penicillata")
CountSpTAK("scopus", "Bettongia", "penicillata")

#Example including additional keywords
CountSpTAK("scopus", "Phascolarctos", "cinereus",
           additionalkeywords = "(consrv* OR protect* OR reintrod* OR restor*)")
#search string: TITLE-ABS-KEY("Phascolarctos cinereus" AND (consrv* OR protect* OR reintrod* OR restor*))

#Example including synonyms
CountSpT("scopus", "Osphranter", "rufus",
         synonyms = "Macropus rufus", additionalkeywords = "conserv*")
#search string: TITLE(("Osphranter rufus" OR "Macropus rufus") AND conserv*)
```

### :fishing\_pole\_and\_fish: Extracting citaiton records

In order to calculate the indices, you will need to download the
citation records. The parameters of the Count and Fetch functions are
exactly the same. Let’s say you want to compare the species h-index of a
few marsupials. First, you would need to download the citation
information using either `FetchSpT()` for title only or `FetchSpTAK()`
for title+abstract+keywords. Remember to use binomial names.

``` r
#Extract citation data
Woylie <- FetchSpTAK("scopus", "Bettongia", "penicillata")
Quokka <- FetchSpTAK("scopus", "Setonix", "brachyurus")
Platypus <- FetchSpTAK("scopus", "Ornithorhynchus", "anatinus")
Koala <- FetchSpTAK("scopus", "Phascolarctos", "cinereus")
```

### :bar\_chart: Index calculation and plotting

Now that you have the data, you can use the `Allindices()` function to
create a dataframe that shows their indices.

``` r
# Calculate indices
W <- Allindices(Woylie, genus = "Bettongia", species = "penicillata")
Q <- Allindices(Quokka, genus = "Setonix", species = "brachyurus")
P <- Allindices(Platypus, genus = "Ornithorhynchus", species = "anatinus")
K <- Allindices(Koala, genus = "Phascolarctos", species = "cinereus")

CombineSp <- dplyr::bind_rows(W, Q, P, K) #combining the citation records
CombineSp
```

    ##              genus_species     species           genus publications citations
    ## 1    Bettongia penicillata penicillata       Bettongia          113      1903
    ## 2       Setonix brachyurus  brachyurus         Setonix          242      3427
    ## 3 Ornithorhynchus anatinus    anatinus Ornithorhynchus          321      6365
    ## 4   Phascolarctos cinereus    cinereus   Phascolarctos          773     14291
    ##   journals years_publishing  h     m i10 h5
    ## 1       55               44 26 0.591  54  6
    ## 2      107               67 29 0.433 121  3
    ## 3      153               68 41 0.603 177  6
    ## 4      227              140 53 0.379 427 10

Once you are happy with your dataset, you can make some nice plots.
Using `plotAllindices()`, we can compare the indices against each other.

``` r
plotAllindices(CombineSp)
```

<img src="README_files/figure-gfm/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

**Figure 1.** The *h*-index, *m*-index, *i10* index, and *h5* index of
the Woylie, Platypus, Koala, and Quokka.

<br/>

You can also visualise the total publication per year with `getYear()`
and `plotPub()`.

``` r
extract_year_W <- getYear(data = Woylie, genus = "Bettongia", species = "penicillata")
extract_year_Q <- getYear(data = Quokka, genus = "Setonix", species = "brachyurus")
extract_year_P <- getYear(data = Platypus, genus = "Ornithorhynchus", species = "anatinus")
extract_year_K <- getYear(data = Koala, genus = "Phascolarctos", species = "cinereus")
Combine_pub <- rbind(extract_year_W, extract_year_Q, extract_year_P, extract_year_K)
plotPub(Combine_pub)
```

<img src="README_files/figure-gfm/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

**Figure 2.** The total number of publications per year of the Woylie,
Platypus, Koala, and Quokka.

<br/>

## :rocket: Acknowledgements

`specieshindex` is enabled by Scopus, Web of Science, and BASE.

## :gem: Contributing to `specieshindex`

To propose any bug fixes or new features, please refer to our [community
guidelines](https://github.com/jessicatytam/specieshindex/blob/52a1d30c86dc425de2b3966cfa6d802260b7229a/.github/CONTRIBUTING.md).
