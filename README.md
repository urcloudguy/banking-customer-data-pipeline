# Banking Customer Data Quality & ETL Pipeline

## Project Overview

This project demonstrates a small banking customer data-quality and ETL pipeline built using Python, Pandas, and MySQL.

The pipeline reads customer data from a CSV source, performs data-quality validations, separates valid records from exceptions, loads valid records into a MySQL target table, and performs source-to-target reconciliation.

The project also demonstrates an idempotent target load using an upsert approach so that rerunning the same data does not create duplicate customer records.

> **Note:** This project uses synthetic customer data for learning and portfolio purposes. No real customer or employer data is used.

---

## Business Objective

In banking environments, customer data must be complete, accurate, consistent, and suitable for downstream processes.

The objective of this project is to demonstrate how a data pipeline can:

- Extract customer data from a source file
- Apply data-quality business rules
- Identify and isolate invalid records
- Load valid records into a target database
- Reconcile source and target populations
- Safely rerun the load without creating duplicate records

---

## ETL Flow

```text
CSV Source
    |
    v
Python / Pandas
    |
    +--> Data Quality Validation
    |       |
    |       +--> Valid Records
    |       |
    |       +--> Exception Records
    |
    v
MySQL Target
    |
    v
Source-to-Target Reconciliation
```

---

## Data Quality Rules

The following validation rules are applied to the customer data:

| Rule | Description |
|---|---|
| Customer ID | Must be present |
| Customer ID uniqueness | Duplicate customer IDs are identified |
| Customer Name | Must not be missing |
| KYC Status | Must be `Completed`, `Pending`, or `Rejected` |
| Balance | Must not be negative for this project's business rule |

Records failing one or more validation rules are classified as exceptions.

---

## Exception Handling

Invalid records are separated from valid records and stored as exception output.

Examples of identified exceptions in the sample data include:

- Missing customer name
- Invalid KYC status
- Negative balance
- Duplicate customer ID

The exception output allows the invalid population to be reviewed separately rather than loading it into the target table.

---

## Reconciliation

The pipeline performs source-to-target reconciliation after loading.

The reconciliation includes:

### Record-level reconciliation

- Source valid record count
- Target record count
- Customer IDs missing from the target
- Unexpected customer IDs in the target

### Attribute-level validation

Customer attributes such as:

- Customer name
- KYC status
- Balance

can be compared between the source and target populations.

A reconciliation is considered successful when the expected source and target populations agree according to the defined business rules.

---

## Idempotent Load

The target load uses an upsert approach based on `customer_id`.

The pipeline handles:

- New customer → Insert
- Existing customer with changed attributes → Update
- Existing customer with unchanged attributes → No duplicate record created

This allows the same pipeline to be rerun without creating duplicate customer records.

For example:

```text
First run:
6 valid customers → 6 target records

Second run with the same data:
0 new records
Target remains at 6 records
```

A test update was also performed on an existing customer's balance to verify that the target record could be updated successfully.

---

## Technologies Used

- **Python**
- **Pandas**
- **MySQL**
- **MySQL Connector/Python**
- **Jupyter Notebook**
- **Git**
- **GitHub**
- **python-dotenv**

---

## Project Structure

```text
banking-customer-data-pipeline/
│
├── .env                  # Local database credentials - not committed
├── .env.example          # Example environment configuration
├── .gitignore            # Files excluded from Git
├── README.md             # Project documentation
│
├── data/
│   ├── source/
│   │   ├── customers.csv
│   │   └── valid_customers.csv
│   │
│   └── exceptions/
│       └── exception_customers.csv
│
├── notebooks/
│   └── 01_customer_validation.ipynb
│
├── sql/
│   └── create_tables.sql
│
├── src/
│   └── pipeline.py
│
├── logs/
└── tests/
```

---

## Security

Database credentials are not stored directly in the Python notebook.

The project uses environment variables stored in a local `.env` file.

The `.env` file is excluded from Git using `.gitignore`.

An `.env.example` file is provided with placeholder values so that another user can configure their own database credentials.

No real customer information or employer data is included in this project.

---

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/urcloudguy/banking-customer-data-pipeline.git
```

### 2. Create the Python environment

Install the required packages:

```bash
pip install pandas mysql-connector-python python-dotenv
```

### 3. Configure database credentials

Create a local `.env` file using `.env.example` as a template.

Example:

```text
DB_HOST=localhost
DB_PORT=3306
DB_USER=your_database_user
DB_PASSWORD=your_database_password
DB_NAME=banking_pipeline
```

### 4. Configure MySQL

Create the required database and target table using the SQL script provided in the project.

### 5. Run the notebook

Open:

```text
notebooks/01_customer_validation.ipynb
```

Run the notebook from beginning to end.

---

## Key Learning Outcomes

This project provided practical experience with:

- ETL pipeline design
- Pandas-based data transformation
- Data-quality validation
- Exception identification
- Source-to-target reconciliation
- MySQL database loading
- Upsert and idempotent processing
- Environment-based credential management
- Basic Git and GitHub workflow

---

## Future Enhancements

Potential future enhancements include:

- Incremental data loading
- Pipeline logging and monitoring
- Automated data-quality tests
- Cloud storage using AWS S3
- Serverless querying using AWS Athena
- AWS Glue-based ETL implementation
- Pipeline orchestration

These enhancements are intentionally outside the scope of the current local implementation.