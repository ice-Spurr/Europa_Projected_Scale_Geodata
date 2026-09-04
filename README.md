# EUROPA PROJECTED SCALE GEODATA (EPSG)
Initially started as the _EU4 Province Map_, aims to recreate the Europa Universalis IV game map in QGIS, for geospatial analyses and enhanced realism.

---
# Inspiration
I'm a map "fanatic", and QGIS is like a game to me (no wonder why I play EU4 then...). BUt I'm not alone ! I took my inspiration from the mysterious _Missouri_EMGIS_ who uploaded its version of the map on ArcGIS : [online viewer](https://www.arcgis.com/apps/mapviewer/index.html?webmap=194c90245ba4421486afa888c622dd1e) and [download](https://www.arcgis.com/home/item.html?id=16f470b5be2b4c25a76e0151bd4f76e7#overview). Sadly, this files is largely outdated, and with simple geometries.
# Data Origin
The raw data comes from varied sources, including game files and geospatial datasets.

## EUIV data
Game files were scrapped with python codes to extract `.xlsx` and `.csv` files in `\DEFAULT_values`. All values correspond to the 1444-11-11 start date :
- `countries.xlsx` links countries tags, names and map colours. Tags are from `Europa Universalis 4/history/countries`, and colours from `Europa Universalis 4/common/countries`. Some end-game tags colours are missing.
- `religions.xlsx` links the religions codes, names, groups and map colours.
- `provinces.xlsx` contains most of the provinces data, like the owner, development, region etc. Provinces/cities in blue are already mapped. Some data are directly extracted from a 1444-11-11 game save.
- `definition.csv` links the provinces IDs and their map colour. Sourced from `Europa Univrsalis 4/map`, I've added each province colour in it. I don't know what the "x" column is for.
- `regions.csv` links regions and their colours. Manually compiled from the wiki.
- `religions.csv`links religions and their colours. Manually compiled from the wiki.
- `trade_goods.csv` links trade goods and their colours. Manually compiled from the wiki.
- `save_default_1444-11-11.txt` is a game save from the start date, to retrieve miscellaneous data.

## Basemaps
The "real world data" I'm using comes from these sources :
- The coastline is from [Natural Earth](https://www.naturalearthdata.com/). I'm using the [10 meters](https://www.naturalearthdata.com/downloads/10m-physical-vectors/10m-land/) version, which provides a sufficient precision for this project.
- The lakes are also from [Natural Earth](https://www.naturalearthdata.com/). I'm also using a [10 meters](https://www.naturalearthdata.com/downloads/10m-physical-vectors/10m-lakes/) model, with both "lakes" (todays lakes) and "historic lakes" (for past lakes, such as some Netherlands polders or the Aral Sea).

I'm using 2 datasets for the political subdivisions :
- one from [World Bank](https://datacatalog.worldbank.org/search/dataset/0038272/world-bank-official-boundaries) : unknown precision, but simpler geometry with fewer errors. Useful for dense areas (e.g., Poland, where GADM data is overly complex).
- one from [GADM](https://gadm.org/download_world36.html) : high precision, with full administrative subdivisions (up to level 6 in some countries like France). Allows flexible province boundaries but can be resource-intensive.

**Why using 2 political datasets ?**
GADM offers high precision and deep subdivisions, enabling detailed province boundaries. However, its complexity can slow down processing (e.g., Poland’s polygons are overly detailed). The World Bank dataset provides a simpler, error-free alternative for performance-critical areas.

## Custom Data and Layers
- `Cities` layer : a point layer with the cities, you guessed it. I chose the exact point coordinates depending on a few factors :
	- For coastal cities, I snapped the point to the coast vertex/line.
	- For inland cities, I placed the point on the biggest religious edifice (Notre Dame de Paris, Milano Duomo...) or a major military feature (Kruja Fortress, Russian Kremlins...).
	- If none was available (province capital is a really small town, the city doesn't exists anymore), I placed the point in a central park or an intersection.
- `my_run_XXX` : used for custom runs. In this repository, my own runs are not included. I'll do it later. This layer allows you to map your own country (see [How to make a map for your own run ?](#how-to-make-a-map-for-your-own-run-?)). You can edit many properties, including the possession type :
	- Subject :
		- Personal Union
		- Vassal
		- March
		- Colonial Nation
		- Client State
	- Own :
		- Full Core
		- Territory
		- Trade Company
		- Colonial Outpost
	
# How I map
1. Georeferencing : `provinces.bmp` needed to be accurately georeferenced to draw the provinces over it. the georeferenced version is named `MAP_provinces_projected.tif` in `\DEFAULT_vales\Provinces 1.37`.2100 reference points were used on coastlines and lakes.
2. Placing the cities : since I don't want to draw a province and find out that its capital is not included in it (meaning reworking the provinces around it) I place all cities from a region, **then** I map the provinces. I made this mistake with Germany, it was a hell to recover.
3. Province Splitting : using the _split features_ tool with the _snapping_/_tracing_ options, the NE_10m map was split into provinces, loosely following today's political borders.

**/!\ Disclaimer /!\\**
This project **does not intend to make political judgments** about regional ownership. Modern borders are used as a _reference only_ and may not fully align with historical or cultural realities. Errors or omissions are unintentional.

# How to make a map for you own run ?
Ok, maybe there's a more efficient way to do it. Here how I go :
1. On the `provinces` layer, select and copy (CTRL+C) the desired provinces.
2. On the `my_run_XXX` layer, past your provinces. Edit them to provide the country tag, the date where you started and stopped being their lord, what type of possession it is (see the [Custom data and layers](#custom-data-and-layers) chapter above), and what country you got them from. For possession dates, you can go the esea way (give all you provinces either _full core_ or the appropriate subject type) or the mad way (start and end dates for each stage of legitimacy : territorial and full core, even trade company of needed).
3. Repeat until done.
4. Change the map style (right click on layer > Style > chose) if you want.
5. Some styles are "temporal", such as _Political_. Activate the Temporal Controller Panel in the QGIS toolbar to view an animation of your border !
6. Share on Reddit !

---
# Author
- iceSpurr : [u/iceSpurr](https://www.reddit.com/user/iceSpurr/submitted/), [DeviantArt](https://www.deviantart.com/icespurr)

# Changelog

| **Version** | **Date** | **Changes** | **Completion** (land+wasteland) |
| 0.2.6.2 | 2026-09-04 | **Provinces:** : _West Siberia_. | 815/3359 |
| 0.2.6.1 | 2026-09-03 | **Cities:** Part of _West Siberia_. Now using the help of the "Dynamic Province Names" mod to get different city names when the original isn't available.<br>Converted `provinces.xlsx` to `provinces.ods` for usage of LibreOffice.| 774/3359 |
| 0.2.6   | 2026-08-31 | **Provinces:** _Ural_.<br>**Cities:** _Ural_. (some provinces without cities due to unrealistic city names)| 774/3359 |
| 0.2.5   | 2026-08-26 | **Provinces:** _Russia_, _Pontic Steppe_ (had to make weird shapes, far from the game provinces, to follow the cities locations).<br>**Cities:** _Pontic Steppe_| 739/3359 |
| 0.2.4.1 | 2026-08-18 | README.md written | 697/3359 |
| 0.2.4   | 2026-08-17 | **Provinces:** Part of _Russia_.<br>**Cities:** _Russia_. | 697/3359 |
| 0.2.3.2 | 2026-08-15 | **Provinces:** _Ruthenia_.<br>**Cities:** _South Germany_, _Italy_ (now up to date).<br>Added `my_run_XXX` layer. | 677/3359 |
| 0.2.3   | 2026-07-31 | **Cities:** _Ruthenia_. | 636/3359                    |
| 0.2.2   | 2026-07-29 | **Provinces:** _Poland_.<br>**Cities:** Additional entries.<br>Added hexadecimal colors to files. | 634/3359 |
| 0.2.1   | 2026-07-22 | **Provinces:** _Carpathia_ (remaining).<br>**Cities:** _North Germany_. | 603/3359 |
| 0.1.4   | 2026-07-20 | **Provinces:** _Balkans_, _Wallachia_, _Moldova_. | 581/3359 |
| 0.1.3   | 2026-07-17 | **Provinces:** Baltic.<br>Added `provinces.bmp` with reference points. | 523/3359 |
| 0.1.2   | 2026-07-10 | **Provinces:** _Scandinavia_, _France_, _Iberia_, _Britain_, _Italy_, _Low Countries_, _North Germany_, _South Germany_. | 515/3359 |
| 0.1.1   | 2026-07-08 | **Initial upload.**<br>Provinces: Greenland and Iceland.<br>Added trade goods pictures. | 005/3359 |
