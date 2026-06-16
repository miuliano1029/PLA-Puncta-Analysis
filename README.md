# PLA-Puncta-Analysis
This workflow analysis is used to quantify the puncta density of PLA signal within HEK cells or other cdell lines where individual cells can be selected. Further selection of cell area ROI via thresholding allows for additional restriction or quantification to a specific region, such as cell membrane, cytoplasm, or nucleus.

**1. Arrange channels + stack to .tif from .ims**
This macro allows for initial processing of raw image files.
  - Starting image folder selection (no nesting, metadata files are allowed)
  - Output folder selection
  - Initial prompt allows to:
     - Identify the number of channels within the image
      - How many channels you would like in the final image
      - The file extension type (to isolate/limit file list to that type)
      - If a projection is needed (blank does not perform a projection)
   - Next prompt allows for:
       - Identification of signal in each channel (additional channels can be customized within the macro)
       - Reorganization of channels (if starting vs ending channels differs, it will by default list in chronological order)
   - Files will then open individually and process. NOTE: depending on the stack selection, a suffix will be added to the new images (MAX, MIN, AVG)
   - Excel file listing starting/ending channel information will also be saved in output

**2. ROI Selection** 
This macro will open images and prompt for selection of an ROI (here it sould be an individual cell). This macro randomizes and blinds the image files during the selection process to reduce bias. .csv output will list the order of the randomized files, in case the macro shuts down before it finishes completely.
  - Starting image folder
  - Output folder for ROIs
  - Experiment Info Prompt
     - Experiment Title: To name/identify saved files
     - Pixel scale: to assign the correct scale
  - Channel Info Prompt
     - ROI Type: polygon is default, though freehand is particularly useful with a tablet or drawing pad
     - Number of channels within the starting images
     - Number of channels for final image (this is for a merged image. All channels will be extracted and saved as single channel files!)
  - Assigning Channel Colors: this allows for organization/identification of the single channel images saved
    - Channel color: this reorders the starting channels for your merged file as RGBC (2 channel file will select and order the channels labeled Red and Green, respectively) Make sure to select the appropriate color for each channel (though this can also be done manually after, using the single channel images)
  - Visualize in composite view: this allows you to set/limit which channels you are able to see when selecting an ROI
- Macro will then randomize and blind all image files for processing
- Selecting ROIS:
  - Macro will open image file and prompt if you have a new ROI in frame
  - Once selected, "OK" will add to the ROI manager and save single channel and merge files of that ROI
  - Boolean will continue to prompt for new ROIs. "No" will save all ROIs, exit file, and continue on
- Output/results will include:
  - Excel file with channel info
  - Folders containing single channel and merged image files of the ROIs

**3. HEK PLA Thresholding**
  This macro allows you to open the merged images of individual cells for initial thresholding of the secondary ROI (transfection marker, cell compartment, etc) and PLA signal (signal of interest). ROI thresholding will be unique to each image, and done manually by user for accurate isolation/selection. Output will include cleared images of the secondary ROI channel and PLA. PLA thresholding will be compiled to determine an average or baseline thresholding value tobe used for puncta analysis.
  - Starting image folder
  - Output folder for images, ROIs and results
  - Experiment Info Prompt
     - Pixel scale: to assign the correct scale
     - Number of channels: how many channels are in the starting image
  - Channel Assignment
     - Channel for Thresholding: which channel will be used to generate an ROI based on a set threshold.
     - Channel to Measure: channel that will be examined for puncta analysis within the ROI selection
- Macro will open a merged image and split channels. It will then select the channel for thresholding and prompt user to set and record the appropriate threshold (one that accurately identifies the selection). Upon confirmation, macfro generates a selection, clears the image outside of it, and saves within a nested folder in output.
- Macro then selects the channel to measure, clears the image outside the thresholding ROI, saves the image in a nested folder.
- User is then prompted to record a threshold for the measurement channel, one that accurately detects the signal if interest (PLA) without excess background.
- Output/results will include:
  - Excel file with channel info
  - Folders containing single channel image files of Thresholding and Measurement Channels, both cleared based on the Thresholding ROI
  - A folder containing the individual thresholding ROIs for each image, annotated with the thresholding value ("Txx")
  - .csv file of recorded thresholding values for both channels
  - .csv file of results, which include the ROI area based on the pixel scale

**4. HEK PLA Puncta Counts**
 This macro takes selected single channel cleared ROI images and performs maxima detection and particle analysis using the recorded thresholding value. NOTE: This is to quantify puncta, not examine signal intensity
  - Starting image folder (single channel cleared images of the Measurement channel from macro 3)
  - Output folder for ROIs
  - User will need to have a baseline threshold value based on the results of macro 3 for exclusion of nonspecific/negative signal. Typically, the average of condition(s) with expected positive values will work, though it is also helpful to check negative controls for non-specific signal
  - Set threshold prompt
     - Threshold value: chosen baeline threshold to identify signal
     - Noise tolerance/prominence: this value is the minimum value needed to differentiate to maxima from eachother (if it does not meet this criteria, they are grouped together). A good value is typically 5-10% of the average thresholding value for your control condition (e.g. 50-100 for 1000)
     - Min size: lower limit to remove any small, possibly nonspecific particles from analysis
  -  Macro will then open an image file, apply the set thresholding value, run find maxima, analyze particles with the set min size and prominence, and then generate masks, ROI's and counts of the puncta.
- Results:
  - Nested folders of Thresholded masks and accompanying ROI.zip files used for quantification
  - Results.csv (Thresholding-Prominence-MinSize will be noted within the file name) with compiled puncta counts from each image. **Counts will need to be standardized to the ROI area associated with each image, as compiled in the .csv from macro 3, as puncta/um2 or whichever unit is preferred**
