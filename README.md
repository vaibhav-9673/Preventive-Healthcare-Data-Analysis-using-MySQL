# Preventive-Healthcare-Data-Analysis-using-MySQL
## A) Projet Overview
Project Title: Preventive-Healthcare-Data-Analysis-using-MySQL
Database: pre_care_db
        This project focuses on analyzing preventive healthcare data using MySQL. It simulates a real-world database system that stores patient information, lifestyle patterns, family medical history, preventive checkups, and risk levels. The goal is to extract insights that can help predict and prevent health risks early through data analysis.

<img width="1500" height="1000" alt="image" src="https://github.com/user-attachments/assets/509c908f-c4a4-4a7b-b081-942267efb410" />


## 🎯 B) Objective
The objective of this project is to design and analyze a Preventive Healthcare Database System using MySQL to identify health risk patterns through patient, lifestyle, and medical data. The project aims to simulate real-world healthcare scenarios where data-driven insights can help in early disease prediction, risk assessment, and preventive decision-making.
1.Through this project, the goal is to:
2.Organize healthcare data in a structured relational database.
3.Perform SQL-based data analysis to extract meaningful insights.
4.Understand the relationship between lifestyle factors and health risks.
5.Support data-driven preventive healthcare strategies using analytics.

## C) 🧱 Database Structure
The database pre_care_db is designed to store and analyze preventive healthcare information. It contains five main tables, each connected through the PATIENT_ID foreign key, ensuring referential integrity and enabling relational analysis.
1. 🩺 PATIENTS
Stores basic patient demographic information.
Columns: PATIENT_ID – Primary Key (Unique ID for each patient)
         FIRST_NAME, LAST_NAME – Patient’s name details
         GENDER – Male, Female, or Other
         BIRTH_DATE – Date of birth
         CITY – City of residence
         PHONE – Unique contact number
         ADDRESS – Full address

2. 🍎 LIFESTYLE
Contains details about each patient’s daily lifestyle habits.
Columns: LIFESTYLE_ID – Primary Key
         PATIENT_ID – Foreign Key referencing PATIENTS(PATIENT_ID)
         DIET_QUALITY – Poor / Average / Good.
         EXERCISE_HOURS_PER_WEEK – Total weekly exercise hours
         SLEEP_HOURS_PER_DAY – Average daily sleep duration
         SMOKING – Yes / No
         ALCOHOL – Yes / No

3. 👨‍👩‍👧‍👦 FAMILY_HISTORY
Represents family medical history and hereditary risk factors.
Columns: FAMILY_ID – Primary Key
         PATIENT_ID – Foreign Key referencing PATIENTS(PATIENT_ID)
         DIABETES, HEART_DISEASE, CANCER, ASTHMA – Yes / No indicators

4. 🧾 CHECKUPS
Records preventive health checkups conducted for each patient.
Columns: CHECKUP_ID – Primary Key
         PATIENT_ID – Foreign Key referencing PATIENTS(PATIENT_ID)
         CHECKUP_TYPE – Type of medical checkup (e.g., Blood Test, ECG, Vision Test)
         CHECKUP_DATE – Date of checkup
         RESULT_SUMMARY – Key result or doctor’s remark
         NEXT_DUE_DATE – Recommended next checkup date

### D) Database Schema
```
-- CREATE DATABASE
CREATE DATABASE PRE_CARE_DB;
USE PRE_CARE_DB;

-- PATIENTS TABLE
CREATE TABLE PATIENTS (
    PATIENT_ID INT AUTO_INCREMENT PRIMARY KEY,
    FIRST_NAME VARCHAR(50) NOT NULL,
    LAST_NAME VARCHAR(50) NOT NULL,
    GENDER ENUM('MALE','FEMALE','OTHER') NOT NULL,
    BIRTH_DATE DATE NOT NULL,
    CITY VARCHAR(50),
    PHONE VARCHAR (20) UNIQUE,
    ADDRESS VARCHAR(200)
);

-- LIFESTYLE TABLE
CREATE TABLE LIFESTYLE (
    LIFESTYLE_ID INT AUTO_INCREMENT PRIMARY KEY,
    PATIENT_ID INT NOT NULL,
    DIET_QUALITY ENUM('POOR','AVERAGE','GOOD'),
    EXERCISE_HOURS_PER_WEEK DECIMAL CHECK (EXERCISE_HOURS_PER_WEEK >= 0),
    SLEEP_HOURS_PER_DAY DECIMAL(3,1) CHECK (SLEEP_HOURS_PER_DAY >= 0),
    SMOKING ENUM('YES','NO') DEFAULT 'NO',
    ALCOHOL ENUM('YES','NO') DEFAULT 'NO',
    FOREIGN KEY (PATIENT_ID) REFERENCES PATIENTS(PATIENT_ID) ON DELETE CASCADE
);

-- FAMILY HISTORY TABLE
CREATE TABLE FAMILY_HISTORY (
    FAMILY_ID INT AUTO_INCREMENT PRIMARY KEY,
    PATIENT_ID INT NOT NULL,
    DIABETES ENUM('YES','NO') DEFAULT 'NO',
    HEART_DISEASE ENUM('YES','NO') DEFAULT 'NO',
    CANCER ENUM('YES','NO') DEFAULT 'NO',
    ASTHMA ENUM('YES','NO') DEFAULT 'NO',
    FOREIGN KEY (PATIENT_ID) REFERENCES PATIENTS(PATIENT_ID) ON DELETE CASCADE
);

-- PREVENTIVE CHECKUPS TABLE
CREATE TABLE CHECKUPS (
    CHECKUP_ID INT AUTO_INCREMENT PRIMARY KEY,
    PATIENT_ID INT NOT NULL,
    CHECKUP_TYPE VARCHAR(100) NOT NULL,
    CHECKUP_DATE DATE NOT NULL,
    RESULT_SUMMARY VARCHAR(200),
    NEXT_DUE_DATE DATE,
    FOREIGN KEY (PATIENT_ID) REFERENCES PATIENTS(PATIENT_ID) ON DELETE CASCADE
);

-- RISK SCORES TABLE
CREATE TABLE RISK_SCORES (
    RISK_ID INT AUTO_INCREMENT PRIMARY KEY,
    PATIENT_ID INT NOT NULL,
    CALCULATED_DATE DATE ,
    RISK_LEVEL ENUM('LOW','MEDIUM','HIGH'),
    REMARKS VARCHAR(200),
    FOREIGN KEY (PATIENT_ID) REFERENCES PATIENTS(PATIENT_ID) ON DELETE CASCADE
);
```

