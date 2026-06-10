# An analysis of the aging murine extracellular vesicle (EV) proteome

This repository contains the input files and the analyses described in the manuscript **"Circulating extracellular vesicles in plasma carry accessible molecular signatures of aging in mice"**, which is pending submission to bioRxiv.

LC-MS Data was processed manually or with Nextflow workflows using Skyline and the results were exported using the Skyline document grid. Nanosight data was processed manually and with R.

All downstream analysis and figure generation were perfomed using R (R Markdown), Python (Jupyter Notebooks), and Inkscape (When illustrations needed).

$~$


## Repository Layout

* **data_analysis:** Contains Jupyter Notebooks used to generate manuscript figures

  - **data:** Contains a copy of protein and peptide-level data generated in Nextflow and Skyline
 
  - **metadata:** Contains a copy of metadata
 
  - **results:** Contains output from Jupyter Notebooks

* **rmd_analysis:** Contains R Markdown file used to generate manuscript figures

  - **input:** Contains a copy of protein and peptide-level data generated in Nextflow and Skyline, metadata, and EV method confirmation data
 
  - **rmd_output:** Contains output from Rmd file

* **miscellaneous:** Contains additional, miscellaneous files.

   **skyline_reports_templates:** Contains Skyline report format files (.skyr) and template Skyline documents for DIA sample QC and PRM system suitability


$~$

## Important resources

* **Manuscript Preprint:** Pending submission

* **Panorama Public:** Pending submission

* **ProteomeXchange registration:** Pending submission

* **Dataset DOI:** Pending submission

