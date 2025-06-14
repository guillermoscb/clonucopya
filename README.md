<img src="./.img/clonucopya_header.png" width="500">

## Introduction

Cancer heterogeneity presents significant challenges in developing antitumoral effective treatments, as diverse clonal and subclonal populations can exhibit varied therapeutic responses. While advanced sequencing technologies enable detailed tumor genomic alterations characterization, translating subclonal inference analyses into clinical applications remains challenging. Here we present Clonucopya, a comprehensive snakemake workflow that bridges this gap by combining subclonal inference algorithms with silico drug prioritization. Using whole-genome or whole-exome sequencing data, Clonucopya reconstructs clonal and subclonal evolutionary trees of tumor cells also identifying FDA/EMA approved and candidate drugs targeting the specific genomic alterations within each population. The software offers easy configuration, detailed tables of drug response results associated with tumor clonality, clonality plots and intuitive reports integrating tumor heterogeneity and anticancer drug treatments. This integrated approach enables the design of therapeutic strategies that effectively target tumor clonality, guiding the selection of personalized therapies and providing an accessible tool that translates clonality data into actionable clinical insights for precision oncology.

<img src="./.img/graphical_abstract.png" width="900">

## Workflow overview

Clonucopya is a snakemake workflow that combines the clone inference of Pyclone-VI with the power of drug priorization from Pandrugs.

<img src="./.img/clonucopya-wf.png" width="900">


## Main applications

* Compare clonal populations and potential treatments in primary tumors vs metastases
* Propose treatments by considering temporal intratumor heterogeneity (ITH) through comparison of biopsies from the same tumor at different timepoints.
* Suggest antitumor treatments addressing spatial ITH comparing different regions of the same tumor (1 or more timepoints).


## Instalation:

```bash
# Clone Clonucopya repository
git clone https://github.com/cnio-bu/clonucopya.git

# Create a conda environment
conda create -n clonucopya

# Activate the environment
conda activate clonucopya

# Intall Required Packages
mamba install snakemake apptainer snakemake-executor-plugin-slurm
```

## Usage

### CNV Preprocessing

Due to the fact that there are many CNV callers, there are as many as output formats of called CNVs. So that, before running Clonucopya, you must make sure your CNVs has the propper format to use them as input. The expected format is a TSV file with the following columns:

1. Chrom: chromosome which contains the CNV, BED format (i.e chr1).
2. Start: start position of the CNV (integer).   
3. End: end position of the CNV (integer).
4. major_cn: major copy number of segment overlapping mutation (integer).
5. minor_cn: minor copy number of segment overlapping mutation (integer).
6. normal_cn: total copy number of segment in healthy tissue. For autosome this will be two and male sex chromosomes one (integer).


Example:

| Chrom 	| Start     	| End       	| major_cn 	| minor_cn 	| normal_cn 	|
|-------	|-----------	|-----------	|----------	|----------	|-----------	|
| chr1  	| 109367944 	| 109371874 	| 1        	| 0        	| 2         	|


> Tip: to facilitate this step, there are a couple of scripts at `workflow/scripts` to carry out this task for facets and ascat3 output.


### Configure workflow

Once the workflow has been downloaded, and the conda environment is ready, the parameters must be set.

* sampleshet: there is a samplesheet_template.csv available at config directory. The path to the samplesheet must be set in the config.yaml. 
* config.yaml: there is a config_template.yaml available at config directory. Please change the name to config.yaml or use the name you desire at workflow/Snakefile. 

> Generate your own seed for config.yaml. Manually chosen seeds may be too low-complexity and more stochastically dependent. Further, pseudorandom number generators from standard libraries of software like numpy in python (used in Pyclone-VI and Phyclone), often do not meet quality checks for randomness. To minimize seed bias, we instead recommend using the terminal to read four bytes from the operating system (/dev/random) to generate unpredictable 32-bit random seed values. For example, it can be easily generated on linux with this command `head -c 4 /dev/urandom | od -An -tu4`.

### Run Clonucopya

This workflow can be executed locally or in a cluster (best option).

- **Local run:**

```bash
# Go to workflow directory
cd workflow/

snakemake --software-deployment-method conda -j unlimited --cache
```


- **Cluster run:**

```bash
# Go to workflow directory
cd workflow/

sbatch -p long -e error.txt -c 8 --mem=32G -t1200 --wrap "snakemake --executor slurm --software-deployment-method conda -j unlimited --cache"
```

> First successful execution will last over 7-8 hours. VEP's reference needs to be cached.


## Results



## Authors

* Guillermo Sánchez-Cid
* Gonzalo Gómez-López
* Carlos León-Ramos
* Ester Arroba
* Fátima Al-Shahrour
