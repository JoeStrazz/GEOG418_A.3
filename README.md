# GEOG418_A.3
---
title: "Spatial Autocorrelation Tutorial"
author: "Geog 418"
date: "`r format(Sys.time(), '%B %d, %Y')`"
output: pdf_document
#output: 
#  bookdown::html_document2:
#    number_sections: false
#   fig_caption: true
#    global_numbering: true 
---


## Introduction

In understanding Spatial Autocorrelation it is important to be reminded of Tobler’s First Law of Geography which states ‘Everything is related to everything else, but near things are more related than distant things (O’Sullivan & Unwin, 2003). To describe this very notion, the technical term Spatial Autocorrelation is used. In spatial data analysis, spatial autocorrelation dictates that attributes are more likely to be similar to attributes that are closer together compared to attributes that are farther away. For example, housing prices are more likely to be influenced by the price of a neighbouring house and less likely to be influenced by the price of a house 10 km away (Laposa & Thrall, 2004). Spatial Autocorrelation then is a measurement that determines how closely correlated near neighbours are (Laposa & Thrall, 2004). Near neighbours can be thought of as two attributes that are close in distance. 


If it is found that there is a high level of correlation between near neighbours, this is known as positive spatial autocorrelation (Laposa & Thrall, 2004). Positive spatial autocorrelation relates to a clustered spatial pattern (C.Bone, personal communication, 2024, October 3). If it is found that there is a low level of correlation between near neighbours, this is known as negative spatial autocorrelation (Laposa & Thrall, 2004). Negative spatial autocorrelation relates to a dispersed clustered spatial pattern (C.Bone, personal communication, 2024, October 3).   


In this tutorial we will be investigating the spatial autocorrelation of two variables gathered from the 2016 Canadian census. Completed every 5 years, the Canadian Census provides a great deal of Statistical Information pertaining to a wide range of topics (Statistics Canada, 2023, August 02). For this tutorial we will be investigating the spatial autocorrelation of Income and Knowledge of French in North Bay, ON.    


Census information is an ideal database to use when attempting to understand the spatial autocorrelation of a region. As indicated by the Statistics Canada website, small geographic areas are mapped in detail allowing every dwelling to be located during the data collection process (Statistics Canada, 2023 October 26). This means that data pertaining to an individual is tied to a geolocation. Another benefit of using census information to determine spatial autocorrelation is that data collection methodologies will be consistent across wide distances. This is ideal in our situation, because we are embarking on the analysis of how attributes change their relationship with one another over space. It would be imperative that the same data collection techniques remain consistent. 

In regards to the use of Income data, it is important to understand that Income data is not collected through the census questionnaire (Statistics Canada, 2023, October 26). Instead, this information is collected from personal income tax and benefits records (Statistics Canada, 2023 October 26). This methodology of collection reduces the response burden and improves data quality (Statistics Canada, 2023 October 26). It is also important to note that Income data is collected for individuals who are 15 years or older and only features those living in private dwellings. The data being used in this tutorial will be a persons annual income in 2016.

In regards to the use of Knowledge of French data, it is important to understand that Knowledge of French refers to the ability of an individual to conduct a conversation in French (Statistics Canada, 2023 October 26). This data is collected through the means of the census questionnaire. Knowledge of a Canadian official language is classified four ways; English Only, French Only, English and French and Neither French or English. For the purposes of this tutorial we will be working with data pertaining to Knowledge of French Only.  

Now that we understand what spatial autocorrelation is and the data we will be using, let us understand how to determine spatial autocorrelation of an area using R Studio. 


## Step 1: Install Packages 

It is important to note that when working within R Studio, there is a large swath of built-in functions available that can be used to accomplish a task. Though, sometimes it is necessary to install external packages to accomplish a particular task. For example, in this particular tutorial we will be creating a map. This can not be accomplished by only using the built-in functions in R Studio alone. In order to accomplish this task, we need to bring in the package ‘tmap’. 

The command to install a package is install.packages( ). The package we would like to install is to be placed inside the brackets with quotation marks. So for example, to install the package ‘tmap’ we would write the code:

install.packages(‘tmap’)

Now that we understand how to install a package. Let’s install the following packages: 
```{r Packages, echo=TRUE, eval=TRUE, message=FALSE, warning=FALSE}
#Install packages:

#install.packages("knitr")
#install.packages("sf")
#install.packages("terra")
#install.packages("tmap")
#install.packages("spdep")
#install.packages("raster")
#install.packages("shinyjs")
#install.packages("e1071")
```


## Step 2: Load in Libraries 

After we have installed a package into RStudio, we cannot use it quite yet. There is one more additional step we have to do. We have to ensure that the packages are set to our library and this is accomplished by loading the packages we installed into the R library. Each package contains a variety of different commands associated with them. Any attempt to run those commands without loading installed packages into the library will result in an error message. 

To load libraries into RStudio we use the command library( ). For example, to load in the package ‘tmap’ into the R library, we would write library(‘tmap’). 

Now that we understand how to load a package into a library, load all packages installed in Step 1 into your library. 

```{r Libraries, echo=TRUE, eval=TRUE, message=FALSE, warning=FALSE }
#Load in libraries:

library('knitr')
library('sf')
library ('terra')
library('tmap')
library('spdep')
library('raster')
library('shinyjs')
library('e1071')
```


## Step 3: Set a Working Directory

One of the most important elements of working in R Studio is file maintenance. R Studio has the ability to call upon files saved to your computer. This presents the opportunity to call upon for example a .csv file and bring it into R Studio to conduct data analysis. In order to do so, we must set a working directory. The working directory is where within your file manager that R Studio will reach to call upon a file. For example, it is commonplace for files to be saved in Documents. 

To accomplish this, you want to copy the path that leads to your desired file. 

