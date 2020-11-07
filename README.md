# US elections 2020
## @kjhealy

- Results as of YYYY_MM_DD_HH_MM_SS timestamp in file title. Not final.
- NB: The unit of observation in these rows varies. President, Senate and Governor races are reported at the level of (a) the state _and_ (b) some sub-state place unit, which will usually be county but may be a township or similar place. Setting the state-level totals aside, the smaller unit is _either_ a county or something like a township. A variable (`fips5`) is provided that classifies places by their county FIPS. Votes are not reported more than once, however. So, again excluding state totals first, within any given state summing votes across `id` should yield the same total as summing them by `fips5` and then summing those totals. 

- For example, for Massachusetts:

``` r
results_df %>% 
  filter(race == "President", id != "0", fips_char == "25", mpc == "1") %>% 
  select(race, id, fips_char, fips5,  place, lname, votes)

## A tibble: 702 x 7
#   race      id         fips_char fips5 place      lname votes
#   <chr>     <chr>      <chr>     <chr> <chr>      <chr> <int>
# 1 President 2500103690 25        25001 Barnstable Biden 15685
# 2 President 2500103690 25        25001 Barnstable Trump 10824
# 3 President 2500107175 25        25001 Bourne     Biden  5988
# 4 President 2500107175 25        25001 Bourne     Trump  5026
# 5 President 2500107980 25        25001 Brewster   Biden  4905
# 6 President 2500107980 25        25001 Brewster   Trump  2334
# 7 President 2500112995 25        25001 Chatham    Biden  3043
# 8 President 2500112995 25        25001 Chatham    Trump  1827
# 9 President 2500116775 25        25001 Dennis     Biden  6179
#10 President 2500116775 25        25001 Dennis     Trump  3934
# … with 692 more rows

```


First, group by `id` for township totals, then sum to the state:

``` r

results_df %>% 
  filter(race == "President", id != "0", fips_char == "25", mpc == "1") %>% 
  select(race, id, fips_char, fips5,  place, lname, votes) %>% 
  group_by(id, lname) %>%  # Group by ten digit id
  summarize(votes = sum(votes)) %>% 
  group_by(lname) %>% 
  summarize(state_total = sum(votes))

## A tibble: 2 x 2
#  lname state_total
#  <chr>       <int>
#1 Biden     2246208
#2 Trump     1117260

```


Alternatively, group by `fips5` for county totals, then sum to the state:


``` r

results_df %>% 
  filter(race == "President", id != "0", fips_char == "25", mpc == "1") %>% 
  select(race, id, fips_char, fips5,  place, lname, votes) %>% 
  group_by(fips5, lname) %>%  # Group by county fips
  summarize(votes = sum(votes)) %>% 
  group_by(lname) %>% 
  summarize(state_total = sum(votes))
  
## A tibble: 2 x 2
#  lname state_total
#  <chr>       <int>
#1 Biden     2246208
#2 Trump     1117260
```

For many states, `id` and `fips5` will be identical as all results are reported by county. But for states reporting by township, you must sum by `fips5` to get county-level results:

``` r
## MA reported by township

results_df %>% 
  filter(race == "President", id != "0", fips_char == "25", mpc == "1") %>% 
  select(race, id, fips_char, fips5,  place, lname, votes)
  
## A tibble: 702 x 7
#   race      id         fips_char fips5 place      lname votes
#   <chr>     <chr>      <chr>     <chr> <chr>      <chr> <int>
# 1 President 2500103690 25        25001 Barnstable Biden 15685
# 2 President 2500103690 25        25001 Barnstable Trump 10824
# 3 President 2500107175 25        25001 Bourne     Biden  5988
# 4 President 2500107175 25        25001 Bourne     Trump  5026
# 5 President 2500107980 25        25001 Brewster   Biden  4905
# 6 President 2500107980 25        25001 Brewster   Trump  2334
# 7 President 2500112995 25        25001 Chatham    Biden  3043
# 8 President 2500112995 25        25001 Chatham    Trump  1827
# 9 President 2500116775 25        25001 Dennis     Biden  6179
#10 President 2500116775 25        25001 Dennis     Trump  3934  
```


