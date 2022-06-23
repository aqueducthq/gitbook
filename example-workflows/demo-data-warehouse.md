# Demo Data Warehouse

To make it easy for you to get started and play around with Aqueduct, every Aqueduct server comes with a demo database. The `aqueduct_demo` database instance comes with the following pre-loaded datasets, which you can use in your experiments:

*   **tips \[**[**Source**](https://github.com/mwaskom/seaborn-data/blob/master/tips.csv)**]:** This classic dataset contains information about the TIPS received by a waiter at various times of day along with patron characteristics.

    ```sql
    SELECT total_bill, tip, sex, smoker, day, time, size
    FROM tips;
    ```
*   **mpg \[**[**Source**](https://github.com/mwaskom/seaborn-data/blob/master/mpg.csv)**]:** This classic dataset contains the fuel efficiency for many different models of cars along with properties of each car.

    ```sql
    SELECT mpg, cylinders, displacement, horsepower, weight, acceleration
    		   model_year, origin, name
    FROM mpg;
    ```
*   **wine \[**[**Source**](https://archive.ics.uci.edu/ml/datasets/wine+quality)**]:** This contains information about various wines along with ratings. The data has been slightly corrupted to make things more interesting. You can use the builtin  [decision-tree](https://www.notion.so/Decision-Tree-61dc9f001c494452923af4ff58a8ba55) function to try predicting the rating or imputing missing values.

    ```sql
    SELECT index, color, fixed_acidity, volatile_acidity, citric_acid, 
           residual_sugar, chlorides, free_sulfur_dioxide, 
           total_sulfur_dioxide, density, ph, sulphates, alcohol, quality 
    FROM wine;
    ```
*   **hotel\_reviews:** Reviews for various hotels.

    ```sql
    SELECT hotel_name, review_date, reviewer_nationality, review
    FROM hotel_reviews;
    ```
*   **customers**: Synthetic data capturing features about Aqueduct customers, including how many data scientists they have and whether or not they use dbt.

    ```sql
    SELECT cust_id, n_workflows, n_rows, n_users, company_size, n_integrations, 
    			 n_support_tickets, duration_months, using_deep_learning, n_data_eng,
    			 using_dbt
    FROM hotel_reviews;
    ```
