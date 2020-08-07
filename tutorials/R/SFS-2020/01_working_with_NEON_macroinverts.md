---
syncID: e0778998a7ea4b7c93da38a364d12a65
title: "Explore and work with NEON biodiversity data from aquatic ecosystems"
description: "Download and explore NEON macroinvertebrate data. This includes instruction on how to convert to long and wide tables, as well as an exploration of alpha, beta, and gamma diversity from Jost (2007)."
dateCreated: 2020-06-22
authors: Eric R. Sokol
contributors: Donal O'Leary
estimatedTime: 1 Hour
packagesLibraries: tidyverse, neonUtilities, vegan, vegetarian
topics:
languagesTool: R
dataProduct:
code1: R/SFS-2020/01_working_with_NEON_macroinverts.R
tutorialSeries: 
urlTitle: aquatic-diversity-macroinvertebrates
---

<div id="ds-objectives" markdown="1">

## Learning Objectives 
After completing this tutorial you will be able to: 

* Download NEON macroinvertebrate data.
* Organize those data into long and wide tables.
* Calculate alpha, beta, and gamma diversity following Jost (2007).

## Things You’ll Need To Complete This Tutorial

### R Programming Language
You will need a current version of R to complete this tutorial. We also recommend 
the RStudio IDE to work with R. 

### R Packages to Install
Prior to starting the tutorial ensure that the following packages are installed. 

* **tidyverse:** `install.packages("tidyverse")`
* **neonUtilities:** `install.packages("neonUtilities")`
* **vegan:** `install.packages("vegan")`
* **vegetarian:** `install.packages("vegetarian")`

<a href="/packages-in-r" target="_blank"> More on Packages in R </a>– Adapted from Software Carpentry.

</div>

## Introduction
Biodiversity is an popular topic within ecology, but quantifying and describing biodiversity precisely can be elusive. In this tutorial, we will describe many of the aspects of biodiversity using NEON's <a href="https://data.neonscience.org/data-products/DP1.20120.001">Macroinvertebrate Collection data</a>.

This tutorial was prepared for the <a href="https://freshwater-science.org/sfs-summer-science"> Society for Freshwater Science 2020 "Summer of Science" </a> program.

## Load Libraries and Prepare Workspace
First, we will load all necessary libraries into our R environment. If you have not already installed these libraries, please see the 'R Packages to Install' section above.

There are also two optional sections in this code chunk: clearing your environment, and loading your NEON API token. Clearning out your environment will erase _all_ of the variables and data that are currently loaded in your R session. This is a good practice for many reasons, but only do this if you are completely sure that you won't be losing any important information! Secondly, your NEON API token will allow you increased download speeds, and helps NEON __anonymously__ track data usage statistics, which helps us optimize our data delivery platforms, and informs our monthly and annual reporting to our funding agency, the National Science Foundation. Please consider signing up for a NEON data user account, and using your token <a href="https://www.neonscience.org/neon-api-tokens-tutorial">as described in this tutorial here</a>.


    # clean out workspace
    
    #rm(list = ls()) # OPTIONAL - clear out your environment
    #gc()            # Uncomment these lines if desired
    
    # load libraries 
    library(tidyverse)
    library(neonUtilities)
    
    
    # source .r file with my NEON_TOKEN
    # source("my_neon_token.R") # OPTIONAL - load NEON token
    # See: https://www.neonscience.org/neon-api-tokens-tutorial

