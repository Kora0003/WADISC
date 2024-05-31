# WADISC: Annual Impervious Surface Data for Ghana, Togo, Benin, and Nigeria from 2001 – 2020




## User Guide Version 1.1
## April 31, 2023





## Authors
### Andrews Korah; Michael C. Wimberly
### Department of Geography and Environmental Sustainability, University of Oklahoma, Norman, OK, 73019, USA

### Citation:
Korah, A., & Wimberly, M. (2024). WADISC: Annual Impervious Surface Data for Ghana, Togo, Benin, and Nigeria from 2001 – 2020 [Dataset]. figshare. https://doi.org/10.6084/m9.figshare.24716481.v3




#### 1. Introduction

Most African countries are rapidly increasing their impervious areas, including paved roads, concrete, and building roofs. Because global impervious datasets are not necessarily suitable for regional scale urban expansion analysis, and local data are often inconsistent across cities and countries, we leveraged the Google Earth Engine cloud computing platform and the Landsat archive to generate consistent regional data for the whole of Ghana, Togo, Benin, and Nigeria from 2001 – 2020. These data can assist modelers, researchers, and interested stakeholders in conducting large-scale regional assessments of urban expansion and its impacts on people and the environment and making future projections for multiple countries in West Africa. This guide shows the major steps used to generate the West Africa Dataset of Impervious Surface Change (WADISC).

#### 2. Components of WADISC in Google Earth Engine

We used four scripts to generate annual impervious surface cover and developed area classification in WADISC.
1. WADISC_1_Composites
2. WADISC_2_ImperCover_RF
3. WADISC_3_ImperCover_LT
4. WADISC_4_DevClass

These scripts are available in WADISC GEE scripts archive: https://code.earthengine.google.com/?accept_repo=users/koraha/WADISC.

All the GEE assets are configured for read-only access, and users cannot store new assets in users/koraha/WADISC. However, users can modify the scripts to store new assets in their GEE cloud storage.

##### 2.1 Script #1: WADISC_1_Composites. 
The first GEE script generates spectral indices, creates annual composites of indices, and exports the composites to GEE Assets or Google Drive. Users can modify several parameters of the script depending on their preferences, including study year to generate composite, composite time window, study countries, and image export. 

###### 2.1.1. Study Year: 
The variable yearC allows users to set the year to generate annual composite. The default yearC is 2001, meaning running the script will generate the annual spectral composite for 2001.

###### 2.1.2. Annual composite Months:
The variables yearS  is the beginning month for yearC and yearE is the last month for yearC. In this script, we used annual composites, meaning we used all Landsat images from January 01 to December 31 for yearC to generate an image collection of indices. 

###### 2.1.3. Study Area:
The variable studycountries defines the area extent to generate composites. The default setting is Ghana, and one can set it to create composites for any area extent. In this script, users can adjust the studycountries using Ghana, TB (Togo and Benin), and Nigeria. 

###### 2.1.4. Export Image Composite:
When the script is run, spectral indices will automatically be calculated for yearC and the defined studycountries. After running this script, the Task Manager (in the right-hand side panel) will be highlighted in yellow, prompting you to click the RUN button to export the composite image. If you click on run, the image collection of indices for yearC is automatically saved in GEE assets unless you set it for export to Google Drive. The exported composites from this script will be used as inputs in the second script.

##### 2.2. Script#2: WADISC_2_ImperCover_RF

The second script performs Random Forest regression using annual spectral composites and generates percent annual impervious cover estimates. Users can adjust sections of the script before generating Random Forest (RF) impervious cover estimates, ncluding study year, annual spectral composites as inputs, observation data, and output exports. 

