# MySQL-PowerBI-HR-Dashboard-Project
<img width="1241" height="717" alt="Image" src="https://github.com/user-attachments/assets/bf074f5f-4daa-43a9-b2da-c3dd79922a71" />

# HR Analytics Dashboard (2000–2020)

## ✅ Overview

This project focuses on analyzing Human Resources (HR) data to uncover meaningful workforce insights such as demographics, turnover rates, departmental distributions, tenure, and location-wise employee breakdowns. The end-to-end process includes **data cleaning using MySQL**, **exploratory analysis using SQL queries**, and **interactive visualization using Power BI**.

---

## 📂 Data Used

- **Dataset**: HR data of over **22,000 employees** from **2000 to 2020**
- **Format**: CSV file with details such as gender, birthdate, hire date, termination date, job title, department, and location.

---

## 🧹 Data Cleaning with MySQL

The raw dataset had several inconsistencies, especially in date formats and column naming. The data was cleaned and prepared using **MySQL Workbench**, where the following tasks were performed:

- Renamed unreadable or corrupted columns (e.g., employee ID).
- Standardized date formats for `birthdate`, `hire_date`, and `termdate` to `YYYY-MM-DD`.
- Converted datetime values with timezones to valid date formats.
- Fixed future birth years caused by two-digit year formatting (e.g., '2067' → '1967').
- Handled missing or invalid `termdate` values by replacing them with `0000-00-00` for active employees.
- Created a new `age` column using `birthdate`.

Once cleaned, the data was ready for querying to derive business insights.

---

## 🔍 HR Business Questions & SQL Queries
### 1. What is the gender breakdown of employees in the company?
```sql
SELECT gender, count(*) AS count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY gender;
```
### 2. What is the race/ethnicity breakdown of employees in the company?
```sql
SELECT race, count(*) as count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY race
ORDER BY count DESC;
```
### 3. What is the age distribution of employees by gender in the company?
```sql
SELECT 
	CASE
		WHEN age>=18 AND age<=24 THEN '18-24'
        WHEN age>=25 AND age<=34 THEN '25-34'
        WHEN age>=35 AND age<=44 THEN '35-44'
        WHEN age>=45 AND age<=54 THEN '45-54'
		WHEN age>=55 AND age<=64 THEN '55-64'
        ELSE '65+'
	END AS age_group, gender,
    count(*) as count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY age_group, gender
ORDER BY age_group, gender;
```
### 4. How many employees work at headquarters versus remote locations?
```sql
SELECT location, count(*) as count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY location;
```
### 5. What is the average length of employment for employees who have been terminated?
```sql
SELECT
	round(avg(datediff(termdate, hire_date))/365,0) as avg_length_employment
FROM hr
WHERE age >=18 AND termdate <= curdate() AND termdate != '0000-00-00';
```
### 6. How does the gender distribution vary across departments and job titles?
```sql
SELECT department, gender, count(*) as count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY department, gender
ORDER BY department;
```
### 7. What is the distribution of job titles across the company?
```sql
SELECT jobtitle, count(*) as count
FROM hr
WHERE age >=18 AND termdate = '0000-00-00'
GROUP BY jobtitle
ORDER BY jobtitle DESC;
```
### 8. Which department has the highest turnover rate?
```sql
SELECT department,
	total_count,
    terminated_count,
    terminated_count/total_count as termination_rate
    FROM(
		SELECT department,
        count(*) as total_count,
        sum(CASE WHEN termdate != '0000-00-00' AND termdate <= curdate() THEN 1 ELSE 0 END) as terminated_count
        FROM hr
		WHERE age >= 18
		GROUP BY department
        ) as subquery
ORDER BY termination_rate DESC;
```
### 9. What is the distribution of employees across locations by state?
```sql
SELECT location_state, COUNT(*) as count
FROM hr
WHERE age >= 18 AND termdate = '0000-00-00'
GROUP BY location_state
ORDER BY count DESC;
```
### 10. How has the company's employee count changed over time based on hire and term dates?
```sql
SELECT 
    year, 
    hires, 
    terminations, 
    (hires - terminations) AS net_change,
    ROUND(((hires - terminations) / hires * 100), 2) AS net_change_percent
FROM (
    SELECT 
        YEAR(hire_date) AS year, 
        COUNT(*) AS hires, 
        SUM(CASE WHEN termdate <> '0000-00-00' AND termdate <= CURDATE() THEN 1 ELSE 0 END) AS terminations
    FROM 
        hr
    WHERE age >= 18
    GROUP BY 
        YEAR(hire_date)
) subquery
ORDER BY 
    year ASC;
```
### 11. What is the tenure distribution for each department?
```sql
SELECT department, round(avg(datediff(termdate,hire_date)/365),0) as avg_tenure
FROM hr
WHERE termdate <= CURDATE() AND termdate != '0000-00-00' AND age >= 18
GROUP BY department;
```
## 📊 Power BI Dashboard

The cleaned and aggregated data from MySQL was imported into **Power BI** to build an interactive and dynamic dashboard. The visualizations include:

- **Gender and Age Group Breakdown**
- **Race/Ethnicity Representation**
- **Job Title & Department Distribution**
- **Employee Turnover & Tenure Analysis**
- **Geographic Distribution by State & Location Type**
- **Year-over-Year Employee Growth**

The dashboard enables users to filter data by department, state, job title, and time period to gain detailed and actionable insights quickly and efficiently.

---

## 📌 Summary / Conclusion

- 👤 **Gender**: Male employees are slightly more than female employees.  
- 🌎 **Race**: Majority of employees are White; Native Hawaiian and American Indian are least represented.  
- 🎂 **Age**: Most employees are between 25–34 and 35–44 years old. Youngest is 22, oldest is 59.  
- 🏢 **Location**: More employees work at headquarters than remotely.  
- ⏳ **Tenure**: Average employment length for terminated employees is ~8 years.  
- 📋 **Departments**: Balanced gender ratio, with some variation by department.  
- 📍 **State**: Most employees are located in Ohio.  
- 📈 **Growth**: Company has seen steady net employee growth over the years.  
- 🧾 **Tenure by Department**: Sales and Marketing show the longest average tenure; Legal, Services and etc. have the shortest.


## 👨‍💻 Author

**Rehan Behera**  
- 📧 Email: [rehanbehera7@gmail.com](mailto:rehanbehera7@gmail.com)  
- 🌐 GitHub: [rehanbehera22](https://github.com/rehanbehera22)  
- 💼 LinkedIn: [rehan-behera-31982b315](https://www.linkedin.com/in/rehan-behera-31982b315)
