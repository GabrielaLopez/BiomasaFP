<!--

        %\VignetteEngine{knitr::rmarkdown}
        %\VignetteIndexEntry{An Introduction to the BiomasaFP Package}
       
-->
        title: "BiomasaFP Introduction Vignette"
        author: "Gabriela Lopez, Martin Sullivan, Tim Baker"
        date: "Sunday, February 28, 2015"
        output: html_document
---

**Introduction**

The BiomasaFP R package is designed to allow ForestPlots.net users to calculate plot-level summary statistics such as aboveground biomass (AGB) and stem density from the [Forest Plots database] [http://www.forestplots.net/]. It also provides functions to produce standardised and cleaned plot data to facilitate any type of analysis using your data.

---

**Input data**

Three files, downloaded from ForestPlots.net are needed as input data:  
a) Individuals csv file from the Advanced Search 
b) Wood density file  from the query library  
c) Plot View metadata file from the query library  

The three files should correspond to the same Plot Views.

These files are read into R using `mergefp`, which merges the files into a standard format and performs some basic cleaning:

```
library(BiomasaFP)
# reads, cleans and merges the Census data file, metadata file and wd file.
mergedCensus <- mergefp('TestDataset.csv','Metadata10nov.csv','wd10nov.csv' )
```

`mergefp` can also be used to merge and clean these three datasets when the have already been read into R. `mergefp` automatically checks whether each argument passed to it is a `data.frame` object, so you **do not** need to specify whether your arguments are dataframes or file paths. The dataset returned by `mergefp` is used as input in many of the other functions in *BiomasaFP*.


-----

**Producing summary statistics**

Once you have obtained your merged dataset using `mergefp`, you can calculate a number of summary statistics at Plot View level:  
- The coordinates, dates of the first and last census, and number of censuses (`SummaryPlotviews`).
- The number of species and individuals in each family in your data (`SummaryFamilies`).
- Total AGB in each census (`SummaryAGB`).
- Change in AGB between the first and last census (`AGBch`).


These functions return dataframes indexed by PlotView ID (and Census number, where summary statistics are calculated for each census).

The three functions that calculate AGB summary statistics (`SummaryAGB`,`AGBch` and `LastAGB`) require an equation to estimate AGB from tree diameter to be specified. Seven allometric equations are included in *BiomasaFP*; five equations from Chave et al. 2005: `AGBChv05DH` (dry forest equation including tree height), `AGBChv05M` (moist forest equation based on tree diameter), `AGBChv05MH` (moist forest equation including tree height),`AGBChv05W` (wet forest equation based on tree diameter), and `AGBChv05WH` (wet forest equation including tree height) and the Chave et al. (2014) pantropical model (model 4, 'AGBChv14'). Where tree height is used in an equation, it is estimated based on tree diameter, using region-specific parameters for a Weibull model of the height/diameter relationship (Feldpaush et al., 2011). 

```
#Estimate total AGB per Plot View in each census
#Use Chave 2005 moist forest equation without height
AGB.sum<-SummaryAGB(mergedCensus,AGBChv05M)
head(AGB.sum)
#Repeat using Chave 2005 moist forest equation with height
AGB.sum.height<-SummaryAGB(mergedCensus,AGBChv05MH)
head(AGB.sum.height)
```

By default, these three functions use DBH4, but this can be set by the user to be any one of DBH1, DBH2, DBH3 or DBH4.

```
#Default, uses DBH4
AGB.Last<-LastAGB(mergedCensus,AGBChv05M)
head(AGB.Last)
#Use DBH3 instead
AGB.Last.dbh3<-LastAGB(mergedCensus,AGBChv05M,"DBH3")
head(AGB.Last.dbh3)
```
----
**Estimating tree level AGB**

The allometric equations called by `SummaryAGB`,`AGBch` and `LastAGB` can also be accessed individually, and return estimates of AGB and, where height is used in the equation, the estimated height for each tree in each census. Again, these functions use DBH4 by default, but this can be changed by the user.

```
#Use DBH4 (default)
tree.AGB<-AGBChv05M(mergedCensus)
head(tree.AGB)
#Use DBH3
tree.AGB.dbh3<-AGBChv05M(mergedCensus,"DBH3")
head(tree.AGB.dbh3)
```

**Citation**

If you use this package, please cite as:

Lopez-Gonzalez G, Sullivan M and Baker TR (2015). BiomasaFP: Estimate biomass for data downloaded from ForestPlots.net. R package version 1.0.

-----
**References**

**Database**

Lopez-Gonzalez G, Lewis SL, Burkitt M, Baker TR and Phillips OL (2009) ForestPlots.net Database. www.forestplots.net. Date of extraction *[dd,mm,yy]* 

Lopez-Gonzalez G, Lewis SL, Burkitt M and Phillips OL (2011) ForestPlots.net: a web application and research tool to manage and analyse tropical forest plot data. Journal of Vegetation Science 22: 610-613. doi: 10.1111/j.1654-1103.2011.01312.x

**Biomass and height estimates**

Chave C, Andalo C, Brown S, et al. (2005) Tree allometry and improved estimation of carbon stocks and balance in tropical forests. Oecologia 145 (1):87-99. doi:10.1007/s00442-005-0100-x.

Chave J, Rejou-Mechain M, Burquez A et al. (2014) Improved allometric models to estimate the aboveground biomass of tropical trees. Global Change Biology 20: 3177-3190. doi: 10.1111/gcb.12629

Feldpausch TR, Banin L, Phillips OL, Baker TR, Lewis SL et al. (2011) Height-diameter allometry of tropical forest trees. Biogeosciences 8 (5):1081-1106. doi:10.5194/bg-8-1081-2011.

**Wood Density**

Chave J, Coomes DA, Jansen S, Lewis SL, Swenson NG, Zanne AE. (2009) Towards a worldwide wood economics spectrum. Ecology Letters 12(4): 351-366. http://dx.doi.org/10.1111/j.1461-0248.2009.01285.x

Zanne AE, Lopez-Gonzalez G, Coomes DA, Ilic J, Jansen S, Lewis SL, Miller RB, Swenson NG, Wiemann MC, Chave J. (2009) Data from: Towards a worldwide wood economics spectrum. Dryad Digital Repository. http://dx.doi.org/10.5061/dryad.234
