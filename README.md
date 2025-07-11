# 2025-calhoun_et_al
Shinyapp code to combine maves and make figures similar to those in the paper titled "Combining multiplexed functional data to improve variant classification" Preprint: https://arxiv.org/abs/2503.18810v1

## Before use, please read this important note:  
This tool is only suitable for genes with a known loss-of-function mechanism. It takes a flexible approach to model training. By default, it uses synonymous (SYN) and premature termination codon (PTC) variants as a training set. If a particular set of MAVEss does not have a suitable number of SYN and PTC variants for model training, we provide the option to supplement with a proportion of the missense truth set variants (typically benign or likely benign and pathogenic or likely pathogenic variants curated from a suitable database such as Clinvar). Bear in mind that it is highly recommended to reserve >= 20% of missense variants for model testing to check for possible overfitting.

## Instructions to use the Shiny app

This app will integrate two or more MAVE datasets for a single gene. You will need to prepare the input files according to these instructions:

1: Each MAVE dataset will need its own csv file with two columns. One column must be named 'hgvs_pro', and each row of this column will need to have a unique variant. The other column should have a single score generated from the MAVE.

2: A truth set is also necessary input. This is a three column csv file. One column needs to be a variant column called 'hgvs_pro' as this facilitates merging all of the dataframes. Again, like above, each row of this column should contain a unique variant. The second column needs to be called 'binary_clinvar_class', with benign or likely benign variants coded as 'B' and pathogenic or likely pathogenic variants coded as 'P'. The third column must be 'SYNorPTC' where synonymous variants are coded 'SYN' and premature truncation codon variants are coded 'PTC'. It is necessary that synonymous variants are also coded as 'B' in the 'binary_clinvar_class' column.  IMPORTANT: It will likely be necessary to exclude some PTC variants depending on their location within the gene and the context of the assay. For cDNA assays, the C-terminal end of some proteins is dispensable for function and stability, so it may be worthwhile to exclude any variants in this region of the gene (pro tip: scrolling through heatmaps provided on MAVEdb can help sort this out). For endogenous assays, the same principal applies, with the added potential complication of nonsense-mediated decay. Any PTC variants lying in the last exon or the final 50 bp of the penultimate exon should be excluded. Finally, it may be necessary to exclude a small number of synonymous variants if they are predicted to impact splicing. Variant effect predictors tailored to splicing such as SpliceAI may be useful here.   

Please note there are example files you can view to make sure you follow the correct formatting (see below for more detail). Once the input files are ready, simply navigate to our current stable release of the Shiny app at https://jagannathan-lab.shinyapps.io/AVE_shinyApp_Jun2025/.

Input your files using the file upload buttons in the upper left of the screen. You will need to submit a minimum of two MAVE datasets. Only one truth set file is allowed. Please note the truth set csv file upload is a separate button.  Then, adjust any of the options for setting seed for reproducibility, number of trees for random forest, or threshold for classifying variants using the Naive bayes or random forest classifier integrated score. You can also adjust what proportion of variants from the SYN/PTC truth set are used in model training. Similarly, you can also adjust what proportion of missense benign or likely bening and pathogenic or likely pathogenic variants are 'spiked' in with the SYN/PTC for model training. The remaining proportion of missense variants will be used for testing. IMPORTANT: By default, the app uses ROC analysis to select the 'best' threshold based on the Youden statistic. Optionally, we provide the option to adjust thresholds for each of the integration methods which have a numeric value (PC1, Naive Bayes proba, and random forest proba). We provide ridgeline plots (essentially histograms with each group offset) to inform your decision on selecting thresholds.  


Next, click the analysis button at the bottom of the side panel to generate plots and the output files. At the top of the app, you can transition to the 'Analysis Results' tab to view plots and random forest cross-validation error. Finally, switch to the 'Downloads' tab. Each output has a separate button to click which will download the corresponding file. Current available outputs include:

1. Model metrics for full truth set for each individual MAVE and also the integration methods  
2. Variant level dataframe containing each variant from the test split with annotated class and the outputs from different integration methods  
3. Variant level dataframe containing each variant from the truth set with annotated class and the outputs from different integration methods  
4. Variant level dataframe for all variants overlapping between input MAVEs and output from integration methods  
5. Model metrics for training and test splits for supervised learning methods (Naive Bayes and random forest)  
6. SVG of principal component plots with individual variants colored by either k-means clusters or truth set class  
7. SVG of OddsPath_Abnormal and OddsPath_Normal bar plots for individual MAVEs and integration methods  
8. SVG of Ridgeline plots showing the distribution of scores for Naive Bayes (NBpred) or random forest (RFpred) for the clinical truth set  

We have files that can be downloaded from Github that can be used as examples.

## Gene1: TP53  
Number of MAVE datasets: Up to 5  
File names: Giacomelli_NULLetoposide.csv, Giacomelli_NULLnutlin.csv,Giacomelli_WTnutlin.csv,FunkTP53_wSYNandPTC_NOdups.csv, Boettcher_DNreporter  
Number of truth sets: 1  
Truth set name: TP53_TS2wSYNandPTC_23jun2025.csv  
  
