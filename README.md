# US elections 2020
## @kjhealy

- Results as of timestamp in file title. Not final.

## Columns

- `race`: President, Senate, House
- `fips_char`: State FIPS code
- `place`: State name, or ciybtt name. House races are reported by District and have NA for place names.
- `id`: 0 for all states (Presidential and Senate races); five-digit county FIPS code for counties (Presidential and Senate Races); or four-digit State FIPS + House District for House races.
- `fname`: Candidate first name
- `lname`: Candidate last name
- `party`: Three-letter party code
- `pab`: One letter party code
- votes: N votes
- incumbents: 1 = is incumbent, 0 otherwise
