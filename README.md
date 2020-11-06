The Covid Age Study
===================

<img src="https://img.shields.io/badge/Study%20Status-Started-blue.svg" alt="Study Status: Started">

- Analytics use case(s): **Patient-level Prediction**
- Study type: **Cohort study**
- Tags: **COVID-19, ohdsi-studies**
- Study lead: **Sara Khalid, Jenna Reps, Daniel Prieto-Alhambra**
- Study lead forums tag: **[Sara_Khalid1](https://forums.ohdsi.org/u/Sara_Khalid1), [Daniel_Prieto
](https://forums.ohdsi.org/u/Daniel_Prieto
)**
- Study start date: **15 September 2020**
- Study end date: **-**
- Study Protocol: **[PDF document](https://github.com/ohdsi-studies/CoverAge/blob/master/PLP_protocol_young_2020_09_23.pdf)**
- Publications: **-**
- Results explorer: **-**

Development and validation of patient-level prediction models for hospitalization and death amongst young patients presenting with a clinical diagnosis or positive test for COVID-19: a rapid network study to inform the management of COVID-19

Instructions To Install
===================

Make sure you have R, Java and rJava all set up.

```r
  # get the latest PatientLevelPrediction
  install.packages("devtools")
  devtools::install_github("OHDSI/FeatureExtraction")
  devtools::install_github("OHDSI/PatientLevelPrediction")
  # check the package
  PatientLevelPrediction::checkPlpInstallation()
  
  # install the network package
  devtools::install_github("ohdsi-studies/CoverAge")
```


Instructions To Run Package
===================
- Execute the study by running the code in (extras/CodeToRun.R) :
```r
  library(CoverAge)
  # USER INPUTS
#=======================
# The folder where the study intermediate and result files will be written:
outputFolder <- "./CoverAgeResults"

# Specify where the temporary files (used by the ff package) will be created:
options(fftempdir = "location with space to save big data")

# Details for connecting to the server:
dbms <- "you dbms"
user <- 'your username'
pw <- 'your password'
server <- 'your server'
port <- 'your port'

connectionDetails <- DatabaseConnector::createConnectionDetails(dbms = dbms,
                                                                server = server,
                                                                user = user,
                                                                password = pw,
                                                                port = port)

# Add the database containing the OMOP CDM data
cdmDatabaseSchema <- 'cdm database schema'
# Add a sharebale name for the database containing the OMOP CDM data
cdmDatabaseName <- 'a friendly shareable  name for your database'
# Add a database with read/write access as this is where the cohorts will be generated
cohortDatabaseSchema <- 'work database schema'

oracleTempSchema <- NULL

# table name where the cohorts will be generated
cohortTable <- 'CoverAgeCohort'
#=======================

execute(connectionDetails = connectionDetails,
        cdmDatabaseSchema = cdmDatabaseSchema,
		cdmDatabaseName = cdmDatabaseName,
        cohortDatabaseSchema = cohortDatabaseSchema,
        cohortTable = cohortTable,
        oracleTempSchema = oracleTempSchema,
        outputFolder = outputFolder,
        createProtocol = F,
        createCohorts = T,
        runAnalyses = T,
        createResultsDoc = F,
        packageResults = F,
        createValidationPackage = F,
        minCellCount= 5)
```

The 'createCohorts' option will create the target and outcome cohorts into cohortDatabaseSchema.cohortTable if set to T.  The 'runAnalyses' option will create/extract the data for each prediction problem setting (each Analysis), develop a prediction model, internally validate it if set to T.  The results of each Analysis are saved in the 'outputFolder' directory under the subdirectories 'Analysis_1' to 'Analysis_N', where N is the total analyses specified.  After running execute with 'runAnalyses set to T, a 'Validation' subdirectory will be created in the 'outputFolder' directory where you can add the external validation results to make them viewable in the shiny app or journal document that can be automatically generated.


- You can then easily transport all the trained models into a network validation study package by running:
```r
  
  execute(connectionDetails = connectionDetails,
        cdmDatabaseSchema = cdmDatabaseSchema,
		cdmDatabaseName = cdmDatabaseName,
        cohortDatabaseSchema = cohortDatabaseSchema,
        cohortTable = cohortTable,
        outputFolder = outputFolder,
        createValidationPackage = T)
  

```