Next, you have to set that file to a variable. This will provide ease as it would be cumbersome to type out the file path in its entirety. You can set the the file path to the variable dir. 

Next, to actually set the file as your working directory you can use the command setwd( ).

Finally, to confirm that your file path was saved correctly to your variable, you can use the command getwd( ) to confirm, indeed your working directory is what you set it as. With this particular command, the brackets will be left empty. The response should be the file path you set up with the setwd( ). 

```{r Working Directory, echo=TRUE, eval=TRUE, warning=FALSE}
#Set working directory: 

dir <- "C:/Users/dstrazzanti/Documents"
setwd(dir)
getwd()
```


## Step 4: Read In Files

With our working directory set, we can now call upon files and read them into RStudio. For this tutorial we want to read in a .csv file and a .shp file. 

To accomplish this there are two commands that can complete this task. The command to read in a .csv file is read.csv(“ “). A command to read in a .shp file is st_read (“ “). With this command we want to put the file path that leads to the file in the bracket. After such, distinguish the operation with a variable. Set read.csv(“ “) to csv and st_read(“ “) to shp. 

```{r Read in Files, echo=TRUE, eval=TRUE, warning=FALSE}
#Read in files: 

csv <- read.csv("C:/Users/dstrazzanti/Documents/ucgsJQnBVLvP_data.csv")

shp <- st_read('C:/Users/dstrazzanti/Documents/lda_000a16a_e.shp')
```


## Step 5: Clean Data

## PART A: Column Names 

In the console window. Type in the variable csv. This will pull up the .csv file we called upon in Step 4. You will notice that none of the columns have a column name. In order to alter column names, the first step would be to create a vector listing desired column names. To create a vector we use the command c( ) and place desired column names in the order of how the columns appear. For example, Column 0 should be named ‘GEO UID’ so that will be placed first in the vector command. Column 1 should be named ‘Province code’ so that should be placed second. It may appear confusing as to why the first column is labelled as Column 0 and not Column 1, but this is a common trait when working with data from the Government of Canada. Once the vector is set assign a variable. The column names can then be adjusted with the colnames( ) command.

```{r Clean data pt.a, echo=TRUE, eval=TRUE, warning=FALSE}
#Change column names: 

cols <- c("GEO UID", "Province code", "Province name", "CD code",
        "CD name", "DA name", "Population", "Land area", 
        "Median total income", "Income Sample Size", "French Knowledge", 
        "Language Sample Size")

#Apply names to dataframe: 

colnames(csv) <- cols
```

If you run csv in the console window again, you’ll now find that the columns have the column names you set.

## PART B: Data Adjustment 

As you may have noticed, the first column in csv is a unique geographic identifier. Given we are dealing with spatial autocorrelation, it is imperative we are using geographically sound data. A Geographic Unique Identifier has 8 digits. We can run the following code to remove any unique geographic identifiers that have less than 8 digits which would be poor data.

```{r Clean data pt.b, echo=TRUE, eval=TRUE, warning=FALSE}
#Add column to count number of ID characters: 

csv$len <- nchar(csv$`GEO UID`)

#Remove IDs with less than 8 numbers:

csv_clean <- subset(csv, csv$len == 8)
```
## PART C: Merge Data 

With our data cleaned up, next we want to merge our data with our .shp file we called upon before. Go ahead and enter shp into the Console  to get an understanding what this database is comprised of. In this part we will be merging shp and csv_clean from the previous step. This can be accomplished by using the command merge( ). Make sure to set it to a variable as shown here.   

```{r Clean data pt.c, echo=TRUE, eval=TRUE, warning=FALSE}
#Merge spatial and aspatial data:

census_DAs <- merge(shp, 
                    csv_clean, 
                    by.x = "DAUID", 
                    by.y = "GEO UID", 
                    all.x = TRUE)
```


## Step 6: Pick a Municipality

As mentioned prior, we are investigating the spatial autocorrelation of income and Knowledge of French. Let us select a city we want to investigate using the subset( ) command. For this tutorial we will be using the City of ‘North Bay’. The subset command is used to select particular columns in a dataset. From our new database we created in Part C of Step 5, we want to pull data that is only related to the City of North Bay. In R when you notice $, it is most often related to an action that is calling to pull information. So with the following line of code, we are asking to make a new database using the subset command, get the information from the census_DAs database we created and only pull data related to North Bay in the CMANAME column. Make sure to then save it to the variable Municp.

```{r Municipality, echo=TRUE, eval=TRUE, warning=FALSE}
#Create a subset for North Bay: 

Municp <- subset(census_DAs, census_DAs$CMANAME == "North Bay")
```

Now, go ahead and enter Municp into your console. Notice that the only city under the column CMANAME is North Bay, where previously it listed St.John’s. Mind you, the console can only show you a snippet of the dataset. To confirm if your code was a success and that you are truly only working with data pertaining to North Bay you can use the commans unique(). This will pull only unique items in a column. After running this code you should only receive ‘North Bay’. 

```{r Municipality pt.2, echo=TRUE, eval=TRUE, warning=FALSE}
#Check if working with North Bay data only: 

unique(Municp$CMANAME)
```


## Step 7: Variable Selection 

Next, enter Municp into your Console window again. Notice how each row is tied to a DAUID. A DAUID is a geographic identifier code tied to a unique location. In the Console you will also see the columns French Knowledge and Language Sample Size. This lets us know at a location, how many folks were sampled and of that sample how many speak French. We want this data in a rate so we are able to conduct descriptive statistics on the data. We need to standardize the data given that the sample size varies from DAUID to DAUID. We can do that with the following code: 

```{r French, echo=TRUE, eval=TRUE, warning=FALSE}
#Convert Knowledge of French into a rate: 

Municp$PercFrench <- (Municp$`French Knowledge`/Municp$`Language Sample Size`)*100
```