``` r
## MA county names and fips codes

tmp <- county_data %>% 
  as_tibble() %>% 
  select(id, name, state) %>% 
  filter(state == "MA") %>% 
  rename(fips5 = id)

tmp
  
#> tmp
## A tibble: 15 x 3
#   fips5 name              state
#   <chr> <chr>             <fct>
# 1 25000 22                MA   
# 2 25001 Barnstable County MA   
# 3 25003 Berkshire County  MA   
# 4 25005 Bristol County    MA   
# 5 25007 Dukes County      MA   
# 6 25009 Essex County      MA   
# 7 25011 Franklin County   MA   
# 8 25013 Hampden County    MA   
# 9 25015 Hampshire County  MA   
#10 25017 Middlesex County  MA   
#11 25019 Nantucket County  MA   
#12 25021 Norfolk County    MA   
#13 25023 Plymouth County   MA   
#14 25025 Suffolk County    MA   
#15 25027 Worcester County  MA   
  
```

``` r

## Aggregate by county FIPS and merge county names.

results_df %>% 
  filter(race == "President", id != "0", fips_char == "25", mpc == "1") %>% 
  select(race, id, fips_char, fips5,  place, lname, votes) %>% 
  group_by(fips5, lname) %>%  # Group by county fips
  summarize(votes = sum(votes)) %>% 
  left_join(tmp, by = "fips5")
  
# # A tibble: 28 x 5
# # Groups:   fips5 [14]
#    fips5 lname  votes name              state
#    <chr> <chr>  <int> <chr>             <fct>
#  1 25001 Biden  89732 Barnstable County MA   
#  2 25001 Trump  54132 Barnstable County MA   
#  3 25003 Biden  41521 Berkshire County  MA   
#  4 25003 Trump  14015 Berkshire County  MA   
#  5 25005 Biden 150063 Bristol County    MA   
#  6 25005 Trump 118085 Bristol County    MA   
#  7 25007 Biden   9762 Dukes County      MA   
#  8 25007 Trump   2587 Dukes County      MA   
#  9 25009 Biden 259792 Essex County      MA   
# 10 25009 Trump 141135 Essex County      MA   
# # … with 18 more rows  
#   
```





## Columns

- `race`: President, Senate, House, Governor
- `id`: Variable length character. Codes are as follows:
  - _For President, Governor, and Senate Races_. ONE OF: (a) "0", if the row refers to results for a whole state. Identify states using `fips_char` instead. (b) A *five*-digit county FIPS code if the row refers to results for a county. (c) A *ten*-digit FIPS location code for results from a township or similar location (the first five characters are this location's county FIPS). Note zero padding. 
  - _For House races only_: A four-digit code consisting of a two-digit State FIPS + two-digit House District. Note zero padding. 
  - This column should be parsed as character, not numeric.
- `fips_char`: Two digit state FIPS code. Note zero padding. This column should be parsed as character, not numeric.
- `fips5`: Five digit FIPS code identifying the county the place is in. Note zero padding. This column should be parsed as character, not numeric.
- `place`: State name, or place name. House races are reported by District and have NA for place names. In some states (for example, Vermont), the Presidential, Senate, and Governor results are reported by township or 
similar location, not county. Thus, (a) place is not county and (b) if you filter out rows where `id` = 0 (i.e., whole states), the rows you are left with are still are not unique counties  To get 
true county-level results for these races you will have to aggregate vote counts in the rows by `fips5`. 
- `fname`: Candidate first name
- `lname`: Candidate last name
- `party`: Three-letter party code
- `pab`: One letter party code
- `votes`: N votes
- `incumbent`: 1 = is incumbent, 0 otherwise
- `mpc`: 1 if candidate is a main party candidate (Rep or Dem), 0 otherwise

## FIPS codes
The state-level FIPS codes, extracted from a page at the [NRCS](https://www.nrcs.usda.gov/wps/portal/nrcs/detail/?cid=nrcs143_013696), are in `fips.csv`. The columns are as follows:

- `state`: The state or region, i.e. 'Alabama'
- `abbr`: The postal code, i.e. 'AL'
- `fips_char`: The abbreviation, i.e. '01'; note zero padding.
