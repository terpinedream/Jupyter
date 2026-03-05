# **Practice with US Airport Dataset**

## **Find cols. with highest % of missing values**

```
missing = airports.isnull().mean().sort_values(ascending=False) # .mul(100) for true %
```

## **Drop cols. missing > 60% of values**

```
airports = airports.drop(columns=missing[missing > 0.6].index)
```

## **Create dictionary to match ISO to country name**

```
isoMap = {
  c.alpha_2: c.name # alpha_2 for ISO standard from pycountry lib
  for c in pycountry.countries
}
# create map
airports['expected_country'] = airports['iso_country'].map(isoMap)
# create df to store mismatched iso/country name
mismatch = airports[airports['country_name'] != airports['expected_country']]
# format country name to title case
airports['country_name'] = airports['country_name'].str.title()
```

## **Find duplicates based on ident code**

```
duplcates = airports[airports.duplicated(subset='ident', keep=False)]
apClean = airports.sort_values('score', ascending=False).drop_duplicates(subset='ident',keep='first')
```
