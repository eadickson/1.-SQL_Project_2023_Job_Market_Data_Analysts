# Introduction
Project "Data Job Analysis" is a story about the 2023 job market for professionals in data analytics; I tell it in SQL expressions. Kudos to Luke Barousse, expert in data analysis and extraordinary educator, for his invaluable teachings and recommendations. This project was possible thanks to him. 

"Data Job Analysis" concentrates on data analyst positions; however, each of its topics and the significance of the project as a whole constitute a strategic guide for analysis and data processing that you can customize to your needs. You will learn about the job market trends in 2023 for top paying jobs, in-demand skills, the relationship between high demand jobs and high salary, and other interesting facts particular to jobs in the data analytics world. 

You can find the SQL queries for this project here: [project_sql folder](/project_sql/).

# Background
I created this project as part of my journey in the refinement of analytical skills for my professional career in Business Administration and Finance. "Data Job Analysis" provides great insights - and the SQL steps for arriving there - about the data analyst job market. I will be showing you what may be the most impactful insights into data analyst jobs, built from the amazing database kindly provided by Luke for this purpose. 

## The questions that my SQL queries answer are the following:

1. Which are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. Which are the optimal skills to learn?

# Used Tools
Throughout this fabulous learning journey, the following were my key tools:

-**SQL:** The essence of my analysis; critical for: querying the database, piece together the information into great insights.

-**PostgreSQL:** The chosen database management system, a robust tool for managing the data required.

-**Visual Studio Code:** My code editor for data management and SQL query execution.

-**Git & GitHub:** For version control and Git repository hosting, also for ensuring collaboration and project tracking; these make possible the sharing of my SQL scripts and analyses with you.

# The Analysis
Each query in the project covers a different aspect of the data analyst job market. The following are the answers to the above listed questions:

### 1. Top Paying Data Analyst Jobs:
This exercise identifies the highest-paying roles. 

Steps: filtering data analyst positions by average yearly salary and location, particularly for remote jobs. 

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC
LIMIT 10;
```
What it means:

- Top 10 paying data analyst roles range from $184,000 to $650,000; the interest in data analysis is showcased across industries. 

You can find the chart for top paying jobs here: [Top Paying Jobs](https://public.tableau.com/views/1_Top_Paying_Jobs_17512342837910/Sheet1?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

(This bar chart visualizes the salary for the top 10 salaries for data analysts; the chart was generated in Tableau from my SQL query results, retrieved from an excel file.)

### 2. Skills for Top Paying Jobs:

This query finds which skills are required for the top-paying jobs. Steps: joined the job postings with the skills data, thus providing information about which skills do employers value more for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY 
        salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*, --selects all the columns from that table
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
What it means:

The query uncovered which are the most demanded skills for the top 10 highest paying data analyst jobs in 2023, in the following order: 1.  **SQL**, 2. **Python**, 3. **Tableau**. Other skills like **R, Showflake, Pandas**, and **Excel** show varying degrees of demand.

You can find the chart for the top-paying job skills here: [Skills for Top Paying Jobs](https://public.tableau.com/views/2_Skills_For_Top_Paying_Jobs/Sheet1?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

(This bar chart visualizes the count of skills for the top 10 paying jobs for data analysts; the chart was generated in Tableau from my SQL query results saved in an Excel file.)

### 3. In-Demand Skills for Data Analysts:
This query is for identifying the skills most demanded in job postings.

```sql
SELECT 
  skills,
  COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_title_short = 'Data Analyst' AND
  job_work_from_home = True
GROUP BY
  skills
ORDER BY
  demand_count DESC
