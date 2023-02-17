---
layout: default
title: 16S explainer
---

# Overview

16S rRNA amplicon sequence allows for genus or species level analysis of bacteria and archaea in a mixed environmental sample. To produce amplicons, PCR is used to amplify a variable target region of the 16S rRNA gene. Then, sequences can be assigned taxonomy and analyzed across sample metadata (date, location, testing condition, etc.) to understand changes in the microbial community over time. A good video explainer of 16S amplicon analysis can be found [here](https://www.youtube.com/watch?v=U75SSJRzu3g).

I typically use the PCR primers 515F-926R, [detailed in this paper](https://sfamjournals.onlinelibrary.wiley.com/doi/full/10.1111/1462-2920.13023), which cover the V4 and V5 regions of the gene. After completing PCR, I send the amplicons to a sequencing center and request paired end 2x300 base pair (bp) sequencing. This length provides enough overlap to account for both the sequence of interest (~300 bp) plus the length of the primers and sequencing adapters on each end (~50 bp each).


# Sample processing

Environmental samples undergo two main processing steps before sequencing: DNA extraction and 16S rRNA amplification using PCR. These steps are detailed in the protocols on the Wells Lab Sharepoint site, also linked below:

[DNA extraction using the FastDNA SPIN Kit for Soil](https://nuwildcat.sharepoint.com/:w:/r/sites/MCC-WellsResearchGroup/Wells%20Group%20Methods%20and%20Organization/PCR,%20qPCR,%20nucleic%20acid%20archiving%20and%20extraction/DNA%20extraction%20protocol.docx?d=wa6fb93f31ca74c15a1487ae6d8c5d7d0&csf=1&web=1&e=kqpYPr)

[16S rRNA amplification using PCR](https://nuwildcat.sharepoint.com/:w:/r/sites/MCC-WellsResearchGroup/Wells%20Group%20Methods%20and%20Organization/Gene%20sequencing%20and%20cloning,%20sample%20preparation/Methods%20and%20Templates/16S%20rRNA%20gene%20sequencing%20sample%20prep%20protocol.docx?d=wcff64265cc764ee893f14a6422857a72&csf=1&web=1&e=o20UyB)

[Checking amplification with gel electrophoresis](https://nuwildcat.sharepoint.com/:w:/r/sites/MCC-WellsResearchGroup/Wells%20Group%20Methods%20and%20Organization/PCR,%20qPCR,%20nucleic%20acid%20archiving%20and%20extraction/PCR%20agarose%20gel%20electrophoresis%20protocol.docx?d=wdc775448e4374ac896e1152e7ba61a8b&csf=1&web=1&e=anMRdX)

After amplified DNA has been produced, we send samples for sequencing to a sequencing center. I’ve sent samples to the Rush University Genomics and Microbiome Core, which returns data in about 1 month. The steps for preparing and sending samples to Rush are [detailed in this folder](https://nuwildcat.sharepoint.com/:f:/r/sites/MCC-WellsResearchGroup/Wells%20Group%20Methods%20and%20Organization/Gene%20sequencing%20and%20cloning,%20sample%20preparation/Sequencing%20center%20info/Rush%20University?csf=1&web=1&e=AU9HRq).


# Data processing

I typically analyze 16S data in two parts - (1) sequence cleaning/taxonomic assignment and (2) community analysis and data visualization. Many analysis pipelines can integrate both functions, including some of the software I will suggest below, but I prefer performing them separately. I perform sequence cleaning and taxonomic assignment on [Quest, Northwestern’s high performance computing cluster](https://services.northwestern.edu/TDClient/30/Portal/Requests/ServiceDet?ID=88). We interact with the computing cluster through command line and bash scripting, which are detailed very nicely in the [intro to Quest tutorial video](https://www.youtube.com/watch?v=rIFbHt_2g4s) provided through IT. 

I save most of my scripts and analysis files on [Github](https://github.com/), a popular platform for software development and data science. The biggest advantages of using Github over a basic local save on your hardware are (a) the files uploaded to Github are backed up online and (b) Github repositories (where you store your files) are easy to share and are collaborative across Github users. Github has a [desktop client](https://desktop.github.com/) that you can use to sync files between your computer and the online storage repository. 

I also use a program to move files back and forth between my computer and Quest storage. I use [WinSCP](https://winscp.net/eng/index.php) (only available on Windows). [Filezilla](https://filezilla-project.org/) or [Cyberduck](https://cyberduck.io/) are good alternatives.

## Sequence cleaning and taxonomic assignment

I use the program [QIIME2](https://qiime2.org/) to process 16S rRNA sequences. QIIME2 is a nice way to access multiple processing programs under one umbrella and syntax, reducing the total number of installations you need to worry about. QIIME2 has relatively small learning curve and is accessible on Quest through an installed version called a module. I have examples of my QIIME pipeline available on the [16S example workflow page](https://mckfarm.github.io/library/docs/16S_workflow.html).

The major steps are listed below:

- Convert raw sequences to a QIIME artifact, which is required to process the sequences in QIIME
- Trim the PCR primers, which were sequenced along with our sequence of interest
- Trim reads based on the sequence quality over the length of the base pairs. Typically, sequencing quality is reduced over the length of a sequence.
- Denoise (remove sequencing errors) and summarize sequences into amplicon sequence variants (ASVs), which are individual unique sequences
- Assign taxonomy using a database that pairs sequences to taxonomic classification. There is a specialty database for wastewater systems called [MIDAS](https://midasfieldguide.org/guide) that I use for this, otherwise a more general bacterial database is [SILVA](https://www.arb-silva.de/). MIDAS builds upon SILVA but is curated specifically for wastewater microbiomes.
- OPTIONAL: Rarefy samples to the same read depth (number of reads). Rarefaction is sort of a personal preference - rarefaction is the process of randomly sampling to a specified depth. You normalize all samples to the same number of reads, which makes more directly comparable. For example, you could directly compare read counts between rarefied samples. However, you are losing biologically relevant data in the process.

More detail can be found on the 16S analysis workflow page.

## Data analysis

I mostly use R for data analysis and visualization. Python is also an option for this purpose, but I personally find R more intuitive for making visualizations. I use R and [RStudio](https://www.rstudio.com/), a visual interface to use R, locally on my computer. This means that I download QIIME outputs to my computer. I usually set this up by making a folder for QIIME outputs in my Github repository, then saving the analysis script in the same repository. RStudio has a very nice feature called R Projects that interfaces very nicely with Github - you can make an R Project in your Github folder and every script you create in that project folder with be pointing to the correct working directory. 

There are a few R packages that interface nicely with QIIME outputs. I specifically use [phyloseq](https://joey711.github.io/phyloseq/) and [qiime2R](https://github.com/jbisanz/qiime2R) for reading in and manipulating QIIME outputs. I mainly use the visualization package [ggplot2](https://ggplot2.tidyverse.org/) for making plots and figures.