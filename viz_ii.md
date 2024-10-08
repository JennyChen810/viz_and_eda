viz_ii
================
Jianing Chen
2024-09-26

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(patchwork )
```

## Load the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") %>%
  mutate(
    name = case_match(
      id, 
      "USW00094728" ~ "CentralPark_NY", 
      "USW00022534" ~ "Molokai_HI",
      "USS0023B17S" ~ "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## using cached file: /Users/jianingchen/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2024-09-26 10:28:11.932947 (8.651)

    ## file min/max dates: 1869-01-01 / 2024-09-30

    ## using cached file: /Users/jianingchen/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2024-09-26 10:28:17.893781 (3.932)

    ## file min/max dates: 1949-10-01 / 2024-09-30

    ## using cached file: /Users/jianingchen/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2024-09-26 10:28:19.926044 (1.036)

    ## file min/max dates: 1999-09-01 / 2024-09-30

``` r
weather_df
```

    ## # A tibble: 2,190 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1  
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6
    ## # ℹ 2,180 more rows

## labels

``` r
weather_df %>%
  ggplot(aes(x=tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (c)",
    y = "Maximum daily temperature (c)",
    caption = "Data from ronoaa package; temperatures in 2017"
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- --> \## Scales

start with the same plot; x and y scales.

``` r
weather_df %>%
  ggplot(aes(x=tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (c)",
    y = "Maximum daily temperature (c)",
    caption = "Data from ronoaa package; temperatures in 2017"
  ) +
  scale_x_continuous(
    breaks = c(-15,0,15),
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous(
    ## trans = "sqrt"
    ## "log"
    position = "right"
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- --> Look at color
scales.

``` r
weather_df %>%
  ggplot(aes(x=tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (c)",
    y = "Maximum daily temperature (c)",
    caption = "Data from ronoaa package; temperatures in 2017"
  ) +
  ##scale_color_hue(
    ##name = "location", 
    ##h = c(100,300))
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> \## Themes

shift the legend.

``` r
weather_df %>%
  ggplot(aes(x=tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (c)",
    y = "Maximum daily temperature (c)",
    caption = "Data from ronoaa package; temperatures in 2017"
  )+
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE)+
  theme(legend.position = "bottom")
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

Change the overall theme.

``` r
weather_df %>%
  ggplot(aes(x=tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Minimum daily temperature (c)",
    y = "Maximum daily temperature (c)",
    caption = "Data from ronoaa package; temperatures in 2017"
  )+
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE)+
    theme_minimal() +
     theme(legend.positions = "bottom")
```

    ## Warning in plot_theme(plot): The `legend.positions` theme element is not
    ## defined in the element hierarchy.

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
     ##theme_classic()
     ##ggthemes::theme_economist()
     ##ggthemes::theme_excel()
```

## Setting options

``` r
library(tidyverse)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
theme_set(theme_minimal()+theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill="viridis"
)
scale_color_discrete= scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Data args in `geom`

``` r
central_park= 
  weather_df %>%
filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>%
  filter(name == "Waikiki_HA" )

ggplot(data = waikiki, aes(x=date, y = tmax, color = name)) + 
  geom_point() + 
  geom_line(data = central_park)
```

![](viz_ii_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## `patchwork`

remember faceting?

``` r
weather_df %>%
  ggplot(aes(x= tmin, fill = name)) +
  geom_density(alpha = .5) +
  facet_grid(.~name)
```

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_density()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

what happens when you want multopanel plots but can’t facet ..?

``` r
tmax_tmin_p= 
   weather_df %>%
  ggplot(aes(x= tmin, y= tmax , color = name))+
  geom_point(alpha = .5) +
  theme(legend.position = "name")

prcp_dens_p = 
  weather_df %>%
  filter(prcp>0) %>%
  ggplot(aes(x = prcp, fill = name)) +
    geom_density(alpha = 0.5)

tmax_date_p = 
  weather_df%>%
  ggplot(aes(x= date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se= FALSE) +
  theme(legend.position = "name")

##tmax_tmin_p /(prcp_dens_p + tmax_date_p)
(tmax_tmin_p + prcp_dens_p) / tmax_date_p
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).
    ## Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Data manipulation

Contral your factors.

``` r
weather_df %>%
  mutate(
    name = factor(name),
    name = forcats::fct_relevel(name, c("Waikiki_HA"))
  ) %>%
  ggplot(aes(x= name, y = tmax, fill = name)) +
  geom_violin(alpha= .5)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = forcats::fct_relevel(name, c("Waikiki_HA"))`.
    ## Caused by warning:
    ## ! 1 unknown level in `f`: Waikiki_HA

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

What if I wanted densities for tmin and tmax simultaneously?

``` r
weather_df%>%
  filter(name == "CentralPark_NY") %>%
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperatures"
  )%>%
  ggplot(aes(x=temperatures, fill = observation))+
  geom_density(alpha= .5) +
  facet_grid(.~name)
```

![](viz_ii_files/figure-gfm/unnamed-chunk-12-1.png)<!-- --> \## Revisit
the pups

Data from the FAS study.

``` r
pup_data=
  read.csv("./data/FAS_pups.csv") %>%
  janitor::clean_names() %>%
  mutate(sex= recode(sex,`1`= "male", `2`= "female"))

litters_data = 
  read.csv("./data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  separate(group, into=c("dose","day_of_tx"),sep=3)

fas_data = left_join(pup_data, litters_data, by="litter_number")

##fas_data %>%
##select(dose,day_of_tx, starts_with("pd_")) %>%
 ## pivot_longer(
 ##   pd_ears:pd_walk,
 ##  names_to= "outcome",
  ##  values_to = "pn_day"
 ## ) %>%
 ## drop_na()%>%
 # mutate(outcome = forcats::fct_reorder("pd_ears","pd_pivot","pd_walk","pd_eyes")) %>%
 # ggplot(aes(x=dose, y = outcome)) +
 # geom_violin()+
 # facet_grid(day_of_tx ~ outcome)
```