## Download NEON Macroinvertebrate Data
Now that the workspace is prepared, we will download NEON macroinvertebrate data using the neonUtilities function `loadByProduct()`.


    # Macroinvert dpid
    my_dpid <- 'DP1.20120.001'
    
    # list of sites
    my_site_list <- c('ARIK', 'POSE', 'MAYF')
    
    # get all tables for these sites from the API -- takes < 1 minute
    all_tabs_inv <- neonUtilities::loadByProduct(
      dpID = my_dpid,
      site = my_site_list,
      #token = NEON_TOKEN, #Uncomment to use your token
      check.size = F)

    ## Finding available files
    ##   |                                                                            |                                                                    |   0%  |                                                                            |=                                                                   |   2%  |                                                                            |==                                                                  |   4%  |                                                                            |====                                                                |   5%  |                                                                            |=====                                                               |   7%  |                                                                            |======                                                              |   9%  |                                                                            |=======                                                             |  11%  |                                                                            |=========                                                           |  13%  |                                                                            |==========                                                          |  15%  |                                                                            |===========                                                         |  16%  |                                                                            |============                                                        |  18%  |                                                                            |==============                                                      |  20%  |                                                                            |===============                                                     |  22%  |                                                                            |================                                                    |  24%  |                                                                            |=================                                                   |  25%  |                                                                            |===================                                                 |  27%  |                                                                            |====================                                                |  29%  |                                                                            |=====================                                               |  31%  |                                                                            |======================                                              |  33%  |                                                                            |=======================                                             |  35%  |                                                                            |=========================                                           |  36%  |                                                                            |==========================                                          |  38%  |                                                                            |===========================                                         |  40%  |                                                                            |============================                                        |  42%  |                                                                            |==============================                                      |  44%  |                                                                            |===============================                                     |  45%  |                                                                            |================================                                    |  47%  |                                                                            |=================================                                   |  49%  |                                                                            |===================================                                 |  51%  |                                                                            |====================================                                |  53%  |                                                                            |=====================================                               |  55%  |                                                                            |======================================                              |  56%  |                                                                            |========================================                            |  58%  |                                                                            |=========================================                           |  60%  |                                                                            |==========================================                          |  62%  |                                                                            |===========================================                         |  64%  |                                                                            |=============================================                       |  65%  |                                                                            |==============================================                      |  67%  |                                                                            |===============================================                     |  69%  |                                                                            |================================================                    |  71%  |                                                                            |=================================================                   |  73%  |                                                                            |===================================================                 |  75%  |                                                                            |====================================================                |  76%  |                                                                            |=====================================================               |  78%  |                                                                            |======================================================              |  80%  |                                                                            |========================================================            |  82%  |                                                                            |=========================================================           |  84%  |                                                                            |==========================================================          |  85%  |                                                                            |===========================================================         |  87%  |                                                                            |=============================================================       |  89%  |                                                                            |==============================================================      |  91%  |                                                                            |===============================================================     |  93%  |                                                                            |================================================================    |  95%  |                                                                            |==================================================================  |  96%  |                                                                            |=================================================================== |  98%  |                                                                            |====================================================================| 100%
    ## 
    ## Downloading files totaling approximately 2.3 MiB
    ## Downloading 55 files
    ##   |                                                                            |                                                                    |   0%  |                                                                            |=                                                                   |   2%  |                                                                            |===                                                                 |   4%  |                                                                            |====                                                                |   6%  |                                                                            |=====                                                               |   7%  |                                                                            |======                                                              |   9%  |                                                                            |========                                                            |  11%  |                                                                            |=========                                                           |  13%  |                                                                            |==========                                                          |  15%  |                                                                            |===========                                                         |  17%  |                                                                            |=============                                                       |  19%  |                                                                            |==============                                                      |  20%  |                                                                            |===============                                                     |  22%  |                                                                            |================                                                    |  24%  |                                                                            |==================                                                  |  26%  |                                                                            |===================                                                 |  28%  |                                                                            |====================                                                |  30%  |                                                                            |=====================                                               |  31%  |                                                                            |=======================                                             |  33%  |                                                                            |========================                                            |  35%  |                                                                            |=========================                                           |  37%  |                                                                            |==========================                                          |  39%  |                                                                            |============================                                        |  41%  |                                                                            |=============================                                       |  43%  |                                                                            |==============================                                      |  44%  |                                                                            |===============================                                     |  46%  |                                                                            |=================================                                   |  48%  |                                                                            |==================================                                  |  50%  |                                                                            |===================================                                 |  52%  |                                                                            |=====================================                               |  54%  |                                                                            |======================================                              |  56%  |                                                                            |=======================================                             |  57%  |                                                                            |========================================                            |  59%  |                                                                            |==========================================                          |  61%  |                                                                            |===========================================                         |  63%  |                                                                            |============================================                        |  65%  |                                                                            |=============================================                       |  67%  |                                                                            |===============================================                     |  69%  |                                                                            |================================================                    |  70%  |                                                                            |=================================================                   |  72%  |                                                                            |==================================================                  |  74%  |                                                                            |====================================================                |  76%  |                                                                            |=====================================================               |  78%  |                                                                            |======================================================              |  80%  |                                                                            |=======================================================             |  81%  |                                                                            |=========================================================           |  83%  |                                                                            |==========================================================          |  85%  |                                                                            |===========================================================         |  87%  |                                                                            |============================================================        |  89%  |                                                                            |==============================================================      |  91%  |                                                                            |===============================================================     |  93%  |                                                                            |================================================================    |  94%  |                                                                            |=================================================================   |  96%  |                                                                            |=================================================================== |  98%  |                                                                            |====================================================================| 100%
    ## 
    ## Unpacking zip files using 1 cores.
    ## Stacking operation across a single core.
    ## Stacking table inv_fieldData
    ## Stacking table inv_persample
    ## Stacking table inv_taxonomyProcessed
    ## Copied the most recent publication of validation file to /stackedFiles
    ## Copied the most recent publication of categoricalCodes file to /stackedFiles
    ## Copied the most recent publication of variable definition file to /stackedFiles
    ## Finished: Stacked 3 data tables and 3 metadata tables!
    ## Stacking took 2.04793 secs
    ## All unzipped monthly data folders have been removed.