Take note of the $ symbol again. Notice how in the brackets the dollar sign reads as pulling data from the columns from Municp and dividing it by 100. Yet, there is a $ in the variable. The $ here is not performing a pulling action but rather creating a new column. After you run this code, enter Municp into your Console window again. You’ll notice the last column in the dataset is French Knowledge in a percentage. 

The other variable we will be using is Median Income. Statistics Canada has provided a column with this information so no additional steps need to be taken. Though, it is important to acknowledge that the use of the median value is an appropriate statistic for understanding income as an average would skew the findings given a large pull an outlier can have on the results.


## Step 8: Clean Polygon 

When working with census data it is commonplace that some entries will have missing data. We want to make sure to exclude these from our findings as the inclusion of such data can alter our findings. 

With the upcoming code you'll notice the command which(). This is a selection tool, think of it as reading “in which”. It is used to select something. You will also notice the “!” which is commonly used as an exclusionary tool. The command is.na() will highlight all values that have missing data known as NA. So working inside out, this code reads in the database Municp, focuses on the column ‘Median total income’. Highlight all NA’s in this column and removes them. 

Do the same for the Knowledge of French and make sure to set both to a variable. 

```{r NA Remove, echo=TRUE, eval=TRUE, warning=FALSE}
#Remove Income NA: 

Income_noNA <- Municp[which(!is.na(Municp$`Median total income`)),]

#Remove French NA: 

French_noNA <- Municp[which(!is.na(Municp$Perc)),]
```


## Step 9: Calculate Descriptive Statistics

A good way to get an understanding of your data is to gather some descriptive statistics. mean() will gather the mean, sd() will gather the standard deviation, skewness() will gather the skewness. 

Do this for both Income data and Knowledge of French data. 

```{r DescriptiveStats, echo=TRUE, eval=TRUE, warning=FALSE}
#Calculate descriptive stats for Income: 

meanIncome <- mean(Income_noNA$`Median total income`)
stdevIncome <- sd(Income_noNA$`Median total income`)
skewIncome <- skewness(Income_noNA$`Median total income`)

#Calculate descriptive stats for French: 

meanFrench <- mean(French_noNA$'PercFrench')
stdevFrench <- sd(French_noNA$'PercFrench')
skewFrench <- skewness(French_noNA$'PercFrench')
```


## Step 10: Create a Table

With our descriptive statistics established, next it would be great to view the statistics in a table format. This can be accomplished by first creating a dataframe. This can be achieved with the command data.frame(). The data.frame command can take vectors as their argument. Arguments are what is entered into the brackets. As you can see each vector is equaled to a title. You can see the command round() is being used. This will round the value to two decimal values. That is what the number two is referring to. 

If you enter the variable data in the Counsel window you will find a bare bones table appear. To get a more professional looking table you can use the command kable(). Just make sure to include a title.

```{r Table, echo=TRUE, eval=TRUE, warning=FALSE}
#Create dataframe for table: 

data <- data.frame(Variable = c("Median Income", "French Language/per 100"),
                   Mean = c(round(meanIncome,2), round(meanFrench,2)),
                   StandardDeviation = c(round(stdevIncome,2), round(stdevFrench,2)),
                   Skewness = c(round(skewIncome,2), round(skewFrench,2)))

#Produce table

kable(data, caption = paste0("Median Income and French Knowledge in North Bay, ON ", 2016, ". "))

```


## Step 11: Create a Map

Data cleaned. Municipality chosen. Descriptive statistics calculated, it is now time to understand how this data forms spatially. We can accomplish this with the creation of a map. This can be achieved with the use of the tmap package. The code will take a different form here. You will notice the + sign. Within the package tmaps, there are commands that can be called upon. Those commands, which can take arguments in there brackets, are added together to create a complete map. The commands we will be using from the tmaps package is tm_shape(), tm_polygons(), tm_layout(). For more information about the tmaps package view this website: https://cran.r-project.org/web/packages/tmap/vignettes/tmap-getstarted.html

Have a map completed for both Median Income and French Knowledge. 

The final code will arrange the maps side by side.

```{r StudyArea, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap="North Bay, ON Census dissemination areas showing median total income (left) and percentage of respondants with knowledge of French (right)."}
#Map median Income: 

map_Income <- tm_shape(Income_noNA) + 
              tm_polygons(col = "Median total income", 
                          title = "Median total income", 
                          style = "jenks", 
                          palette = "BuGn", 
                          n = 6,
                          border.alpha = 1,
                          colorNA = "black") +
              tm_layout(legend.position = c("RIGHT", "TOP"))

#Map French Knowledge: 

map_French <- tm_shape(French_noNA) + 
              tm_polygons(col = "PercFrench", 
                          title = "Percentage with \n French Knowledge", 
                          style = "jenks", 
                          palette = "BuGn", 
                          n = 6,
                          border.alpha = 1,
                          colorNA = "black") +
              tm_layout(legend.position = c("RIGHT", "TOP"))

#Print maps side by side:

tmap_arrange(map_Income, map_French, ncol = 2, nrow = 1)
```

## Step 12: Neighbourhood Matrix 

With our maps established, we now want to establish neighbourhoods. Recall spatial autocorrelation is a determination if the relationship between two attributes is strong, weak or random. Ultimately, we are attempting to understand what is the strength of connection between one attribute to another. To distinguish these relationships, positive and negative autocorrelation, we are going to assign different weights to different attributes. The form in which weight distribution is established can be administered with two types of formats. 

The first format is the rook form. This creates a neighbourhood in which every attribute that shares an edge with another attribute will be granted a weight value (Lloyd, 2010). If the attribute does not share an edge, the attribute will receive a weight of 0 (Lloyd, 2010). 

