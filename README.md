
<!-- README.md is generated from README.Rmd. Please edit that file -->
ddhodge
=======

*Modeling Latent Flow Structure using Hodge Decomposition*

The goal of ddhodge is to be a toolkit to analyse a underlying "flow" structure (e.g., time or causal relations) in the *hodge-podge* collection of data points. ddhodge will help to interpret/visualize the large data sets including, but not limited to, high-throughput measurements of biological data (e.g., single-cell RNA-seq) which we mainly focus on.

ddhodge is built on a mathematical framework of Hodge decomposition on simplicial complex that gives us fruitful analogy of gradient, curl and harmonic (cyclic) flows on manifold. ddhoge can thus potentially handles any (both of directed acyclic and cyclic) graph structure; however, the first version of this package implements only `diffusionGraph` which is a potential flow (directed acyclic graph).

Installation
------------

You can install the alpha version of `ddhodge` with:

``` r
devtools::install_github("kazumits/ddhodge")
```

Methods
-------

*In preparation*

Example workflow
----------------

This is a basic example which shows you how to dissect the pseudo-time flow, which we define here as a potential flow (through diffusion maps), of the single-cell RNA-seq data by Treutlein et. al.. ddhodge can further extract and visualize *sink* and *source* information as a divercence of the extracted flow.

``` r
library(ddhodge)
library(ggsci)
library(ggraph)
#> Loading required package: ggplot2

# fibrablast reprogramming data
X <- t(dyno::fibroblast_reprogramming_treutlein$counts)
group <- dyno::fibroblast_reprogramming_treutlein$grouping

# ddhode part: specify input data and roots
g <- diffusionGraph(X,group=="MEF")

# visualization using ggraph package
set.seed(33) 
igraph::V(g)$group <- group
lo <- create_layout(g,"nicely")

ggraph(lo) + ggtitle("Gradient") + theme_void() +
  geom_edge_link(
    aes(width=weight),
    colour="black",
    arrow=arrow(length=unit(1.2,"mm")),
    end_cap = circle(1.2,'mm'), alpha=0.6,
  ) + scale_edge_width(range=c(0.2,0.8)) +
  geom_node_point(aes(colour=group),size=2) +
  scale_color_d3("category20")
```

<img src="man/figures/README-unnamed-chunk-1-1.png" width="100%" />

``` r

ggraph(lo) + ggtitle("Divergence") + theme_void() +
  geom_edge_link(
      aes(width=weight),colour="black",
      arrow=arrow(length=unit(1.2,"mm")),
      end_cap = circle(1.2,'mm'), alpha=0.6,
  ) + scale_edge_width(range=c(0.2,0.8)) +
  geom_node_point(aes(colour=div,size=div^2)) +
  scale_color_gradient2(low="blue",mid="grey",high="red")
```

<img src="man/figures/README-unnamed-chunk-1-2.png" width="100%" />

TODO
----

-   Add more examples and documentations
-   Extracting nodes along specified paths (e.g., from source to sink)
-   Assessing cluster-to-cluster flow for coarse-grained interpretation of data
-   Construction of causal graph structure involving cyclic flows
-   Pseudo-dynamics reconstruction using extracted pseudo-time structures
-   benchmark for [dyno](https://github.com/dynverse/dyno)