LIMIT 5;
```
What it means:
The query's results showcase the most demanded skills for data analysts in 2023. **SQL** and **Excel** are fundamental. **Programming** and **Visualization Tools** like **Python**, **Tableau**, and **Power BI** are increasing in importance for data storytelling and decision support.

| Skills | Demand Count |
|--------|--------------|
| SQL    |	   7,291    |
| Excel	 |     4,611    |
| Python |	   4,330    |
|Tableau |     3,745    |
|Power BI|     2,609    |


(Table with the top 5 skills in data analyst jobs postings and their demand in the job market.)

### 4. Skills Based on Salary:
This query helps discover which skills are the highest paying ones; the results show the relation between the average salaries and the different skills.

```sql
SELECT 
  skills,
  ROUND(AVG(salary_year_avg),0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
  job_title_short = 'Data Analyst' 
  AND salary_year_avg IS NOT NULL
  AND  job_work_from_home = True 
GROUP BY
  skills
ORDER BY
  avg_salary DESC
LIMIT 25;
```
What it means:

- Top salaries are demanded for big data technologies, machine learning tools, and Python libraries; the results show that the industry highly values the data processing and predictive modeling capabilities. There is also a tendency for skills demand in development and deployment tools within a highly remunerated mix of data analysis and engineering. The impact of the proficiency in cloud and data engineering tools hints to the increasing need for cloud-based analytics environments and earning potential for data analytic roles.


|    Skills      |Avg Salary($)|
|----------------|-------------|
|Pyspark	       |  208,172    |
|Bitbucket	     |  189,155    |
|Couchbase	     |  160,515    |
|Watson	         |  160,515    |  
|Datarobot	     |  155,486    |
|Gitlab	         |  154,500    |
|Swift	         |  153,750    |
|Jupyter         |	152,777    |
|Pandas	         |  151,821    |
|Elasticsearch	 |  145,000    |
|Golang          |	145,000    |
|Numpy           |	143,513    |
|Databricks      |	141,907    |
|Linux	         |  136,508    |
|Kubernetes	     |  132,500    |
|Atlassian	     |  131,162    |
|Twilio	         |  127,000    |
|Airflow	       |  126,103    |
|Scikit-learn    |  125,781    |
|Jenkins	       |  125,436    |
|Notion	         |  125,000    |
|Scala	         |  124,903    |
|Postgresql	     |  123,879    |
|Gcp	           |  122,500    |
|Microstrategy	 |  121,619    |


(Table with the top 10 paying skills and their corresponding average salary, for data analysts.)

### 5. Optimal Skills to Learn:
This query shows the relationship between skills, levels of demand and the potential for high pay, hinting to a strategic approach for skill development.

```sql
SELECT

    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) as demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg),0) AS avg_salary
FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND  job_work_from_home = True 
    GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
   LIMIT 25;
   ```
  You can fid the chart for optimal skills to learn data analytics here: [Optimal Skills to Learn](https://public.tableau.com/views/1_Top_Paying_Jobs/Sheet2?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

(This bar chart visualizes the optimal skills for data analysts, sorted by demand; the chart was generated in Tableau from my SQL query results saved in an Excel file.)

What it means:

- Python and R are remarkable in the results due to their high demand. The data also points out to the fact that these skills are widely available, which reflects in the average salaries. Specialized skills have  significant demand with relatively high average salaries. Tableau reflects high demand and an average salary of $99,288, a statement to the growing importance of data visualization and business intelligence tools for critical insights in decision-making. The increasing need in expertise for data storage retrieval and management reflects in the demand for skills in traditional and NoSQL databases.


# What I Learned
Throughout my journey in the job market world for data analysts, I could strategically redesign my analytical approach to precious data with the extraordinary power of SQL. The journey included extracting data, piecing it together, making sense of it, and then preparing it for helping others to gain insights and make decisions. My journey included: **complex query crafting**, **data aggregation** and **increasee in analytical power**.

# Conclusions

## Insights:

**1. Top-paying data analyst jobs:**
- Data analyst salaries range widely, up to $650,000, showing high earning potential.
- Top-paying roles are found across industries, including tech, telecom, and finance.
- There is a broad variety of job titles and specializations within the data analytics field.

**2. Skills required for top-paying jobs:**
- SQL, Python, and Tableau are the top 3 most required skills in high-paying analyst jobs.
- Other relevant tools include R, Snowflake, Pandas, and Excel, showing a mix of traditional and modern skills in demand.

**3. Skills with highest demand for data analysts:**
- Foundational tools like SQL and Excel are essential for everyday data handling.
- Programming and visualization tools (e.g., Python, Tableau, Power BI) are crucial for storytelling and supporting decisions.

**4. Skills are associated with higher salaries:**
- High-paying roles often require big data and machine learning tools like PySpark, Jupyter, and Pandas.
- Engineering skills such as GitLab, Kubernetes, and Airflow show demand for pipeline automation and deployment.
- Cloud expertise (e.g., GCP, Databricks) indicates growing reliance on cloud analytics environments.

**5. Optimal skills to learn:**
- Python and R are in high demand but also widely available.
- Cloud tools like Snowflake, AWS, Azure, and BigQuery are well-paid and increasingly important.
- Business intelligence tools (especially Tableau) are critical for communicating insights.
- Database technologies, both traditional (SQL, Oracle) and NoSQL, remain central to data analysis work.

## Closing Thoughts:

This project has been a pivotal step in my SQL learning journey, allowing me to apply foundational and advanced querying techniques to explore real-world data. Beyond strengthening my technical skills, the analysis offered strategic insights into the data analyst job market, revealing not only which tools are in the highest demand, but also how industry trends are shaping salary potential and required expertise. It became clear that mastering SQL, alongside tools like Python, Tableau, and cloud technologies, is essential for standing out in a competitive field. This experience reinforced my commitment to continuous learning and has helped me map a clearer, more focused path toward a future in data analytics.
