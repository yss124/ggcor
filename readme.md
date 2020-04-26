
# ggcor

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
<!-- badges: end -->

The goal of `ggcor` is to provide a set of functions that can be used to
visualize a correlation matrix quickly.

## Installation

Now `ggcor` is not on cran, You can install the development version of
ggcor from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("houyunhuang/ggcor")
```

## Correlation plot

``` r
library(ggplot2)
library(ggcor)
quickcor(mtcars) + geom_square()
```

<img src="man/figures/README-example01-1.png" width="100%" style="display: block; margin: auto;" />

``` r
quickcor(mtcars, type = "upper") + geom_circle2()
```

<img src="man/figures/README-example01-2.png" width="100%" style="display: block; margin: auto;" />

``` r
quickcor(mtcars, cor.test = TRUE) +
  geom_square(data = get_data(type = "lower", show.diag = FALSE)) +
  geom_mark(data = get_data(type = "upper", show.diag = FALSE), size = 2.5) +
  geom_abline(slope = -1, intercept = 12)
```

<img src="man/figures/README-example01-3.png" width="100%" style="display: block; margin: auto;" />

## Mantel test plot

``` r
library(dplyr)
data("varechem", package = "vegan")
data("varespec", package = "vegan")

mantel <- mantel_test(varespec, varechem,
                      spec.select = list(Spec01 = 1:7,
                                         Spec02 = 8:18,
                                         Spec03 = 19:37,
                                         Spec04 = 38:44)) %>% 
  mutate(rd = cut(r, breaks = c(-Inf, 0.2, 0.4, Inf),
                  labels = c("< 0.2", "0.2 - 0.4", ">= 0.4")),
         pd = cut(p.value, breaks = c(-Inf, 0.01, 0.05, Inf),
                  labels = c("< 0.01", "0.01 - 0.05", ">= 0.05")))

quickcor(varechem, type = "upper") +
  geom_square() +
  anno_link(aes(colour = pd, size = rd), data = mantel) +
  anno_link_label(geom = "label") +
  scale_size_manual(values = c(0.5, 1, 2)) +
  scale_colour_manual(values = c("#D95F02", "#1B9E77", "#A2A2A288")) +
  guides(size = guide_legend(title = "Mantel's r",
                             override.aes = list(colour = "grey35"), 
                             order = 2),
         colour = guide_legend(title = "Mantel's p", 
                               override.aes = list(size = 3), 
                               order = 1),
         fill = guide_colorbar(title = "Pearson's r", order = 3))
```

<img src="man/figures/README-example03-1.png" width="100%" style="display: block; margin: auto;" />

# Circular heatmap

``` r
rand_correlate(100, 8) %>% ## require ambient packages
  quickcor(circular = TRUE, cluster = TRUE) +
  geom_colour(colour = "white", size = 0.125) +
  anno_tree()
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" style="display: block; margin: auto;" />

# General heatmap

``` r
cols <- RColorBrewer::brewer.pal(4, "Set2")
rand_dataset(vars = 8) %>% ## require ambient packages
  gcor_tbl("var", cluster = TRUE) %>% 
  quickcor(circular = TRUE, cluster = TRUE, open = 30, 
           bcols = list(row.bcols = cols)) +
  geom_colour(aes(fill = var), colour = NA) +
  anno_tree() +
  scale_fill_viridis_c()
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="100%" style="display: block; margin: auto;" />