###### 2.2.1. Study Year:
The yearC variable allows users to choose which year to generate RF impervious cover predictions. An input of 2001 in yearC will create RF impervious cover for 2001. In this script, you can only generate RF predictions for a particular year if you have already generated the annual spectral composite (script#1) and stored it in GEE asset. 

###### 2.2.2. Annual Spectral Composite as Input for RF:
This script uses image collection of annual spectral composites produced from script #1. Users need to specify the asset location in variable annual_composites. Each year in annual_composites consist of a collection of spectral indices (bands), which are used as predictor variables for estimating impervious cover with RF. 

###### 2.2.3. Training and Validation Data for RF:
The variables train_pts and val_pts are feature collections that contain manually interpreted impervious cover observations from very high-resolution images in Google Earth. The features in train_pts are used to fit the RF impervious cover model, and the features in val_pts are used to test the RF impervious cover accuracy.

###### 2.2.4. Study Area:
The variable studycountries defines the area extent. The default setting is Nigeria, and it can also be adjusted to generate RF impervious cover for Ghana or TB (Togo and Benin). 

###### 2.2.5. Export RF Impervious Cover:
When the script is run, the RF regression fits the training data to the annual spectral composites and generates percent impervious cover across the study countries for yearC. If you run the task for RF percent impervious cover image for each year, it is exported to a specified image collection in GEE asset (Table 1). The generated impervious cover products from this script will be input for the third script. 

###### 2.2.6. Training and Validation Data
Run the training and validation exports in the task panel, the RF percent impervious cover estimate for each point are automatically exported to the user-specified Google Drive in a comma separated values (CSV) format. These exports are used to access the RF model performance in estimating impervious cover. 

###### Table 1: Export locations of RF percent impervious covers from 2001 – 2020.

###### RF Image Collection Location for each Country                               
Ghana              projects/ee-koraha/assets/Ghana_ImperCov_RF_1y

Togo and Benin     projects/ee-koraha/assets/Benin_Togo_ImperCov_RF_1y

Nigeria            projects/ee-koraha/assets/Nigeria_NLF_GFS_ImperCov_RF_1y	

Nigeria            projects/ee-koraha/assets/Nigeria_WSS_ImperCov_RF_1y	    

##### 2.3 Script#3: WADISC_3_ImperCover_LT

The third script applies the LandTrendr algorithm (Kennedy et al., 2010) to fit a temporally segmented piecewise regression model for each pixel in the percent impervious cover time series products from RF. Thus, LandTrendr (LT) smoothed the RF percent impervious cover time series to minimize year-to-year noise and fill data gaps. The final outputs after applying LT represent the impervious cover data for WADISC. The LT impervious cover data will be used as input in the fourth and final script.

###### 2.3.1 Study Year:
The variable yearC operates as in scripts one and two. The LT algorithm is applied on all the impervious cover series from RF. Before generating the final impervious cover for each year, users need to specify the initial year (year_init) and the final year (year_final). The yearC can be any year within the time series. 

###### 2.3.2 LandTrendr Input:
The image collections of percent impervious cover from RF are used as input for LT temporal segmentation. These data are stored in GEE assets (Table 1).

###### 2.3.3 LandTrendr Parameters:
Users can adjust several parameters in run_params variable to generate better results for the study area. More detailed descriptions of the LT parameters are in Kennedy et al. 2010 and the LT guide (https://emapr.github.io/LT-GEE/index.html).

###### 2.3.4: Validation Data:
The variable val_pts is a feature collection that was overlaid on the percent impervious cover from LT, and predicted values were extracted and used to test the overall performance of processing with LT.

###### 2.3.5 Study Area:
The variable studycountries defines the area extent. The default setting is Ghana, and it can be adjusted to generate composites for different area extents. In this script, one can specificy the studycountries as Ghana, TB (Togo and Benin), or Nigeria. 

###### 2.3.6 Export LT impervious cover:
When the script is run, LT applies a linear regression model to each pixel in the input data within the studycountries and generates a smoothed impervious cover. Users can export the impervious cover data by setting yearC to any year from 2001-2020. Users can view the generated impervious cover across multiple years in Map Viewer. Clicking on any location (pixel) populates the times series for the raw RF impervious cover and the processed LT impervious cover. Also, in the task panel, the processed LT impervious cover can be exported as GEE assets (Table 2) or can be exported to Google Drive. The export to GEE assets will be used as input in script four. Additionally, users can export the validation data layer for accuracy analysis. 

###### Table 2: Export locations of LT percent impervious covers from 2001 – 2020.

###### LT Image Collection GEE Assets	for each Country
Ghana               projects/ee-koraha/assets/Ghana_ImperCov_LT_1y	

Togo and Benin      projects/ee-koraha/assets/BT_ImperCov_LT_1y	     

Nigeria             users/owitt83/Nigeria_Model1_ImperCov_LT_1y	 

Nigeria             users/owitt83/Nigeria_Model2_ImperCov_LT_1y	     

##### 2.4 Script #4: WADISC_4_DevClass

The fourth and final script involves change detection. We classified the impervious cover data into developed and undeveloped and applied a rule-based filter to remove unrealistic changes. 

###### 2.4.1 Study Year:
The variable yearC operates similarly to previous scripts. 

###### 2.4.2 Input Data:
The image collections of percent impervious cover from LT are used as input for developed area classification. These data are stored in GEE assets (Table 2).

###### 2.4.3 Classification:
We classified the processed LT impervious cover into developed and undeveloped using a 20% in threshold_ic. Pixel values greater than 20% are classified as developed, and pixel vales less than or equal 20% is classified as undeveloped. Also, we applied the rule that pixel remained developed even if the pixel values falls below the threshold in subsequent years. 

###### 2.4.4. Study Area:
The variable studycountries defines the area extent. The default setting is Nigeria, and it can be adjusted to generate processed LT impervious covers for Ghana or TB (Togo and Benin). 

###### 2.4.5. Export Classified Images:
When the script is run, it will generate the developed area classification, and in the task panel the image can be exported as a GEE asset or exported to Google Drive (default settings). The script also generates summaries of total developed area, and annual changes for each study area extent, major ecological zones, and cities within each country (using Africapolis city boundaries). Thus, users can generate summaries of the classified data for any extent and export as CSV.

#### Reference

Kennedy, R. E., Yang, Z., & Cohen, W. B. (2010). Detecting trends in forest disturbance and recovery using yearly Landsat time series: 1. LandTrendr — Temporal segmentation algorithms. Remote Sensing of Environment, 114(12), 2897‐2910
