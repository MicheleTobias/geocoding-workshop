
# This workshop is in progress.
# I plan to have this workshop completed by 15 Aug 2019.
# Please check back then.
.
.
.
# Geocoding Workshop

# The Big Concepts

## What is Geocoding?

Geocoding is the process of translating postal addresses into geographic coordinates.

## The Road Network
We do the process of geocoding in our brains every time we go to a new address.  Let's imaging your friend moved to a new house and you're on your way to their house warming party.  Their address is 150 Main Street.  If you're familiar with the town and you're not using a turn-by-turn direction program, you'd probably go to Main Street and start looking at addresses.  Once you found the 100 block (the addresses are something like 110, 125, etc.), you would probably guess that house number 150 is about in the middle of the block and on the side of the street with the even numbered houses.

Why does this work? Because most cities have an organized system of addresses.  Postal codes are typically made up of a string of increasingly broad information - Building Number, Street, City, State, Postal Code, Country - for example the City of Davis' city hall address is "23 Russell Boulevard, Davis, CA 95616, USA" and the city hall in Sydney, Australia, is "483 George St, Sydney NSW 2000, Australia".

Buildings are arranged in numerical order along a street and each block typically starts a new number at the front of the building number.  So all the 100 addresses are on one block, the 200s are on the next block, etc.  Further, usually the odd numbers are on one side of the street and the even numbers are on the other.

# Software
There are many tools to help you geocode addresses.  A typical geocoding tool consists of software that compares your addresses to the road network or known address locations.  You may need to provide the road network data, depending on the software you choose.


There are some important things to consider when choosing a tool:

1. **Budget** How much will it cost to use the software.  Some tools are free.  Others are free for a certain number of addresses and then charge if you exceed that number. Others have a subscription, yearly, or one-time fee.
1. **Data Required** Does the software require you to provide a road network or does the software come with its own network? If you need to provide one, is the data available for the part of the world you are working in?
1. **Geographic Restrictions** Different tools specialize in different regions of the world.  For example, Geocodio's online tool is excellent for the US, but doesn't have the data necessary for geocoding for international addresses.
1. **Platform** What platform does the tool work on? It could be implemented in R, Python, or SQL. Maybe it runs inside a graphical tool like QGIS. Maybe you have to write a script to work with an API. Consider what skills you have or want to learn and if the tool is a good fit for your situation.
1. **Local Processing or Third Party**  Geocoding can be run on your local computer.  To do this, you typically need geocoding software and to download road network data with address ranges included.  Third party tools don't require you to download road network data because they send your addresses to a cloud service for processing. Addresses by their nature identify the location of people so we should be careful of sending non-public addresses to a third party.  Online geocoders are all third party products.  **Personally Identifiable Information (PII) or HIPAA Data should never be sent to third party geocoder for processing unless that tool is certified to be HIPAA-compliant.**

## Some Software Options

There are many, many options for geocoder tools.  I'll list the ones I'm familiar with here, but this is not an exhaustive list.

### Local Geocoders

