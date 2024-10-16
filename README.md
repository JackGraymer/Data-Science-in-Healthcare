# Data-Science-in-Healthcare

library(readxl)
library(dplyr)
library(zoo)

Data <- read_excel("Path to/Drug_related_Deaths_2018-2024.xlsx")

# Overview for the Data
head(Data, 10)
str(Data)

# look into Notes
unique(Data$Notes)

# only NA's so we delete this column
Data <- Data %>% select(-Notes)

# look into Dates
unique(Data$Month)

# Delete Row Sep., 2024 since the data is not complete
Data <- Data[Data$Month != "Sep., 2024 (provisional and partial)", ]

# Create column Provisional (TRUE/FALSE) based on the col Month '(provisional)'
Data$Provisional <- grepl("\\(provisional\\)", Data$Month)

# Delete " (provisional)" from the Date
Data$Month <- gsub(" \\(provisional\\)", "", Data$Month)

# Convert into Date Variable
Data$Month <- as.yearmon(Data$Month, format = "%b., %Y")

# look into Deaths
Data$Deaths

# Some of the numbers are "Suppressed" this means that there are between 1 and 9 cases
# To use Deaths as a number we can leave them as NA's or substitute by a random number between 1 and 9

# Set seed for reproducibility if needed
set.seed(23)  

# Replace "Suppressed" in the Death column with a random number between 1 and 9
Data$Deaths <- ifelse(Data$Deaths == "Suppressed", sample(1:9, sum(Data$Deaths == "Suppressed"), replace = TRUE), Data$Deaths)
Data$Deaths <- as.numeric(Data$Deaths)

