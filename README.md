# DBT Operation on BigQuery

## Overview
This project demonstrates the use of DBT (Data Build Tool) and Python with BigQuery for data transformation and analysis. DBT is an open-source tool that enables data analysts and engineers to transform data in their warehouses by building and executing SQL queries. It provides a structured development environment and a range of capabilities that help with data modeling, testing, documentation, and deployment.

## Requirements
- Python (version 3.6 or later)
- DBT (version 0.20.0 or later)
- GCP Service Account

## Setup
1. Install Python and DBT on your local machine.
2. Create a new project directory and navigate to it.
3. Initialize DBT in the project directory using the following 
    ```
        dbt init
    ```

4. Create new folder as `source` inside project directory and create `dbt_learning.yml` file as follows:
    ```
    version: 2

    sources:
    - name: dbt_learning
        tables:
        - name: netflix_titles
    ```

5. Get your key credential from GCP servie account. Generate is to json and copy the file into `service-account.json`.

6. Prepate your GCP-BigQuery.Create new Dataset as `dbt_learning` and table as `netflix_titles`. You can download netflix data on
 
    https://www.google.com/url?sa=D&q=https://drive.google.com/file/d/1-sO2CY0tJ9IdycsJqBPyDNIhm85gfeDA/view%3Fusp%3Dshare_link&ust=1681534860000000&usg=AOvVaw3HB2bfCON80Q-m-EGmAJK1&hl=en-GB

6. Create sql file as `netflix_titles.sql`. the File will contain jinja source to make easier populate bigquery resource.
    ```
        {{
            config(
                materialized="table"
            )
        }}

        SELECT
        *
        FROM
            {{ ref("netflix_titles") }}
        WHERE
            type = "Movie"
    ```

7. Create SQL File `movie_titles.sql` for movie table in bigQuery and it will refer to `netflix_titles.sql` as CTE table.
    ```
        {{
            config(
                materialized="table"
            )
        }}

        SELECT
        *
        FROM
            {{ ref("netflix_titles") }}
        WHERE
            type = "Movie"
    ```
8. Create SQL File `show_titles.sql` for show table in bigQuery and it will refer to `netflix_titles.sql` as CTE table.
    ```
        {{
            config(
            materialized="incremental"
            )
        }}
        SELECT
            *
        FROM
            {{ ref("netflix_titles") }}
        WHERE
            type = "TV Show"

        {% if is_incremental() %}
            AND date_added > (
                SELECT
                    MAX(date_added)
                FROM
                    {{ this }}
            )
        {% endif %}
    ```
9. Build the DBT project using the following command:
    ```
        dbt run
    ```
    if running successfully, it will trigger to create new table on BigQuery
    
    ![image](md_image\BigQuery_Result.png)
10. Generate Documentation
    ```
        dbt docs generate
        dbt docs serve
    ```
    ![image](md_image\DBTDocs.png)
## Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](https://community.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
