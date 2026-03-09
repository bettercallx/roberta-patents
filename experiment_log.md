2026.3.3-3.8

DATASETS:
-
---sql query data quantity---
```  SELECT COUNT(*) 
  FROM 
  `patents-public-data.patents.publications_201802`
  WHERE 
  country_code = 'US'AND publication_number NOT LIKE 'US-D%
```
result is 15108025

---sql query patents type---

[patents type](https://www.uspto.gov/patents/basics/apply)
There are three types of patents: utility, design and plant
patents application type distribution are
```
SELECT DISTINCT application_kind, COUNT(*) as cnt 
FROM 
`patents-public-data.patents.publications_201802`
WHERE 
country_code = 'US'
GROUP BY application_kind
ORDER BY cnt DESC
```
```
1 A	15037811
2	F	566527
3		32422
4	V	31996
5	E	4802
6	D	3419
7	H	1084
8	K	8
9	J	3
10 P 2...
```
[kind_code_in_patent](https://www.uspto.gov/learning-and-resources/support-centers/electronic-business-center/kind-codes-included-uspto-patent)
A means Pre-grant publication, B means No previously published pre-grant publication.
patents publication type distribution are
```
SELECT 
SPLIT(publication_number, '-')[SAFE_OFFSET(2)] AS suffix,
COUNT(*) as cnt 
FROM 
`patents-public-data.patents.publications_201802`
WHERE 
country_code = 'US'
GROUP BY suffix
ORDER BY cnt DESC
```
```
1	A	6150494
2	A1	5166459
3	B2	2969953
4	B1	740002
5	S	570054
6	E	31457
7	P2	12041
8	P	9776
9	P1	7088
10	A9	7071
11	P3	5172
12	H	2267
13	I5	2242
```
#decide only obtain B2 patents
3696530
```
SELECT
publication_number,
title_localized.text AS title,
claims_localized.text AS claims
FROM 
`patents-public-data.patents.publications_201802`,
UNNEST(title_localized) AS title_localized,
UNNEST(claims_localized) AS claims_localized
WHERE
country_code = "US"
AND SPLIT(publication_number, '-')[SAFE_OFFSET(2)] IN ('B1', 'B2')
AND title_localized.language = 'en'
AND claims_localized.language = 'en'
LIMIT
50000
```
generate v1.csv means only contains claims1 and delete prefix

experiments design
-
datasets
train 
human:ai:refined = 1000:1000:1000
validation
human:ai:refined = 200:200:200
test
human:ai:refined = 200:200:200

roberta only read 512 tokens
1.truncate: only first 512 tokens
2.truncate: only last 512 tokens
3.sliding window?
