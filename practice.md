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
