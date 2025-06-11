# An analysis of the aging murine extracellular vesicle (EV) proteome

This repository contains the input files and the analyses described in the manuscript **"An analysis of the aging murine extracellular vesicle (EV) proteome"**, which is pending submission to bioRxiv.

LC-MS Data was processed manually or with Nextflow workflows using Skyline and the results were exported using the Skyline document grid. Nanosight data was processed manually and with R.

All downstream analysis and figure generation were perfomed using R (R Markdown), Python (Jupyter Notebooks), and Inkscape.

$~$


## Repository Layout

* **bin:** Contains R scripts and Jupyter Notebooks used to generate manuscript figures

* **input:** Contains files used as input for the scripts in the bin folder

* **miscellaneous:** Contains additional, miscellaneous files.

* **skyline_reports_templates:** Contains Skyline report format files (.skyr) and template Skyline documents for DIA sample QC and PRM system suitability


$~$

## Important resources

* **Manuscript Preprint:** Pending submission

* **Panorama Public:** Pending submission

* **ProteomeXchange registration:** Pending submission

* **Dataset DOI:** Pending submission


$~$

## Scripts

Scripts are located in the **bin** folder.

* **aging_ev_proteome_figures_1_2_3_4_S1_S2_S3_S4.Rmd:** R Markdown that is used to generate the following figures:

  - Figure 1
  - Figure 2
  - Figure 3
  - Figure 4
  - Supplementary Figure S1
  - Supplementary Figure S2
  - Supplementary Figure S3
  - Supplementary Figure S4
    


$~$


## Files

Input files 25 MB or less for scripts are located in the **input** folder. All of the files uploaded to **input** will also located on the Aging Mouse EV page of PanoramaWeb. Any files larger than 25 MB are freely available on PanoramaWeb.

### aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd input files

#### ***Skyline Document:*** _________________________________.sky.zip
* **.csv:** 
* **.csv:** 
* **___________metadata.csv:** Metadata of aging mouse cohort run by DIA.

#### ***Skyline Document:*** _________________________________.sky.zip
* **.csv:** 
* **.csv:** 
* **___________metadata.csv:** Metadata of method development samples.

#### ***Nanosight particle counting:*** 
* **.csv:** 
* **.csv:** 

#### ***Additional input files:*** 
* **.csv:** 
* **.csv:** 



### ____________________.ipynb input files

#### ***Skyline Document:*** _________________________________.sky.zip
* **.csv:** 
* **.csv:** 
* **___________metadata.csv:** Metadata of aging mouse cohort run by DIA.



$~$


## Figure Generation

Details of how each figure panel was generated.

* **Figure 1:**
Panels generated using R, Excel, and Inkscape. Gradients and age marker generated in R, the remainder generated and assembled in Inkscape.
  - **1A:** Summary of mouse age range and median lifespan of males and female C57Bl/6J mice.
  - **1B:** Table summarizing cohort. Made in Microsoft Excel and added to figure in Inkscape.
  - **1C:** Summary of experimental workflow. Made in Inkscape.

* **Figure 2:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **2A:** Protein percent CV distribution in the youngest and oldest mice.
  - **2B:** Principal Component Analysis of all proteins found in the dataset, colored by mouse age (in months).
  - **2C:** Principal Component Analysis of the proteins found to correlate with age by Spearman Correlation in the dataset, colored by mouse age (in months).
  - **2D:** Clustered heatmap of the proteins that correlate with age. 
  - **2E:** Individual plots of protein Spearman correlation across the cohort.
  
* **Figure 3:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **3A:** .
  - **3B:** .

* **Figure 4:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **4A:** .
  - **4B:** .

* **Figure 5:**
Panels generated in "____________________.ipynb".
  - **5A:** .
  - **5B:** .

* **Supplementary Figure S1:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **S1A:** .
  - **S1B:** .

* **Supplementary Figure S2:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **S2A:** .
  - **S2B:** .

* **Supplementary Figure S3:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **S3A:** .
  - **S3B:** .

* **Supplementary Figure S4:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **S4A:** .
  - **S4B:** .

* **Supplementary Figure S4:**
Panels generated in "aging_ev_proteome_figures_2_3_4_S1_S2_S3.Rmd".
  - **S4A:** .
  - **S4B:** .

* **Supplementary Figure S5:**
Panels generated in "____________________.ipynb".
  - **S5A:** .
  - **S5B:** .
