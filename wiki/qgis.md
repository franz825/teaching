---
layout: default
title: QGIS
parent: Tricks and tips
nav_order: 1
---

# QGIS tips and hints

## Good practices

- Always save the outputs of toolboxes on the disk of your machine. Do **not** use temporary files.
- Always work with layers which have **the same projection**. QGIS efficiently deals with the EPSG codes, i.e. 4-5 digit numbers that represent coordinate reference system definitions. Also make sure that the data frame has the same projection than layers. EPSG codes are listed on the [epsg.io](https://epsg.io/) or [spatialreference.org](https://spatialreference.org/ref/epsg/) websites. As in ArcGIS, the data frame projection is defined by the projection of the first layer imported in the project.
  - To reproject a shapefile: "Menu Vector > Data Management Tools > Reproject Layer".
  - To reproject a raster: "Menu Raster > Projections > Warp (Reproject)".
  - To define the data frame coordinate reference system: "Menu Project > Properties" > Tab "CRS".
- Avoid spaces and special characters in filenames.
- Use `.shp` for vector data and `.tif` for raster data. For raster data, it might also be `.sgrd` or `.sdat`, depending on the toolboxes that you run.

Some common coordinate system EPSG identifiers: 

| Coordinate system                                            | EPSG ID |
| ------------------------------------------------------------ | ------- |
| WGS 84                                                       | 4326    |
| WGS 84 - Pseudo-Mercator (Google / OSM WMS data)             | 3857    |
| Belgian Lambert 72                                           | 31370   |
| Belgian Lambert 2008                                         | 3812    |
| UTM 31 N (covers Belgium)                                    | 32631   |
| UTM 32 N (covers the most eastern part of Belgium, i.e. High Fens and Cantons de l'Est) | 32632   |

## DEM symbology / DEM not rendering properly

Single-band raster datasets are usually rendered using a color ramp from black to white. In practice, it means that the range of values contained in the datasets is stretched over the black to white gradient. If the distribution of these values is not relatively constant over the entire range, e.g. the majority of values is less than 1000 while only a few pixels have very large values (e.g. > 10^6), the GIS will render a completely black layer. This might be caused by a mis-interpretation of no data values in digital elevation models. It is also typical for flow accumulation data (few large values in rivers while majority of hillslopes have low values).

To properly render such raster datasets, the idea is to ask QGIS to render values only on the currently visible range of pixel values. To do so:

1. Open the properties of the layer.

2. Open the "Symbology" tab.

3. In the "Min/Max Value Settings" section, define "Statistics Extent" as "Updated canvas" in the dropdown menu.

   ![raster-rendering](imgs/qgis-stretch-values.png)

4. Zoom in on small portions of the DEM. You will see that the colour of the pixels will adapt to the range of the visible elevation.

## Processing toolboxes

If the "Processing Toolbox" panel has disappeared, simply go to the "Menu Processing > Toolbox".

## Install plugins in QGIS

1. Go to menu "Plugins > Manage and install plugins".
2. Browse the plugins and install the ones needed, e.g. "Profile tools" or "Base layers".
    ![Plugins_image](imgs/Plugins.png)
3. Plugins are now accessible either in the menu "Plugins" or in the other menus, e.g. "Processing".

## Add base layers

You can add base layers, e.g. OpenStreetMap, using the plugin "QuickMapServices" plugin in QGIS.

1. Install the plugin via the Plugin Menu.
2. Activate base layers via the menu "Web > QuickMapServices".

## Add WMS layer

GIS resources may be stored on GIS servers. This is often the case in companies, public administrations,... where several people need to access up-to-date versions of spatial datasets. Such datasets can potentially be accessed using the Web Map Service (WMS) protocol by external users, directly in their local GIS software. To do that, you need the url of the WMS and, if applicable, the credentials.  

WMS adresses are usually stored in metadata of spatial datasets. For instance, wms of WalonMap data are accessible in the "Fiche descriptive > Accès" of each data.

![wms-walonmap](imgs/wms-walonmap.png)

Two lists of WMS in Belgium:

- The "institutional" repository: [https://www.geo.be/](https://www.geo.be/)
- A "unofficial" list: [https://wms.michelstuyts.be/](https://wms.michelstuyts.be/)

In practice, in QGIS, for the IGN cartoweb layer:

1. Menu "Layer" > "Add Layer" > "Add WMS/WMTS Layer"
2. Click "New" and enter these parameters.

    | Setting | Value                                                    |
    | ------- | -------------------------------------------------------- |
    | Name    | IGN                                                      |
    | URL     | [http://wms.ngi.be/inspire/topomaps/service?version=1.3.0](http://wms.ngi.be/inspire/topomaps/service?version=1.3.0) |

3. Click "OK".
4. Select the layer and click "Connect". A list of all available layers is provided in the table.
5. Choose which layer you want to add in your project and click "Add".

    ![wms-ign](imgs/wms-ign.png)

## Crop DEM

1. Open the DEM in QGIS.
2. Select the tool "Clip Raster by Extent" in the menu "Raster > Extraction".
3. Fill the toolbox. You can either define the extent interactively by drawing a rectangle in the dataframe or use a shapefile.
4. Export the result in the **.bil** format if to be used as an input of LSDTopoTools or as a **.tif** in other cases.

## Add text-delimited layers

Based on a csv file containing coordinates, you can create a layer to be spatialised in QGIS, e.g. channel network outputs from LSDTopoTools.

1. Open the tool 'Add delimited text layer' in the menu "Layer > Add Layer".
2. Browse to find the csv file containing coordinates to be spatialised.
   1. Select **csv file format** just below where you select the file.
   2. You need to check on the **X Field** and **Y Field** entries to make sure that they are correct!
   3. Define the correct coordinate system, in which points are defined.
   4. Click on the button "Add".
   5. A temporary point layer is created. You need to save it to a permanent file if needed.

![Delimited-text-layer_image](imgs/Text-delimited-layer.png)

## Convert raster to vector

This tool is useful to create polygons defining the extent of river catchments, because LSDTopoTools provides a raster as output.
1. Open the "Polygonize" tool in the menu "Raster > Conversion".
2. Define which raster you need to process and run...

    ![Polygonize_image](imgs/Polygonize.png)

## Extract values of rasters to points

You can extract raster values at the location of points, defined by a shapefile. To do so, use the tool "Add raster values to poitns", available in the menu "Processing > Toolbox". Either search by name or find in the the toolbox structure: SAGA > Vector / Raster.

![ExtractValuesToPoints](imgs/ExtractValuesPoints.png)

If the "Add raster values to points" does not work properly, an built-in QGIS alternative is the "Sample Raster Values".

![qgis-sample-values](imgs/qgis-sample-values.png)