## Macroinvertebrate Data Munging
Now that we have the data downloaded, we will need to do some 'data munging' to reorganize our data into a more useful format for this analysis. First, let's take a look at some of the tables that were generated by `loadByProduct()`:


    # what tables do you get with macroinvertebrate 
    # data product
    names(all_tabs_inv)

    ## [1] "categoricalCodes_20120" "inv_fieldData"          "inv_persample"         
    ## [4] "inv_taxonomyProcessed"  "readme_20120"           "validation_20120"      
    ## [7] "variables_20120"

    # extract items from list and put in R env. 
    all_tabs_inv %>% list2env(.GlobalEnv)

    ## <environment: R_GlobalEnv>

    # readme has the same informaiton as what you 
    # will find on the landing page on the data portal
    
    # The variables file describes each field in 
    # the returned data tables
    head(variables_20120)

    ##            table        fieldName
    ## 1: inv_fieldData              uid
    ## 2: inv_fieldData         domainID
    ## 3: inv_fieldData           siteID
    ## 4: inv_fieldData    namedLocation
    ## 5: inv_fieldData  decimalLatitude
    ## 6: inv_fieldData decimalLongitude
    ##                                                                                            description
    ## 1:                                        Unique ID within NEON database; an identifier for the record
    ## 2:                                                                Unique identifier of the NEON domain
    ## 3:                                                                                      NEON site code
    ## 4:                                               Name of the measurement location in the NEON database
    ## 5:  The geographic latitude (in decimal degrees, WGS84) of the geographic center of the reference area
    ## 6: The geographic longitude (in decimal degrees, WGS84) of the geographic center of the reference area
    ##    dataType         units downloadPkg       pubFormat primaryKey
    ## 1:   string          <NA>       basic            asIs          N
    ## 2:   string          <NA>       basic            asIs          N
    ## 3:   string          <NA>       basic            asIs          N
    ## 4:   string          <NA>       basic            asIs          N
    ## 5:     real decimalDegree       basic *.######(round)          N
    ## 6:     real decimalDegree       basic *.######(round)          N
    ##    categoricalCodeName
    ## 1:                    
    ## 2:                    
    ## 3:                    
    ## 4:                    
    ## 5:                    
    ## 6:

    # The validation file provides the rules that 
    # constrain data upon ingest into the NEON database:
    head(validation_20120)

    ##            table           fieldName
    ## 1: inv_fielddata                 uid
    ## 2: inv_fielddata          locationID
    ## 3: inv_fielddata     aquaticSiteType
    ## 4: inv_fielddata           startDate
    ## 5: inv_fielddata         collectDate
    ## 6: inv_fielddata samplingImpractical
    ##                                                                         description
    ## 1:                     Unique ID within NEON database; an identifier for the record
    ## 2:                               Identifier for location where sample was collected
    ## 3:                             Type of aquatic site, includes lake, river or stream
    ## 4:                   The start date-time or interval during which an event occurred
    ## 5:                                                     Date of the collection event
    ## 6: Samples and/or measurements were not collected due to the indicated circumstance
    ##    dataType units                  parserToCreate
    ## 1:   string  <NA>                    [CREATE_UID]
    ## 2:   string  <NA>                                
    ## 3:   string  <NA>                                
    ## 4: dateTime  <NA>          [collectDate][REQUIRE]
    ## 5: dateTime  <NA> [CONVERT_TO_UTC(namedLocation)]
    ## 6:   string  <NA>                                
    ##                                                                                                                                                                               entryValidationRulesParser
    ## 1:                                                                                                                                                                                                      
    ## 2: [NAMED_LOCATION_TYPE('AOS outlet named location type' OR 'AOS inlet named location type' OR 'AOS buoy named location type' OR 'AOS reach named location type' OR 'AOS riparian named location type')]
    ## 3:                                                                                                                                                                                                 [LOV]
    ## 4:                                                                                                                                                                                                      
    ## 5:                                                                                                                                                                                                      
    ## 6:                                                                                                                                                                                                 [LOV]
    ##                                                                                                                                                                                                                                                                                                                                                                                      entryValidationRulesForm
    ## 1:                                                                                                                                                                                                                                                                                                                                                                                                   [HIDDEN]
    ## 2: [REQUIRE][FROM_AOS_SPATIAL(IF(siteType='lake'),  AOS outlet named location type OR AOS inlet named location type OR AOS buoy named location type OR AOS riparian named location type)][FROM_AOS_SPATIAL(IF(siteType='stream'), AOS reach named location type)][FROM_AOS_SPATIAL(IF(siteType='river'),  AOS buoy named location type OR AOS riparian named location type OR AOS reach named location type)]
    ## 3:                                                                                                                                                                                                                                                                                                                                                                                                           
    ## 4:                                                                                                                                                                                                                                                                                                                                                                          [DEFAULT_TO(collectDate)][HIDDEN]
    ## 5:                                                                                                                                                                                                                                                                                                                             [DATE_FORMAT('YYYY-MM-DDTHH:MM')][REQUIRE][LESS_THAN_OR_EQUAL_TO (NOW + 24hr)]
    ## 6:

    # the categoricalCodes file provides controlled 
    # lists used in the data
    head(categoricalCodes_20120)

    ##                   name                                 pubCode
    ## 1:     aquaticSiteType                                    lake
    ## 2:     aquaticSiteType                                  stream
    ## 3:     aquaticSiteType                                   river
    ## 4: biophysicalCriteria                OK - no known exceptions
    ## 5: biophysicalCriteria OK - schedule change but conditions met
    ## 6: biophysicalCriteria                      conditions not met
    ##                                                                                            description
    ## 1:                                                                                                lake
    ## 2:                                                                                              stream
    ## 3:                                                                                               river
    ## 4:                                                      Sampling occurred on schedule, no known issues
    ## 5: Sampling occurred not within defined sampling window but reflects the target biophysical conditions
    ## 6:                                         Sampling does not reflect the target biophysical conditions
    ##     startDate endDate
    ## 1: 2012-01-01    <NA>
    ## 2: 2012-01-01    <NA>
    ## 3: 2012-01-01    <NA>
    ## 4: 2012-01-01    <NA>
    ## 5: 2012-01-01    <NA>
    ## 6: 2012-01-01    <NA>

