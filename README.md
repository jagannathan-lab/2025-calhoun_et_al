# 2025-calhoun_et_al
Shinyapp code to combine maves and make figures similar to those in the paper titled "Combining multiplexed functional data to improve variant classification" Preprint: https://arxiv.org/abs/2503.18810v1


Instructions to use the Shiny app

This app will integrate two or more MAVE datasets for a single gene. You will need to prepare the input files according to these instructions:

1: Each MAVE dataset will need its own csv file with two columns. One column must be named 'hgvs_pro', and each row of this column will need to have a unique variant. The other column should have a single score generated from the MAVE.

2: A truth set is also necessary input. This is also a two column csv file. One column needs to be a variant column called 'hgvs_pro' as this facilitates merging all of the dataframes. Again, like above, each row of this column should contain a unique variant. The other column needs to be called 'binary_clinvar_class', with benign or likely benign variants coded as 'B' and pathogenic or likely pathogenic variants coded as 'P'.

Once the input files are ready, simply navigate to our current stable release of the Shiny app at https://calhoujd12.shinyapps.io/AVE_shinyApp_Jun2025_v2p1/.

Input your files using the file upload buttons in the upper left of the screen. You will need to submit a minimum of two MAVE datasets. Only one truth set file is allowed. Please note the truth set csv file upload is a separate button.  Then, adjust any of the options for setting seed for reproducibility, k-means clustering, training/test split proportions, and number of trees for random forest as needed. Next, click the analysis button at the bottom of the side panel to generate plots and the output files. At the top of the app, you can transition to the 'Analysis Results' tab to view plots and random forest cross-validation error. Finally, switch to the 'Downloads' tab. Each output has a separate button to click which will download the corresponding file. Current available outputs include:

1. Model metrics (using pROC package coords function to determine "best" threshold) for full truth set for each individual MAVE and also the integration methods  
2. Variant level dataframe containing each variant from the truth set with annotated class and the outputs from different integration methods  
3. Variant level dataframe for all variants overlapping between input MAVEs and output from integration methods  
4. Model metrics (derived similarly as output file 1 using the threshold defined in output file 1) for training and test splits  
5. SVG of principal component plots with individual variants colored by either k-means clusters or truth set class  
6. SVG of OddsPath_Path and OddsPath_Benign bar plots for individual MAVEs and integration methods  

We have files that can be downloaded from Github that can be used as examples.

Gene1: TP53  
Number of MAVE datasets: Up to 4  
File names: Giacomelli_MAVE_A.csv, Giacomelli_MAVE_B.csv,Giacomelli_MAVE_C.csv,Funketal_TP53_dataset_noDUPscopy.csv  
Number of truth sets: 1  
Truth set name: TP53_classicOG_TS.csv

Gene2: BRCA2  
Number of MAVE datasets: 2  
MAVE dataset file names: Couch_BRCA2.csv, Sharan_BRCA2.csv  
Number of truth sets: 1  
Truth set name: BRCA2_ClinvarTS_11jun2025.csv  

How does the app work?

Our code is shared, so you can investigate for yourself! Briefly, all of the individual two column dataframes are merged. We plot the data in principal component space (PC1 & PC2) for visual representation. K-means clustering is used to identify potential clusters within the data. The data are split into training and test sets based on user input, followed by training a Naive Bayes model and a random forest model. Lastly, we calculate OddsPath based on the threshold which best separates benign and pathogenic variants for each input MAVE, principal component 1, the Naive Bayes model, or the random forest model. 

Future directions:

We hope to continue to maintain this app and add additional functionality over time! Please message us if there is something you would like added and we will try to incorporate in the next update. One thing that is definitely on our list is to add plots which compare scores for missense variants to scores for synonymous and premature truncation codon variants for each MAVE experiment uploaded to the app. Also, we would like to add c. variant naming as an alternative option to p. naming. 

