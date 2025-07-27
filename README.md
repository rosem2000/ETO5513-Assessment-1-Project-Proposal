# ETO5513-Assessment-1-Project-Proposal

This repository contains the necessary files to produce the ETO5513 Assessment 1 Project Proposal report analysing the Global Burden of Disease 2021 data set.

## Description

This project explores the impact of behavioural, environmental, and occupational risk factors on self-harm burdens in 20–24 year-olds, using data from the [Global Burden of Disease Study 2021](https://vizhub.healthdata.org/gbd-results/). It is presented as a report using R and Bookdown packages.

## Data

The dataset is a subset of the GBD study, containing:
- **Years Lived with Disability (YLDs)**
- **Years of Life Lost (YLLs)**
- **Deaths**

Each observation corresponds to a specific **risk factor** and **year**, with values for the above measures.

## Dependencies

Please ensure your device complies with the below:

* [R 3.6.0+](https://posit.co/download/rstudio-desktop/) downloaded on local device
* [RStudio Cloud](https://posit.cloud/) account or [RStudio Desktop](https://posit.co/download/rstudio-desktop/) downloaded on local device
* 64-bit operating system

## Analysis

The report:
- Cleans and reshapes the dataset using `dplyr` and `tidyr`
- Computes descriptive and grouped summary statistics
- Visualises trends over time using `ggplot2`
- Focuses on **behavioural risks** as a key factor in self-harm outcomes

## Use

This project is built using the **Bookdown** package. To compile the report, add the below to the YAML header:

output:
  bookdown::html_document2:
    keep_md: true
    toc: true
    toc_float: true

## Authors

Megan O'Rorke 
[moro0003@student.monash.edu](mailto:moro0003@student.monash.edu)

## Version History

* See [commit change]() or See [release history]()