Next, we will perform several operations in a row to re-organize our data. Each step is described by a code comment.


    # extract year from date, add it as a new column
    inv_fieldData <- inv_fieldData %>%
      mutate(
        year = collectDate %>% 
          lubridate::as_date() %>% 
          lubridate::year())
    
    
    # extract location data into a separate table
    table_location <- inv_fieldData %>%
      
      # keep only the columns listed below
      select(siteID, 
             domainID,
             namedLocation, 
             decimalLatitude, 
             decimalLongitude, 
             elevation) %>%
      
      # keep rows with unique combinations of values, 
      # i.e., no duplicate records
      distinct()
    
    
    
    # create a taxon table, which describes each 
    # taxonID that appears in the data set
    # start with inv_taxonomyProcessed
    table_taxon <- inv_taxonomyProcessed %>%
    
      # keep only the coluns listed below
      select(acceptedTaxonID, taxonRank, scientificName,
             order, family, genus, 
             identificationQualifier,
             identificationReferences) %>%
    
      # remove rows with duplicate information
      distinct()
    
    
    
    # taxon table information for all taxa in 
    # our database can be downloaded here:
    # takes 1-2 minutes
    # full_taxon_table_from_api <- neonUtilities::getTaxonTable("MACROINVERTEBRATE", token = NEON_TOKEN)
    
    
    
    # Make the observation table.
    # start with inv_taxonomyProcessed
    table_observation <- inv_taxonomyProcessed %>% 
      
      # select a subset of columns from
      # inv_taxonomyProcessed
      select(uid,
             sampleID,
             domainID,
             siteID,
             namedLocation,
             collectDate,
             subsamplePercent,
             individualCount,
             estimatedTotalCount,
             acceptedTaxonID,
             order, family, genus, 
             scientificName,
             taxonRank) %>%
      
      # Join the columns selected above with two 
      # columns from inv_fieldData (the two columns 
      # are sampleID and benthicArea)
      left_join(inv_fieldData %>% 
                  select(sampleID, eventID, year, 
                         habitatType, samplerType,
                         benthicArea)) %>%
      
      # some new columns called 'variable_name', 
      # 'value', and 'unit', and assign values for 
      # all rows in the table.
      # variable_name and unit are both assigned the 
      # same text strint for all rows. 
      mutate(inv_dens = estimatedTotalCount / benthicArea,
             inv_dens_unit = 'count per square meter')

    ## Joining, by = "sampleID"

    # extract sample info
    table_sample_info <- table_observation %>%
      select(sampleID, domainID, siteID, namedLocation, 
             collectDate, eventID, year, 
             habitatType, samplerType, benthicArea, 
             inv_dens_unit) %>%
      distinct()
    
    
    
    # remove singletons and doubletons
    # create an occurrence summary table
    taxa_occurrence_summary <- table_observation %>%
      select(sampleID, acceptedTaxonID) %>%
      distinct() %>%
      group_by(acceptedTaxonID) %>%
      summarize(occurrences = n())

    ## `summarise()` ungrouping output (override with `.groups` argument)

    # filter out taxa that are only observed 1 or 2 times
    taxa_list_cleaned <- taxa_occurrence_summary %>%
      filter(occurrences > 2)
    
    # filter observation table based on taxon list above
    table_observation_cleaned <- table_observation %>%
      filter(acceptedTaxonID %in%
                 taxa_list_cleaned$acceptedTaxonID,
             !sampleID %in% c("MAYF.20190729.CORE.1",
                              "POSE.20160718.HESS.1")) 
                          #this is an outlier sampleID
    
    
    # some summary data
    sampling_effort_summary <- table_sample_info %>%
      
      # group by siteID, year
      group_by(siteID, year, samplerType) %>%
      
      # count samples and habitat types within each event
      summarise(
        event_count = eventID %>% unique() %>% length(),
        sample_count = sampleID %>% unique() %>% length(),
        habitat_count = habitatType %>% 
            unique() %>% length())

    ## `summarise()` regrouping output by 'siteID', 'year' (override with `.groups` argument)

    View(sampling_effort_summary)

