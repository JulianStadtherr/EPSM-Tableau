# Project: Education

O: observation
S: solution
Q: question

## Dataset 
- Source: https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/MNM5Q5 
- Background info: EPSM covers 157 countries with populations exceeding 1 million people, and the time series extends from 1789 to the present

## Data
- Histname: Name of the country in that year
- COWcode: 'Correlates of war' is a code assigning a number to each country. Can be used to allow for comparability accross different datasets

# Data cleaning & preparation
O: Columns starting with 'gap' contain no values
S: Drop all. 

Q: What doe the columns 'project, historical' represent?
S: Drop

Q: How does COWcode behave to country_id?
O: There are more country_ids than COWcodes. 
S: Use country_id to identify countries, as it is specific to the dataset
Note
- There are presumably 202-195 = 7 more countries with IDs. 
- We find 12 countries without COW. 
- To find where there is a mismatch, we would need to map each country id to a COW and then check where there is an issue. 

Q: Are object columns correctly formatted?
O: In column 'free_exist' should only be 0, 1, 2 as result but there is also 2;3;4
S: Assumption: '2;3;4' entries are part of the column 'compul_exempt' & entry in 'free_exist' = 1. Change rows accordingly & adjust data type of column 'free_exist' 
O: Col 'books_oblig' has cells with '2;3', meaning books of former AND current leaders are read
O: All other columns are objects as several answers can apply

O: The map showed there is a case without compuls ed (compuls_exist = 1) AND free_exist = 2, which can logically not be.
S: Filter for all these cases and turn free_exist = 1 (Colombia, Oman: ed is free, but not compulsory S: EPSM_dataset_full)

O: The map showed there is a case whithout compuls ed. (compuls_exist = 1) AND (compuls_exempt != 0), which can logically not be.
S: Change Nigeria: compuls_exist = 2, rest: turn compuls_exempt = 0

O: There are 161 cases where education is compulsory for all (compuls_exist = 3), yet some groups are excluded (compuls_exempt != 0). 
S: Set compuls_exist to 2 ('for some groups'). Only .6% of all rows are affected

Q: Is compuls_exist = 2, ie for some groups, always related to compuls_exempt being true, ie there being groups exluded?
O: After the previous adjustment, compuls_exist = 2 is equal to compuls_exempt != 0 or null. So compuls_exempt only serves as column to drill on details which groups are affected
S: Remove compuls_exempt from map

Q: Are there cases where there is no compuls. ed. (compul_ed = 1) but a positive number of years of compul.ed?
O: There are
S: Set years of comp. ed. to 0 OR set compul. ed. to that of previous / following years for that country
    - Sierra Leone: 9 rows (1995-2003) -> changed years to 0 based on rows for 2004 ff. 
    - Urugay: 1826, 1848 
        - 1826: changed comp. ed. to 3, based on 1827
        - 1848: chamged years to 0 based on rows for 1849 ff. 
    - Trinidad & Tobago: 1921 -> changed comp. ed. to 3, based on 1922 ff.
    - Malaysia 1996: chamged years to 0 based on rows before and after + Compulsory ed only from 2002 onward (S: EPSM_dataset_full)
    - Oman: 2006-2020: changed years to 0 based on online research (compulsory since 2023: https://www.omanobserver.om/article/1137504/oman/education/basic-education-is-mandatory-in-oman-no-corporal-punishment) + EPSM_dataset_full (There is no compulsory education in Oman, but education is provided free of charge to all children from the age of 6 through scholarships that the family has to apply for (UNESCO))

O: There is an entry for Chile in 1886 where years of comp. ed. is 0.41. These show up as 0 years on the map because I made compuls_year an int
Q: Are there other rows where 0 < years < 1
O: There are 86 rows concerning Chile & Vietnam (0.41 & 0.11 resp.)
S: Viet Nam: 6 weeks -> leave as is; Chile: 5 Months -> leave as is (S: EPSM_dataset_full)

O: There is a big spike in # of countries with No compulsory ed in 1900 without a drop in other categories. 
S: The size of the dataset must be taken into account by at least showing the number of countries in 2020

O: The total number of countries in the dataset in 2020 seems low compared to the total # of countries in 2020 and what the map looks like
S: Leave as is, it gives an honest evaluation of the completeness of the dataset