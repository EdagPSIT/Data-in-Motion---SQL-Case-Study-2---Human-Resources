-- SQL Challenge Questions

--1. Find the longest ongoing project for each department.
SELECT 
	d.name,
    SUM(end_date - start_date) AS Project_Ongoing_Days
FROM departments d
INNER JOIN projects p
ON d.id = p.department_id
GROUP BY d.name;

--2. Find all employees who are not managers.
SELECT 
	name,
    job_title
FROM employees 
WHERE id NOT IN (SELECT manager_id FROM departments);
	

--3. Find all employees who have been hired after the start of a project in their department.
SELECT 
	e.name,
    hire_date
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id
INNER JOIN projects p
ON d.id = p.department_id
WHERE hire_date > start_date;

--4. Rank employees within each department based on their hire date (earliest hire gets the highest rank).
SELECT e.*,
	d.name,
    DENSE_RANK() OVER(PARTITION BY d.name ORDER BY hire_date ASC) AS joining_rank
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id;

--5. Find the duration between the hire date of each employee and the hire date of the next employee hired in the same department.
WITH next_hire_data AS(
SELECT 
	e.*,
    d.name,
    LEAD(hire_date,1) OVER(PARTITION BY d.name ORDER BY hire_date) AS next_hiree
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id
)
SELECT *,
	next_hiree - hire_date AS next_hiree_days
FROM next_hire_data;
	

