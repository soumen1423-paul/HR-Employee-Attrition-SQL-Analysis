# 📊 HR Employee Attrition Analysis using SQL

## 🎯 Project Objective

The objective of this project is to analyze HR employee data using SQL and answer business questions related to employee attrition, salary, performance, promotions, and workforce planning.

## 📁 Dataset

- Dataset: HR Employee Attrition
- Records: 1470
- Columns: 35
- Database: MySQL

## 🛠 Tools Used

- MySQL Workbench
- SQL
- GitHub
## 🧠 SQL Skills Used

- SELECT
- WHERE
- GROUP BY
- HAVING
- ORDER BY
- Aggregate Functions
- CASE WHEN
- Subqueries
- Correlated Subqueries
- Common Table Expressions (CTEs)
- Window Functions
  - DENSE_RANK()
  - LAG()
  - LEAD()
  - NTILE()
  - PERCENT_RANK()
  - SUM() OVER()
- Views
## 📌 Business Questions

### 👥 Employee Demographics Analysis

- Average age of employees by department
- Highest and lowest monthly income
- Employees by education field
- Employees by marital status
- Employees who worked longer than the company average
- Employees who have not received a promotion for more than 5 years

### 💰 Salary & Compensation Analysis

- Average monthly income by department
- Department salary expense and ranking
- Employees earning more than their department average
- Second-highest-paid employee in every department
- Top 3 highest-paid employees in each department
- Top 5 highest-paid employees in the company
- Employee salary ranking
- Compare salary using LAG()
- Compare salary using LEAD()
- Salary quartiles using NTILE()
- Top 10% salary earners using PERCENT_RANK()
- Cumulative monthly salary expense

### ⭐ Employee Performance Analysis

- Employees with above-average performance rating
- Best-performing department

### 📉 Attrition Analysis

- Department with the highest attrition rate
- Job role with the highest attrition rate
- Compare attrition between male and female employees
- Impact of overtime on attrition
- Business travel category with the highest attrition
- Age group with the highest attrition
- Compare average salary of employees who left and stayed
- Education field with the highest attrition
- Identify employees at high risk of attrition

### 🏢 Department Analysis

- Departments where more than 20% of employees work overtime
- Departments with below-average job satisfaction
- Department KPI Dashboard
- ---

# ⭐ Featured SQL Queries

This project highlights advanced SQL techniques used to solve real-world HR business problems.

---

## 1️⃣ Employees Earning Above Department Average

**Skills:** Correlated Subquery

```sql
SELECT employee_number,
       department,
       monthly_income
FROM hr_employees e
WHERE monthly_income >
(
    SELECT AVG(monthly_income)
    FROM hr_employees
    WHERE department = e.department
);
```

---

## 2️⃣ Second Highest Paid Employee in Every Department

**Skills:** CTE + DENSE_RANK()

```sql
WITH monthly_dept_rank AS
(
    SELECT employee_number,
           department,
           monthly_income,
           DENSE_RANK() OVER
           (
               PARTITION BY department
               ORDER BY monthly_income DESC
           ) AS salary_rank
    FROM hr_employees
)

SELECT employee_number,
       department,
       monthly_income
FROM monthly_dept_rank
WHERE salary_rank = 2;
```

---

## 3️⃣ Compare Employee Salary Using LAG()

**Skills:** Window Function

```sql
WITH previous_salary AS
(
    SELECT employee_number,
           department,
           monthly_income,
           LAG(monthly_income)
           OVER
           (
               PARTITION BY department
               ORDER BY monthly_income DESC
           ) AS previous_month_income
    FROM hr_employees
)

SELECT employee_number,
       department,
       monthly_income,
       previous_month_income,
       monthly_income - previous_month_income AS salary_difference
FROM previous_salary
ORDER BY department,
         monthly_income DESC;
```

---

## 4️⃣ Top 10% Highest Paid Employees

**Skills:** PERCENT_RANK()

```sql
WITH salary_percent_rank AS
(
    SELECT employee_number,
           job_role,
           department,
           monthly_income,
           PERCENT_RANK() OVER
           (
               ORDER BY monthly_income DESC
           ) AS income_percent_rank
    FROM hr_employees
)

SELECT employee_number,
       job_role,
       department,
       monthly_income,
       income_percent_rank
FROM salary_percent_rank
WHERE income_percent_rank <= 0.10
ORDER BY monthly_income DESC;
```

---

## 5️⃣ Department KPI Dashboard

**Skills:** VIEW + Aggregate Functions

```sql
CREATE VIEW department_kpi_dashboard AS

SELECT department,
       COUNT(*) AS total_employees,
       SUM(attrition='yes') AS employees_left,
       ROUND(SUM(attrition='yes')*100/COUNT(*),2) AS attrition_rate,
       ROUND(AVG(monthly_income),2) AS avg_monthly_income,
       ROUND(AVG(age),2) AS avg_age,
       ROUND(AVG(job_satisfaction),2) AS avg_job_satisfaction,
       ROUND(AVG(years_at_company),2) AS avg_years_at_company
FROM hr_employees
GROUP BY department;
```

---

## 6️⃣ High-Risk Employees Based on Attrition Factors

**Skills:** Business Logic + Filtering

```sql
SELECT employee_number,
       department,
       job_role,
       over_time,
       job_satisfaction,
       work_life_balance,
       attrition
FROM hr_employees
WHERE over_time = 'yes'
  AND job_satisfaction <= 2
  AND work_life_balance <= 2
ORDER BY job_satisfaction,
         work_life_balance;
```
# 📈 Key Business Insights

- Sales department has the highest employee attrition rate.
- Employees working overtime are more likely to leave the company.
- Employees under 30 have the highest attrition rate.
- Lower-paid employees have a higher attrition rate.
- Department KPI Dashboard provides department-wise HR performance metrics.
- Salary ranking and quartile analysis support compensation planning.
- High-risk employees can be identified using overtime, job satisfaction, and work-life balance.
- Employees earning above the department average often hold key positions.
