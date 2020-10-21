# scREAD

This repository serves as the front end part of scREAD, it also contains scREAD workflow in the /script folder.

[scREAD](https://bmbls.bmi.osumc.edu/scread/) (A Single-Cell Rna-sEq database for Alzheimer’s Disease) is the first database dedicated to collect all existing Human and Mouse Alzheimer's Disease scRNA-Seq data, and provide comprehensive interpretations.

If you have any questions, suggestions, or found a new AD paper generated scRNA-seq datasets, please let us know through [google form](https://docs.google.com/forms/d/e/1FAIpQLSereTkpOfJ4LJLe9Ke5dZq78SnX3D7qXjQWY0ofDut0kIfDPg/viewform), or email: [qin.ma@osumc.edu](qin.ma@osumc.edu).


## How to run scREAD backend workflow locally

The workflow in R can be found in https://github.com/OSU-BMBL/scread/tree/master/script, the folder contains the following files:
1.	custom_marker.csv. A manually created marker gene list file used for identified cell types.
2.	functions.R. Visualization functions used in R.
3.	build_control_atlas.R: build control cells atlas Seurat object from count matrix file.
4.	transfer_cell_type.R: filter out control-like cells in disease dataset
5.	run_analysis.R: run analysis workflow, and export tables in scREAD database format.

### Build control atlas 

Note: You may skip this process if you want to use control atlas from scREAD, simply download the processed control atlas from: https://bmbls.bmi.osumc.edu/scread/downloads

If you wish to build from own controls:

1.	Goal: Build the control atlas file from raw gene expression matrix.
2.	Prepare your control gene expression data in fst format (https://www.fstpackage.org/), we used fst package to store raw data in scREAD since it provides a fast, easy and flexible way to serialize data frames. In the data frame, the first column should be gene symbols, and other columns as cell labels. Put all code and data in a working directory. (e.g PATH_TO_WD), in this tutorial, we will run example_control.fst.
3.	build_control_atlas.R takes three parameters: 1. Working directory path; 2. Control data path. 3. Output data ID
```{R}
cd PATH_TO_WD  
Rscript build_control_atlas.R PATH_TO_WD example_control.fst control_example 
```

4.	The output should contain four files:

 - control_example.rds. The Seurat R object storing example control data.
- control_example_expr.txt. Filtered gene expression matrix.
- control_example_cell_label.txt. The first column is the cell names, the second column is the cell type information.
- 	control_example_umap.png. UMAP plot of example control data colored by cell types.

## Transfer cell types based on control atlas
1.	Goal: Annotate cell type using control atlas as the reference, onto the disease gene expression matrix file.
2.	Put all code and data in a working directory. (e.g PATH_TO_WD), after you have generated the control atlas file (control_example.rds). 
3.	build_control_atlas.R takes four parameters: 1. Working directory path; 2. Control atlas Seurat object file name; 3. Disease gene expression matrix name; 4. Output disease data ID.

```{r}
cd PATH_TO_WD  
Rscript transfer_cell_type.R PATH_TO_WD control_example.rds example_disease.fst disease_example 
```
4.	The output should contain four files:
-	disease_example.rds. The Seurat R object storing example disease data.
-	disease_example_expr.txt. Filtered gene expression matrix.
-	disease_example_cell_label.txt. The first column is the cell names, the second column is the cell type information.
-	disease_example_umap.png. UMAP plot for both control and disease disease data colored by cell types.

## Run data analysis

1.	Goal: Perform analysis between disease and control data
2.	Put all code and data in a working directory. (e.g PATH_TO_WD), after you have generated the control atlas file (control_example.rds), and the disease file (disease_example.rds)
3.	run_analysis.R takes three parameters: 1. Working directory path; 2. Control Seurat object file name. 3. Disease Seurat object file name.

```{r}
cd PATH_TO_WD  
Rscript run_analysis.R PATH_TO_WD control_example disease_example
```

4.	The output should be stored in three folders:
-	/de. Differential gene expression analysis results. 1. Cell-type-specific genes; 2. Sub-cluster specific genes; 3. Cell type DE genes between two conditions.
-	/dimension. UMAP coordinates for two datasets.
-	/subcluster_dimension. UMAP coordinates for each sub-clusters in two datasets.


## Local development

First, install [Node.js](https://nodejs.org/en/)

### Add configuration file

```bash
git clone git@github.com:OSU-BMBL/scread.git
cd scread
```

Create a `.env` file in project root and put API URL in the env file:

```env
API_URL=https://bmbls.bmi.osumc.edu/api/scread
```

Next,
```bash
npm install
npm run dev
```