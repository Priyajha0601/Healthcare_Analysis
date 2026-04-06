# Healthcare Data Analysis & Patient Stratification
Author: Priya Kumari
Tech Stack: Databricks

## Project Overview 
This project involves the design and analysis of a relational healthcare database. The goal is to perform patient stratification, analyze treatment costs, and identify clinical abnormalities. By joining patient records with lab results and diagnostic data, the analysis provides insights into hospital efficiency and patient health outcomes.

## Database Schema & Design 
The database consists of four primary tables designed to maintain data integrity through primary and foreign key relationships:
- Patients: Core demographic and admission data.
- Diagnosis: Reference table for medical conditions.
- Outcomes: Tracks patient status post-discharge (e.g., Recovered, Deceased, Stable).
- Labs: Historical lab test results for individual patients.

## Deep Dive: Clinical & Operational Analysis

1.  ### Unified Clinical Patient View Objective: 
	To provide medical staff with a single, consolidated view of a patient's stay, including their diagnosis, discharge status, and most recent lab results.
	Business Value: Reduces manual data lookup time for clinicians.

	```SQL SELECT p.patientid, p.name, d.diagnosisname, o.outcomename, l.testname, l.result, l.normalrange FROM patients p JOIN diagnosis d ON p.diagnosisid = d.diagnosisid JOIN outcomes o ON p.outcomeid = o.outcomeid JOIN labs l ON p.patientid = l.patientid ORDER BY p.patientid;```

<img width="1568" height="513" alt="image" src="https://github.com/user-attachments/assets/a25c7d00-3168-4075-a08b-176caed9ff5d" />

 3. ### Diagnostic Benchmarking (Average Lab Values) Objective: 
	To calculate the mean lab result for every test, grouped by diagnosis.
	Insight: This helps establish a "clinical baseline" for specific conditions. For example, it identifies what the average blood sugar looks like specifically for "Diabetes" patients versus "General Checkup" patients.

	```SQL SELECT d.diagnosisname, l.testname, AVG(l.result) AS AvgResult FROM patients p JOIN diagnosis d ON p.diagnosisid = d.diagnosisid JOIN labs l ON p.patientid = l.patientid GROUP BY d.DiagnosisName, l.TestName ORDER BY d.DiagnosisName, l.TestName;```

<img width="1567" height="487" alt="image" src="https://github.com/user-attachments/assets/a157602f-a794-4e0d-8c19-f861aebe0bb9" />

2. ### High-Risk Patient Stratification (Abnormal Flags) Objective:
	To flag patients who meet specific clinical "danger zones" (e.g., Blood Sugar > 120, Cholesterol > 200, or Hemoglobin < 13).
Risk Management: This query identifies patients requiring immediate intervention or specialized care plans.

	```SQL SELECT p.patientid, p.name, COUNT(*) AS AbnormalCount FROM Healthcare.patients p JOIN Healthcare.labs l ON p.patientid = l.patientid WHERE (l.testname = 'Blood Sugar' AND l.result > 120) OR (l.testname = 'Cholesterol' AND l.result > 200) OR (l.testname = 'Hemoglobin' AND l.result < 13) GROUP BY p.patientid, p.name ORDER BY AbnormalCount DESC;```

<img width="1562" height="477" alt="image" src="https://github.com/user-attachments/assets/106f51cc-6efa-49cb-af1e-ec24d55df2d2" />


3. ### Revenue & Cost Analysis by Department Objective: 
	Aggregating total treatment costs per diagnosis category.
	Financial Impact: Identifies the most expensive conditions to treat, allowing hospital leadership to optimize insurance billing and resource budgeting.
	
	```SQL SELECT d.diagnosisname, SUM(p.treatmentcost) AS TotalCost FROM Healthcare.patients p JOIN Healthcare.diagnosis d ON p.diagnosisid = d.diagnosisid GROUP BY d.DiagnosisName ORDER BY TotalCost DESC;```

<img width="1562" height="480" alt="image" src="https://github.com/user-attachments/assets/c2e0f213-1833-4fef-b1e1-62e2c1a22011" />

5. ### Geriatric Vulnerability Monitoring Objective:
	Filtering for male patients over 65 who were not fully "Recovered" at discharge.
	Readmission Prevention: This cohort has a high risk of hospital readmission. By identifying them, social workers can coordinate home-health services.
	
	```SQL SELECT p.patientid, p.name, d.diagnosisname, o.outcomename FROM Healthcare.patients p JOIN Healthcare.diagnosis d ON p.diagnosisid = d.diagnosisid JOIN Healthcare.outcomes o ON p.outcomeid = o.outcomeid WHERE p.age > 65 AND gender = 'M' AND o.outcomename != 'Recovered';```

<img width="1567" height="527" alt="image" src="https://github.com/user-attachments/assets/37e1f175-b0dc-40ff-af9d-105ec7c283fe" />

	
6. ### Longitudinal Patient History (Case Study: Patient 10) Objective:
	Extracting a time-series view of lab results for a specific patient.
	Clinical Utility: Allows doctors to see if a patient’s condition improved or deteriorated during their admission period.
	
	```SQL SELECT l.testname, l.result, p.admissiondate FROM healthcare.labs l JOIN healthcare.patients p ON l.patientid = p.patientid WHERE p.PatientID = 10 ORDER BY p.AdmissionDate;```

<img width="1568" height="482" alt="image" src="https://github.com/user-attachments/assets/8e36670d-7e61-495b-954d-5a4fb2e2b6e6" />

	
7. ### Outcome Distribution Analysis Objective: 
	A cross-tabulation of Diagnosis vs. Outcome.
	Quality of Care: Highlights which diagnoses have high recovery rates and which have higher mortality or complication rates, driving internal quality audits.
	
	```SQL SELECT d.diagnosisname, o.outcomename, COUNT(*) AS OutcomeCount FROM Healthcare.patients p JOIN Healthcare.diagnosis d ON p.diagnosisid = d.diagnosisid JOIN Healthcare.outcomes o ON p.outcomeid = o.outcomeid GROUP BY d.diagnosisname, o.outcomename ORDER BY d.diagnosisname, o.outcomename DESC;``` 

	<img width="1577" height="527" alt="image" src="https://github.com/user-attachments/assets/5458aa5b-2153-4769-ba35-55ce14403c11" />


## Technical Skills Demonstrated Advanced Joins:
- Multi-table Inner Joins to connect clinical and financial data.
- Aggregation & Grouping: Using SUM, AVG, and COUNT to extract high-level metrics.
- Conditional Filtering: Complex WHERE clauses for risk stratification.
- Data Modeling: Designing a schema with Primary/Foreign key constraints.
