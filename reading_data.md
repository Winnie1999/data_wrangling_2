reading\_data
================
Yiqun Jin
10/19/2021

## Scrape a table

I want the first table from [this
page](http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm)

read in the html

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

extract the tables(s); focus on the first one

``` r
tabl_marj = 
  drug_use_html %>% 
  html_nodes(css = "table") %>% 
  first() %>% 
  html_table() %>% 
  slice(-1) %>% 
  as_tibble()

tabl_marj
```

    ## # A tibble: 56 × 16
    ##    State      `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014…
    ##    <chr>      <chr>            <chr>            <chr>          <chr>            
    ##  1 Total U.S. 12.90a           13.36            0.002          13.28b           
    ##  2 Northeast  13.88a           14.66            0.005          13.98            
    ##  3 Midwest    12.40b           12.76            0.082          12.45            
    ##  4 South      11.24a           11.64            0.029          12.02            
    ##  5 West       15.27            15.62            0.262          15.53a           
    ##  6 Alabama    9.98             9.60             0.426          9.90             
    ##  7 Alaska     19.60a           21.92            0.010          17.30            
    ##  8 Arizona    13.69            13.12            0.364          15.12            
    ##  9 Arkansas   11.37            11.59            0.678          12.79            
    ## 10 California 14.49            15.25            0.103          15.03            
    ## # … with 46 more rows, and 11 more variables: 12-17(2014-2015) <chr>,
    ## #   12-17(P Value) <chr>, 18-25(2013-2014) <chr>, 18-25(2014-2015) <chr>,
    ## #   18-25(P Value) <chr>, 26+(2013-2014) <chr>, 26+(2014-2015) <chr>,
    ## #   26+(P Value) <chr>, 18+(2013-2014) <chr>, 18+(2014-2015) <chr>,
    ## #   18+(P Value) <chr>

## Star Wars Movie info

I want the data from [here](https://www.imdb.com/list/ls070150896/)

``` r
url = "https://www.imdb.com/list/ls070150896/"

swm_html = read_html(url)
```

Grab elements that I want.

``` r
title_vec = 
  swm_html %>% 
  html_nodes(css = ".lister-item-header a") %>% 
  html_text()

gross_rev_vec = 
  swm_html %>% 
  html_nodes(css = ".text-muted .ghost~ .text-muted+ span") %>% 
  html_text()

runtime_vec = 
  swm_html %>% 
  html_nodes(css = ".runtime") %>% 
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    gross_rev = gross_rev_vec,
    runtime = runtime_vec
  )
swm_df
```

    ## # A tibble: 9 × 3
    ##   title                                          gross_rev runtime
    ##   <chr>                                          <chr>     <chr>  
    ## 1 Star Wars: Episode I - The Phantom Menace      $474.54M  136 min
    ## 2 Star Wars: Episode II - Attack of the Clones   $310.68M  142 min
    ## 3 Star Wars: Episode III - Revenge of the Sith   $380.26M  140 min
    ## 4 Star Wars: Episode IV - A New Hope             $322.74M  121 min
    ## 5 Star Wars: Episode V - The Empire Strikes Back $290.48M  124 min
    ## 6 Star Wars: Episode VI - Return of the Jedi     $309.13M  131 min
    ## 7 Star Wars: Episode VII - The Force Awakens     $936.66M  138 min
    ## 8 Star Wars: Episode VIII - The Last Jedi        $620.18M  152 min
    ## 9 Star Wars: The Rise Of Skywalker               $515.20M  141 min

## Get some water data

This is coming from an API

``` r
#csv
nyc_water = 
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.csv") %>% 
  content("parsed")
```

    ## Rows: 42 Columns: 4

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## dbl (4): year, new_york_city_population, nyc_consumption_million_gallons_per...

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
#json
nyc_water_json =
  GET("https://data.cityofnewyork.us/resource/ia2d-e54m.json") %>% 
  content("text") %>% 
  jsonlite::fromJSON() %>% 
  as_tibble()

nyc_water_json
```

    ## # A tibble: 42 × 4
    ##    year  new_york_city_population nyc_consumption_millio… per_capita_gallons_pe…
    ##    <chr> <chr>                    <chr>                   <chr>                 
    ##  1 1979  7102100                  1512                    213                   
    ##  2 1980  7071639                  1506                    213                   
    ##  3 1981  7089241                  1309                    185                   
    ##  4 1982  7109105                  1382                    194                   
    ##  5 1983  7181224                  1424                    198                   
    ##  6 1984  7234514                  1465                    203                   
    ##  7 1985  7274054                  1326                    182                   
    ##  8 1986  7319246                  1351                    185                   
    ##  9 1987  7342476                  1447                    197                   
    ## 10 1988  7353719                  1484                    202                   
    ## # … with 32 more rows

## BRFSS

Same process, different data

``` r
brfss_2010 = 
  GET("https://chronicdata.cdc.gov/resource/acme-vg9e.csv",
      query = list("$limit" = 5000)) %>% 
  content("parsed")
```

    ## Rows: 5000 Columns: 23

    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (16): locationabbr, locationdesc, class, topic, question, response, data...
    ## dbl  (6): year, sample_size, data_value, confidence_limit_low, confidence_li...
    ## lgl  (1): locationid

    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
brfss_2010
```

    ## # A tibble: 5,000 × 23
    ##     year locationabbr locationdesc  class  topic  question  response sample_size
    ##    <dbl> <chr>        <chr>         <chr>  <chr>  <chr>     <chr>          <dbl>
    ##  1  2010 AL           AL - Mobile … Healt… Overa… How is y… Excelle…          91
    ##  2  2010 AL           AL - Jeffers… Healt… Overa… How is y… Excelle…          94
    ##  3  2010 AL           AL - Tuscalo… Healt… Overa… How is y… Excelle…          58
    ##  4  2010 AL           AL - Jeffers… Healt… Overa… How is y… Very go…         148
    ##  5  2010 AL           AL - Tuscalo… Healt… Overa… How is y… Very go…         109
    ##  6  2010 AL           AL - Mobile … Healt… Overa… How is y… Very go…         177
    ##  7  2010 AL           AL - Jeffers… Healt… Overa… How is y… Good             208
    ##  8  2010 AL           AL - Mobile … Healt… Overa… How is y… Good             224
    ##  9  2010 AL           AL - Tuscalo… Healt… Overa… How is y… Good             171
    ## 10  2010 AL           AL - Mobile … Healt… Overa… How is y… Fair             120
    ## # … with 4,990 more rows, and 15 more variables: data_value <dbl>,
    ## #   confidence_limit_low <dbl>, confidence_limit_high <dbl>,
    ## #   display_order <dbl>, data_value_unit <chr>, data_value_type <chr>,
    ## #   data_value_footnote_symbol <chr>, data_value_footnote <chr>,
    ## #   datasource <chr>, classid <chr>, topicid <chr>, locationid <lgl>,
    ## #   questionid <chr>, respid <chr>, geolocation <chr>
