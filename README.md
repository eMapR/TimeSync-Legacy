# TimeSync-Legacy


[1. Upload a Shapefile to GEE](step1)
[<br>2. Run TimeSync-Legacy Source Data GEE Script](step2)
[<br>3. Download TimeSync-Legacy Source Data from Google Drive](step3)
[<br>4. Run TimeSync-Legacy Source Data Preparation Python Script](step4)
[<br>5. Create TimeSync-Legacy Project Database](step5)

## <a id='step1'></a> 1. Upload a Shapefile to GEE

You need to create a shapefile of your study area and upload it to GEE. It will define
the area of TimeSync-Legacy data that is exported from GEE. It should be uploaded as 
an asset. Please see the [GEE guide on importing shapefiles as assests](https://developers.google.com/earth-engine/importing)
 
## <a id='step2'></a> 2. Run TimeSync-Legacy Source Data GEE Script

Link to this [GEE script](https://code.earthengine.google.com/164689e462b2dc748ec9d0fd3d9be81a) to download
source data. 

You will need to fill out these variables in the *inputs* section.

```
var startYear = 1984;
var endYear = 2017;
var startDay = '06-20';
var endDay = '09-20';
var aoi = ee.FeatureCollection('users/emaprlab/timesync_legacy_demo/timesync_legacy_demo_aoi').first().geometry();
var gDriveFolder = 'timesync_legacy_demo';
var crs = 'EPSG:5070';
var affine = [30.0, 0, 15.0, 0, -30.0, 15.0];
```

|Variable|Type|Definition
|-|-|-
|startYear|integer|the minimum year for the range over which you want to generate annual TimeSync source data
|endYear|integer|the maximum year for the range over which you want to generate annual TimeSync source data
|startDay|string|the minimum calendar day for the range over which to generate annual image composites
|endDay|string|the maximum calendar day for the range over which to generate annual image composites
|aoi|ee.Geometry|the area of interest that will define the area of TimeSync source data to be exported
|gDriveFolder|string|the name of the Google Drive folder where the exported GEE data should be housed
|crs|EPSG string|the coordinate reference system you'd like to export the data in formatted as EPSG definition. In the demo we use EPSG:5070 to match USGS NLCD data product projection
|affine|numeric array|the affine transformation to be applied to the exported data. In the demo we align the outputs to USGS NLCD grid system 

After you've defined the parameters, smash the *run* button and you'll be prompted to confirm your data export request.
Confirm the request and wait for the task to finish.

## <a id='step3'></a> 3. Download TimeSync-Legacy Source Data from Google Drive

Once the export task has completed, go to the Google Drive associated with your GEE account. Find the folder that was defined
as the `gDriveFolder` variable in the previous step. Download the folder or the contents. Depending on how large your AOI is, there may be only a single file or there many be serveral files that represent a series of spatial subsets of the AOI.

## <a id='step4'></a> 4. Run TimeSync-Legacy Source Data Preparation Python Script





## <a id='step5'></a> 5. Create TimeSync-Legacy Project Database