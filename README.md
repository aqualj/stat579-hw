# stat579-hw
STAT 579 HW
---
title: 'Stat 579 - Homework #4'
author: "Amin Shirazi "
date: "9/18/2018"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Subsets and Visualizations: Movie Mojo

For this homework we are using the data set `mojo` from the classdata package. `mojo` constains data on box office revenue for movies based on the website https://www.boxofficemojo.com .


Run the following two commands to install the newest version of the package from github:

```{r, eval = FALSE}
library(devtools) # error? then run install.packages("devtools")
devtools::install_github("heike/classdata")
library(dplyr)
```

Check what is in the data set:
```{r}
library(classdata)
head(mojo)
```

1. Download the RMarkdown file with these homework instructions to use as a template for your work.
Make sure to replace "Your Name" in the YAML with your name.

####2.
What is the difference between the variables `Week` and `WeekNo`? Describe in your words. 
```{r}
str(mojo)
summary(mojo$Week)
summary(mojo$WeekNo)

```
Using the funcitons str and summary for variables week and weekno, week is a numeric variable, but weekno seems to be a factor variable. 


#### 3. 
Use `ggplot2` to plot total gross (`Total Gross`) against week number (`WeekNo`). Facet by Year. Interpret the result. 
Which movie had the highest total gross over the time frame? How many weeks was that movie on rank 1? How long was it in box offices overall?
```{r}
library(tidyverse)
library(ggplot2)
mojo%>%
  ggplot(aes(x = WeekNo, y = `Total Gross`))+
  geom_point()+
  facet_wrap(~Year, scales = "free")

  which.max(mojo$`Total Gross`)
mojo[12881, "Title"]
mojo%>%
  filter(Title=="Star Wars: The Force Awakens" )-> mojo.starwars
sum(mojo.starwars$TW=="1")

length(mojo.starwars$Week)
```

Over time, the total gross of the movies over weeks have increased considerably. There is one top- selling movie in 2016 with the highest total gross among the movies of the years 2013-2018. This movie is the "Star wars: The Force Awakens". It was on rank one of the box office for five weeks. This movie was overall in box offices for 25 weeks. 

#### 4. 
Pick two movies that were in box office some time between 2013 and 2018 and find the corresponding data in the `mojo` data. How does total gross of the two movies compare? Draw a plot and comment on the result. 
```{r}

mojo%>%
  filter(Title=="The Equalizer 2" | Title=="Hacksaw Ridge" )->fav.movies
fav.movies%>%
  ggplot(aes(x = Week, y = `Total Gross`, colour=Title)) +
  geom_line(linetype = "dashed")+
  geom_point()
```
```{r}
fav.movies%>%
  filter(Title=="The Equalizer 2")->the.equalizer


fav.movies%>%
  filter(Title=="Hacksaw Ridge")->hacksaw.ridge



the.equalizer[, "Total Gross"]<-rev(c(the.equalizer[7, "Total Gross"],abs(diff(sort(the.equalizer$`Total Gross`)))))

hacksaw.ridge[, "Total Gross"]<-rev(c(hacksaw.ridge[18, "Total Gross"],abs(diff(sort(hacksaw.ridge$`Total Gross`)))))

fav.movies[, "Total Gross"]<- c(the.equalizer$`Total Gross`, hacksaw.ridge$`Total Gross`)


fav.movies%>%
  ggplot(aes(x = Week, y = `Total Gross`, colour=Title)) +
  geom_line(linetype = "dashed")+
  geom_point()


```

For this part of the exercise, I pick two movies "The Equalizer 2" and "Hacksaw Ridge". If we plot their total gross, we would have two increasing trends because the total gross of the movies is cumlative over the weeks the movie was on box office. Therefore, I make the total gross per week and plot them versus week number. 
Looking at the first plot, The equalizer 2 had a larger total gross in the first weeks and it ended up with a higher total gross comparing to Hacksa Ridge which was in box office for 18 weeks. 
In the second plot, it is evident that they both lost popularity over time however there was an increase in the total gross of Hacksa Ridge on the second week. 



#### 5. a)
Hit or Flop? The variable `Budget (in Millions)` contains estimated budget numbers for some movies. For how many movies is this information available (careful! trick question - look at what the function `unique` does)? 
```{r}
#mojo[which(is.na(mojo$`Budget (in Million)`)),]
# all rows are unique at least in one variable, so we do not get anything by using unique
na.budget<-which(is.na(mojo$`Budget (in Million)`))
mojo.new<-mojo[-na.budget, ]
#table(mojo$Title)
length(unique(mojo.new$Title))

```

There are 658 movies which have the info of Budget (in Million).

#### 5. b)
Studios would like to see their budget returned by the opening weekend. What is the percentage of movies for which that happened? How many movies did not have their budget matched in total gross by the third weekend?
```{r}

mojo.new%>%
  filter(Week==1)%>%
  filter(`Weekend Gross`/10^6 >= `Budget (in Million)`)->returned.budget
nrow(returned.budget)
```

There are only 103 movies that chould return their budget on the first opening week. 

```{r}
mojo.new%>%
  filter(Week==3)%>%
  filter(`Total Gross`/10^6 >= `Budget (in Million)`)->week.3
nrow(week.3)
```

 308 studios with available budget value earned back their budget by the third week. 

#### 5. c)
For each of these two questions describe your 'plan of attack', i.e. lay out how you go about finding an answer to the question.

First remove the NAs from the variable Budget (in million), then find the unique movies with their titles because after removing NAs from Budget, there are still some movies with duplicated titles, but different information in other variables. Then the length of the vector shows the number of unique movies with available Budget information.
For the second part of the problem, I filtered the variable week==1 for the data with available information of their Budget. Then filter again using a logical vector to get the number of movies which earned back their budget on the opening week.

#### 5.

Identify one movie, that did not match its budget by week 3. Plot the incurred loss over time. 

```{r}
mojo.new%>%
  filter(Week==3)%>%
  filter(`Total Gross`/10^6 < `Budget (in Million)`)->week.3
head(week.3)

mojo.new%>%
  filter(Title=="The Meg")%>%
  ggplot(aes(x=Week, y = `Weekend Gross`))+
  geom_point(colour="Blue")+
  geom_line(linetype = "dashed", colour="Red")
```   