## Working with 'Long' data
'Reshaping' your data to use as an input to a particular fuction may require you to consider: do I want 'long' or 'wide' data? Here's a link to <a href="https://www.theanalysisfactor.com/wide-and-long-data/">a great article from 'the analysis factor' that describes the differences</a>.

For this first step, we will use data in a 'long' table:


    # no. taxa by rank by site
    table_observation_cleaned %>% 
      group_by(domainID, siteID, taxonRank) %>%
      summarize(
        n_taxa = acceptedTaxonID %>% 
            unique() %>% length()) %>%
      ggplot(aes(n_taxa, taxonRank)) +
      facet_wrap(~ domainID + siteID) +
      geom_col()

    ## `summarise()` regrouping output by 'domainID', 'siteID' (override with `.groups` argument)

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/long-data-1.png)

    # library(scales)
    # sum densities by order for each sampleID
    table_observation_by_order <- 
        table_observation_cleaned %>% 
        filter(!is.na(order)) %>%
        group_by(domainID, siteID, year, 
                 eventID, sampleID, habitatType, order) %>%
        summarize(order_dens = sum(inv_dens, na.rm = TRUE))

    ## `summarise()` regrouping output by 'domainID', 'siteID', 'year', 'eventID', 'sampleID', 'habitatType' (override with `.groups` argument)

    # rank occurrence by order
    table_observation_by_order %>% head()

    ## # A tibble: 6 x 8
    ## # Groups:   domainID, siteID, year, eventID, sampleID, habitatType [2]
    ##   domainID siteID  year eventID   sampleID      habitatType order   order_dens
    ##   <chr>    <chr>  <dbl> <chr>     <chr>         <chr>       <chr>        <dbl>
    ## 1 D02      POSE    2014 POSE.201… POSE.2014072… riffle      Coleop…      516. 
    ## 2 D02      POSE    2014 POSE.201… POSE.2014072… riffle      Epheme…     5172. 
    ## 3 D02      POSE    2014 POSE.201… POSE.2014072… riffle      Odonata      129. 
    ## 4 D02      POSE    2014 POSE.201… POSE.2014072… riffle      Plecop…     1290. 
    ## 5 D02      POSE    2014 POSE.201… POSE.2014072… riffle      Tricho…     8839. 
    ## 6 D02      POSE    2014 POSE.201… POSE.2014102… pool        Collem…       34.9

    # stacked rank occurrence plot
    table_observation_by_order %>%
      group_by(order, siteID) %>%
      summarize(
        occurrence = (order_dens > 0) %>% sum()) %>%
        ggplot(aes(
            x = reorder(order, -occurrence), 
            y = occurrence,
            color = siteID,
            fill = siteID)) +
        geom_col() +
        theme(axis.text.x = 
                  element_text(angle = 45, hjust = 1))

    ## `summarise()` regrouping output by 'order' (override with `.groups` argument)

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/long-data-2-1.png)

    # faceted densities plot
    table_observation_by_order %>%
      ggplot(aes(
        x = reorder(order, -order_dens), 
        y = log10(order_dens),
        color = siteID,
        fill = siteID)) +
      geom_boxplot(alpha = .5) +
      facet_grid(siteID ~ .) +
      theme(axis.text.x = 
                element_text(angle = 45, hjust = 1))

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/long-data-3-1.png)

