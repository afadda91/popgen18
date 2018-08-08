# Exercise 1: Basic association testing (single SNP analysis)

In this exercise we will go through a couple of simple tests for association. We will use the statistical software R and look at (imaginary) data from
1237 cases and 4991 controls at two SNP loci.

Open R by typing R in your terminal window on the server (you may also open R or Rstudio on your own laptop if you prefer).

## Exercise 1A: the genotype distribution test

Test if disease is associated with genotype of the first SNP is by running the code below in R.

Input the SNP1 count data into a matrix
```
countsSNP1<-matrix(c(423, 1955, 568, 2295, 246, 741), nrow=2)
```
Print the matrix (so you can see it)
```
print(countsSNP1) 
```
Perform a chi-square test on the full count matrix
```
chisq.test(countsSNP1)
```
Note the p-value. 
Does the test suggest that the SNP is associated with the disease (you can use a p-value threshold of 0.05 when answering this question)? 

Plot the proportions of cases in each genotype category in this SNP:
```
barplot(countsSNP1[1,]/(countsSNP1[1,]+countsSNP1[2,]), 
				xlab="Genotypes", ylab="Proportion of cases", 
				names=c("AA","Aa","aa"), las=1, ylim=c(0,0.25))
```
Does is look like the SNP is associated with the disease? 

Is the p-value consistent with the plot? 

Input the SNP2 count data into a matrix
```
countsSNP2<-matrix(c(1003,4043,222,899,12,49),nrow=2)
```
Then answer the same questions for SNP 2 (you can reuse the R code used on SNP1 above but with "countsSNP2" instead).


## Exercise 1B: the allelic test

Another simple way to test for association is by using the allelic test. 

To perform an allelic test on SNP1 in R first generate the allelic count matrix

```
allelecountsSNP1<-cbind(2*countsSNP1[,1]+countsSNP1[,2],
												countsSNP1[,2]+2*countsSNP1[,3])
```
Print them (so you can see it)
```
print(allelecountsSNP1)
```
Perform the allelic test

```
chisq.test(allelecountsSNP1, correct=F)
```
Does this test lead to the same conclusion you reached in exercise 1A for SNP1?

## Exercise 1C: Additive trend test
We can also test for association by performing an additive trend test. 

Do that for SNP1 by running the following code in R:

Get number of cases for each genotype
```
casecounts <- countsSNP1[1,]
```
Get total number of individuals for each genotype
```
totalcounts <- apply(countsSNP1, 2, sum)
```
Perform trend test
```
prop.trend.test(casecounts, totalcounts)
```
Does this test lead to the same conclusions as you reached for SNP1 in exercise 1A?


## Exercise 1D: Logistic regression (multiplicative inheritance model)

We can also perform tests for association using a logistic regression based test. 

First reshape the count table into a dataset with three variables:

Make a vector out of the count matrix
```
countsSNP1vec = as.vector(countsSNP1)
```
Make vector that indicates which of the numbers in the count vector are from cases (1) and which are from controls (0)
```
status<-rep(1:0,3)
```
Make vector that show which of the numbers in the count vector are from AA (0), Aa (1) and aa (2):
```
geno<-rep(0:2,each=2)
```
Combine these in a dataset
```
data1 <- data.frame(geno = geno, status = status, counts = countsSNP1vec)
```
View the dataset
```
print(data1)
```
Perform logistic regression test
```
add_glm1<-glm(status~geno, weight = counts, data = data1, family = "binomial")
c("p-value"=summary(add_glm1)$coefficients[-1,"Pr(>|z|)"])
```
Does this test lead to the same conclusion as you reached in exercise 1A?


## Exercise 1E: Extra questions if you have time

How would you perform a test for association that assumes a recessive inheritance model in this logistic regression framework for SNP1? 
And how would you do it using a simple chi-squared test? Do the results of these tests suggest there is an association?

## Exercise 1F: effect size (OR)

Try to get odds ratio (OR) from the multiplicative logistic regression output in exercise 1D.

Hint 1: summary(add_glm)$coefficients[-1,"Estimate"] will give you beta_1

Hint 2: beta_1 is an estimate of log(OR)

What does this tell us about the effect size in the locus we are looking at?