The second format is the queen form. This creates a neighbourhood in which every attribute that shares an edge and/or a corner will be granted a weight value. If the attribute does not  share an edge or corner, they will receive a weight of 0 (Lloyd, 2010). 

In order to apply these formats to our map we can use the command poly2nb(). We want to input our databases under analysis as an argument. The command nb2lines() ties spatial coordinates to the default format which is the queen format. Indicating queen = FALSE will change ploy2nb() to the rook format. The command crs() is pulling in the coordinate reference system to make all of this possible.     

Go head and apply this for both Income and Knowledge of French. 


```{r Neighbours, echo=TRUE, eval=TRUE, warning=FALSE}

#Income Neighbours - Queens weight: 

Income.nb <- poly2nb(Income_noNA)

# Get the coordinates: 

Income.net <- nb2lines(Income.nb, coords=st_coordinates(st_centroid(Income_noNA)))
crs(Income.net) <- crs(Income_noNA)

#Income Neighbours - Rooks weight: 

Income.nb2 <- poly2nb(Income_noNA, queen = FALSE)
Income.net2 <- nb2lines(Income.nb2, coords=st_coordinates(st_centroid(Income_noNA)))
crs(Income.net2) <- crs(Income_noNA)

#French Neighbours - Queens weight: 

French.nb <- poly2nb(French_noNA)
French.net <- nb2lines(French.nb, coords=st_coordinates(st_centroid(French_noNA)))
crs(French.net) <- crs(French_noNA)

#French Neighbours - Rooks weight: 

French.nb2 <- poly2nb(French_noNA, queen = FALSE)
French.net2 <- nb2lines(French.nb2, coords=st_coordinates(st_centroid(French_noNA)))
crs(French.net2) <- crs(French_noNA)
```


## Step 13: Rook Map. Queen map. Combined Map 

Now that we have entered code to construct the queen format and the rook format. Lets examine how this plays out visually. To do so, we can once again use commands apart of the tmap package to construct three maps for Income and three maps for French. One will have the queen format. The other will have the rook format. And the last will be a combination of both.

```{r Neighboursmap, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap="North Bay, ON census dissemination areas showing median total income neighbours queens weight (left)  rooks weight (middle) and the combination of the two (right)."}

#Make queens map for Income: 

IncomeQueen <- tm_shape(Income_noNA) + 
               tm_borders(col='black', lwd = 2) + 
               tm_shape(Income.net) + 
               tm_lines(col='red', lwd = 0.5)
  
#Make rooks map for Income: 

IncomeRook <- tm_shape(Income_noNA) + 
              tm_borders(col='black', lwd = 2) + 
              tm_shape(Income.net2) + 
              tm_lines(col='blue', lwd = 0.5)

#Make combined map for Income: 

IncomeBoth <- tm_shape(Income_noNA) + 
              tm_borders(col='black', lwd = 2) + 
              tm_shape(Income.net) + 
              tm_lines(col='red', lwd = 1) +
              tm_shape(Income.net2) + 
              tm_lines(col='blue', lwd = 1)

#Make queen map for French: 

FrenchQueen <- tm_shape(French_noNA) + 
               tm_borders(col='black', lwd = 2) + 
               tm_shape(French.net) + 
               tm_lines(col='orange', lwd = 0.5)

#Make rook map for French: 

FrenchRook <- tm_shape(French_noNA) + 
              tm_borders(col='black', lwd = 2) + 
              tm_shape(French.net2) + 
              tm_lines(col='purple', lwd = 0.5) 

#Make combined map for French: 

FrenchBoth <- tm_shape(French_noNA) + 
              tm_borders(col='black', lwd = 2) + 
              tm_shape(French.net) + 
              tm_lines(col='orange', lwd = 1) +
              tm_shape(French.net2) + 
              tm_lines(col='purple', lwd = 1)


#Print maps in a three pane figure: 
tmap_arrange(IncomeQueen, IncomeRook, IncomeBoth, ncol = 3, nrow = 1)
```

```{r Neighboursmap pt.2, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap="North Bay, ON census dissemination areas showing French Knowledge queens weight (left)  rooks weight (middle) and the combination of the two (right)."}

tmap_arrange(FrenchQueen, FrenchRook, FrenchBoth, ncol = 3, nrow = 1)
```


## Step 14: Weight Decision

Now that we have been able to accomplish a visual representation of how a rook format appears and how a queen format appears we now need to choose, which format is appropriate for our analysis. You may have notice when examining the ‘Both’ map that there are lines that are representative of a queen format that the rook format is unable to capture. It is important that when we are conducting spatial autocorrelation in a municipal setting that we are not being exclusionary and all possible relationships are being examined. We do not want to pass on the opportunity to note clustering if it is present. For that, for the purposes of this analysis let’s choose the queen format. 

The next decision we have to make is what weight value are we going to assign an attribute that shares an edge or corner. There are two common weight schemes that can be applied. 

The first is the most common which is binary connectivity. This establishes a weighting scheme in which if an attribute (or better known as j) is contiguous with an attribute under analysis (or better known as i) the attribute will be granted a weight of 1 (Rogerson, 2001). If j is not contiguous with i a weight of 0 will be granted (Rogerson, 2001).

The second weighting scheme is a standardized version of binary connectivity (Rogerson, 2001). This establishes a weighting scheme in which the number of attributes that i is contiguous with is divided by the weight value (Rogerson, 2001). So for example, if i has five j’s and the weight is one, you would divided 1 by 5 so that each j gets a weight of 0.2. In essence, the sum of all contiguous attributes are granted a total weight that sums to 1 (Rogerson, 2001) . If not contiguous, an attribute is granted a weight of 0. 

