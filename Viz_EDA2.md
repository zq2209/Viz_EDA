Viz_EDA 2
================
Zining Qi
2022-10-04

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.0      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
library(patchwork)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-09-29 10:45:02 (8.401)

    ## file min/max dates: 1869-01-01 / 2022-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-29 10:45:08 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-29 10:45:10 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

## Scatterplot

``` r
# Better this time
weather_df %>%
  ggplot(aes(x = tmin, y = tmax,, color = name)) +
  geom_point(alpha = .5) +
  labs(
    x = "Minnimum Daily Temp",
    y = "Maximum Daily Temp",
    title = "scatterplot of daily temp",
    caption = "Data come from rnoaa package"
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax,, color = name)) +
  geom_point(alpha = .5) +
  labs(
    x = "Minnimum Daily Temp",
    y = "Maximum Daily Temp",
    title = "scatterplot of daily temp",
    caption = "Data come from rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15), 
    labels = c("-15º C", "0", "15")
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax,, color = name)) +
  geom_point(alpha = .5) +
  labs(
    x = "Minnimum Daily Temp",
    y = "Maximum Daily Temp",
    title = "scatterplot of daily temp",
    caption = "Data come from rnoaa package"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15), 
    labels = c("-15º C", "0", "15")
  ) +
  scale_y_continuous(
    trans = "sqrt", 
    position = "right"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax,, color = name)) +
  geom_point(alpha = .5) +
  labs(
    x = "Minnimum Daily Temp",
    y = "Maximum Daily Temp",
    title = "scatterplot of daily temp",
    caption = "Data come from rnoaa package"
  ) +
  scale_color_hue(
    name = "Location", 
    h = c(100, 300)
  )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
ggp_temp_plot = weather_df %>%
  ggplot(aes(x = tmin, y = tmax,, color = name)) +
  geom_point(alpha = .5) +
  labs(
    x = "Minnimum Daily Temp",
    y = "Maximum Daily Temp",
    title = "scatterplot of daily temp",
    caption = "Data come from rnoaa package"
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )
ggp_temp_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## Theme

``` r
ggp_temp_plot + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
ggp_temp_plot + 
  theme_minimal()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Data in Geom\_

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki_park = 
  weather_df %>% 
  filter(name == "Waikiki_HA")

ggplot(data = waikiki_park, aes(x = date, y = tmax, color = name)) + 
  geom_point() + 
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Patchwork

``` r
tmax_tmin_plot = 
  weather_df %>%
  ggplot(aes(x = tmin, y = tmin, color = name)) +
  geom_point()

prcp_dens_plot =
  weather_df %>%
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) + 
  geom_density(alpha = .5) + 
  theme(legend.position = "none")

tmax_date_plot = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  theme(legend.position = "bottom")

(tmax_tmin_plot + prcp_dens_plot) / tmax_date_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Data Manipulation

``` r
weather_df %>%
  mutate(name = fct_reorder(name, tmax)) %>%
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
pulse_data = 
  haven::read_sas("/Users/qizining/Desktop/Data-Wrangling/public_pulse_data.sas7bdat") %>%
  janitor::clean_names() %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>%
  select(id, visit, everything()) %>%
  mutate(
    visit = recode(visit, "bl" = "00m"),
    visit = factor(visit, levels = str_c(c("00", "01", "06", "12"), "m"))) %>%
  arrange(id, visit)

ggplot(pulse_data, aes(x = visit, y = bdi)) + 
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite values (stat_boxplot).

![](Viz_EDA2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->
