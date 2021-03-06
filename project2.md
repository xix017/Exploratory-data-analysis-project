Project 2
======================================

#Introduction

Fine particulate matter (PM2.5) is an ambient air pollutant for which there is strong evidence that it is harmful to human health. In the United States, the Environmental Protection Agency (EPA) is tasked with setting national ambient air quality standards for fine PM and for tracking the emissions of this pollutant into the atmosphere. Approximatly every 3 years, the EPA releases its database on emissions of PM2.5. This database is known as the National Emissions Inventory (NEI). You can read more information about the NEI at the EPA National Emissions Inventory web site.

For each year and for each type of PM source, the NEI records how many tons of PM2.5 were emitted from that source over the course of the entire year. The data that you will use for this assignment are for 1999, 2002, 2005, and 2008.

#Data

The zip file contains two files:

PM2.5 Emissions Data (summarySCC_PM25.rds): This file contains a data frame with all of the PM2.5 emissions data for 1999, 2002, 2005, and 2008. For each year, the table contains number of tons of PM2.5 emitted from a specific type of source for the entire year. Here are the first few rows.

- fips: A five-digit number (represented as a string) indicating the 
U.S. county

- SCC: The name of the source as indicated by a digit string 

- Pollutant: A string indicating the pollutant

- Emissions: Amount of PM2.5 emitted, in tons

- type: The type of source (point, non-point, on-road, or non-road)

- year: The year of emissions recorded

Source Classification Code Table: 
This table provides a mapping from the SCC digit strings int he Emissions table to the actual name of the PM2.5 source. The sources are categorized in a few different ways from more general to more specific and you may choose to explore whatever categories you think are most useful. For example, source "10100101" is known as "Ext Comb /Electric Gen /Anthracite Coal /Pulverized Coal".



```r
download.file("http://d396qusza40orc.cloudfront.net/exdata%2Fdata%2FNEI_data.zip","data.zip")
unzip("data.zip")
NEI <- readRDS("summarySCC_PM25.rds")
SCC <- readRDS("Source_Classification_Code.rds")
```

###Question 1

Have total emissions from PM2.5 decreased in the United States from 1999 to 2008? Using the base plotting system, make a plot showing the total PM2.5 emission from all sources for each of the years 1999, 2002, 2005, and 2008.


```r
emission = with(NEI,tapply(Emissions,year,sum))
plot(names(emission),emission,
     xlab = "year",ylab = expression("Total Emissions, PM"[2.5]),
     main = "Total PM2.5 emission from 1999 to 2009" )
text(as.numeric(names(emission))+1,emission+1,names(emission))
```

![plot of chunk plot1](figure/plot1.png) 

As we can see, the total emissions of PM2.5 from all sources arcoss USA
decreased from 1999 to 2008.


###Question 2

Have total emissions from PM2.5 decreased in the Baltimore City, Maryland (fips == "24510") from 1999 to 2008? Use the base plotting system to make a plot answering this question.


```r
emission2 = with(NEI[NEI$fips=="24510",],tapply(Emissions,year,sum))
plot(names(emission2),emission2,
     xlab = "year",ylab = expression("Total Emissions,PM"[2.5]),
     main = "Total PM2.5 emission in Baltimore City from 1999 to 2009" )
text(as.numeric(names(emission2))+1,emission2+1,names(emission2))
```

![plot of chunk plot2](figure/plot2.png) 

From the figure, we can tell that the emission of PM2.5 in Baltimore City deCreased from 1999 to 2002, but then raised in the following 3 year. After that, the emission deceased dramatically till 2008.


###Question 3

Of the four types of sources indicated by the type (point, nonpoint, onroad, nonroad) variable, which of these four sources have seen decreases in emissions from 1999-2008 for Baltimore City? Which have seen increases in emissions from 1999-2008? Use the ggplot2 plotting system to make a plot answer this question.


```r
library(ggplot2)
emission3 = xtabs(Emissions ~ type+year,data = NEI[NEI$fips=="24510",])
emission3 = data.frame(emission3)
colnames(emission3) [3] = "Emissions"
qplot(year, Emissions, data = emission3, group = type, 
      color = type, geom = c("point", "line"), 
      main = "Total Emissions in Baltimore City by Type of Pollutant")
```

