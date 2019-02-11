# GIS Workshop for PAM 5280

Tutorial written by Keith Jenkins, GIS Librarian at Mann Library, Cornell University, spring 2019.

In this workshop, we will use QGIS create a map of tract-level cancer data for New York City.  The steps below could be modified to map other health variables, or other cities.  (The same methods could also be applied to any other spreadsheet of data that includes tract ids.)  The map we create for New York City will look something like this:

![Finished map image](finished-map/finished-map-image.png)



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

**1. Download the workshop data**

  * Download the workshop folder from [https://github.com/kgjenkins/pam5280-sp2019/archive/v1.zip](https://github.com/kgjenkins/pam5280-sp2019/archive/v1.zip)
  * Go to your downloads folder and unzip the file (right-click the file > 7-Zip > Extract Here)
  It is very import to unzip the the .zip file -- things will not work otherwise!


**2. Open QGIS**

* Start menu > QGIS Desktop 3.4.3

QGIS is a free, open-source geographic information system that can be used to create maps and perform spatial analysis.  If you would like to install QGIS on your own Windows, Mac, or Linux computer, visit the QGIS web site at [qgis.org](http://qgis.org/)


**3. Load the tract boundaries**

    * Layer > Data Source Manager
    * Select the "Vector" tab on the left
    * Click the "..." button to browse to the `tracts-ny` folder within the unzipped workshop files
    * Select the `tl_2018_36_tract.shp` file and click the "Open" button
    * Click "Add" to add the selected shapefile to your map
    * Click "Close" to close the data source manager

    "Vector" means points, lines, and polygons.  In this case, the tract boundaries are polygons.
    Shapefiles are an archaic spatial data format, but still commonly used.  Shapefiles are comprised of several separate files, all with the same name, but with different extensions.  In this case, all the files that start with `tl_2018_36_tract`.  When selecting the shapefile to load, pick the one with the `.shp` extension.


**4. Basic Styling**

    The shapefile only contains data, and lacks any sense of style.  The default polygon style is used with a random color, but we can change it.

    * Click the colorful paintbrush above the list of layers on the left in order to open the "Layer Styling" panel.
    * Adjust the size and position of the panel as necessary.
    * To change the color, click the color bar to choose a new color.  There are many ways to select a color!  (I prefer the triangle within the circle.)
    * When you are done selecting a color, click the back arrow near the top of the the Layer Styling dock.
    * Clicking the "Simple fill" part of the style will let you adjust other things, such as the stroke (border) color and width.
    * Experiment a bit, but be sure to end up with a black stroke, 0.26mm wide.


**5. Explore the tract boundary data**

    There are two basic ways to explore a shapefile:

    * View the info for a specific polygon by selecting the "Identify Features" tool (blue circle with white "i") and then clicking a polygon.
    * View the attribute table for the whole layer by right-clicking the layer name > Open Attribute Table

    Notice that there is no demographic data in the boundary shapefile, just identifiers and geometric data.  We will be using the GEOID column, which contains a unique id for each tract, to join the 500 Cities data in order to have something interesting to map.


**6. Load the 500 Cities health data**

    * Layer > Data Source Manager
    * Select the "Delimited Text" tab on the left
    * Click the "..." button to browse to the top level of the workshop files
    * Select the `cdc_500_cities.csv` file

    QGIS is pretty good at detecting things in a CSV, but check the options and make sure that the "Sample Data" preview at the bottom looks good.  Make sure that "Detect field types" is checked. and that "No geometry" is selected (meaning that there are no coordinates in our table).

    * Click "Add" to add the CSV to QGIS
    * Click "Close" to close the data source manager

    A delimited table won't yet show up on the map, but you can explore the data table:

    * Right-click the CSV layer name > Open Attribute Table

    Notice especially the "TractFIPS" column, with contains the unique ids for each tract.  We will use these values to match each row to the corresponding row in the tract boundary shapefile.


**7. Join tables**

    We want to join the 500 Cities data to the tract boundaries, so that we can use colors to display the 500 Cities data on the map.

    * Right-click the tract layer > Properties...
    * Select the "Joins" tab on the left
    * Click the green "+" button at the bottom to add a new join

    Set the following options in the "Add Vector Join" dialog:

    * Join layer = cdc_500_cities
    * Join field = TractFIPS
    * Target field = GEOID
    * Custom Field Name Prefix = `_`

    Be sure to save the join!

    * Click "OK"

    Open the attribute table for the tract boundaries to see the joined data.  Note that some of the rows will have null values because they were not part of the 500 Cities dataset.


8. Graduated colors

    Now that we have some interesting health data in our tract boundary attribute table, we can make a choropleth map, using the numeric values of a particular column to set the color of the corresponding polygon.

    We will be mapping New York City, so zoom to that area on the map.  Now we'll change the map styles.

    * Open the "Layer Styling" dock and be sure that the tract boundary layer is selected.
    * Change "Single symbol" (near the top) to "Graduated"
    * Set Column = `_CANCER_CrudePrev`
    * Click the "Classify" button near the bottom

    If all goes well, you should see a range of colors across the city.  Notice that the tracts that had null values (i.e. were not included in the 500 Cities dataset) do not appear at all.

    The default scaling mode is "equal interval", which is not usually the best way to view the data.  Change it to "Quantile" and observe the difference.  Also try "Natural Breaks", which is especially designed for mapping numeric data -- it tries to minimize the variation within each classification.

    You can change the number of classes (default is 5), and you can also change the color ramp -- click it to edit the colors.  There are many other ways to customize map styles in QGIS -- the possibilities are endless!


9. Add a Basemap

    Sometimes it is useful to load a global base layer from the web, to add context to your map, or just to help confirmthat your data is correctly aligned.  The QuickMapServices plugin makes this easy.  (It's already installed on the Mann Library computers.)

    * Web menu > QuickMapServices > ESRI > ESRI Gray (light)

    To avoid the pixelization caused by reprojecting the basemap image, right-click the basemap layer > Set CRS > Set Project CRS from Layer.  (CRS means Coordinate Reference System.)  In fact, the CRS of basemap is a better choice for ensuring that shapes on the ground are true, and not distorted (as in the case of the original latitude/longitude CRS that was being used when we first loaded the tract boundary shapefile).



