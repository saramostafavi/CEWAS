# Cascading Epigenome-Wide Association Scan
This code uses the existing [MetaXcan](https://github.com/hakyimlab/MetaXcan) package to run a cascading analysis linking variation in genotype to traits through its effect on DNA methylation to gene expression.
## Installation
### Requirements
The dependencies for CEWAS can be installed via [Anaconda](https://www.anaconda.com/products/individual) by running the following fommand from the `cewas/` directory:
```
conda env create -f "cewas_env.yml"
```
The software help can then be accessed using:
```
conda activate cewas_env
python cewas.py -h
```
### Testing
Included is a script entitled `test_run.sh` which will run CEWAS on the subset of the [ROSMAP](https://adknowledgeportal.synapse.org/Explore/Studies?Study=syn3219045) data for chromosome 22, which is included in the `data` directory of this repository. It will output a file named `"test_results.txt"` which can be compared to the file labelled `"sample_output.txt"` as a sanity check that the program is working as intended:

```
./test_run.sh
```
This output can then be compared to our provided output in `sample_output.txt`.

## Usage
### Command help
```
$ python cewas.py -h
usage: cewas.py [-h] --output OUTPUT --sum_stats SUM_STATS
                [--beta_column BETA_COLUMN] [--or_column OR_COLUMN]
                [--se_column SE_COLUMN] [--pvalue_column PVALUE_COLUMN]
                [--zscore_column ZSCORE_COLUMN] --mapping MAPPING
                --reference_allele REFERENCE_ALLELE --alternative_allele
                ALTERNATIVE_ALLELE --snp_column SNP_COLUMN --cov_file_snp_fmt
                COV_FILE_SNP_FMT --cov_file_methy COV_FILE_METHY
                --predict_db_methy_fmt PREDICT_DB_METHY_FMT --predict_db_expr
                PREDICT_DB_EXPR [--ncores NCORES]

optional arguments:
  -h, --help            show this help message and exit
  --output OUTPUT       Name of file for CEWAS results (default: None)
  --sum_stats SUM_STATS
                        Path to GWAS summary statistics (default: None)
  --beta_column BETA_COLUMN
                        Name of effect size column in GWAS summary statistics
                        (default: None)
  --or_column OR_COLUMN
                        Name of odds ratio column in GWAS summary statistics
                        (default: None)
  --se_column SE_COLUMN
                        Name of standard error column in GWAS summary
                        statistics (default: None)
  --pvalue_column PVALUE_COLUMN
                        Name of P value column in GWAS summary statistics
                        (default: None)
  --zscore_column ZSCORE_COLUMN
                        Name of z-score column in GWAS summary statistics
                        (default: None)
  --mapping MAPPING     ENSEMBL mart file for re-mapping gene names (default:
                        data/ensemblHG19.tsv)
  --reference_allele REFERENCE_ALLELE
                        Reference allele column in GWAS summary statistics
                        (default: A1)
  --alternative_allele ALTERNATIVE_ALLELE
                        Alternative allele column in GWAS summary statistics
                        (default: A2)
  --snp_column SNP_COLUMN
                        SNP id column in GWAS summary statistics (default:
                        SNP)
  --cov_file_snp_fmt COV_FILE_SNP_FMT
                        Covariance file prefix for snp to methylation model
                        (default: data/covfiles/ROSMAP_CEWAS_snp_methy_cov)
  --cov_file_methy COV_FILE_METHY
                        Covariance file for methylation to expression model
                        (default:
                        data/covfiles/ROSMAP_CEWAS_pred_methy_expr_cov.txt.gz)
  --predict_db_methy_fmt PREDICT_DB_METHY_FMT
                        SNP to methylation predictdb prefix (default:
                        data/predictdb/ROSMAP_CEWAS_snp_methy)
  --predict_db_expr PREDICT_DB_EXPR
                        Methylation to expression predictdb (default:
                        data/predictdb/ROSMAP_CEWAS_methy_expr_pred.db)
  --ncores NCORES       Number of cores available for job (default: 1)
```
### Required data
As input, CEWAS requires several models that describe the association between genotype, methylation, and gene expression in a given tissue, as well as a reference for covariance between data comprising those models. These models and covariances can be found in the `data/` directory of this project.

Briefly the required data are:
* SNP to methylation predictDB, split by chromosome: This an SQLite database describing the association between genotype and DNA methylation in *cis*, and closely resembles the [format used in MetaXcan](https://github.com/hakyimlab/MetaXcan/wiki/Command-Line-Reference#spredixcanpy)`{name}_chr{1-22}.db`
* SNP to methylation covariance files, split by chromosome: This is a set of text files describing the covariance between SNPs in the model above, and they closely resemble the [format used in MetaXcan](https://github.com/hakyimlab/MetaXcan/wiki/Command-Line-Reference#spredixcanpy). The files should be named as `{name}_chr{1-22}.txt.gz`, and must be gzipped and whitespace delimited.
* Methylation to expression predictDB: This is an SQLite data base that describes the association between DNA methylation and Gene expression in *cis*, and closely resembles the [format used in MetaXcan](https://github.com/hakyimlab/MetaXcan/wiki/Command-Line-Reference#spredixcanpy).
* Methylation to expression covariance: This is a text file describing covariance between DNA methylation probes used in the model above, and it closely resembles the [format used in MetaXcan](https://github.com/hakyimlab/MetaXcan/wiki/Command-Line-Reference#spredixcanpy). The file must be gzipped and whitespace delimited.
* Mapping/ENSEMBL mart file: This is a text file that is used in mapping ENSEMBL IDs from the genome build corresponding to the models above (here HG19), and is used in labelling genes with their HGNC id


