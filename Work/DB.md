## Prod
Read only snapshot with 1-2 days delay
Two instances in a master/slave setting
NCEPLL0000SQL12.etv.nce.amadeus.net
ncepll0000sql20.rnd.amadeus.net

`dhandlerRW` is the user/pw

use the `CURRENT_` DB only
## Acceptance
https://rndwww.nce.amadeus.net/confluence/display/SECODOC/How+to+connect+to+PLL+database

for ACC, it is actually on ncepll0000sql20.rnd.amadeus.net
​password is same as login name
you have 2 DBs:
- CURRENT_ACM_SECOCSMEGA1ACC
- CURRENT_ACM_SECOCSMEGA2ACC
​
to find the active one, run: 
`select top 10 * from cmp.TP_Component  order by CreatedOn desc`
on both, and the DB on which there is the most recent created component is the active one