## E) Data Exploration & Cleaning
### 1.
```
-- Check number of records
SELECT COUNT(*) FROM PATIENTS;

-- Preview first 5 rows
SELECT * FROM LIFESTYLE LIMIT 5;

-- Check unique patients
SELECT COUNT(DISTINCT PATIENT_ID) FROM CHECKUPS;
```
### 2.Identify Missing or Null Values
```
-- Find nulls in critical columns
SELECT COUNT(*) AS missing_birthdate FROM PATIENTS WHERE BIRTH_DATE IS NULL;

SELECT COUNT(*) AS missing_sleep FROM LIFESTYLE WHERE SLEEP_HOURS_PER_DAY IS NULL;
```
### 3.Check for Duplicates
```
-- Find duplicate patients by phone number
SELECT PHONE, COUNT(*) AS count
FROM PATIENTS
GROUP BY PHONE
HAVING COUNT(*) > 1;
```
### 4.Validate Data Consistency
```
-- Exercise hours should be >= 0
SELECT * FROM LIFESTYLE WHERE EXERCISE_HOURS_PER_WEEK < 0;

-- Sleep hours should be reasonable
SELECT * FROM LIFESTYLE WHERE SLEEP_HOURS_PER_DAY NOT BETWEEN 0 AND 24;
```
### 5.Check Relationships
```
-- Check if any lifestyle record has missing patient
SELECT * 
FROM LIFESTYLE
WHERE PATIENT_ID NOT IN (SELECT PATIENT_ID FROM PATIENTS);

-- Check orphan checkups
SELECT * 
FROM CHECKUPS
WHERE PATIENT_ID NOT IN (SELECT PATIENT_ID FROM PATIENTS);
```
## F) Data Analysis & Findings
 1.Patient Demographics
 How many patients are in the database?
 What is the gender distribution of patients?
 Which city has the highest number of patients?
 What is the average age of patients?
 How many patients share the same first or last name?

 2.Lifestyle Analysis
 What is the average sleep duration of patients by city?
 How many patients smoke or drink alcohol?
 Is there any correlation between exercise hours and risk levels?
 What percentage of patients have “GOOD” diet quality?
 Compare sleep hours between smokers and non-smokers.

 3.Family History
 How many patients have diabetes, heart disease, cancer, or asthma in family history?
 Which disease is the most common in family history?
 How many patients have multiple family health risks (e.g., both diabetes and heart disease)?
 What percentage of patients with family history of diabetes are marked as high risk?

 4.Preventive Checkups
 What types of checkups are most common?
 How many checkups have been done in the last 6 months?
 Which patients missed their next due checkup?
 What is the average time gap between two checkups for the same patient?
 Which patients have never had a checkup recorded?

 5.Risk Scores
 What percentage of patients are LOW, MEDIUM, and HIGH risk?
 Which city has the highest concentration of high-risk patients?
 How many patients had their risk level downgraded (from HIGH → MEDIUM/LOW) over time?
 Which lifestyle factors (sleep, exercise, diet) are most common among HIGH risk patients?
 What remarks are most frequently given in risk scores?

 6.Combined Insights
 Are smokers more likely to be in HIGH risk compared to non-smokers?
 Do patients with poor diet quality and family history of heart disease have higher risk scores?
 Which age group (20–30, 31–40, 41–50, etc.) has the most HIGH risk patients?
 What percentage of patients with cancer in family history have done preventive checkups?
 Is there a relationship between alcohol consumption and number of checkups attended?
 Which patients are HIGH risk but have not had a checkup in the last year?

## G) Conclusion
This project demonstrates how a relational database can organize and analyze preventive healthcare data. Using SQL, we explored patient demographics, lifestyle habits, family history, checkups, and risk scores to generate actionable insights. The analysis highlights patterns in health risks and supports data-driven preventive care and early intervention.

## H) Author
### Vaibhav Ashok Gade
This project, Preventive Healthcare Data Analysis using MySQL, is part of my portfolio, showcasing SQL and data analysis skills essential for Data Analyst roles. It demonstrates my ability to design relational databases, clean and explore data, and generate actionable insights from complex datasets.
   