| Software | Region | Cost | Tutorial | Notes |
|---|---|---|---|---|
|[MMQGIS Plugin for QGIS - Geocode from Street Layer tool](http://michaelminn.com/linux/mmqgis/) | Anywhere | Free & Open Source | Scroll down | You need to download the road network data to use with the tool |
|PostGIS Tiger Geocoder | US | Free & Open Source | [Building a PostGIS Geocoder](https://experimentalcraft.wordpress.com/2017/11/01/how-to-make-a-postgis-tiger-geocoder-in-less-than-5-days/) | Uses US Census data |
|PostGIS with OpenAddresses | Dependent on OpenAdresses data coverage | Free & Open Source | [Loading OpenAddresses Data into PostGIS](https://experimentalcraft.wordpress.com/2018/10/19/loading-open-addresses-data-into-postgis/) | |
|[Pelias Geocoder](https://github.com/pelias/pelias) Installed Locally |

### Third Party Geocoders

| Software | Region | Cost | Tutorial | Notes |
|---|---|---|---|---|
| [Geocodio](https://www.geocod.io) | US | 2,500 free lookups per day; $0.50 per 1,000 after that | |  |
| [Geocodio+HIPAA](https://www.geocod.io/healthcare/) | US | Monthly or Yearly Subscription | | HIPAA-compliant |
| OpenStreetMap Nominatim API | | | | Use the API with your favorite programming tools |
| Nominatim Locator Filter Plugin for QGIS | | | | Uses the OpenStreetMap Nominatim API to process data |
| Pelias Geocoding Plugin for QGIS | | | | Uses the Pelias geocoder for processing data |
| [R package tmaptools's geocode_OSM()](https://cran.r-project.org/web/packages/tmaptools/index.html) | Worldwide | Free & Open Source | | Sends data to the OpenStreetMap Nominatim API |
|[Pelias Geocoder](https://github.com/pelias/pelias)|
| ArcGIS Geocoder | | Software + Extensions + Credits | | Using any ESRI web service costs credits. Geocoding is particularly credit-intensive so UC Davis affiliates should consult with esrisupport@ucdavis.edu before using this service |

# Hands-On Geocoding

We will be working with QGIS 3 (this was written and tested on 3.8, but 3.4 or 3.6 should also work) and using the MMQGIS Plugin to geocode from a street file that we download - so we'll be geocoding locally.

## Get the Software

Download & install [QGIS 3](https://qgis.org) - the download page will have install files for Mac, Windows, & Linux.

## Organizing Your Address Data

The biggest mistake I see when people try to geocode data is trying to use messy data.  Geocoders are very particular about how an address is formatted and what information you include.  Most geocoders require each address to be separated into columns for each piece of information (check your chosen tool's documentation to find out how it specifically needs you to format your data) - so separate columns for Street Address (number + street name), City, State, Postal Code, Country.  

You should **not include** things like 

1. Apartment number, suite, or half addresses.  The geocoder usually doesn't know what to do with these and it will fail.  Remember that the goal is usually to estimate a location along a block face, not to match an exact address (unless you are using OpenAddresses).  "150 Main Street" is probably in the middle of the 100 block.  "150 Main Street suite A" will fail because the geocoder can't find a street called "Main Street suite A".
2. Non-standard street abbreviations. St., Blvd., Dr. are all fine.  Shortening "Paseo" to PSO will fail. (True story - I've seen this one before.)

***The MMQGIS Geocode from Street Layer tool requires that the street address be separated into two columns*** - one for the street number and another for the street name.

### You Try:

This list of restaurants and addresses in downtown Davis came directly from their listing in Google Maps and is pretty typical of geocoding datasets.  You can get your own copy in the [data folder](/data). How would you clean this dataset & prepare it for use with the MMQGIS plugin?

|Restaurant|Street|City|State|Zip|
|---|---|---|---|---|
|Ali Baba|220 3rd St|Davis|CA|95616
|Burgers & Brew|403 3rd St|Davis|CA|95616|
|Davis Noodle City|129 E St #1D|Davis|CA|95616|
|Davis' Pinata Mexican Grill|305 1st St|Davis|CA|95616|
|Three Ladies Cafe|130 G St Suite A|Davis|CA|95616|

## Download the Road Network Data

Because we want to use a local geocoder, we need to download a road network dataset to match our addresses to.  If we were using an online cloud service, the service would provide the road network for us.

Not just any road shapefile will work for a geocoder.  Specifically, it needs to have attributes for the address ranges (from 100 to 199, for example).

A good source of current and recent road data for the US is the US Census TIGER files, specifically the Edges files.  We will download the edges file for California's Yolo County.  The Census' FTP site is organized with FIPS codes indicating the state and county with a numeric code.  We can look up the FIPS codes at the [County FIPS Codes page of the USDA's NRCS](https://www.nrcs.usda.gov/wps/portal/nrcs/detail/ca/home/?cid=nrcs143_013697) among other places and see the **Yolo County's FIPS code is 06113** - 06 at the front tells us it's California and 113 refers to Yolo County.

Now we'll navigate through the Census' FTP site to download the Yolo County Edges file (be patient, it can be slow):

1. Go to the [US Census' FTP Site](ftp://ftp2.census.gov/geo/tiger)
1. Click on the *TIGER2018/* folder - note that you can select the folder for the year you want to work with.
1. Click on the *EDGES/* folder
1. Click on the *tl_2018_06113_edges.zip* file and download it to a folder on your computer (that you have permissions for and can find again).  Note that the *06113* part of the file name tells us we're downloading the edges for Yolo county.
1. Navigate in the file browser for your computer to where you saved the file and unzip it.

## Set Up Your QGIS Project
Let's set up our QGIS project so we have everything we need.  Let's open a new project:

1. Start QGIS
2. In the upper left corner, click the *Project menu*, then select *New*.  Or click the *New Project* icon just below the *Project mneu* that looks like a blank page with the corner turned down.

You now should have a new project open with no data loaded.

### Load the Road Network Data
First, let's load our road network data:

1. Open the *Data Source Manager* dialog either by clicking the button in the tool bar or picking it from the *Layer menu*.
1. On the navigation bar on the left side of the windo, pick *Vector* because our road network is lines.
1. Click on the "..." button in the *Source* section of the dialog and navigate though your file structure to where you saved your *tl_2018_06113_edges.shp* file. Slect the file and click *Open*.
1. Finally, in the *Data Source Manager*, click *Add* to add the file to the project.  You may need to move the *Data Source Manager* window out of the way to see it.  If it looks good, click the *Close* button.

Right click on the *tl_2018_06113_edges* layer in the *Layers panel* (also known as the *Table of Contents* in some GIS programs) and select *Open Attribute Table*.  Make note of the kinds of data stored here.  Specifically, notice the columns with "to" and "from" in the names like *LFROMADD* and *lTOADD*.  These are the fields that contain the address ranges for the blocks that the geocoder needs to be able to estimate where and address is along the roads.  Explore this data further and try to get a feel for what kind of data is stored in the attribute table.


### Get the MMQGIS plugin

We'll be using the MMQGIS Plugin to do our geocoding.  It's free to use, but we need to enable it first.  Here's how:

1. In the *Plugins menu*, and select *Manage & Install Plugins*
1. In the search box at the top, type "MMQGIS".  When you find it, select it from the list.  Details about the plugin will appear on the right side of the screen.
1. Click the *Install Plugin* button.
1. Close the *Manage & Install Plugins* dialog.
1. You should now see an *MMQGIS menu* at the top of your QGIS window.

## Geocode with the MMQGIS Plugin

Now we're ready to geocode.

1. From the *MMQGIS menu*, select *Geocode*, then *Geocode from Street Layer* to start the *Geocode from Street Layer* tool.  Notice that there are other options here.  The *Geocode CSV with Web Service* tool is a good option for data you don't need to protect.  You could use this workshop data with this tool and not be concerned since this data is all public businesses, but in this we'll use the private option.
1. For the *Input CSV file (UTF-8)* option, click on the "..." button to navigate to your *restaurant_addresses.csv* file.  Note that the UTF-8 designation here refers to the file encoding.  This indicates which character set the tool is expecting.  UTF-8 doesn't have special characters.
1. The tool will guess at which columns to assign to each option for the Number, Street Name, and Zip columns and will likely get it right if you named the columns names similar to their content, but you should review these just in case.
1. For the *Street Layer*, choose the *tl_2018_06113_edges* layer from the drop-down menu.
1. Fill in the columns to match the data requested.  Because the data we are using is from the census and the tool expects this kind of data, it will generally get it right, but you should review the choices.  You can leave the *From X Attribute*, *From Y Attribute*, *To X Attribute*, & *To Y Attribute* with the default selection.
1. Finally, choose a name and location for your *Output File Name* and *Not Found Output List*.  Give them names that remind you of what these files contain.
1. Click *Apply*.
1. The window will indicate how many of the total number of addresses it was able to geocode. If it missed any, those addresses will be recorded in the *Not Found Output List*.
1. Close the window when it is done.


### Review the Results

Look at the distribution of points.  Does it look right?  Did the points show up where you expected?

Open your *Not Found Output List* file to see if any of the points didn't geocode.  If there are any, what happened?  Can you edit the addresses to work better. For example, if there was an appartment number in the address, you could try removing it.

Fix any addresses that need editing and run the addresses again.  If this was a big dataset, I would suggest only re-running the addresses that didn't geocode to reduce the amount of run time.


### Why did some points geocode to the wrong city?
The default code for version 2019.6.11 of the MMQGIS plugin doesn't check the zip code.  The code makes a list of streets and blocks that match, but keeps the first one on the list because the section where it checks the zip codes is commented out (a special character at the front of the lines of code tells the program to skip those lines).  We can alter the code because this is an open source program.  We can edit the MMQGIS Plugin to handle zip code checking:

1. On the *Settings menu* in QGIS, select *User Profiles*, then select *Open Active Profile Folder*.  This should open your file browser and take you to the folder where your profile data is stored.
1. In the file browser, open the *python* folder, then the *plugins folder*, and finally the *mmqgis folder*.
1. Open the *mmqgis_library.py* file in a text editor.
1. Carefully remove the comments code ( # ) from lines 1835-1838 - be careful to make sure you don't alter the amount of space before the text, just delete the hash symbols.
1. Save the file.
1. Restart qgis.
1. Re-try the plugin.
1. If the plugin no longer works, open the *Plugin Manger* and uninstall, then reinstall the plugin to restore it to the default code.
