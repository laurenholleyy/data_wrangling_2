Data from the Web
================
Lauren Holley
2026-10-15

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.1     ✔ readr     2.2.0
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.3     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.5     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

## Import NSDUH

``` r
url = "https://www.samhsa.gov/data/sites/default/files/NSDUHsaeShortTermCHG2015/NSDUHsaeShortTermCHG2015.htm"

drug_use_html =
  read_html(url)
```

Pull tables out

``` r
marj_df = 
  drug_use_html |>
  html_table() |>
  first() |>
  slice(-1)
```

## Import some SW data

pull in html

``` r
swm_html = 
  read_html_live("https://www.imdb.com/list/ls070150896/")
```

Next up…selector gadget

## Use APIs

getting a csv

``` r
nyc_water_df =
  GET("https://data.cityofnewyork.us/api/v3/views/ia2d-e54m/query.csv") |>
  content("parsed") |>
  select(year:per_capita_gallons_per_person_per_day)
```

    ## Rows: 65 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): :id, :version
    ## dbl  (4): year, new_york_city_population, nyc_consumption_million_gallons_pe...
    ## dttm (2): :created_at, :updated_at
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

get a json file

``` r
nyc_water_json =
  GET("https://data.cityofnewyork.us/api/v3/views/ia2d-e54m/query.json")

nyc_water_json |>
  content("text") |>
  jsonlite::fromJSON() |>
  as_tibble() |>
  select(year:per_capita_gallons_per_person_per_day)
```

    ## # A tibble: 65 × 4
    ##    year  new_york_city_population nyc_consumption_milli…¹ per_capita_gallons_p…²
    ##    <chr> <chr>                    <chr>                   <chr>                 
    ##  1 2024  8478000                  1002.0                  118.19                
    ##  2 2023  8391000                  997.0                   118.82                
    ##  3 2022  8356000                  999                     119.55                
    ##  4 2021  8454000                  979.0                   115.80                
    ##  5 2020  8804190                  981.0                   111.42                
    ##  6 2019  8824751                  987.4                   111.89                
    ##  7 2018  8826377                  1008.0                  114.20                
    ##  8 2017  8815395                  990.2                   112.33                
    ##  9 2016  8794592                  1002.0                  113.93                
    ## 10 2015  8736590                  1009.0                  115.49                
    ## # ℹ 55 more rows
    ## # ℹ abbreviated names: ¹​nyc_consumption_million_gallons_per_day,
    ## #   ²​per_capita_gallons_per_person_per_day

Looking at pokemon

``` r
pokemon_json = 
  GET("https://pokeapi.co/api/v2/pokemon/1")

pokemon_data = 
  pokemon_json |>
  content()

pokemon_data[["name"]]
```

    ## [1] "bulbasaur"

``` r
pokemon_data[["height"]]
```

    ## [1] 7

``` r
pokemon_data[["abilities"]]
```

    ## [[1]]
    ## [[1]]$is_hidden
    ## [1] FALSE
    ## 
    ## [[1]]$slot
    ## [1] 1
    ## 
    ## [[1]]$ability
    ## [[1]]$ability$name
    ## [1] "overgrow"
    ## 
    ## [[1]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/65/"
    ## 
    ## 
    ## 
    ## [[2]]
    ## [[2]]$is_hidden
    ## [1] TRUE
    ## 
    ## [[2]]$slot
    ## [1] 3
    ## 
    ## [[2]]$ability
    ## [[2]]$ability$name
    ## [1] "chlorophyll"
    ## 
    ## [[2]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/34/"