### Making Data 'wide'
For the next process, we will need to make our data table in the 'wide' format.


    # select only site by species density info and remove duplicate records
    table_sample_by_taxon_density_long <- table_observation_cleaned %>%
      select(sampleID, acceptedTaxonID, inv_dens) %>%
      distinct() %>%
      filter(!is.na(inv_dens))
    
    # table_sample_by_taxon_density_long %>% nrow()
    # table_sample_by_taxon_density_long %>% distinct() %>% nrow()
    
    
    
    # pivot to wide format, sum multiple counts per sampleID
    table_sample_by_taxon_density_wide <- table_sample_by_taxon_density_long %>%
      tidyr::pivot_wider(id_cols = sampleID, 
                         names_from = acceptedTaxonID,
                         values_from = inv_dens,
                         values_fill = list(inv_dens = 0),
                         values_fn = list(inv_dens = sum)) %>%
      column_to_rownames(var = "sampleID") 
    
    # checl col and row sums
    colSums(table_sample_by_taxon_density_wide) %>% min()

    ## [1] 28

    rowSums(table_sample_by_taxon_density_wide) %>% min()

    ## [1] 18.86792

## Multiscale Biodiversity


Reference:
Jost, L. 2007. Partitioning diversity into independent alpha and beta components. Ecology 88:2427–2439. <a href="https://doi.org/10.1890/06-1736.1">https://doi.org/10.1890/06-1736.1</a>.

These metrics are based on Robert Whittaker's multiplicative diversity where
* gamma is regional biodiversity
* alpha is local biodiversity (e.g., the mean diversity at a patch)
* and beta diversity is a measure of among-patch variability in community composition. 

Beta could be interpreted as the number of "distinct" communities present within the region.

The relationship among alpha, beta, and gamma diversity is:
   __beta = gamma / alpha__ 

The influence of relative abundances over the calculation of alpha, beta, and gamma diversity metrics is determined by the coefficient q. The coefficient "q" determines the "order" of the diversity metric, where q = 0 provides diversity measures based on richness, and higher orders of q give more weight to taxa that have higher abundances in the data. Order q = 1 is related to Shannon diveristy metrics, and order q = 2 is related to Simpson diversity metrics.

#### Alpha diversity is average local richness.
Order q = 0 alpha diversity calcuated for our dataset returns a mean local richness (i.e., species counts) of ~27 taxa per sample across the entire data set.


    table_sample_by_taxon_density_wide %>%
      vegetarian::d(lev = 'alpha', q = 0)

    ## [1] 27.74303

#### Comparing alpha diversity calculated using different orders:

Order q = 1 alpha diversity returns mean number of "species equivalents" per sample in the data set. This approach incoporates evenness because when abundances are more even across taxa, taxa are weighted more equally toward counting as a "species equivalent". For example, if you have a sample with 100 individuals, spread across 10 species, and each species is represented by 10 individuals, the number of order q = 1 species equivalents will equal the richness (10).

Alternatively, if 90 of the 100 individuals in the sample are one species, and the other 10 individuals are spread across the other 9 species, there will only be 1.72 order q = 1 species equivalents, whereas, there are still 10 species in the sample.


    # even distribution, order q = 0 diversity = 10 
    vegetarian::d(
      data.frame(spp.a = 10, spp.b = 10, spp.c = 10, 
                 spp.d = 10, spp.e = 10, spp.f = 10, 
                 spp.g = 10, spp.h = 10, spp.i = 10, 
                 spp.j = 10),
      q = 0, 
      lev = "alpha")

    ## [1] 10

    # even distribution, order q = 1 diversity = 10
    vegetarian::d(
      data.frame(spp.a = 10, spp.b = 10, spp.c = 10, 
                 spp.d = 10, spp.e = 10, spp.f = 10, 
                 spp.g = 10, spp.h = 10, spp.i = 10, 
                 spp.j = 10),
      q = 1, 
      lev = "alpha")

    ## [1] 10

    # un-even distribution, order q = 0 diversity = 10
    vegetarian::d(
      data.frame(spp.a = 90, spp.b = 2, spp.c = 1, 
                 spp.d = 1, spp.e = 1, spp.f = 1, 
                 spp.g = 1, spp.h = 1, spp.i = 1, 
                 spp.j = 1),
      q = 0, 
      lev = "alpha")

    ## [1] 10

    # un-even distribution, order q = 1 diversity = 1.72
    vegetarian::d(
      data.frame(spp.a = 90, spp.b = 2, spp.c = 1, 
                 spp.d = 1, spp.e = 1, spp.f = 1, 
                 spp.g = 1, spp.h = 1, spp.i = 1, 
                 spp.j = 1),
      q = 1, 
      lev = "alpha")

    ## [1] 1.718546

## Comparing orders of q for NEON data