![plot of chunk plot3](figure/plot3.png) 

In terms of diffente type of emission sources of PM2.5 in B.C:

- NONPOINT: is the most serious PM.5 emission source, although the amount of its emission decreased from 1999 to 2002 and then stay stable inthe following 3 years and continued to drop from 2005 to 2008.
- ON-ROAD and NON-ROAD: followed similar trend as NONPOINT: decreased from 1999 to 2002 and stay stable from 2002 to 2005. NON-ROAD droped dramatically from 2005 to 2008 while the level of decrease is slight in terms of ON-ROAD.
-POINT: is the only one that owns a period of increasing emission.The PM2.5 emission by POINT increased significantly from 1999 to 2005.After reaching its peak in 2005, it decreased obviously in the following 3 years.


###Question 4

Across the United States, how have emissions from coal combustion-related sources changed from 1999-2008?


```r
index = grep("coal",SCC$Short.Name,ignore.case = TRUE)
scc = SCC$SCC[index]
NEI$SCC = as.factor(NEI$SCC)
#SubNEI = NEI[na.omit(match(scc,NEI$SCC)),]
SubNEI = NEI[NEI$SCC %in% scc,]
emission4 = with(SubNEI,tapply(Emissions,year,sum))
plot(names(emission4),emission4,type="l", col = "red",
     xlab = "year",ylab = expression("Total Emissions,PM"[2.5]),
     main = "Total PM2.5 emission from coal combustion-related sources from 1999 to 2009" )
text(as.numeric(names(emission4))+1,emission4+1,names(emission4))
```

![plot of chunk plot4](figure/plot4.png) 

```r
emission4
```

```
##   1999   2002   2005   2008 
## 602624 564940 569655 358084
```

The PM2.5 emission from coal combustion-related sources droped from 1999 to 2002 and then raised slightly from 2002 to 2005. And then it drcreased dramatically from 2005 to 2008.


###Question 5

How have emissions from motor vehicle sources changed from 1999-2008 in Baltimore City?


```r
plot(names(emission5),emission5,type="l", col = "red",
     xlab = "year",ylab = expression("Total Emissions,PM"[2.5]),
     main = "Total PM2.5 emission in Baltimore City from motor vehicle sources from 1999 to 2008" )
```

![plot of chunk plot](figure/plot.png) 

```
##    1999    2002    2005    2008 
##  0.5600 10.5184 10.2241  0.4772
```

The PM2.5 emission in Baltimore City from motor vehicle source raised
dramatically from 1999 to 2002 and then droped slightly from 2002 to
2005. After that, the emission decreased obviously from 2005 to 2008.


###Question 6

Compare emissions from motor vehicle sources in Baltimore City with emissions from motor vehicle sources in Los Angeles County, California (fips == "06037"). Which city has seen greater changes over time in motor vehicle emissions?


```r
SubNEI3 = SubNEI2[SubNEI2$fips=="24510"|SubNEI2$fips=="06037",]

emission6 = xtabs(Emissions ~ year+fips,data = SubNEI3)
emission6 = data.frame(emission6)
emission6$fips = as.character(emission6$fips)
emission6$fips[which(emission6$fips=="06037")] = "Los Angels"
emission6$fips[which(emission6$fips=="24510")] = "Baltimore City"
emission6$fips = as.factor(emission6$fips)
colnames(emission6) [3] = "Emissions"
qplot(year, Emissions, data = emission6, group = fips, 
      color = fips, geom = c("point", "line"), 
      main = "Total Emissions from motor vehicle source in BC vs LA")
```

![plot of chunk plot6](figure/plot6.png) 

Emission from motor vehicle source form 1999 to 2008 in L.A is much 
higher than that in B.C. In L.A, the emission from this source grew
stably from 1999 to 2005 and then decreased a little from 2005 to 2008

In B.C, the emssion from this source frist grew stably from 1999 to 
2002 and then stay stable from 2002 to 2005. After that, it decreased
at from 2005 to 200 almost at the same speed as it grew from 1999 to
2002.
