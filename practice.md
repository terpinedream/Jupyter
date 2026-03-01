# Jupyter / Panda with Olympic Data

## **Data Frame**

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
bios[bios["bonr_country"] == "FRA"]
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
