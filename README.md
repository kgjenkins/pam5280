# GIS Workshop for PAM 5280

Tutorial written by Keith Jenkins, GIS Librarian at Mann Library, Cornell University, spring 2019.

In this workshop, we will create a map of tract-level cancer data for New York City that looks something like this:

![Finished map image](finished-map-image.png)

The steps in this tutorial could also be applied to other health variables, or other cities.


## Data sources

The CDC (Centers for Disease Control and Prevention) have compiled a variety of health data for 500 cities across the United States.  This data is aggregated at the Census tract level, which is detailed enough to see variation across a city.

**500 Cities tract-level health data**
  * CSV downloaded from [https://chronicdata.cdc.gov/500-Cities/500-Cities-Census-Tract-level-Data-GIS-Friendly-Fo/k86t-wghb](https://chronicdata.cdc.gov/500-Cities/500-Cities-Census-Tract-level-Data-GIS-Friendly-Fo/k86t-wghb)
  * renamed to cdc_500_cities.csv

The only location data provided by this CSV file is found in the columns containing the state abbreviation, placename, and tract FIPS codes (which uniquely identify each tract).  In order to make a map, we will need the Census tract boundaries.

**Census Tract boundaries**
  * shapefile downloaded from [https://www.census.gov/cgi-bin/geo/shapefiles/index.php?year=2018&layergroup=Census+Tracts](https://www.census.gov/cgi-bin/geo/shapefiles/index.php?year=2018&layergroup=Census+Tracts)

Those two data sources are sufficient to make a map.  But for New York City, which is on the coast, we will use one additional dataset of water polygons in order make our map more legible, by clearly distinguishing land from water.  (Census tracts often extend across rivers, and into the ocean.)

**Water polygons**
  * shapefile downloaded (projection: WGS84) from [http://openstreetmapdata.com/data/water-polygons](http://openstreetmapdata.com/data/water-polygons)
  * this global dataset was clipped to the area surrounding New York City


## Step by Step

Load tract boundaries
basic symbology

Load health data
join tables

Graduated colors

Cut water areas out of tracts
save to new file

Basemap
