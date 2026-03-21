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

3.15-3.25

access genai api, manually clean some compound patents, human dataset describe is 
```
count      999.000000
mean      1082.883884
std        629.869197
min        132.000000
25%        697.500000
50%        963.000000
75%       1347.000000
max      10846.000000
Name: claim1, dtype: float64
Filter out the exceptionally long ones
df = df[df['claim1'].str.len() <= 3000]
```
ai dataset is
```
count     999.000000
mean      720.272272
std       299.938932
min       106.000000
25%       502.500000
50%       687.000000
75%       893.500000
max      2117.000000
Name: claim1, dtype: float64
```
refine dataset is 
```
count     998.000000
mean      700.666333
std       293.405266
min       118.000000
25%       488.250000
50%       661.000000
75%       865.750000
max      1841.000000
Name: claim1, dtype: float64
```
512 tokens is about 1200 words, experiment 1 is testing whether roberta classify three types based on length
So I applied random forest classifier only take length of claims as baseline test, verify if it rely on the length to classify

train_test_split is randomly choose 0.2 as test datasets in 3000
```
label
human           999
ai_generated    999
refine          998
Name: count, dtype: int64
(2996, 6)
accuracy only rely on length is : 36.67%
```
36.67 is similar random classificaiton, it indicates that length of claims not effects a lot,

Then use roberta model to do the pipeline classification
