# Using Sentinel-2 to Map the Yearly Mining Extent in Central Appalachia
This work was performed by Lucy Meyer as part of her work at SkyTruth.

## Background
This reposiory contains four scripts that are numbered by the order they were executed, followed by an underscore and the title of the script. The function of these tools are to evaluate the accuracy of mining classification using Google Earth Enginge and Sentinel-2 imagery. In addition, the basis of this work was to determine whether high-resolution imagery yields more accurate results than lower resolution imagery. 

This study continues the research performed by a collaborative group from SkyTruth, Appalachian Voices, and Duke University. In July 2018, the group published a paper in the Open Access journal PLOS ONE called, “ Mapping the yearly extent of surface coal mining in Central Appalachia using Landsat and Google Earth Engine.” The original paper provided the first comprehensive map of annual surface coal mining extent in Central Appalachia from 1985 through 2015 using Google Earth Engine and Landsat imagery (Pericak et al., 2018 ). My research furthers he original, not only by updating the region’s yearly mining extent from 2016 to 2018 using Sentinel-2 imagery, but by executing an adapted F-mask cloud masking algorithm (developed by Frantz et al., 2018).

## Summary of Scripts in Repository
For this study, Sentinel-2 imagery was acquired and processed in Google Earth Engine’s application programming interface. In total, four scripts were composed:
- Script 1, SENTINEL-2 CLOUD-FREE MULTI-INDEX GREEN PIXEL COMPOSITES, creates an annual cloud-free, multi-index greenest pixel composite using
- Script 2, ANNUAL THRESHOLD COMPOSITES, generates annual threshold composites
- Script 3, CREATING TRAINING SITES FOR ACCURACY ASSESSMENT, produces imagery used for the testing sites
- script 4, ANNUAL MINING AREA, generates an accuracy assessment, and vectors and statistics on active mining. 
Scripts 2-4 were updated using source code from Pericak et al (2018).

## In-depth Description of Script Functions
### 1 - SENTINEL-2 CLOUD-FREE MULTI-INDEX GREEN PIXEL COMPOSITES
Cleans Sentinel 2 imagery of data abnormalities and clouds using the Fmask algorithm, added eight spectral vegetation indices, and exported the final cloud-free, multi-indice annual composites as raster images. Additional pre-cleaning removed outliers pixels then applied Sentinel-2’s Fmask algorithm to mask clouds using the methodology provided by Frantz et al (2018). This involved creating a normalized difference variance ratio denoted as the Cloud Displacement Index (CDI). 

Additionally, the following indices are added to each annual composite: normalized difference vegetation index (NDVI), normalized difference moisture index (NDMI), normalized difference water index (NDWI), enhanced vegetation index (EVI), soil adjusted vegetation index (SAVI), modified soil adjusted vegetation index (MSAVI), normalized burn ratio (NBR), and normalized burn ratio 2 (NBR2). Maximum NDVI is later used to derive annual greenest-pixel composite (GPC). The other indices, while not used in this study, are added to be used by other researchers. Lastly, the cloud-masked, multi-index Sentinel-2 image composites should be exported as Google Earth Engine assets to be used to generate GPC thresholds in Script 2.

### 2 - ANNUAL THRESHOLD COMPOSITES
The second process examines each county ( n=74 ) in the study area individually, collects all the NDVI values within that county's borders per year, masks out urban/water and mine permit areas, and sets the threshold at the mean value of the 0th - 1st percentiles of the remaining NDVI values. Then, by mosaicking the county-scale thresholds into one image, the script creates an annual image showing each county with its unique threshold. These yearly, county-scale threshold images are then used in the mine analysis to compare the NDVI threshold with the NDVI values presented in the greenest pixel composites.

### 3 - CREATING TRAINING SITES FOR ACCURACY ASSESSMENT
This step exports training sites of Sentinel-2 imagery in the visible spectrum (B2, B3, B4) and NIR (B8). Only images that fell directly underneath given sample areas (plots), plus a 1 kilometer buffer around those sample plots are used as training iamages. The training sites used in my research are the same from the PLOS ONE paper (Pericak et al, 2018). These sites are exportable to Google Drive where they can be downloaded, classified, and used in for an accuracy assessment.

Personally, I downloaded and opened the annual training image composites in QGIS and performed a supervised classification. 
Per study area, 12,500 random points were distributed (i.e. each sample area has a unique set of points for each year) and
classified as either Mine (1) or Non-Mine (0). Mine points included any point which fell within an area containing any land where mining activity (i.e., earth removal and replacement) was likely occurring, or where mining activities had recently ceased ( i.e. landscape still resembled a mine in active development). Non-mine points were any point that intersected
any other land cover type ( e.g. forest, river, town, road). A minimum of 5,000 point were classified. 

My  study conducted a second round of sampling. In the second sampling, the first set of classified point files were exported to CSV files which were then uploaded to Google Sheets. Creating a new column called ‘rand_order’, 50 mine points and 150 non-mine points were randomly selected from the first sampling of randomly classified set of points. In total, 4,200 random points were sampled. This sampling was used script 4 on change detection and accuracy assessment.

### 4 - ANNUAL MINING AREA
The final script merges the new Sentinel-2 imagery and existing Landsat imagery, cleans the mining data, performs an accuracy assessment, and exports annual and cumulative miningfootprints. The merge of Sentinel and Landsat was not pertinent to my research, however, this step provided a comprehensive mining dataset for future researchers.

Then, the data is masked using buffered shapefiles of infrastructure and regions on the surface. Mining analysis created a band called ‘mineCandidate’, a duplicate of NDVI which is compared to the NDVI threshold. Imagery then undergoes null and noise cleaning which classified pixels based on mining likelihood from the years before and after each target year. After cleaning, an accuracy assessment is performed using the classified series of 4,200 random sample points. Additionally, mining area is summarized for each feature per year and exported to shapefiles. The final exports include: CSV of accuracy assessment results, GeoJSON of active mining year vectors with statistics, GeoJSON of yearly mining footprints, TIFF of yearly mining footprint with data, CSV of active mining footprint, and CSV of cumulative mining area.