For the purposes of this analysis, we will be applying the standardized version of binary connectivity. We want to ensure that the influence a neighbour has on an attribute is the same across the board. We are not interested in introducing biases into our analysis by establishing that the number of neighbours impact weight. It is important that no matter how many neighbours an attribute has that the weight in the neighbourhood remains consistent. By choosing the standardized version of binary connectivity we can ensure this is accomplished. 

So, how do we put this into practice? We can use the command nb2listw(). We are going to input the variable for the queens format of Income and French as an argument, zero.policy = TRUE, which establishes the format that non contiguous attributes receive a weight of zero and style = ‘W’ is representative of choosing the standardized version of binary connectivity.

```{r Final weights, echo=TRUE, eval=TRUE, warning=FALSE}
#Create Income weights matrix: 

Income.lw <- nb2listw(Income.nb, zero.policy = TRUE, style = "W")

#Create French weights matrix: 

French.lw <- nb2listw(French.nb, zero.policy = TRUE, style = "W")

head(Income.lw[["weights"]])[c(1:3)]
```


## Step 15: Calculate Global Moran's I

So exactly how is the spatial autocorrelation within a geography determined. To uncover this we can use Global Moran’s I. Through the incorporation of geolocations and values associated to such geolocations we can use this inferential statistic to determine what the global spatial autocorrelation is (Grekousis, 2020). Essentially we are attempting to understand the overall pattern of a spatial dataset (Grekousis, 2020). Is the spatial dataset completely random? Is there positive spatial autocorrelation? How about negative spatial autocorrelation. 

To answer this we need to establish an expected value. The expected value will be an indicator as to what a random spatial arrangement would equate to (Grekousis, 2020). After such, we are going to calculate the Moran’s I. The generation of a value that is larger than the expected value would indicate clustering and positive spatial autocorrelation (Grekousis, 2020). The generation of a value that is smaller than the expected value would indicate dispersion and negative spatial autocorrelation (Grekousis, 2020). The generation of a value that is close to the expected value indicates no autocorrelation (Grekousis, 2020).   

Now that we have an understanding as to what we are calculating, let us look at the formula we will be using to generate this calculation: 

$$
I = \frac{\sum_{i=1}^n\sum_{j=1}^nW_{i,j}(x_i - \bar{x})(x_j - \bar{x})}{(\sum_{i=1}^n\sum_{j=1}^nW_{i,j})\sum_{i=1}^n(x_i - \bar{x})^2}
$$

We can calculate the spatial autocorrelation in 5 parts : 

## Part A: 

You’ll notice in the equation there is an $\bar{x}$ variable. This is the global mean, meaning the mean across the entire geography. You may recall in Step 8 we calculated the global mean. For Median Income the global mean was $34 218.08 and Knowledge of French was 25.72%. 

[ritvikmath]. (2020, September 28). Moran’s I: Data science concepts [Video]. YouTube. https://www.youtube.com/watch?v=OJU8GNW9grc&t=333s

## Part B: 

You’ll also notice in the numerator ($x_i$ - $\bar{x}$). This is the difference from the mean. For the example we are working with, $x_i$ would be each region of North Bay, ON. Go ahead and look at our the map we generated in Step 10. Note the boundary lines indicating the different regions of North Bay, ON. Each of those regions have a median total income or percentage with French knowledge. To really tie this together, do you recall the database we created that we named Municp. That is where the values can be found and how the map was created. For example if we enter Muicp into our counsel again, We can see the Median income for region 36675 (one of the regions on the map) is $50,944. What ($x_i$ - $\bar{x}$) is asking is that we subtract the $50 944 from the global mean $34 218.08. This is going to be repeated for every region. 

[ritvikmath]. (2020, September 28). Moran’s I: Data science concepts [Video]. YouTube. https://www.youtube.com/watch?v=OJU8GNW9grc&t=333s

## Part C: 

You’ll also notice in the numerator ($x_j$ - $\bar{x}$). We now have to calculate how do contiguous regions that surround each region vary from the mean. As you recall, we choose a queen format. For each region that received a weighted value we are going to subtract the value of that region from the mean. So, say for example a bordering region of 36675 is 36676. Say the median income for 36676 is $43,687. We are going subtract $43,687 from the global mean. Next, we are going to multiply the 36675 difference from the mean with the 36676 difference from the mean.

If we discover that region 36674 also borders 36675 then we will complete that same calculation. Go back to Step 12. This was the purpose of laying down a queen format. We wanted to understand what regions borders with other regions. Through this we can understand what regions in North Bay, ON do not border with each other, and are then granted a weight of 0. This is what the $W_{i,j}$ in the formula is. If a region does border with another region, it will be multiplied by the weight assigned. If it does not, it will be multiplied by 0 making it equal to 0.  

So each region will go through that individual process of acknowledging its neighbour and multiply its difference from the mean (i) with its neighbour(s) difference from the mean (j). Once each region has completed this task, all the values generated will be summed together. That is what the calculation in the numerator is. 

[ritvikmath]. (2020, September 28). Moran’s I: Data science concepts [Video]. YouTube. https://www.youtube.com/watch?v=OJU8GNW9grc&t=333s

## Part D

Recall in Step B we gather the value for each region and subtract it from the mean. We are going to do that again, but this time, we are going to square each value. After we have subtracted the global mean from each region and then squared that value we are going to calculate the sum of all those values. This is the total variation in all of North Bay. This is what is being calculated in the denominator. 

[ritvikmath]. (2020, September 28). Moran’s I: Data science concepts [Video]. YouTube. https://www.youtube.com/watch?v=OJU8GNW9grc&t=333s

## Part E 

The final step is to divide the value generated in Step C by the value generated in Step D. 

This is will give you the spatial autocorrelation value. 

