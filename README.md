# 2025-calhoun_et_al
Shinyapp code to combine maves and make figures similar to those in the paper titled "Combining multiplexed functional data to improve variant classification" Preprint: https://arxiv.org/abs/2503.18810v1

## Before use, please read this important note:  
This tool is only suitable for genes with a known loss-of-function mechanism. As such it uses synonymous and premature termination codon variants as a training set. This preserves the full clinical truth set as a test dataset. However, this implementation does come with the trade-off that a suitable number of synonymous and premature termination codon variants need to present in each individual MAVE dataset.

## Instructions to use the Shiny app

This app will integrate two or more MAVE datasets for a single gene. You will need to prepare the input files according to these instructions:

1: Each MAVE dataset will need its own csv file with two columns. One column must be named 'hgvs_pro', and each row of this column will need to have a unique variant. The other column should have a single score generated from the MAVE.

2: A truth set is also necessary input. This is a three column csv file. One column needs to be a variant column called 'hgvs_pro' as this facilitates merging all of the dataframes. Again, like above, each row of this column should contain a unique variant. The second column needs to be called 'binary_clinvar_class', with benign or likely benign variants coded as 'B' and pathogenic or likely pathogenic variants coded as 'P'. The third column must be 'SYNorPTC' where synonymous variants are coded 'SYN' and premature truncation codon variants are coded 'PTC'. It is necessary that synonymous variants are also coded as 'B' in the 'binary_clinvar_class' column.  IMPORTANT: It will likely be necessary to exclude some PTC variants depending on their location within the gene and the context of the assay. For cDNA assays, the C-terminal end of some proteins is dispensable for function and stability, so it may be worthwhile to exclude any variants in this region of the gene (pro tip: scrolling through heatmaps provided on MAVEdb can help sort this out). For endogenous assays, the same principal applies, with the added potential complication of nonsense-mediated decay. Any PTC variants lying in the last exon or the final 50 bp of the penultimate exon should be excluded. Finally, it may be necessary to exclude a small number of synonymous variants if they are predicted to impact splicing. Variant effect predictors tailored to splicing such as SpliceAI may be useful here.   

Please note there are example files you can view to make sure you follow the correct formatting (see below for more detail). Once the input files are ready, simply navigate to our current stable release of the Shiny app at https://calhoujd12.shinyapps.io/AVE_shinyApp_Jun2025_v2p3/.

Input your files using the file upload buttons in the upper left of the screen. You will need to submit a minimum of two MAVE datasets. Only one truth set file is allowed. Please note the truth set csv file upload is a separate button.  Then, adjust any of the options for setting seed for reproducibility, number of trees for random forest, or threshold for classifying variants using the Naive bayes or random forest classifier integrated score. IMPORTANT: Please make sure to try different thresholds, and use the ridgeline plots (and if needbe the output csv files) to inform your decision. We anticipate in many scenarios, a value between 0.1 and 0.9 will be optimal. However, depending on the distribution of the integrated score, values less than 0.1 or greater than 0.9 may need to be explored.  


Next, click the analysis button at the bottom of the side panel to generate plots and the output files. At the top of the app, you can transition to the 'Analysis Results' tab to view plots and random forest cross-validation error. Finally, switch to the 'Downloads' tab. Each output has a separate button to click which will download the corresponding file. Current available outputs include:

1. Model metrics (using pROC package coords function to determine "best" threshold) for full truth set for each individual MAVE and also the integration methods  
2. Variant level dataframe containing each variant from the truth set with annotated class and the outputs from different integration methods  
3. Variant level dataframe for all variants overlapping between input MAVEs and output from integration methods  
4. SVG of principal component plots with individual variants colored by either k-means clusters or truth set class  
5. SVG of OddsPath_Abnormal and OddsPath_Normal bar plots for individual MAVEs and integration methods  
6. SVG of Ridgeline plots showing the distribution of scores for Naive Bayes (NBpred) or random forest (RFpred) for the clinical truth set  

We have files that can be downloaded from Github that can be used as examples.

## Gene1: TP53  
Number of MAVE datasets: Up to 4  
File names: Giacomelli_NULLetoposide.csv, Giacomelli_NULLnutlin.csv,Giacomelli_WTnutlin.csv,FunkTP53_wSYNandPTC_NOdups.csv  
Number of truth sets: 1  
Truth set name: TP53_TS2wSYNandPTC_23jun2025.csv  
  