Let's compare the different orders q = 0, 1, and 2 measures of alpha diversity across the samples collected from ARIK, POSE, and MAYF.


    # Nest data by siteID
    data_nested_by_siteID <- table_sample_by_taxon_density_wide %>%
      tibble::rownames_to_column("sampleID") %>%
      left_join(table_sample_info %>% 
                    select(sampleID, siteID)) %>%
      tibble::column_to_rownames("sampleID") %>%
      nest(data = -siteID)

    ## Joining, by = "sampleID"

    # apply the calculation by site  
    data_nested_by_siteID %>% mutate(
      alpha_q0 = purrr::map_dbl(
        .x = data,
        .f = ~ vegetarian::d(abundances = .,
        lev = 'alpha', 
        q = 0)))

    ## # A tibble: 3 x 3
    ##   siteID data                 alpha_q0
    ##   <chr>  <list>                  <dbl>
    ## 1 POSE   <tibble [101 × 390]>     38.8
    ## 2 MAYF   <tibble [119 × 390]>     22.2
    ## 3 ARIK   <tibble [103 × 390]>     23.3

    # Note that POSE has the highest mean diversity
    
    
    
    # Now calculate alpha, beta, and gamma using orders 0 and 1,
    # Note that I don't make all the argument assignments as explicitly here
    diversity_partitioning_results <- 
        data_nested_by_siteID %>% 
        mutate(
            n_samples = purrr::map_int(data, ~ nrow(.)),
            alpha_q0 = purrr::map_dbl(data, ~vegetarian::d(
                abundances = ., lev = 'alpha', q = 0)),
            alpha_q1 = purrr::map_dbl(data, ~ vegetarian::d(
                abundances = ., lev = 'alpha', q = 1)),
            beta_q0 = purrr::map_dbl(data, ~ vegetarian::d(
                abundances = ., lev = 'beta', q = 0)),
            beta_q1 = purrr::map_dbl(data, ~ vegetarian::d(
                abundances = ., lev = 'beta', q = 1)),
            gamma_q0 = purrr::map_dbl(data, ~ vegetarian::d(
                abundances = ., lev = 'gamma', q = 0)),
            gamma_q1 = purrr::map_dbl(data, ~ vegetarian::d(
                abundances = ., lev = 'gamma', q = 1)))
    
    
    diversity_partitioning_results %>% select(-data) %>% print()

    ## # A tibble: 3 x 8
    ##   siteID n_samples alpha_q0 alpha_q1 beta_q0 beta_q1 gamma_q0 gamma_q1
    ##   <chr>      <int>    <dbl>    <dbl>   <dbl>   <dbl>    <dbl>    <dbl>
    ## 1 POSE         101     38.8    15.5     7.26    6.43      282     99.5
    ## 2 MAYF         119     22.2     9.50    9.95    6.86      221     65.2
    ## 3 ARIK         103     23.3     8.40    8.25    5.86      192     49.2

    # Note that POSE has the highest mean diversity



## Using NMDS to ordinate samples

