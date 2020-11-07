# US elections 2020
## @kjhealy

- Results as of YYYY_MM_DD_HH_MM_SS timestamp in file title. Not final.
- NB The unit of observation in these rows varies. See below.

## Columns

- `race`: President, Senate, House, Governor
- `id`: Variable length character. Codes are: "0" for all states (Presidential and Senate races), identify states using `fips_char` instead; five-digit county FIPS code for counties (Presidential and Senate Races); 
*ten*-digit FIPS location code for township or similar location (first five characters are county FIPS); or (for House races only) a four-digit code with two-digit State FIPS + two-digit House District. Note zero padding. 
This column should be parsed as character, not numeric.
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

## FIPS codes
The state-level FIPS codes, extracted from a page at the [NRCS](https://www.nrcs.usda.gov/wps/portal/nrcs/detail/?cid=nrcs143_013696), are in `fips.csv`. The columns are as follows:

- `state`: The state or region, i.e. 'Alabama'
- `abbr`: The postal code, i.e. 'AL'
- `fips_char`: The abbreviation, i.e. '01'; note zero padding.
