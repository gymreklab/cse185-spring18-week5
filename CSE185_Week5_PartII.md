# Week 5: What color should my eyes be?  (part 2)
Skills covered: Trait prediction, scripting, and plotting

Today, we'll use known SNPs associated with eye color to try and predict the eye color of a set of samples.

We will use a set of SNPs previously published in the [IrisPlex paper](https://www.ncbi.nlm.nih.gov/pubmed/20457092) (see the table below). We'll use a slightly more complicated model than a simple case/control model to account for the fact that some people have neither brown nor blue eyes, which we'll denote as "other" here.

## 5. Eye color prediction model and data
The IrisPlex paper has fit the following two models, which can be thought of as performing two separate case control analyses: one of blue vs. brown, and the second of other vs. brown.

<a href="https://www.codecogs.com/eqnedit.php?latex=\ln(p_{blue}/p_{brown})&space;=&space;\alpha_1&space;&plus;&space;\sum_{k}\beta_{1,&space;k}X_k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\ln(p_{blue}/p_{brown})&space;=&space;\alpha_1&space;&plus;&space;\sum_{k}\beta_{1,&space;k}X_k" title="\ln(p_{blue}/p_{brown}) = \alpha_1 + \sum_{k}\beta_{1, k}X_k" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\ln(p_{other}/p_{brown})&space;=&space;\alpha_2&space;&plus;&space;\sum_{k}\beta_{2,&space;k}X_k" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\ln(p_{other}/p_{brown})&space;=&space;\alpha_2&space;&plus;&space;\sum_{k}\beta_{2,&space;k}X_k" title="\ln(p_{other}/p_{brown}) = \alpha_2 + \sum_{k}\beta_{2, k}X_k" /></a>

where $\alpha_i$ is the intercept term, $\beta_{i,k}$ is the effect size for model $i$ at the $k$th SNP, and $X_k$ is the number of minor alleles at an individuals genotype for the $k$th SNP (0, 1, or 2). Rearranging these allows us to predict the probability of each class of eye color:

We can rearrange this model to predict the probability of each eye color for a given individual:

<a href="https://www.codecogs.com/eqnedit.php?latex=p_{blue}&space;=&space;\frac{e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}}{1&plus;e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}&plus;e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}&space;\\\\&space;p_{other}&space;=&space;\frac{e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}{1&plus;e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}&plus;e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}&space;\\\\&space;p_{brown}&space;=&space;1-p_{blue}-p_{other}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?p_{blue}&space;=&space;\frac{e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}}{1&plus;e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}&plus;e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}&space;\\\\&space;p_{other}&space;=&space;\frac{e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}{1&plus;e^{\alpha_1&space;&plus;&space;\sum_k&space;\beta_{1,k}X_k}&plus;e^{\alpha_2&space;&plus;&space;\sum_k&space;\beta_{2,k}X_k}}&space;\\\\&space;p_{brown}&space;=&space;1-p_{blue}-p_{other}" title="p_{blue} = \frac{e^{\alpha_1 + \sum_k \beta_{1,k}X_k}}{1+e^{\alpha_1 + \sum_k \beta_{1,k}X_k}+e^{\alpha_2 + \sum_k \beta_{2,k}X_k}} \\\\ p_{other} = \frac{e^{\alpha_2 + \sum_k \beta_{2,k}X_k}}{1+e^{\alpha_1 + \sum_k \beta_{1,k}X_k}+e^{\alpha_2 + \sum_k \beta_{2,k}X_k}} \\\\ p_{brown} = 1-p_{blue}-p_{other}" /></a>

Below is a reproduced table of the model parameters from the 6 predictive SNPs, converted to hg19 coordinates:

| chrom | start | rsid | Minor allele |
|----------|----------|-------|------|
| 15 | 28365618 | rs12913832 | A |
| 15 | 28230318 | rs1800407 | T |
| 14 | 92773663 | rs12896399 | G |
| 5 | 33951693 | rs16891982 | C |
| 11 | 89011046 | rs1393350 | A |
| 6 | 396321 | rs12203592 | T |

For example, see the following example calculation:
https://docs.google.com/spreadsheets/d/1aP4OQdNsBj7gN5v_Hb40VFV3zgToDPMvGTFQYD6yHwg/edit?usp=sharing

and in a format that might be slightly more useful for the exercise below:
https://docs.google.com/spreadsheets/d/1dmup-_Nwrs8r7TfBMojnVJewqWdvrc3IcEPmXScZVjY/edit?usp=sharing

<blockquote>
 **NOT UNIX TIP!** You can do *a lot* with spreadsheets! Although some bioinformaticians might say this is not cool, I think Excel is a great resource especially for working out how to do certain calculations. Please take a chance to look around at this spreadsheet and see how the different fields were calculated.
</blockquote>

For this part of the lab, we'll be working with a smaller set of samples independent from our original GWAS. A VCF file containing these 6 variants for our samples can be found in the `public/week5` directory:

```
lab5_pred_eyecolor.vcf.gz
```

We've seen VCF files in the first couple weeks. But let's take a second to remind ourselves what's going on here and point out a couple of important fields

**TODO example fields in VCF, questions about the VCF for methods section**

Before we move on to predicting eye color, let's convert this VCF file into a format that will be easier for us to process. Use the following commands to create a file with one row per sample and one column per SNP:

```
# Get a header row listing the samples
bcftools query -l lab5_pred_eyecolor.vcf.gz | datamash transpose | awk '{print "ID\t"$0}' > lab5_pred_eyecolor.tab
# Extract the ID and sample genotypes for each variant
bcftools query -f "%ID\t[%TGT\t]\n" final/lab5_pred_eyecolor.vcf.gz | sed 's/|//g' >> lab5_pred_eyecolor.tab
# Transpose the file
cat lab5_pred_eyecolor.tab | datamash transpose > lab5_pred_eyecolor_transpose.tab
```

**TODO explain command above**

Note, these SNPs are sorted by genomic coordinate, so are not in the same order as the SNPs in the table above or in the example spreadsheets! So you might want to use `awk` to rearrange the columns before moving forward.

## 6. Eye color prediction

Predict the eye color of each sample in our dataset using the model given above. We'll let you decide the best way to do this. For example, you could write a python script. Alternatively, it would be pretty easy to modify the example worked out in the spreadsheet above to do all of this in a spreadsheet. Whichever method you choose, be sure to note in your lab notebook what you did and upload any additional scripts (or spreadsheets) to your Github repository.

For each sample, you should report p(brown), p(blue), and p(other), in addition to a prediction based on what eye color has highest probability for that sample. Include a table of results as a *supplementary file* that you reference from your lab report (do not include the table directly in the report!).

## 7. Comparison of predictions by population

It turns out our sample has a mixture of individuals from two population groups. One (denoted CEU) is from Northern Europe, whereas the second (denoted TSI) is from Southern Europe.

Population labels for each sample are given in `public/week5/ceu_tsi_population_labels.txt`.

Calculate the mean probability of blue, brown, or other colored eyes for each population, CEU and TSI. Which group is more likely to have blue eyes? Does this match with what is known about eye color frequencies in those populations? (Note, not all of these samples has genotyopes, so don't worry if some data is missing).

## 8. Variant interpretation

Now, we'd like to understand a little more about what these variants are doing. Look up these six SNPs using either IGV or the UCSC genome browser (be sure to look at human reference genome build hg19). Where do these SNPs fall? Do you have any hypotheses about how these regions might affect eye color? You may also try loading additional tracks to these browsers, such as histone modifications, to see if these SNPs overlap predicted regulatory regions. Discuss your hypotheses in the lab report.

## 9. **(Optional, but fun!) Extra credit**

For extra credit, we can use what we learned about eye color to predict what eye color a child will have based on the genotypes of the parents.

This spreadsheet gives example genotypes for two parents at each of these six SNPs: TODO

Based on the fact that each child will inherit one allele from mom and one from dad, figure out all possible sets of SNP genotypes for a child.


## 10. For your lab report
Similar to last week, the lab report document contains specific prompts that should be answered.

**Congrats, you made it to the end of lab 5! This will be our last full lab report before the final project. Next week's lab will just require answering a series of questions to give you a break before we head into final project mode.**
