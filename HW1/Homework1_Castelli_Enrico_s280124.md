# Homework 1 - Enrico Castelli s280124

## Data Warehouse

#### Conceptual schema

![conceptual](HW1/conceptual_schema.svg)

#### Logical schema

VIEWER(_VID_, AGEGROUP, GENDER, CATEGORY, _LID_)  
LOCATION(_LID_, CITY, PROVINCE, REGION)  
PARTICIPANT(_PID_, PARTICIPANTNAME, _LID_)  
TIMETABLE(_TID_, TIME, HOUROFDAY)  
EDITION(_EID_, PRESENTER, YEAR)  
DATETABLE(_DID_, DATE, DAYOFWEEK, _EID_)  

Facts table:  
VOTE(_VTID_, _VID_, _PID_, _TID_, _EID_, _DID_, NUMVOTES, INCOMES, VALUE, TELEVOTINGMODE)

## Queries

#### (a)

```sql
SELECT E.YEAR, VW.CITY, D.DAYOFWEEK,
       SUM(VT.INCOMES) OVER(ORDER BY E.YEAR
                            ROWS UNBOUNDED PRECEDING),
       100*SUM(VT.INCOMES)/SUM(SUM(VT.INCOMES)) OVER(PARTITION BY E.YEAR),
       AVG(SUM(VT.INCOMES)) OVER(PARTITION BY D.DAYOFWEEK)
FROM VOTE VT, VIEWER VW, DATETABLE D, EDITION E
WHERE VT.VTID=VW.VID AND VT.DID=D.DID AND D.EID=E.EID
      AND VT.TELEVOTINGMODE="Instagram"
GROUP BY E.YEAR, VW.CITY, D.DAYOFWEEK
```

#### (b)

```sql
SELECT E.YEAR, P.PARTICIPANTNAME,
       SUM(SUM(NUMVOTES)) OVER(PARTITION BY E.YEAR),
       100*SUM(NUMVOTES)/SUM(SUM(NUMVOTES)) OVER(PARTITION BY E.YEAR),
       RANK() OVER(PARTITION BY P.PARTICIPANTNAME
                   ORDER BY COUNT(DISTINCT D.DATE) DESC)
FROM VOTE VT, DATETABLE D, EDITION E, PARTICIPANT P
WHERE VT.DID=D.DID AND VT.PID=P.PID AND E.YEAR BETWEEN 2000 AND 2010
GROUP BY E.YEAR, P.PARTICIPANTNAME
```