## Gene2: PTEN  
Number of MAVE datasets: 2  
MAVE dataset file names: PTEN_abundanceVAMPseq_wSynANDptc.csv, PTEN_fitness_wSynANDptc.csv  
Number of truth sets: 1  
Truth set name: PTEN_TS_wSynANDptc.csv  

# How does the app work?  

Our code is shared, so you can investigate for yourself! Briefly, all of the individual dataframes are merged and scaled. We plot the data in principal component space (PC1 & PC2) for visual representation. A portion of synonymous (SYN) and premature termination codon (PTC) variants are used to train the Naive bayes and random forest classifiers, with flexible spike-in of a portion of the missense truth set to increase training set size and expand the dynamic range of the training set. For the integration methods, by default ROC analysis determines assay thresholds. Optionally, users may instead opt to manually set assay thresholds to evaluate the classification of variants in the clinical missense truth set. The app outputs ridgeline plots of score distributions to demonstrate thresholds relative to the distribution of integrated scores. Lastly, we calculate OddsPath Normal and OddsPath Abnormal based on the set threshold for integration methods. OddsPath scores are also calculated for the threshold which best separates benign and pathogenic variants for each input individual MAVE and principal component 1. These are all conveniently plotted together to visually compare how the integrated score compares to the individual input MAVEs.  

# Future directions:  

We hope to continue to maintain this app and add additional functionality over time! Please message us if there is something you would like added and we will try to incorporate in the next update. One thing that is we are considering is either within this app or as a separate app, a functionality based on increasing the number of variants receiving a score. Also, we would like to add support for 'c.' variant naming as an alternative option to 'p.' naming.  

# References

Three of the TP53 example MAVE datasets come from this paper:  
Giacomelli AO, Yang X, Lintner RE, McFarland JM, Duby M, Kim J, Howard TP, Takeda DY, Ly SH, Kim E, Gannon HS, Hurhula B, Sharpe T, Goodale A, Fritchman B, Steelman S, Vazquez F, Tsherniak A, Aguirre AJ, Doench JG, Piccioni F, Roberts CWM, Meyerson M, Getz G, Johannessen CM, Root DE, Hahn WC. Mutational processes shape the landscape of TP53 mutations in human cancer. Nat Genet. 2018 Oct;50(10):1381-1387. doi: 10.1038/s41588-018-0204-y. Epub 2018 Sep 17. PMID: 30224644; PMCID: PMC6168352.

The other twp TP53 example MAVE datasets are from these two papers:  
Funk JS, Klimovich M, Drangenstein D, Pielhoop O, Hunold P, Borowek A, Noeparast M, Pavlakis E, Neumann M, Balourdas DI, Kochhan K, Merle N, Bullwinkel I, Wanzel M, Elmshäuser S, Teply-Szymanski J, Nist A, Procida T, Bartkuhn M, Humpert K, Mernberger M, Savai R, Soussi T, Joerger AC, Stiewe T. Deep CRISPR mutagenesis characterizes the functional diversity of TP53 mutations. Nat Genet. 2025 Jan;57(1):140-153. doi: 10.1038/s41588-024-02039-4. Epub 2025 Jan 7. PMID: 39774325; PMCID: PMC11735402. 

Boettcher S, Miller PG, Sharma R, McConkey M, Leventhal M, Krivtsov AV, Giacomelli AO, Wong W, Kim J, Chao S, Kurppa KJ, Yang X, Milenkowic K, Piccioni F, Root DE, Rücker FG, Flamand Y, Neuberg D, Lindsley RC, Jänne PA, Hahn WC, Jacks T, Döhner H, Armstrong SA, Ebert BL. A dominant-negative effect drives selection of TP53 missense mutations in myeloid malignancies. Science. 2019 Aug 9;365(6453):599-604. doi: 10.1126/science.aax3649. PMID: 31395785; PMCID: PMC7327437.    

PTEN example MAVE datasets come from the following papers:  

Matreyek KA, Starita LM, Stephany JJ, Martin B, Chiasson MA, Gray VE, Kircher M, Khechaduri A, Dines JN, Hause RJ, Bhatia S, Evans WE, Relling MV, Yang W, Shendure J, Fowler DM. Multiplex assessment of protein variant abundance by massively parallel sequencing. Nat Genet. 2018 Jun;50(6):874-882. doi: 10.1038/s41588-018-0122-z. Epub 2018 May 21. PMID: 29785012; PMCID: PMC5980760.

Mighell TL, Evans-Dutson S, O'Roak BJ. A Saturation Mutagenesis Approach to Understanding PTEN Lipid Phosphatase Activity and Genotype-Phenotype Relationships. Am J Hum Genet. 2018 May 3;102(5):943-955. doi: 10.1016/j.ajhg.2018.03.018. Epub 2018 Apr 26. PMID: 29706350; PMCID: PMC5986715.

Mighell TL, Thacker S, Fombonne E, Eng C, O'Roak BJ. An Integrated Deep-Mutational-Scanning Approach Provides Clinical Insights on PTEN Genotype-Phenotype Relationships. Am J Hum Genet. 2020 Jun 4;106(6):818-829. doi: 10.1016/j.ajhg.2020.04.014. Epub 2020 May 21. PMID: 32442409; PMCID: PMC7273526.

