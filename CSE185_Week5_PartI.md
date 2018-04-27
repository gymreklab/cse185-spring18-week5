# Week 5: What color should my eyes be?  (part 1)
Skills covered: GWAS, plink, plotting

The goal of this week's lab is to determine the genetic basis of eye color. This week we'll have fewer tasks to complete for the lab, but less of the code for each section will be provided. Also, we're assuming you're now comfortable with the process of cloning and updating your `git` repository, so those commands won't be given anymore unless we're doing something new.

Today, you'll perform a genome-wide association study (GWAS) for samples with blue vs. brown eyes. Thursday, you'll implement a program to predict eye color from a handful of SNPs. Note: if you have done 23andMe, you can even do Part II on your own genome to predict what color your own eyes should be, and see how close that prediction is to reality! 

## 0. Introduction to plink

For our GWAS, we've gone out and collected DNA samples and recorded eye color for a cohort of samples. We've analyzed our samples using a SNP genotype array and cleaned our dataset to remove duplicated samples and problematic markers. We're ready to conduct our GWAS using the cleaned dataset which can be found in the `public/week5` directory with prefix `lab5_merged`.

Today we'll primarily be using the [Plink](https://www.cog-genomics.org/plink2) package, which is a general toolkit for doing all kinds of operations on genetic datasets. Before we dive into GWAS, let's get familiar with the types of files used by `plink`. Almost every `plink` command takes `--file` as an argument, which gives a prefix to the following files:
You should see these files:

* `$PREFIX.ped`: This file contains all the genotype information. There is one row per individual. The columns are described [here](https://www.cog-genomics.org/plink/1.9/formats#ped). There are V+6 fields, where V is the number of variants. The first 6 columns contain: sample id, family id, id of father (if known), id of mother (if known), sex code, and phenotype value. Each column after that gives the genotype for each variant as 0, 1, or 2 depending if the sample is homozyous for the reference allele, heterozygous, or homozygous for the alternate allele.
* `$PREFIX.map`: This file describes the variants whose genotypes are given in the `.ped` file. It has four columns: chromosome, SNP ID, position in centimorgans (or 0 if ignored), and genomic position.
* `$PREFIX.fam`: Same as the first 6 columns of the `.ped` file.

We have one additional file with phenotype information, which could have alternatively been stored in the `.ped` file:

* `$PREFIX.phen`: This file has 3 columns: sample id, family id, and phenotype. Here we have coded "cases" (blue eyes) as 2 and "controls" (brown eyes) as 1.

Note since we are not using any family information, we have just used the sample id as the family id.

`plink` alternatively can binary files by specifying `--bfile` for commands instead of `--file`. In that case, you would have `.bed`, `.bim`, and `.fam` files.

Take a look at our dataset. Record:
* How many samples are in our cohort?
* What is the frequency of brown vs. blue eyes in our samples?
* How many markers are included in our dataset?
Record the commands you used to determine this in your lab notebook. Note the results in the methods section of your lab report.

## 1. Analyzing population structure
Pop structure PCA and plot

## 2. Performing a basic GWAS

Now, we'll use `plink` to perform our GWAS. The following command performs a basic case-control GWAS using logistic regression:
```
plink \
      --file PATH_TO/lab5_merged \
      --pheno PATH_TO/lab5_merged.phen \
      --out $OUTPREFIX \
      --logistic \
      --allow-no-sex
```
Note you'll have to fill in absolute paths.

Look at the [`plink` documentation](https://www.cog-genomics.org/plink/1.9/assoc#linear) to learn how to add covariates to our analysis. Run the GWAS twice: once with no covariates and once controlling for population structure using the PCs generated above.

## 3. Visualizing GWAS results
QQ plot
manhattan plot

## 4. Analyzing significant hits
clump with plink
compare with irisplex snps

Previously, the following SNPs have been associated with eye color:

| chrom | start | rsid | Minor allele |
|----------|----------|-------|------|
| 15 | 28365618 | rs12913832 | A |
| 15 | 28230318 | rs1800407 | T |
| 14 | 92773663 | rs12896399 | G |
| 5 | 33951693 | rs16891982 | C |
| 11 | 89011046 | rs1393350 | A |
| 6 | 396321 | rs12203592 | T |

Do any of these show up in your results? If not, discuss in your lab report why you might not have been able to identify them. We'll use this set of SNPs on Thursday to predict eye color in samples where the answer is unknown.

**That's it for today. Next time, we'll use GWAS hits to predict eye color in an independent sample of individuals.**


TODO:
remove "other" eye color
use more pop groups here so the PCA is pretty?
pull out two more population groups for next part