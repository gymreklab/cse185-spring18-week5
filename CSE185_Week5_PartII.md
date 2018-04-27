# Week 5: What color should my eyes be?  (part 2)
Skills covered: Trait prediction, scripting, and plotting

Today, we'll use known SNPs associated with eye color to try and predict the eye color of a set of samples.

We will use a set of SNPs previously published in the [IrisPlex paper](https://www.ncbi.nlm.nih.gov/pubmed/20457092) (see the table below). We'll use a slightly more complicated model than a simple case/control model to account for the fact that some people have neither brown nor blue eyes, which we'll denote as "other" here.

## 5. Eye color prediction model and data
The IrisPlex paper has fit the following two models, which can be thought of as performing two separate case control analyses: one of blue vs. brown, and the second of other vs. brown.

TODO write model and define terms

We can rearrange this model to predict the probability of each eye color for a given individual:

TODO write model

Below is a reproduced table of the model parameters from the 6 predictive SNPs, converted to hg19 coordinates:
| chrom | start | rsid | Minor allele |
|----------|----------|-------|------|
| 15 | 28365618 | rs12913832 | A |
| 15 | 28230318 | rs1800407 | T |
| 14 | 92773663 | rs12896399 | G |
| 5 | 33951693 | rs16891982 | C |
| 11 | 89011046 | rs1393350 | A |
| 6 | 396321 | rs12203592 | T |

For example, TODO example calculation with my SNPs in excel file, use same format as they can use below

For this part of the lab, we'll be working with a smaller set of samples independent from our original GWAS. A VCF file containing these 6 variants for our samples can be found in the `public/week5` directory:

```
TODO filename
```

We've seen VCF files in the first couple weeks. But let's take a second to remind ourselves what's going on here and point out a couple of important fields
TODO example fields in VCF, questions about the VCF for methods section

Before we move on to predicting eye color, let's convert this VCF file into a format that will be easier for us to process. Use the following command to create a file with one row per sample and one column per SNP:

```
TODO command
```

## 6. Eye color prediction

Predict the eye color of each sample in our dataset using the model given above. We'll let you decide the best way to do this. For example, you could write a python script.  *Hint*: Alternatively, it would be pretty easy to modify the example worked out in the spreadsheet above to do all of this in Excel. Whichever method you choose, be sure to note in your lab notebook what you did and upload any additional scripts (or in this case, spreadsheets) to your Github repository.

For each sample, you should report p(brown), p(blue), and p(other), in addition to a prediction based on what eye color has highest probability for that sample. Include a table of results as a *supplementary file* that you reference from your lab report (do not include the table directly in the report!).

## 7. Comparison of predictions by population

It turns out our sample has a mixture of individuals from two population groups. One (denoted CEU) is from Northern Europe, whereas the second (denoted TSI) is from Southern Europe.

Population labels for each sample are given in `public/week5/ceu_tsi_population_labels.txt`.

 Calculate the mean probability of blue, brown, or other colored eyes for each population, CEU and TSI. Which group is more likely to have blue eyes? Does this match with what is known about eye color frequencies in those populations?

## 8. Variant interpretation

## 9. **(Optional, but fun!) Extra credit**

## 10. For your lab report

**Congrats, you made it to the end of lab 5! This will be our last full lab report before the final project. Next week's lab will just require answering a series of questions to give you a break before we head into final project mode.**