## Gene2: PTEN  
Number of MAVE datasets: 2  
MAVE dataset file names: PTEN_abundanceVAMPseq_wSynANDptc.csv, PTEN_fitness_wSynANDptc.csv  
Number of truth sets: 1  
Truth set name: PTEN_TS_wSynANDptc.csv  

# How does the app work?  

Our code is shared, so you can investigate for yourself! Briefly, all of the individual dataframes are merged. We plot the data in principal component space (PC1 & PC2) for visual representation. The synonymous (SYN) and premature termination codon (PTC) are used to train the Naive bayes and random forest classifiers. The manually set threshold is used to evaluate the classification of variants in the clinical truth set. Lastly, we calculate OddsPath Normal and OddsPath Abnormal based on the threshold which best separates benign and pathogenic variants for each input MAVE, principal component 1, the Naive Bayes model, or the random forest model.  

# Future directions:  

We hope to continue to maintain this app and add additional functionality over time! Please message us if there is something you would like added and we will try to incorporate in the next update. One thing that is we are considering is either within this app or as a separate app, a functionality based on increasing the number of variants receiving a score. Also, we would like to add support for 'c.' variant naming as an alternative option to 'p.' naming.  

# References

Three of the TP53 example MAVE datasets come from this paper:  
Giacomelli AO, Yang X, Lintner RE, McFarland JM, Duby M, Kim J, Howard TP, Takeda DY, Ly SH, Kim E, Gannon HS, Hurhula B, Sharpe T, Goodale A, Fritchman B, Steelman S, Vazquez F, Tsherniak A, Aguirre AJ, Doench JG, Piccioni F, Roberts CWM, Meyerson M, Getz G, Johannessen CM, Root DE, Hahn WC. Mutational processes shape the landscape of TP53 mutations in human cancer. Nat Genet. 2018 Oct;50(10):1381-1387. doi: 10.1038/s41588-018-0204-y. Epub 2018 Sep 17. PMID: 30224644; PMCID: PMC6168352.

The other twp TP53 example MAVE datasets are from these two papers:  
Funk JS, Klimovich M, Drangenstein D, Pielhoop O, Hunold P, Borowek A, Noeparast M, Pavlakis E, Neumann M, Balourdas DI, Kochhan K, Merle N, Bullwinkel I, Wanzel M, Elmshäuser S, Teply-Szymanski J, Nist A, Procida T, Bartkuhn M, Humpert K, Mernberger M, Savai R, Soussi T, Joerger AC, Stiewe T. Deep CRISPR mutagenesis characterizes the functional diversity of TP53 mutations. Nat Genet. 2025 Jan;57(1):140-153. doi: 10.1038/s41588-024-02039-4. Epub 2025 Jan 7. PMID: 39774325; PMCID: PMC11735402.   

One of the BRCA2 example MAVE datasets is from this paper:  
Sahu S, Galloux M, Southon E, Caylor D, Sullivan T, Arnaudi M, Zanti M, Geh J, Chari R, Michailidou K, Papaleo E, Sharan SK. Saturation genome editing-based clinical classification of BRCA2 variants. Nature. 2025 Feb;638(8050):538-545. doi: 10.1038/s41586-024-08349-1. Epub 2025 Jan 8. PMID: 39779848.

The other BRCA2 example MAVE datasets is from this paper:  
Huang H, Hu C, Na J, Hart SN, Gnanaolivu RD, Abozaid M, Rao T, Tecleab YA; CARRIERS Consortium; Pesaran T, Lyra PCM, Karam R, Yadav S, Nathanson KL, Domchek SM, de la Hoya M, Robson M, Mehine M, Bandlamudi C, Mandelker D, Monteiro ANA, Iversen ES, Boddicker N, Chen W, Richardson ME, Couch FJ. Functional evaluation and clinical classification of BRCA2 variants. Nature. 2025 Feb;638(8050):528-537. doi: 10.1038/s41586-024-08388-8. Epub 2025 Jan 8. PMID: 39779857; PMCID: PMC11821525.

