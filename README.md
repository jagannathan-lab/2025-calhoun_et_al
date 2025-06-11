# 2025-calhoun_et_al
Shinyapp code to combine maves and make figures similar to those in the paper titled "Combining multiplexed functional data to improve variant classification" Preprint: https://arxiv.org/abs/2503.18810v1


Instructions to use the Shiny app

This app will integrate two or more MAVE datasets for a single gene. You will need to prepare the input files according to these instructions:

1: Each MAVE dataset will need its own csv file with two columns. One column must be named 'hgvs_pro', and each row of this column will need to have a unique variant. The other column should have a single score generated from the MAVE.

2: A truth set is also necessary input. This is also a two column csv file. One column needs to be a variant column called 'hgvs_pro' as this facilitates merging all of the dataframes. Again, like above, each row of this column should contain a unique variant. The other column needs to be called 'binary_clinvar_class', with benign or likely benign variants coded as 'B' and pathogenic or likely pathogenic variants coded as 'P'.

Once the input files are ready, simply navigate to our stable release of the Shiny app at https://calhoujd12.shinyapps.io/AVE_integrateMAVEs_RShinyapp_v1p4/.

Input your files using the file upload button in the upper left of the screen. Then, scroll down and adjust any of the options as needed. Next, click the analysis button to generate plots and the output dataframe. Finally, click the download button to download a csv file which as the metrics for each individual MAVE and also the integration methods.

We have files that can be downloaded from Github that can be used as examples.

Gene1: TP53
Number of MAVE datasets: Up to 4
File names: DN_TP53_justOG_TS.csv, Etoposide_TP53.csv,NULLnutlin_TP53_justOG_TS.csv,WTnutlin_TP53_justOG_TS.csv
Truth set name: TP53_classicOG_TS.csv

Gene2: BRCA2
Number of MAVE datasets: Up to 2
File names: coming soon
Truth set name: coming soon

How does the app work?

Our code is shared, so you can investigate for yourself! Briefly, all of the individual two column dataframes are merged. We plot the data in principal component space (PC1 & PC2) for visual representation. The data are split into training and test sets based on user input, followed by training a Naive Bayes model and a random forest model. Lastly, we calculate OddsPath based on the threshold which best separates benign and pathogenic variants for each input MAVE, principal component 1, the Naive Bayes model, or the random forest model. 

Future directions:

We hope to continue to maintain this app and add additional functionality over time! Please message us if there is something you would like added and we will try to incorporate in the next update. One thing that is definitely on our list is to add plots which compare scores for missense variants to scores for synonymous and premature truncation codon variants for each MAVE experiment uploaded to the app.

