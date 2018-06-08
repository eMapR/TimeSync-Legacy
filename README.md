# TimeSync-Legacy

![timesync](https://github.com/eMapR/TimeSync-Legacy/blob/master/imgs/timesync-legacy.jpg)

## Requirements

* Windows PC
* Python
* GDAL (best if associated with Anaconda or OSGeo4W)
* Google Earth Engine (GEE) account
* Google Drive account associated with GEE 
* Program to edit Access .mdb files (MS Access, OpenOffice Base?)


## Getting the Package

The package can be downloaded with this URL: <https://github.com/eMapR/TimeSync-Legacy/releases/download/v1.0/TimeSync-Legacy-Package_v1.0.zip>

It contains these files and directories:

* tsl_v3.9.8.0
* tsl_db_demo.mdb
* tsl_db_template.mdb
* tsl_gee_stack_unpacker.py
* tsl_image_list_maker.py

Once the file has been downloaded unzip it to a place that makes sense for your system of organization. It should be thought of as software and all the files in the folder should stay together. It is generic and can be used for many TimeSync projects.

All version and updates can be viewed in the repository [release](https://github.com/eMapR/TimeSync-Legacy/releases) section.

## Steps

[1. Upload a Shapefile to GEE](#step1)
[<br>2. Run Source Data GEE Script](#step2)
[<br>3. Download Source Data from Google Drive](#step3)
[<br>4. Run Source Data Preparation Python Script](#step4)
[<br>5. Make Source Image Lists](#step5)
[<br>6. Create Project Database](#step6)
[<br>7. Load Project Database in TimeSync-Legacy](#step7) 


### <a id='step1'></a> 1. Upload a Shapefile to GEE

You need to create a shapefile of your study area and upload it to GEE. It will define
the area of TimeSync-Legacy data that is exported from GEE. It should be uploaded as 
an asset. Please see the [GEE guide on importing shapefiles as assests](https://developers.google.com/earth-engine/importing)
 
### <a id='step2'></a> 2. Run TimeSync-Legacy Source Data GEE Script

Add the eMapR public code repository to your GEE account. Visit this URL: <https://code.earthengine.google.com/?accept_repo=users/emaprlab/public> 

Then link to this [GEE script](https://code.earthengine.google.com/9fb3f332946d437edb411137ceae1415) to download
source data. 

You will need to fill out these variables in the *inputs* section.

```
var startYear = 1984;
var endYear = 2017;
var startDay = '06-20';
var endDay = '09-20';
var aoi = ee.FeatureCollection('users/emaprlab/timesync_legacy_demo/timesync_legacy_demo_aoi').first().geometry();
var description = 'timesync_legacy_demo'
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
|description|string|a file name prefix to describe the output (no spaces)
|gDriveFolder|string|the name of the Google Drive folder where the exported GEE data should be housed
|crs|EPSG string|the coordinate reference system you'd like to export the data in formatted as EPSG definition. In the demo we use EPSG:5070 to match USGS NLCD data product projection
|affine|numeric array|the affine transformation to be applied to the exported data. In the demo we align the outputs to USGS NLCD grid system 

After you've defined the parameters, press the *run* button in the code editor, then activate the *Task* tab and you should see two tasks ready to run. One is for exporting the image stack and the other is for exporting the buffered AOI bounding box. Press the *run* the button for each task. You'll be prompted to confirm the export requests. Confirm the requests and wait for the tasks to finish.

### <a id='step3'></a> 3. Download TimeSync-Legacy Source Data from Google Drive

Once the export task has completed, go to the Google Drive associated with your GEE account. Find the folder that was defined
as the `gDriveFolder` variable in the previous step. Download the folder or the contents. Depending on how large your AOI is, there may be only a single file or there many be serveral files that represent a series of spatial subsets of the AOI. Your downloaded files might be zipped, if so, unzip them and move the files to a 'prep' folder in a TimeSync project directory that might look like this:

```
C:\mock\timesync\project\
                 |
                 +-- images\
                 |
                 +-- prep\
```

The 'images' folder should be empty. The 'prep' folder should contain one or several *.tif* files and a single *.geojson* file.

### <a id='step4'></a> 4. Run TimeSync-Legacy Source Data Preparation Python Script

In this step you'll run the `tsl_gee_stack_unpack.py` script from the command prompt. It is recommend that you use the OSGeo4W Shell or Anaconada Prompt to run the script.

1. Open a text editor and write the command for executing `tsl_gee_stack_unpack.py`. The usage is:

```
tsl_gee_stack_unpacker.py [project prep dir] [project images dir] [filename prefix]`
```

Here is a mock call:

```
python tsl_gee_stack_unpacker.py C:\mock\timesync\project\prep C:\mock\timesync\project\images timesync_legacy_demo
```

2. Open a command prompt and cd to the *TimeSync-Legacy-Package* directory where the `tsl_gee_stack_unpacker.py` script is located. Example:

```
cd C:
\mock\TimeSync-Legacy-Package
```

3. copy and paste the command you wrote in the text editor into the command prompt and hit enter.

4. The program will print its progress and will be outputting .bsq and sidecar .hdr files in the **\images* directory. 
 
### <a id='step5'></a> 5. Make TimeSync-Legacy Source Image Lists

In this step you'll create image list files for reflectance and tasselled cap image locations. A python script will automatically generate the lists. The script is `tsl_image_list_maker.py` it is located in the *TimeSync-Legacy-Package* directory. Run the file using a command prompt

1. Open a text editor and write the command for executing `tsl_image_list_maker.py`. The usage is:

```
`tsl_image_list_maker.py` [project images dir]`
```

Here is a mock call:

```
python tsl_image_list_maker.py C:\mock\timesync\project\images
```

2. If your command prompt from the previous step is not still open, open a new one and cd to the *TimeSync-Legacy-Package* directory where the `tsl_image_list_maker.py` script is located. Example:

```
cd C:
\mock\TimeSync-Legacy-Package
```

3. copy and paste the command you wrote in the text editor into the command prompt and hit enter.

If the program executes successfully then there should be two text files in the project *images* directory:

* tc_list.txt
* refl_lst.txt

Each of them contains the list of images and some information about them. The paths to these two file lists will be used as input to cell in the Database so that TimeSync knows where images are located.

python tsl_image_list_maker.py D:\work\proj\timesync_legacy\images


### <a id='step6'></a> 6. Create TimeSync-Legacy Project Database

TimeSync-Legacy uses an MS Access database to store information about the image time series and user interpretations. There are two databases .mdb files included in the *TimeSync-Legacy-Package* directory:

* tsl_db_demo.mdb 
* tsl_db_template.mdb

The *tsl_db_demo.mdb* database contains a few points that intersect with the data that can be downloaded from the Google Earth Engine TimeSync-Legacy source data download link in Step 1. The other is a blank template that you can make a copy of and place directly inside on the project folder. Name the template file and then open it. You'll see 6 tables. You need to edit information in the *imageSettings* and *plots* tables.

Double click the *imageSettings* table to open it. Here is where you'll define the paths to the image lists so that TimeSync-Legacy knows where you get the image data. There is a column for the tasseled cap image list (tc_list.txt) and one for the reflectance image list (refl_lst.txt). Edit these cells to point to the full direcory paths of the files you created in the previous step.

Next you need to define some points you are interested in viewing the image time series, interpreting change, and/or validation. Open the *plots* table and edit the fields. The coordinates need to be in the same projection as the image data. You should be able to copy/paste/import from Excel, CSV files, etc to make this easier.

Save the changes.

### <a id='step7'></a> 7. Load Project Database in TimeSync-Legacy

Now we'll open the TimeSync-Legacy program and load the database you just setup. Navigate to the *TimeSync-Legacy-Package* directory and open the *tsl_v3.9.8.0* folder. In it you'll find and file called *TimeSync.exe* which will run TimeSync without installation. Double click to open it.

Once it is open, click *File > Open* and then navigate to and select the *.mdb* file you just setup in the previous step. It should load up the plots in left panel and default to loading the first plot's time series image chips and spectral profile.









