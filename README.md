# How to download daily data to your personal computer from NASA PODAAC
## 1)  Register for an account with _Earthdata Login_ here: 
> https://urs.earthdata.nasa.gov/oauth/authorize?client_id=HrBaq4rVeFgp2aOo6PoUxA&redirect_uri=https%3A%2F%2Farchive.podaac.earthdata.nasa.gov%2Fredirect&response_type=code&state=%2Fs3credentials

<b> make sure you save your username and password you will need them later! </b>

## 2)  Select the dataset you want to use from NASA PODAAC. This example will use the GHRSST dataset


![GHRSST PO.DAAC](https://raw.githubusercontent.com/behorwitz/podaac_data/902b490f1be6a2408c6fbeb783a7704e8e8efa59/ghrsstpodaac.png)

Note the _short name_ of the dataset. For this example it would be _"MUR-JPL-L4-GLOB-v4.1"_ 

## 3) Download the PODAAC data subscriber package using terminal. On a mac: 

<pre> pip install podaac-data-subscriber  </pre>

## 4) Create a .netrc file using a text editor in terminal (IE using nano)
<pre> nano ~/.netrc </pre> 

## 5) in the .netrc file replace with your earthdata username and password
<pre> machine urs.earthdata.nasa.gov
login your_earthdata_username
password your_earthdata_password </pre>

## 6) make sure the file is only readable by you: 
`chmod 600 ~/.netrc`

## 7) Test if this worked. If you are prompted for a username or password something went wrong
`curl -n https://urs.earthdata.nasa.gov`

## 8) Navigate to where you want the data to be downloaded to on your computer (ie using `cd ____`)

## 9) Download the files! (you can only download one year of data at a time) This is where you need the _short name_ of the dataset from earlier. 
<pre>
podaac-data-downloader -c shortnameofdataset -d ./data \

  --start-date YYYY-MM-DDT00:00:00Z \

 --end-date YYYY-MM-DDT00:00:00Z \

  -b="-179,-90,180,90" --verbose1
  </pre>

  <b> with the example dataset and the year 2002 it would be: </b>
  <pre>

  podaac-data-downloader -c MW_IR_OI-REMSS-L4-GLOB-v5.0 -d ./data \
  --start-date 2002-06-01T00:00:00Z \
  --end-date 2002-06-08T00:00:00Z \
  -b="-179,-90,180,90" --verbose

  </pre>

  <b> _repeat step 9 for as many years as you need changing the start and end date_ </b>

  # Subsetting the files downloaded to a specific region: 

## 1) Make sure you have NCO installed 
using conda: `conda install -c conda-forge nco`
## 2) Navigate to the folder where you downloaded your data in terminal

using `cd....`

## 2) Create a bash file in the folder where your data is located using a text editor (IE nano on a mac)
using nano: `nano yourbashfilename.sh`

## 3) Using your desired bounding box (with start lat/lons and end lat/lons) and subset filenames update this text in the bash file: 
<pre>
#!/bin/bash
# Define the latitude and longitude bounds
lat_min=value
lat_max=value
lon_min=value
lon_max=value

# Loop through the NetCDF files and perform subsetting
for f in *.nc
do
  echo "Processing $f file..."
  # Use value-based subsetting
  ncks -d latitude,$lat_min,$lat_max \
       -d longitude,$lon_min,$lon_max \
       "$f" "updatedfilename_$f"
done
</pre>

<b> make sure you check what format the lat/lons are in the datafiles! Many of the lat/lons on PODAAC are 0-360 not -180 to 180 format. </b>

For a region in the Carribean using lat/lons with 0-360 this is what the bash file text would be: 

<pre>
#!/bin/bash
# Define the latitude and longitude bounds
lat_min=13.955392
lat_max=16.888660
lon_min=290.105
lon_max=295.356

# Loop through the NetCDF files and perform subsetting
for f in *.nc
do
  echo "Processing $f file..."
  # Use value-based subsetting
  ncks -d latitude,$lat_min,$lat_max \
       -d longitude,$lon_min,$lon_max \
       "$f" "carib_$f"
done

</pre>

## 4) run the bash file! 

a) make the file executable: `chmod +x yourbashfilename.sh`
b) run the script: `./yourbashfilename.sh`

## You should have datasets cut to a specific region with the years downloaded from PODAAC! 

![High Five Rotation GIF](https://github.com/behorwitz/podaac_data/raw/main/high-five-rotation.gif)