[ritvikmath]. (2020, September 28). Moran’s I: Data science concepts [Video]. YouTube. https://www.youtube.com/watch?v=OJU8GNW9grc&t=333s




Now that we understand how Global Moran’s I is calculated, let’s see how this translate over into RStudio. 

We can use the command moran.test() to complete this calculation. All we need to enter is our variable, weight matrix and that zero.policy is TRUE, meaning that weights of zero will not be calculated. Lets apply that to both Income and French. 

```{r Global Morans I, echo=TRUE, eval=TRUE, warning=FALSE}
#Calculate Global Moran's I for Income: 

miIncome <- moran.test(Income_noNA$`Median total income`, Income.lw, zero.policy = TRUE)

#Extract Global Moran's I results for Income: 

mIIncome <- miIncome$estimate[[1]]
eIIncome <- miIncome$estimate[[2]]
varIncome <- miIncome$estimate[[3]]

#Calculate Global Moran's I for French: 

miFrench <- moran.test(French_noNA$PercFrench, French.lw, zero.policy = TRUE)

#Extract Global Moran's I results for French: 

mIFrench <- miFrench$estimate[[1]]
eIFrench <- miFrench$estimate[[2]]
varFrench <- miFrench$estimate[[3]]
```

For Median Income, the Moran I statistic that is generated is 0.5262. Moran’s I values range from -1.0 to 1.0 (Grekousis, 2020). A score above 0.3 suggest a relatively strong positive spatial autocorrelation, whereas a score below -0.3 indicates a relatively strong negative autocorrelation (Grekousis, 2020). So for our situation, Median Income in North Bay, ON is showing a strong positive correlation. 

For Knowledge of French the Moran’s I statistic is 0.2718. This falls below 0.3 which would suggest Knowledge of French has a relatively weak positive spatial autocorrelation.    

You’ll also notice that the moran.test() generates the expected value. 

Remember: 

If the Moran’s I value is significantly larger than the expected value this indicates positive spatial autocorrelation is present and that there is clustering (Grekousis, 2020). If the Moran’s I value is significantly smaller than the expected value this indicates negative spatial autocorrelation is present and there is dispersion (Grekousis, 2020). If the Moran’s I value is close to the expected value, this indicates that there is no spatial autocorrelation and the spatial arrangement  is completely random (Grekousis, 2020).   

The expected value for Income is -0.0077, so the Moran’s I is significantly larger suggesting clustering. 

The expected value for French Knowledge is -0.0077, so the Moran’s I is significantly larger suggesting clustering.  


## Step 16: Statistical Test 

We can determine if the Moran I value is statistically significant. This can be accomplished by subtracting the variable under analysis by the expected value and divide that by the square root of the variance of the variable. Go ahead and apply that to both income and Knowledge of French. 

```{r Global Morans Range, echo=TRUE, eval=TRUE, warning=FALSE}

#Calculate range for Global Moran's I: 

moran.range <- function(lw) {
  wmat <- listw2mat(lw)
  return(range(eigen((wmat + t(wmat))/2)$values))
}

#Calculate range for Income variable: 

range <- moran.range(Income.lw)
minRange <- range[1]
maxRange <- range[2]

#Calculate z-test for Income: 

zIncome <- (mIIncome - eIIncome) / (sqrt(varIncome))

mIIncome <- miIncome$estimate[[1]]
eIIncome <- miIncome$estimate[[2]]
varIncome <-miIncome$estimate[[3]]

#Calculate z-test for French: 

zFrench <- (mIFrench - eIFrench) / (sqrt(varFrench))

#Extract Global Moran's I results for French: 

mIFrench <- miFrench$estimate[[1]]
eIFrench <- miFrench$estimate[[2]]
varFrench <- miFrench$estimate[[3]]
```

We are now going to have to decided as to whether the spatial arrangement is a statistically significant positive or negative autocorrelation. The tool to use to make this decision will be the p-value and a z-score. If the p-value < 0.05, this would prompt a rejection of the null hypothesis of the arrangement being random and accept that spatial autocorrelation is occurring (Grekousis, 2020). In this situation if the z-value is positive, positive spatial autocorrelation is occurring, meaning clustering is statistically significant (Grekousis, 2020). If the z-value is negative, there is a negative positive spatial autocorrelation, meaning dispersion is statistically significant (Grekousis, 2020).

The z-score for Income is 10.27. The p-value is  2.2e16. This indicates that we can reject the null hypothesis that Median Income in North Bay, ON is randomly distributed. Rather we can accept that spatial autocorrelation for Median Income is statistically significant in North Bay, ON. Given that our z-score is greater than 1.96 and is positive indicates that the clustering that is occurring around Median Income is statistically significant. 

The z-score for French Knowledge is 5.48. The p-value is  2.064e-08. This indicates that we can reject the null hypothesis that Knowledge of French in North Bay, ON is randomly distributed. Rather we can accept that spatial autocorrelation for Knowledge of French is statistically significant in North Bay, ON. Given that our z-score is greater than 1.96 and is positive indicates that the clustering that is occurring around Knowledge of French is statistically significant.

## Step 17: Calculate Local Moran's I

It is important to note that Step 15 calculated the Global Moran’s I. This is a global statistic that determines if there is a spatial pattern in a spatial dataset (Grekousis, 2020). We also have the opportunity to examine what is the spatial pattern that is occurring between in each attribute, or in our situation what is the pattern that is occurring between each region in North Bay, ON. This is known as local spatial autocorrelation (Grekousis, 2020). The reason why we conduct such a test is that despite clustering occurring at a local level, these may not be revealed through a Global Moran’s I test alone (Grekousis, 2020). We are attempting to uncover what local trends are occurring that are hidden from us when we only look at the global level.

