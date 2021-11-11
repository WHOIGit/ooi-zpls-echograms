# OOI Bioacoustic Sonar Processing and Echogram Generation

**Authors**
James Kuo, jkuo@whoi.edu; Stephanie Petillo, spetillo@whoi.edu; and Christopher Wingard, chris.wingard@oregonstate.edu  

## Description

The Ocean Observatories Initiative (OOI) collects ocean sonar, or bioacoustic sonar data, from most of the sites that
comprise the different Coastal and Global arrays. The sensor type used depends on whether the site is cabled or 
uncabled. The [ASL Environmental Sciences Acoustic Zooplankton Fish Profiler (AZFP)](https://aslenv.com/azfp.html) is 
used at the uncabled Coastal and Global (CG) sites, while the [Kongsberg Maritime Simrad EK60 Echo Sounder](https://www.kongsberg.com/maritime/products/mapping-systems/fishery-research/es_scientific/simrad-ek60/)
is deployed at two of the cabled sites. The table below provides a full list of the arrays, sites and sensor types where 
the bioacoustic sonar sensors (a total of 17) are deployed.

| Array | Site | Sensor Model | Depth | Description |
| :---: | :---: | :---: | :---: | :--- |
| Coastal Endurance | [CE01ISSM](https://oceanobservatories.org/site/ce01issm/) | AZFP | 25 m | Oregon Inshore Surface Mooring |
| Coastal Endurance | [CE02SHBP](https://oceanobservatories.org/site/ce02shbp/) | EK60 | 80 m | Oregon Shelf Cabled Benthic Experiment Package  |
| Coastal Endurance | [CE04OSPS](https://oceanobservatories.org/site/ce04osps/) | EK60 | 200 m | Oregon Offshore Cabled Shallow Profiler Mooring |
| Coastal Endurance | [CE06ISSM](https://oceanobservatories.org/site/ce06issm/) | AZFP | 29 m | Washington Inshore Surface Mooring |
| Coastal Endurance | [CE07SHSM](https://oceanobservatories.org/site/ce07shsm/) | AZFP | 87 m | Washington Shelf Surface Mooring |
| Coastal Endurance | [CE09OSSM](https://oceanobservatories.org/site/ce09ossm/) | AZFP | 542 m | Washington Offshore Surface Mooring |
| Coastal Pioneer | [CP03ISSM](https://oceanobservatories.org/site/cp03issm/) | AZFP | 95 m | Inshore Surface Mooring |
| Coastal Pioneer | [CP01CNSM](https://oceanobservatories.org/site/cp01cnsm/) | AZFP | 135 m | Central Surface Mooring |
| Coastal Pioneer | [CP04OSSM](https://oceanobservatories.org/site/cp04ossm/) | AZFP | 450 m | Offshore Surface Mooring |
| Global Argentine Basin | [GA02HYPM](https://oceanobservatories.org/site/ga02hypm/) | AZFP | 150 m | Apex Profiler Mooring, both upward and downward looking sensors |
| Global Irminger Sea | [GI02HYPM](https://oceanobservatories.org/site/gi02hypm/) | AZFP | 150 m | Apex Profiler Mooring, both upward and downward looking sensors |
| Global Station Papa | [GP02HYPM](https://oceanobservatories.org/site/gp02hypm/) | AZFP | 150 m | Apex Profiler Mooring, both upward and downward looking sensors |
| Global Southern Ocean | [GS02HYPM](https://oceanobservatories.org/site/gs02hypm/) | AZFP | 150 m | Apex Profiler Mooring, both upward and downward looking sensors |

[Echopype](https://echopype.readthedocs.io/en/latest/), the open-source ocean sonar data processing module, is utilized 
to process the OOI bioacoustic sonar data from the cabled and uncabled sensors to generate weekly echograms that 
the user community can use to quickly assess the quality and applicability of the data to their research needs. 
Additionally, preliminary processed data (saved to NetCDF files) is generated to facilitate access to the data in a
standard format for others to use for further processing and analysis.

This code was originally developed as part of the [ooicgsn-data-tools](https://github.com/oceanobservatories/ooicgsn-data-tools) 
toolbox, but was extracted out to make it standalone and installable as a package for internal OOI use in processing 
the realtime cabled data from [CE02SHBP](https://oceanobservatories.org/site/ce02shbp/) and 
[CE04OSPS](https://oceanobservatories.org/site/ce04osps/).

## Installation and Setup

Future releases will make this code available for installation via conda-forge. For now users will need to download (or 
use git to clone) the code to their local computer, setup a virtual environment for the processing, and then install a 
development copy of the code into that environment. The installation and setup steps below, assume the user has already 
installed either [anaconda](https://www.anaconda.com/products/individual) or [miniconda](https://docs.conda.io/en/latest/miniconda.html) 
to use in executing the python code.

```shell
# download the ooi-zpls-echograms code
mkdir -p /home/ooiuser/code
cd /home/ooiuser/code
git clone https://github.com/oceanobservatories/ooi-zpls-echograms.git
cd ooi-zpls-echograms

# configure the OOI python environment
conda env create -f environment.yml
conda activate echopype

# install the package as a local development package
conda develop .
```

Note, the use of `/home/ooiuser/code` above, and `/home/ooiuser/data` below, are solely for the purposes of these
examples. It is expected that users will have their own directory structure. Adapt and change as needed.

## Usage

The code is intended to be used largely from the command line in a shell. Though users can import `ooi_zpls_echograms` 
as a module and run the individual functions directly, the intent is to provide a means of batch processing the 
bioacoustic sonar data for internal OOI use. Scripts used to run the processing are available in the [utilities](utilities)
directory. The basic sequence of commands is:

```shell
cd /home/ooiuser/code/ooi-zpls-echograms/ooi_zpls_echograms

./zpls_echogram.py -s [site_code] -d [data_directory] -o [output_directory] -dr [dates] -zm [instrument_type]
```

The inputs to the function are defined below, based on whether they are required or optional.

| Required Inputs | Description |  
|:-------------------------:|:------------------------------------------------------------|
| -s, -site_code            | The OOI 8-letter site name for where the ZPLSC/G is located. |  
| -d, --data_directory      | The path to the root data directory, below which the .01A or .raw files may be found. |  
| -o, --output_directory    | The path to the root data directory where .nc file and .png plot will be saved. |  
| -dr, --date_range         | The date range to be plotted, e.g.:<br>`-dr "20200118"` will plot the day (01/18/2020),<br>`-dr "202001"` will plot the whole month (01/01/2020 to 01/31/2020),<br>`-dr "202001 202002"` will plot (01/01/2020 to 02/29/2020),<br>`-dr "20200115 20200216"` will plot (01/15/2020 to 02/16/2020) |  
| -zm, --zpls_model         | Sensor model, either an EK60 for cabled data, or an AZFP for uncabled |
| **-xf, --xml_file**       | The path to .xml file to use if the sensor model is an AZFP. **Required if the sensor model is AZFP** |  

| Optional flags | Description |  
|:-------------------------:|:------------------------------------------------------------|
| -tc, --tilt_correction    | Apply tilt correction in degree(s) |  
| -dd, --deployed_depth     | The depth where the ZPLSC/G is located at |  
| -cr, --colorbar_range     | Set colorbar range. Usage: "min max" |  
| -vr, --vertical_range     | Set the range for the y-axis. Usage: "min max" |  

### Usage Notes

* The root data directory depends on the data source. If looking at cabled data and the EK60, the path to the root 
  data directory ends with the year (e.g. `/home/ooiuser/data/CE04OSPS/PC01B/05-ZPLSCB102/2017`). For uncabled data, 
  the path to the root data directory ends with `DATA` 
  (e.g. `/home/ooiuser/data/CP01CNSM/R00012/instruments/dcl37/ZPLSC_sn55080/DATA`).
 
* The path to the `.xml` file is required if the ZPLS model is an AZFP. The `.xml` file contains instrument specific
  settings and calibration coefficients needed to process the data.

* In the case of the global Apex Profiler (subsurface) moorings, the OOI 8-letter site name is appended with either an 
  "_Upper" or "_Lower" to indicate which of the sensors is being used (there are two on the 150 m sphere, one looking 
  up towards the surface, and the other looking down).

* The preset colorbar ranges are the same for every array. Feel free to change it via the optional colorbar_range input.
  
## Raw and Processed Data Folders and Files

### Raw Data
The raw ZPLS data can be found on the [OOI Raw Data server](https://rawdata.oceanobservatories.org/files/), with the 
directory structure dependent on the ZPLS instrument type (either an EK60 or AZFP).

For the AZFP, the data are organized by the instrument in a folder called `DATA`, below which the files are organized
in folders named according the year and month (e.g. `201610`) the data was collected. The files are named according to 
the year, month, day and hour the data were recorded (e.g. `16101401.01A`). The higher level directory structure above
`DATA` is created by OOI technicians after the mooring is recovered. If the ZPLS data from the 
[Global Argentine Basin, upward looking sensor for Deployment 3](https://rawdata.oceanobservatories.org/files/GA02HYPM/R00003/instruments/ZPLSG_sn55067/) 
were downloaded to a local folder in the users `/home/ooiuser/data` directory named `GA`, the folders would look like:

```
/home/ooiuser/data/GA/ZPLSG_sn55067/DATA/201610
    GA/ZPLSG_sn55067/DATA/201610/16101401.01A
    GA/ZPLSG_sn55067/DATA/201610/16101402.01A
    GA/ZPLSG_sn55067/DATA/201610/16101403.01A
    GA/ZPLSG_sn55067/DATA/201610/*.01A
    GA/ZPLSG_sn55067/DATA/201610/16101417.XML
/home/ooiuser/data/GA/ZPLSG_sn55067/DATA/201611
    GA/ZPLSG_sn55067/DATA/201611/16111401.01A
    GA/ZPLSG_sn55067/DATA/201611/16111402.01A
    GA/ZPLSG_sn55067/DATA/201611/16111403.01A
    GA/ZPLSG_sn55067/DATA/201611/*.01A
```

For the EK60, the data are organized by OOI technicians in folders based on the year, month and day (e.g. `2017/01/01`)
the data was collected. Files are named by the instrument according to the date and time the file was created (e.g. 
`OOI-D20170831-T113333.raw`). If the ZPLS data from the [Coastal Endurance, Oregon Offshore Cabled Shallow Profiler 
Mooring, 200 m Platform](https://rawdata.oceanobservatories.org/files/CE04OSPS/PC01B/05-ZPLSCB102) from 2017 were 
downloaded to local folder in the user's `/home/ooiuser/data` directory named `CE04`, the folders would look like:

```
/home/ooiuser/data/CE04/PC01B/05-ZPLSCB102/2017/01/01/
    CE04/PC01B/05-ZPLSCB102/2017/01/01/OOI-D20170101-T000000.bot
    CE04/PC01B/05-ZPLSCB102/2017/01/01/OOI-D20170101-T000000.idx
    CE04/PC01B/05-ZPLSCB102/2017/01/01/OOI-D20170101-T000000.raw
/home/ooiuser/data/CE04/PC01B/05-ZPLSCB102/2017/01/02
    CE04/PC01B/05-ZPLSCB102/2017/01/02/OOI-D20170102-T000000.bot
    CE04/PC01B/05-ZPLSCB102/2017/01/02/OOI-D20170102-T000000.idx
    CE04/PC01B/05-ZPLSCB102/2017/01/02/OOI-D20170102-T000000.raw
```

For the AZFP example above, the root data directory would be `/home/ooiuser/data/GA/ZPLSG_sn55067/DATA`. For the EK60 
example, the root data directory would be `/home/ooiuser/data/CE04/PC01B/05-ZPLSCB102/2017`.

### Processed Data
Processed data are created in a folder of the users choice. We recommend placing the data at the same level as the root 
data directory, but in a separate folder called `processed`. The data in the `processed` folder are organized into 
sub-folders named according to the date range entered for processing. For example, using the AZFP data from above, if 
the date range was 2017-01-01 through 2017-01-08, then the processed data would end up in a folder `20170101-20170108`. 
The full path would be `/home/ooiuser/data/GA/ZPLSG_sn55067/processed/20170101-20170108/`

There are three types of processed data created by the zpls_echogram script:

1. **Converted**: These are files converted from the raw format (either `.01A` or `.raw`) to NetCDF format (`.nc`) by 
   the echopype convert function. They are 1:1 with the source raw data file. These `.nc` files use the 
   [SONAR-NetCDF4](http://www.ices.dk/sites/pub/Publication%20Reports/Cooperative%20Research%20Report%20(CRR)/CRR341.pdf)
   formatting convention.
2. **Processed**: The echopype process function, taking the converted files as inputs, is used to calculate the volume 
   acoustic backscatter strength (Sv), and the range (distance from the sensor face) in meters. This subset of data is 
   saved in daily `.nc` files that use the site and date range to construct the file name. Continuing to use the AZFP
   example from above, data for 2017-01-01 would be saved in `GA02HYPM_Upper_Bioacoustic_Echogram_20170101-20170108_Calibrated_Sv_Full_20170107.nc`.
3. **Averaged**: Takes the processed data one step further and applies temporal averaging (15 minute median averages for
   the coastal ZPLSC and 60 minute median averages for the global ZPLSG) for use in creating the echograms. Missing data
   is filled in via linear interpolation for gaps less than 3x the averaging time window. The averaged data is saved 
   in a single `.nc` file covering the date range specified using a similar file naming convention as the processed 
   data above except the term `Full_20170101` in the file name above is replaced with `Averaged` 
   (e.g. `GA02HYPM_Upper_Bioacoustic_Echogram_20170101-20170108_Calibrated_Sv_Averaged.nc`).
   
An echogram covering the time period specified by the date range is created from the averaged data and saved along with
the three data types listed above. The files share the same naming convention as the averaged data (e.g., 
`GA02HYPM_Upper_Bioacoustic_Echogram_20170101-20170108_Calibrated_Sv_Averaged.png`).

## Processing Examples

The examples below show how to use the python script with some of the different options from above. The examples are
all built around using the AZFP data from the Global Argentine Basin upward looking sensor.

**Example 1:**
```shell
# set default data directories and the XML file 
GA_DATA="/home/ooiuser/data/GA/ZPLSG_sn55067/DATA"
GA_PROC="/home/ooiuser/data/GA/ZPLSG_sn55067/processed"
GA_XML="/home/ooiuser/data/GA/ZPLSG_sn55067/DATA/201610/16101417.XML"

# set the site, the script will look at the default mooring configuration and
# apply the tilt angle correction (15 degrees for GA02HYPM_Upper) and the 
# deployment depth (150 m for GA02HYPM_Upper). 
SITE="GA02HYPM_Upper"

# convert and process the data
conda activate echopype
cd /home/ooiuser/code/ooi-zpls-echograms/ooi_zpls_echograms
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -dr "20161115 20161116" -zm AZFP -xf $GA_XML 
```
Process data from 2016-11-15 through 2016-11-16. The `.nc` files and `.png` plot will be stored under
`/home/ooiuser/data/GA/ZPLSG_sn55067/processed/20161115-20161116/`.

**Example 2:**  
```shell
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -dr "201611" -zm AZFP -xf $GA_XML 
```
Process the data for the entire month of November, from 2016-11-01 through 2016-11-30. The `.nc` files and `.png` plot
will be stored under `/home/ooiuser/data/GA/ZPLSG_sn55067/processed/20161101-20161130/`.

**Example 3:**  
```shell
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -dr "201611" -zm AZFP  -xf $GA_XML -tc 10
```
Change the default tilt correction angle to 10 degrees via the optional `-tc` flag.

**Example 4:**  
```shell
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -dr "201611" -zm AZFP -xf $GA_XML -dd 160
```
If the instrument is deployed at 160 m, instead of the design depth of 150 m. The optional flag `-dd 160` will 
override the mooring configuration.

**Example 5:**
```shell
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -dr "201611" -zm AZFP -xf $GA_XML -cr -120 -20 -dd 160
```
Change both the deployed depth to 160 m and the default colorbar range to -120 to -20 dB via the optional `-cr` flag.

**Example 6:**  
```shell
./zpls_echogram.py -s $SITE -d $GA_DATA -o $GA_PROC -xf $GA_XML -dr "201611" -zm AZFP -vr 0 160 -dd 160
```
Change the default vertical range to use 0 to 160 m for the new deployment depth of 160 m via the optional `-vr` flag.
