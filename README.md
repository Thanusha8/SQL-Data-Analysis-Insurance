# SQL-Data-Analysis-Insurance
Analyzes medical insurance claims patterns by demographics, region, and health factors.


## Project Objective

Examine insurance claim data to identify cost patterns by age, region, BMI, and smoking status through patient analysis and claim amount comparisons.

## Script

-- The Medical Insurance dataset on Kaggle has the following columns:
-- PatientID : Integer Value.
-- age: age of primary beneficiary
-- sex: gender of primary beneficiary (male or female)
-- bmi: body mass index of primary beneficiary
-- children: number of children covered by health insurance / number of dependents
-- smoker: whether the primary beneficiary is a smoker or not (yes or no)
-- region: the beneficiary's residential area in the US (northeast, southeast, southwest, or northwest)
-- charges: individual medical costs billed by health insurance


create database insurance;

use insurance;

-- 1. Select all columns for all patients.

select * from insurance;

-- 2. Display the average claim amount for patients in each region.

select region,avg(claim) as avg_claim from insurance
group by region;

-- 3. Select the maximum and minimum BMI values in the table.

select max(bmi) as max_bmi,min(bmi) as min_bmi from insurance;

-- 4. Select the PatientID, age, and BMI for patients with a BMI between 40 and 50.

select PatientID,age,bmi from insurance
where bmi between 40 and 50;

-- 5. Select the number of smokers in each region.

select region,count(PatientID) from insurance
where smoker="yes"
group by region;

-- 6. What is the average claim amount for patients who are both diabetic and smokers?

select avg(claim) as avg_claim from insurance
where diabetic="yes" and smoker="yes";

-- 7. Retrieve all patients who have a BMI greater 
-- than the average BMI of patients who are smokers.

select * from insurance where smoker="yes" and  
bmi>(select avg(bmi) from insurance where smoker="Yes");


-- 8. Select the average claim amount for patients in each age group.

select 
	case when age < 18 then "Under 18"
    when age between 18 and 30 then "18-30"
    when age between 31 and 50 then "31-50"
    else "Over 50"
end as age_group,
round(avg(claim),2) as avg_claim
from insurance
group by age_group;

-- 9. Retrieve the total claim amount for each patient, 
-- along with the average claim amount across all patients.

select *,sum(claim) over(partition by PatientID) as total_claim , 
avg(claim) over() as avg_claim from insurance;

-- 10. Retrieve the top 3 patients with the highest claim amount, along with their 
-- respective claim amounts and the total claim amount for all patients.

select PatientID, claim,sum(claim) over() as total_claim from insurance
order by claim desc limit 3;

-- 11. Select the details of patients who have a claim amount 
-- greater than the average claim amount for their region.

select * from (select *, avg(claim)  over(partition by region) 
as avg_claim from insurance) as subquery
where claim > avg_claim;



-- 12. Retrieve the rank of each patient based on their claim amount.

select * , rank() over(order by claim desc) from insurance;

-- 13. Select the details of patients along with their claim amount, 
-- and their rank based on claim amount within their region.

select *, rank() over(partition by region order by claim desc) from insurance;

