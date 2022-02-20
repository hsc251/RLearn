---
title: "Data Visualization Final Exercise"
author: "Hsin Chih Chen"
date: "11/8/2021"
---

Before executing this assignment, the [csv link](https://d3c33hcgiwev3.cloudfront.net/D1LYDGZLRAmS2AxmSxQJHw_244a6af25c32479990d299bf82de1a67_cces_sample_coursera.csv?Expires=1645488000&Signature=cec7Bgyk0UGKrjdcMbPpKFAY7WJNtHWfWw3iM1LKGRx3mNuTK3CZDk2e~lkI-9bi9J544tOLSWCt2u8HNIHH9apJ7BGYg7HVkuGefKlSQL3tgU2k7hbDlfOGLR-kVH1IQja2fgFHCHK2o9SICR9JlciloCRo~NvOSpt-D8ng5zQ_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A) and [code book](https://d3c33hcgiwev3.cloudfront.net/-MGcKK_WSUuBnCiv1glLng_2b365483ac5b41728667c0490df7361f_cces_sample_codebook.txt?Expires=1645488000&Signature=IjgaI2wfdHJ~VYRC15mzLfsfwN0J~iySVGoklz3oslYWTiTebWCi0aYNjTH7F998Qd-0xY9lzr4vHVXy2DjB2kiNE2kPgh1ayPJMSGcOD8gTbzuGtuihDvT8tSJNTmaJBmiLzThrGZLXaVyoC3utXgV7Dqw7KFp2f0Q2EH7X27E_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A) is required to understand the question while navigating your desired parameter.

# Problem 1 

Create a vector of five numbers of your choice between 0 and 10, save that vector to an object, and use the sum() function to calculate the sum of the numbers.

```{r,problem1}
p1 <- seq(from = 1,to = 5,1)
sum(p1)
```

# Problem 2

Create a data frame that includes two columns. One column must have the numbers 1 through 5, and the other column must have the numbers 6 through 10. The first column must be named "alpha" and the second column must be named "beta". Name the object "my_dat". Display the data.

Put your code and solution here:

```{r,problem2}
#Put your code here, then delete this commented line before submission. Don't modify the setup code for this chunk - you want your code and output to both display.
alpha <- c(1:5)
beta <- c(6:10)
my_dat <- data.frame(alpha,beta)
print(my_dat)
```

# Problem 3 

Using the data frame created in Problem 2, use the summary() command a create a five-number summary for the column named "beta".

Put your code and solution here:

```{r,problem3}
summary(my_dat$beta)
```

# Problem 4

There is code for importing the example survey data that will run automatically in the setup chunk for this report (Line 13). Using that data, make a boxplot of the Family Income column using the Base R function (not a figure drawn using qplot). Include your name in the title for the plot. Your name should be in the title. Relabel that x-axis as "Family Income".

Hint: consult the codebook to identify the correct column name for the family income question.

Put your code and solution here:

```{r,problem4}
boxplot(dat$faminc_new, xlab = "Family Income", main = "Hsin Chih's Boxplot of Family Income")
```

# Problem 5

Using the survey data, filter to subset the survey data so you only have male survey respondents who live in the northwest or midwest of the United States, are married, and identify as being interested in the news most of the time. 

Use the str() function to provide information about the resulting dataset.

Put your code and solution here:
 
```{r problem5,include=TRUE,echo=TRUE}
dat_5 <- dat %>%
  filter(gender == 1) %>% ## Filter gender = male
  filter(region == 1 | region == 2) %>% ## Filter Northwest(1) and Midwest(2) Regions
  filter(marstat ==1 & newsint == 1) ## Filter news interest is most of the time and married
str(dat_5)
```

# Problem 6

Filter the data the same as in Problem 5. Use a R function to create a frequency table for the responses for the question asking whether these survey respondents are invested in the stock market. 

Put your code and solution here:

```{r problem6,include=TRUE,echo=TRUE}
# Use data coming from question 5 to summarize it
freq_count <- function(x){
  table(x)
}
freq_count(dat_5$investor)
``` 

# Problem 7

Going back to using all rows in the dataset, create a new column in the data using mutate that is equal to either 0, 1, or 2, to reflect whether the respondent supports increasing the standard deduction from 12,000 to 25,000, supports cutting the corporate income tax rate from 39 to 21 percent, or both (so, support for neither policy equals 0, one of the two policies equals 1, and both policies equals two). Name the column "tax_scale". Hint: you'll need to use recode() as well.
 
Display the first twenty elements of the new column you create.

Put your code and solution here:

```{r problem7,include=TRUE,echo=TRUE}
# Change the recode scale for support and reject as a boolean variable
CC18_325a <- recode(dat$CC18_325a, `1` = 1, `2` = 0)
CC18_325d <- recode(dat$CC18_325d, `1` = 1, `2` = 0)
# Recreate the data frame to prevent mis-operation from original data
dat_7 <- dat
# Insert the recode value into the duplicated data frame
dat_7$CC18_325a <- CC18_325a
dat_7$CC18_325d <- CC18_325d
# Mutate new column by the insertion
dat_7 <- dat_7 %>%
  mutate(tax_scale = CC18_325a + CC18_325d)
# Print the 1st 20 summaries for the column
head(dat_7$tax_scale, 20)
```

# Problem 8

Use a frequency table command to show how many 0s, 1s, and 2s are in the column you created in Problem 7.

Put your code and solution here:

```{r problem8,include=TRUE,echo=TRUE}
# Refer to data frame from question 7 to summarize with table.
table(dat_7$tax_scale)
```

# Problem 9

Again using all rows in the original dataset, use summarise and group_by to calculate the average (mean) job of approval for President Trump in each of the four regions listed in the "region" column.

Put your code and solution here:
 
```{r problem9}
dat_9 <- dat %>%
  group_by(region) %>%
  summarize(Trump_Approve_Mean = round(mean(CC18_308a),2))
print(dat_9)
```
 
# Problem 10

Again start with all rows in the original dataset, use summarise() to create a summary table for survey respondents who  are not investors and who have an annual family income of between $40,000 and $119,999 per year. The table should have the mean, median and standard deviations for the importance of religion column.

Put your code and solution here:

```{r problem10}
dat_10 <- dat %>%
  filter(investor == 2) %>% # filtering not investor
  filter(faminc_new %in% c(5:10)) %>% # Income range from 40,000 to 119,999
  summarise(mean(pew_religimp),median(pew_religimp),sd(pew_religimp))
# Rename the columns
names(dat_10) <- c("Mean Religion Imp.", "Median Religion Imp.","Standard Dev. Religion Imp.")
# Print output
print(dat_10)
```

# Problem 11

Use kable() and the the summarise() function to create a table with one row and three columns that provides the mean, median, and standard deviation for the column named faminc_new in the survey data.

Put your code and solution here:

```{r problem11}
dat_11 <- dat %>%
  summarise(mean(faminc_new), median(faminc_new),sd(faminc_new))
kable(dat_11, col.names = c("Mean", "Median", "Std. Dev."))
```

# Problem 12

With the survey data, use qplot() to make a histogram of the column named pid7. Change the x-axis label to "Seven Point Party ID" and the y-axis label to "Count".

Note: you can ignore the "stat_bin()" message that R generates when you draw this. The setup for the code chunk will suppress the message.

Put your code and solution here:

```{r problem12,message=FALSE}
qplot(x = pid7, data = dat, geom = "histogram", xlab = "Seven-Point Party ID")
```