Finally, we will use Nonmetric Multidimensional Scaling (NMDS) to ordinate samples as shown below:


    # create ordination using NMDS
    my_nmds_result <- table_sample_by_taxon_density_wide %>% vegan::metaMDS()

    ## Square root transformation
    ## Wisconsin double standardization
    ## Run 0 stress 0.2134463 
    ## Run 1 stress 0.2255599 
    ## Run 2 stress 0.2165721 
    ## Run 3 stress 0.2230544 
    ## Run 4 stress 0.2232702 
    ## Run 5 stress 0.2159201 
    ## Run 6 stress 0.2394686 
    ## Run 7 stress 0.2388101 
    ## Run 8 stress 0.2240269 
    ## Run 9 stress 0.2155247 
    ## Run 10 stress 0.2171261 
    ## Run 11 stress 0.2239167 
    ## Run 12 stress 0.2426296 
    ## Run 13 stress 0.2215704 
    ## Run 14 stress 0.2207491 
    ## Run 15 stress 0.2175235 
    ## Run 16 stress 0.2345474 
    ## Run 17 stress 0.231081 
    ## Run 18 stress 0.2322107 
    ## Run 19 stress 0.2145703 
    ## Run 20 stress 0.2192855 
    ## *** No convergence -- monoMDS stopping criteria:
    ##     20: stress ratio > sratmax

    # plot stress
    my_nmds_result$stress

    ## [1] 0.2134463

    p1 <- vegan::ordiplot(my_nmds_result)
    vegan::ordilabel(p1, "species")

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/NMDS-1.png)

    # merge NMDS scores with sampleID information for plotting
    nmds_scores <- my_nmds_result %>% vegan::scores() %>%
      as.data.frame() %>%
      tibble::rownames_to_column("sampleID") %>%
      left_join(table_sample_info)

    ## Joining, by = "sampleID"

    # # How I determined the outlier(s)
    nmds_scores %>% arrange(desc(NMDS1)) %>% head()

    ##                  sampleID     NMDS1       NMDS2 domainID siteID
    ## 1  POSE.20150721.SURBER.1 1.5742532 -0.02425078      D02   POSE
    ## 2  POSE.20150330.SURBER.1 1.2299946 -0.75884534      D02   POSE
    ## 3 ARIK.20160919.KICKNET.3 1.0875597 -0.14280868      D10   ARIK
    ## 4 ARIK.20150325.KICKNET.4 0.9929619 -0.27960972      D10   ARIK
    ## 5 ARIK.20160919.KICKNET.4 0.9905714 -0.53136594      D10   ARIK
    ## 6 ARIK.20140714.KICKNET.4 0.9868035 -1.33136322      D10   ARIK
    ##    namedLocation         collectDate       eventID year habitatType
    ## 1 POSE.AOS.reach 2015-07-21 14:43:00 POSE.20150721 2015      riffle
    ## 2 POSE.AOS.reach 2015-03-30 14:30:00 POSE.20150330 2015      riffle
    ## 3 ARIK.AOS.reach 2016-09-19 22:06:00 ARIK.20160919 2016         run
    ## 4 ARIK.AOS.reach 2015-03-25 17:15:00 ARIK.20150325 2015         run
    ## 5 ARIK.AOS.reach 2016-09-19 22:06:00 ARIK.20160919 2016         run
    ## 6 ARIK.AOS.reach 2014-07-14 17:51:00 ARIK.20140714 2014         run
    ##       samplerType benthicArea          inv_dens_unit
    ## 1          surber       0.093 count per square meter
    ## 2          surber       0.093 count per square meter
    ## 3 modifiedKicknet       0.250 count per square meter
    ## 4 modifiedKicknet       0.250 count per square meter
    ## 5 modifiedKicknet       0.250 count per square meter
    ## 6 modifiedKicknet       0.250 count per square meter

    nmds_scores %>% arrange(desc(NMDS1)) %>% tail()

    ##                 sampleID     NMDS1      NMDS2 domainID siteID  namedLocation
    ## 318 MAYF.20170314.CORE.1 -1.045603  1.2495550      D08   MAYF MAYF.AOS.reach
    ## 319 MAYF.20160321.CORE.2 -1.087688  0.9935332      D08   MAYF MAYF.AOS.reach
    ## 320 MAYF.20180326.CORE.3 -1.095837 -0.5646813      D08   MAYF MAYF.AOS.reach
    ## 321 MAYF.20180726.CORE.2 -1.231606  0.1671310      D08   MAYF MAYF.AOS.reach
    ## 322 MAYF.20190311.CORE.1 -1.440107  0.6270889      D08   MAYF MAYF.AOS.reach
    ## 323 MAYF.20190311.CORE.2 -1.504627  0.7647755      D08   MAYF MAYF.AOS.reach
    ##             collectDate       eventID year habitatType samplerType
    ## 318 2017-03-14 14:11:00 MAYF.20170314 2017         run        core
    ## 319 2016-03-21 16:09:00 MAYF.20160321 2016         run        core
    ## 320 2018-03-26 14:50:00 MAYF.20180326 2018         run        core
    ## 321 2018-07-26 14:17:00 MAYF.20180726 2018         run        core
    ## 322 2019-03-11 15:00:00 MAYF.20190311 2019         run        core
    ## 323 2019-03-11 15:00:00 MAYF.20190311 2019         run        core
    ##     benthicArea          inv_dens_unit
    ## 318       0.006 count per square meter
    ## 319       0.006 count per square meter
    ## 320       0.006 count per square meter
    ## 321       0.006 count per square meter
    ## 322       0.006 count per square meter
    ## 323       0.006 count per square meter

    # Plot samples in community composition space by year
    nmds_scores %>%
      ggplot(aes(NMDS1, NMDS2, color = siteID, 
                 shape = samplerType)) +
      geom_point() +
      facet_wrap(~ as.factor(year))

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/NMDS-2.png)

    # Plot samples in community composition space
    # facet by siteID and habitat type
    # color by year
    nmds_scores %>%
      ggplot(aes(NMDS1, NMDS2, color = as.factor(year), 
                 shape = samplerType)) +
      geom_point() +
      facet_grid(habitatType ~ siteID, scales = "free")

![ ]({{ site.baseurl }}/images/rfigs/R/SFS-2020/01_working_with_NEON_macroinverts/NMDS-3.png)