Once again we are going compare results from the Local Moran’s I to a expected value (Grekousis, 2020). To remind you, the expected value is the value if the variable under examination were in a state of complete spatial randomness.

If the Local Moran's I value is positive and significantly larger than the expected value, this would suggest clustering and a positive spatial autocorrelation (Grekousis, 2020). If the Local Moran’s I value is negative and significantly smaller than the expected value, this would suggest dispersion and a negative spatial autocorrelation (Grekousis, 2020). If the Local Moran’s I value is close to the expected value, this would suggest there is no spatial autocorrelation (Grekousis, 2020).

Just like with Global Moran’s I we would then determine if our findings our statistically significant. If the p-value is greater than 0.05, this would indicate that the spatial arrangement is not statistically significant and we cannot reject the null hypothesis indicating that the attributes are is a state of spatial randomness (Grekousis, 2020). If the p-value is less than 0.05, this would indicate that the spatial arrangement is statistically significant and we can reject the null hypothesis (Grekousis, 2020). 
A positive z-value would indicate there is positive spatial autocorrelation and clustering is occurring (Grekousis, 2020). A negative z-value would indicate there is negative spatial autocorrelation and spatial outliers may exist (Grekousis, 2020). 

A high positive z-score indicates that surrounding attributes carry similar values (Grekousis, 2020). This would suggest a High-High clustering (Grekousis, 2020). This means that a region with a high value is surrounded by other regions with a high value (Grekousis, 2020). If the values were low, this would suggest a Low-Low clustering (Grekousis, 2020). This means that a region with a low value is surrounded by other regions with low values (Grekousis, 2020). 

A low negative z-score indicates that attributes that are close by are not similar and potential spatial outliers (Grekousis, 2020). This would suggest a Low-High arrangement (Grekousis, 2020). This means that if a region has a low value, it is surrounded by regions that have high values (Grekousis, 2020). If the region has a high values and is surrounded by regions that have low values, this would suggest a High- Low arrangement (Grekousis, 2020). 

Once again, we can accomplish the calculation of this test statistic, using RStudio. We can use the common localmoran() and input the variable and the weight matrix. Go ahead and do this for both Income and Knowledge of French. 

We also want to add some new columns to our Income_noNA and French_noNa database we were working with previously. Some the columns we want to include are expected value, variance, z-value and p-value of the Local Moran’s I value


```{r Local Morans I, echo=TRUE, eval=TRUE, warning=FALSE}
#Calculate LISA test for Income: 

lisa.testIncome <- localmoran(Income_noNA$`Median total income`, Income.lw)

#Extract LISA test results for Income: 

Income_noNA$Ii <- lisa.testIncome[,1]
Income_noNA$E.Ii<- lisa.testIncome[,2]
Income_noNA$Var.Ii<- lisa.testIncome[,3]
Income_noNA$Z.Ii<- lisa.testIncome[,4]
Income_noNA$P<- lisa.testIncome[,5]

#Calculate LISA test for Income:

lisa.testFrench <- localmoran(French_noNA$PercFrench, French.lw)

#Extract LISA test results for Income: 

French_noNA$Ii <- lisa.testFrench [,1]
French_noNA$E.Ii<- lisa.testFrench [,2]
French_noNA$Var.Ii<- lisa.testFrench [,3]
French_noNA$Z.Ii<- lisa.testFrench [,4]
French_noNA$P<- lisa.testFrench [,5]
```


## Step 18: Map Local Moran's I

Once again, we are going to call upon the tmap package to map what is occurring on a local level in North Bay, ON. 

Go ahead and follow the code. Take a look at the two maps that get generated. Using the description in Step 16 you can analyze what is occurring. It appears with Median Income there is a High-High arrangement occurring and an occurrence of Low-High. The same appears to be true with Knowledge of French.

```{r MappingLocalMoransI, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap="North Bay, ON Census dissemination areas showing LISA z-scores for median total income (left) and percentage of respondants with knowledge of french (right)."}
#Map LISA z-scores for Income:

map_LISA_Income <- tm_shape(Income_noNA) +
  tm_polygons(col = "Z.Ii",
              title = "Local Moran's I Z-Scores",
              style = "fixed",
              border.alpha = 0.1,
              midpoint = NA,
              colorNA = NULL,
              breaks = c(min(Income_noNA$Z.Ii),-1.96,1.96,max(Income_noNA$Z.Ii)),
              palette = "-RdBu", n = 3)+
  tm_compass(position=c("left", "top"))+
  tm_scale_bar(position=c("left", "bottom"))+
  tm_legend(position = c("right", "top"))

#Map LISA z-scores for French: 

map_LISA_French <- tm_shape(French_noNA) +
  tm_polygons(col = "Z.Ii",
              title = "Local Moran's I Z-Scores",
              style = "fixed",
              border.alpha = 0.1,
              midpoint = NA,
              colorNA = NULL,
              breaks = c(min(French_noNA$Z.Ii),-1.96,1.96,max(French_noNA$Z.Ii)),
              palette = "-RdBu", n = 3)+
  tm_compass(position=c("left", "top"))+
  tm_scale_bar(position=c("left", "bottom"))+
  tm_legend(position = c("right", "top"))

#Plot maps in a 2 pane figure: 

tmap_arrange(map_LISA_Income, map_LISA_French, ncol = 2, nrow = 1)
```


## Step 19: Moran’s I Scatterplot

Instead of using a map, we can also understand how the spatial autocorrelation is being applied spatially with the use of a scatterplot. This methodology prompts the understanding how similar an attribute is to its neighbour (Grekousis, 2020). What the scatterplot is also able to determine is which type of spatial autocorrelation exists based on where a point is, and of course in our example each point is representative of a region in North Bay, ON. 

