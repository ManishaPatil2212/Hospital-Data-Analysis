# Patients Emergency Room Visit Report
## Project-Overview

This report evaluates the quality of service provided to patients during their visits to the emergency department. The data collection methodology that was used was patient satisfaction surveys.

## Understanding The Data-set

The data set contains records of patients' emergency room visits. The data contains the date of the patient’s visit, patient ID, patient age, patient wait time before being attended to, patient satisfaction score, etc.

## Data Cleaning Process

1.Data was exported to power query for data cleaning.

2.The column quality was checked to assess for duplicates and missing or null values.

3.Some columns were created to give the data context better. For example

4.The AM/PM column was created to identify whether a patient was admitted during the day or night

5.The date column was created by converting the date time format to the date format

6.The Full name was created column by merging the two columns that contain the last names and first name initials

## Exploratory Data Analysis

Using Dax / Calculated Column to create some important measures that could be used as metrics in the visualization and also to give more perspective to the data

## % of patients that are admins
  DIVIDE( 
  
      COUNTROWS(
       FILTER(
              'dataset',
              'dataset'[patient_admin_flag] = TRUE()
          )
      ),
      [Total_Patients]
  )
## Total patient
  COUNTROWS([‘dataset’])
  
Average satisfactory score

  CALCULATE(
  
    AVERAGE('dataset'[patient_sat_score]),'dataset'[patient_sat_score]<>BLANK())
## %_non_responders (PERCENTAGE OF PATIENT THAT DID NOT RESPOND TO THE SURVEY)
   VAR no_rating = 
   
      CALCULATE([Total_Patients],'dataset'[patient_sat_score]=BLANK()
      RETURN
      DIVIDE(no_rating,[Total_Patients])
## Created a calculated column of different patient age bucket
  SWITCH(
  
          TRUE(),
          'Hospital ER'[patient_age]<=10,"0-10",
          'Hospital ER'[patient_age]<=20,"11-20",
          'Hospital ER'[patient_age]<=30,"21-30",
          'Hospital ER'[patient_age]<=40,"31-40",
          'Hospital ER'[patient_age]<=50,"41-50",
          'Hospital ER'[patient_age]<=60,"51-60",
          'Hospital ER'[patient_age]<=70,"61-70",
## Created another column to depict whether the visit was a weekday or weekend
  Addcolumn
  (calenderauto(),
  
  			"YEAR",YEAR([Date]),
  "MONTH",FORMAT([Date],"mmm"),
  "MonthNum",MONTH([Date]),
  "WeekType", IF(WEEKDAY([Date]) = 1, "Weekend", IF(WEEKDAY([Date])= 7, "Weekend", "WeekDay")),
  "WeekDay",FORMAT([Date],"ddd")
  )
## Calculated a measure for %_patients that was not referred by any department
   var _filteredPatients = 
   
   CALCULATE([Total_Patients],
   
  'Hospital ER'[department_referral]="none")
  
  RETURN
  DIVIDE(
  
      _filteredPatients,[Total_Patients])
