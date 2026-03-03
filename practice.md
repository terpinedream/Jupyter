# Jupyter / Panda with Olympic Data

## **Data Frame Snippet**

```
  athlete_id  name  born_date  born_city  born_region  born_country  NOC  height_cm  weight_kg  died_date
0  1  Jean-François Blanchy  1886-12-12  Bordeaux  Gironde  FRA  France  NaN  NaN  1960-10-02
1  2  Arnaud Boetsch  1969-04-01  Meulan  Yvelines  FRA  France  183.0  76.0  NaN
2  3  Jean Borotra  1898-08-13  Biarritz  Pyrénées-Atlantiques  FRA  France  183.0  76.0  1994-07-17
3  4  Jacques Brugnon  1895-05-11  Paris VIIIe  Paris  FRA  France  168.0  64.0  1978-03-20
4  5  Albert Canet  1878-04-17  Wandsworth  England  GBR  France  NaN  NaN  1930-07-25

```

## **Return athletes who's height is > 215 cm**

```
# using loc
bios.loc[bios["height_cm"] > 215]

# using query
bios.query("height_cm > 215")
```

## **Return athletes born in France**

```
bios[bios["born_country"] == "FRA"]
```

## **Return athletes who have a recorded height**

```
bios[bios["height_cm"].notna()]

# with query
bios.query("height_cm.notna()")
```

## **Return athletes who are still living**

```
bios[bios["died_date"].isnull()]

# with query
bios.query("died_date.isnull()")
```

## **Return athletes born after 1950**

```
# ensure date is dtype datetime64
bios[bios["born_date"].dt.year > 1950]
```

## **Create new col for height in meters**

```
# assuming pandas ignores NaN values at runtime
bios["height_m"] = bios["height_cm"] / 100
```

## **Create bool column for weight status**

```
bios["has_weight"] = bios["weight_kg"].notna()
```

## **Create bool column for height**

```
bios["is_tall"] = bios["height_cm"] > 180
```

## **Sort athletes by height descending (name,height,weight)**

```
bios.sort_values(by="height_cm", ascending = False)[["name", "height_cm", "weight_kg"]]
```

## **Find tallest athlete**

```
bios.sort_values(by="height_cm", ascending = False).head(1)
# or
bios.loc[bios["height_cm"].idxmax()]
```

## **Find top 5 heaviest athletes**

```
bios.sort_values(by="weight_kg", ascending=False).head()
# or with nlargest w/ name, height, & weight
bios.nlargest(5, "weight_kg")[["name", "height_cm", "weight_kg"]]
```

## **Count athletes by country**

```
bios.groupby("born_country").size().reset_index(name="athlete_count")
```

## **Group by NOC & find avg height**

```
bios.groupby("NOC")["height_cm"].mean()
```

## **Group by region with most athletes**

```
bios.groupby("born_region").size().reset_index(name="region_count")
# or
bios["born_region"].value_counts().head(1) # top region
```

## **Athletes born in cities > 1**

```
cityCounts = bios["born_city"].value_counts()
duplicateCities = cityCounts[cityCounts > 1].index
bios[bios["born_city"].isin(duplicateCities)]
```

## **% of athletes missing height data**

```
missingHeight = bios["height_cm"].isna().mean() * 100
print(f"{missingHeight:.2f}% of athletes missing height data")
```

## **Drop rows where height & weight are missing**

```
bios = bios.dropna(subset=["height_cm", "weight_kg"], how="all")
```

## **Calculate BMI rounded to 2nd decimal place**

```
bios["BMI"] = bios["weight_kg"] / (bios["height_m"] ** 2)
bios["BMI"] = bios["BMI"].round(2)
```

## **Count athletes born per decade**

```
bios["born_date"] = pd.to_datetime(bios["born_date"], errors="coerce")
# create col for birth year
bios["born_year"] = bios["born_date"].dt.year
# calculate to get even decades EG 1980, 1970, etc.
bios["born_decade"] = (bios["born_year"] // 10) * 10
# convert to int64 to remove trailing decimal
bios["born_year"] = bios["born_year"].astype("Int64")
bios["born_decade"] = bios["born_decade"].astype("Int64")  # Int64 with capital to preserve NaN values
```

## **Calculate lifespan for deceased athletes**

```
# ensure dates are datetime
bios["born_date"] = pd.to_datetime(bios["born_date"], errors="coerce")
bios["died_date"] = pd.to_datetime(bios["died_date"], errors="coerce")
# calculate lifespan in years
bios["lifespan"] = (bios["died_date"] - bios["born_date"]).dt.days / 365.25
# round to integer or two decimals
bios["lifespan"] = bios["lifespan"].round(0).astype("Int64")
# check + exclude living athletes
lifespanKnown = bios[bios["lifespan"].notna()]
lifespanKnown[["name", "born_date", "died_date", "lifespan"]].head(20)
# athlete with longest lifespan
lifespanKnown[["name", "lifespan"]].max()
```

## **Find complexity of name**

```
bios['name_complexity'] = bios['name'].apply(
  lambda n: None if pd.isna(n)
  else "short" if len(n) < 12
  else "medium" if len(n) <= 20
  else "long"
)
```

## **Create age groupings**

```
bios['age_group'] = bios['born_date'].apply(
  lambda x: 'old' if pd.notna(x) and int(x[:4]) < 1950
  else('middle' if pd.notna(x) and 1950 <= int(x[:4]) < 2000
  else('young' if pd.notna(x) else None)
  )
)
```

## **Create BMI classifications**

```
bios['bmi_class'] = bios.apply(
    lambda row: 'overweight' if pd.notna(row['height_cm']) and pd.notna(row['weight_kg']) and row['weight_kg'] / (row['height_cm']/100)**2 >= 25
        else ('normal' if pd.notna(row['height_cm']) and pd.notna(row['weight_kg']) else None),
    axis=1
)
v
```

## **Create country codes from full name**

```
# one word -> first 3 letters
# two words -> first letter, first 2 of second word
# three words -> first letter each word
bios['country_code'] = bios['NOC'].apply(
    lambda x: (
        "".join([w[0] for w in x.split()[:2]] + [x.split()[2][0]] if len(x.split()) > 2
        else [w[0] for w in x.split()[:1]] + [x.split()[1][:2]])
        .upper() if pd.notna(x) and len(x.split()) > 1
        else x[:3].upper() if pd.notna(x)
        else None
    )
)
```
