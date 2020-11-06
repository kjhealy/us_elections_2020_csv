# US elections 2020
## @kjhealy

- Results as of YYYY_MM_DD_HH_MM_SS timestamp in file title. Not final.

## Columns

- `race`: President, Senate, House
- `fips_char`: State FIPS code
- `place`: State name, or ciybtt name. House races are reported by District and have NA for place names.
- `id`: 0 for all states (Presidential and Senate races); five-digit county FIPS code for counties (Presidential and Senate Races); or four-digit State FIPS + House District for House races.
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