A point in Quadrant 1 would suggest that a region is in a High-High arrangement (Grekousis, 2020). This means that the value of the region is high, and the regions it is surround by also have a high value (Grekousis, 2020). Points in this Quadrant suggest a they carry a positive spatial autocorrelation (Grekousis, 2020).

A point in Quadrant 3 would suggest that a region is in a Low- Low arrangement (Grekousis, 2020). This means that the values of the region is low, an the regions it is surrounded by also have a low value (Grekousis, 2020). Points in this Quadrant suggest that they carry a positive spatial autocorrelation (Grekousis, 2020). 

A point in Quadrant 4 would suggest that a region is in a Low-High arrangement (Grekousis, 2020). This means that the value of the region is low, and the regions it is surrounded by have a high value (Grekousis, 2020). Points in this Quadrant suggest that they carry a negative spatial autocorrelation and the presence of an outlier (Grekousis, 2020). 

A point in Quadrant 3 would suggest that a region is in a High-Low arrangement (Grekousis, 2020). This means that the value of the region is high, and the regions it is surrounded by have a low value (Grekousis, 2020). Points in this Quadrant suggest that they carry a negative spatial autocorrelation and the presence of an outlier (Grekousis, 2020). 

The creation of a Moran I’s scatterplot in RStudio will also include a regression line. The slope of that regression line based on the Moran I’s value (Grekousis, 2020). This indicates that if a dot is close to the line, it would suggest that the attribute the dot is representing is close to the general trend of the overall spatial autocorrelation (Grekousis, 2020). 

It can also be stated that dots that appear as diamonds are considered statistically significant (C.Bone, personal communication, 2024, October 10). 

To create the scatterplot we can use the commmand moran.plot().Make one for both Income and French as followed:  

```{r MoransIScatter, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap= "Moran's I scatter plot for median total income."}
#Create Moran's I scatter plot for Income: 

moran.plot(Income_noNA$`Median total income`, 
           Income.lw, 
           zero.policy=TRUE, 
           spChk=NULL, 
           labels=NULL, 
           xlab="Median Total Income ($)", 
           ylab="Spatially Lagged Median Total Income ($)", quiet=NULL)
```


```{r MoransIScatter2, echo=TRUE, eval=TRUE, warning=FALSE, fig.cap= "Moran's I scatter plot for percentage of respondants with knowledge of french."}
#Create Moran's I scatter plot for French: 

moran.plot(French_noNA$PercFrench, 
           French.lw, 
           zero.policy=TRUE, 
           spChk=NULL, 
           labels=NULL, 
           xlab="Respondants with knowledge of French (%)", 
           ylab="Spatially Lagged knowledge of French (%)", 
           quiet=NULL)
```


## Step 20: Reflection 

Thank you for taking the time to go through this tutorial in order to understand spatial autocorrelation and how to apply it in RStudio. In Step 15 we were able to determine that the Median Income in North Bay, ON is not randomly distributed. We decided that clustering of Median Income is statistically significant. This would suggest that, if you were to visit to North Bay, ON there would be occurrences present in which neighbouring regions share the same Median Income. 

It was also uncovered in Step 15 that Knowledge of French in North Bay, ON is also not randomly distributed. We decided that clustering of French Knowledge is statistically significant. This would suggest that, if you were to visit North Bay, ON there would be occurrences in which regions with Knowledge of French would be close to other regions with Knowledge of French.Parles-tu français?   

In Step 18 we were able to construct a Moran’s I Scatterplot and we discovered that with Income there is a high occurrence of regions that are situated in a High-High arrangement that are statistically significant. We also noted that regions situated closer to the regression line suggest it is line with the overall spatial autocorrelation. This would suggest that there is a great deal of regions in North Bay, ON that have a high medium income and are surrounded by other regions that also have a high medium income 

Examination of the Moran’s I Scatterplot for Knowledge of French revealed a slightly different spatial arrangement. As with Median Income there were several regions that were in a High-High arrangement that were statistically significant. This would suggest that neighbourhoods that had a high knowledge of French were close to each other. But, the opposite was true as well. We saw that several regions exhibited a Low-Low arrangement that were statistically significant. This would suggest that regions that have a low value of Knowledge of French are close to other regions that have a low value of French. What we can gather from this is that the French community are close together in North Bay, ON and the non-French community (presumably English) are close together. 


## References

Grekousis, G. (2020). Spatial analysis methods and practice; Describe, explore, explain through GIS. Cambridge University Press. 

Laposa, S., & Thrall, G. (2004). Mass appraisal and noise: the use of lifestyle segmentation profiles to define neighbourhoods for hedonic housing price mass appraisal models. In J. Stillwell & G. Clarke (Eds.), Applied GIS and spatial analysis (1st ed., pp. 77 - 85). John Wiley & Sons Inc.  

Lloyd D. C. (2010). Local models for spatial analysis. CRC Press

O’ Sullivan, D., & Unwin, D.J. (2003). Geographic information analysis. John Wiley & Sons Inc. 

Rogerson, P.A. (2001). Statistical methods for geography. SAGE Publications. 

Statistics Canada (2023, August 2). Census Learning Centre.  https://www12.statcan.gc.ca/census-recensement/training-formation/index-eng.cfm#producttable
 
Statistics Canada (2023, October 26). Introduction to income data. https://www12.statcan.gc.ca/census-recensement/training-formation/videos/Introduction-to-income-data.htm

Statistics Canada (2023, October 26). Knowledge of official and non-official languages.  
https://www12.statcan.gc.ca/census-recensement/training-formation/videos/Knowledge-of-official-and-non-official-languages.htm

Statistics Canada (2023, October 26). Role of geography. 
https://www12.statcan.gc.ca/census-recensement/training-formation/videos/Role-of-geography.htm

