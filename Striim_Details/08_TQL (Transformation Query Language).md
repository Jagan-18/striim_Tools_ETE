# TQL (Transformation Query Language) in Striim:
**TQL (Transformation Query Language)** is Striim's SQL-like language used to:

* Create Sources
* Create Streams
* Create Targets
* Filter data
* Transform data
* Join streams
* Aggregate data
* Build complete CDC pipelines

Think of TQL as **SQL for streaming data**.

---
# TQL Architecture:

```text
Oracle Reader
      |
      v
OrderStream
      |
      v
TQL (CQ)
      |
      v
FilteredStream
      |
      v
BigQuery Writer
```

---
# Basic TQL Components:

## 1. CREATE SOURCE

Reads data from source systems.

Example:

```sql
CREATE SOURCE OracleSource
USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@localhost:1521:ORCL',
    Username:'admin',
    Password:'password'
)
OUTPUT TO OrderStream;
```

---
## 2. CREATE STREAM:

Defines a stream structure.

Example:

```sql
CREATE STREAM EmployeeStream (
    EMP_ID Integer,
    EMP_NAME String,
    SALARY Double
);
```

---
## 3. CREATE CQ (Continuous Query):

Used for transformations.

```sql
CREATE CQ EmployeeCQ
INSERT INTO NewStream
SELECT *
FROM EmployeeStream;
```

---
# Common TQL Transformations:

## 1. Filtering Data

Only employees with salary greater than 50,000.

```sql
CREATE CQ HighSalaryEmployees
INSERT INTO HighSalaryStream
SELECT *
FROM EmployeeStream
WHERE SALARY > 50000;
```

Input:

```text
EMP_ID | NAME  | SALARY
101    | John  | 30000
102    | David | 60000
```

Output:

```text
102 | David | 60000
```

---
## 2. Selecting Specific Columns:

```sql
CREATE CQ EmployeeNames
INSERT INTO NameStream
SELECT EMP_ID, EMP_NAME
FROM EmployeeStream;
```

---
## 3. Rename Columns:

```sql
CREATE CQ EmployeeRename
INSERT INTO NewStream
SELECT
    EMP_ID,
    EMP_NAME AS EMPLOYEE_NAME
FROM EmployeeStream;
```

---
## 4. Data Transformation:

Increase salary by 10%.

```sql
CREATE CQ SalaryIncrement
INSERT INTO SalaryStream
SELECT
    EMP_ID,
    EMP_NAME,
    SALARY * 1.10 AS NEW_SALARY
FROM EmployeeStream;
```

---
## 5. String Functions:

Convert names to uppercase.

```sql
CREATE CQ EmployeeUpper
INSERT INTO UpperStream
SELECT
    UPPER(EMP_NAME) AS NAME
FROM EmployeeStream;
```

Result:

```text
john
```

becomes

```text
JOHN
```

---
# Aggregation:

## Count Records

```sql
CREATE CQ EmployeeCount
INSERT INTO CountStream
SELECT COUNT(*) AS TOTAL
FROM EmployeeStream;
```

---
## Group By:

```sql
CREATE CQ DepartmentCount
INSERT INTO DeptStream
SELECT
    DEPARTMENT,
    COUNT(*) AS EMP_COUNT
FROM EmployeeStream
GROUP BY DEPARTMENT;
```

Output:

```text
IT      50
HR      20
Finance 15
```

---
# Stream Joins:

Join Employee and Department streams.

```sql
CREATE CQ EmployeeDeptJoin
INSERT INTO JoinedStream
SELECT
    E.EMP_ID,
    E.EMP_NAME,
    D.DEPT_NAME
FROM EmployeeStream E
JOIN DepartmentStream D
ON E.DEPT_ID = D.DEPT_ID;
```

---

# CDC Event Filtering

Capture only INSERT operations.

```sql
CREATE CQ InsertOnly
INSERT INTO InsertStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='INSERT';
```

---

Capture only UPDATE operations.

```sql
CREATE CQ UpdateOnly
INSERT INTO UpdateStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='UPDATE';
```

---

Capture only DELETE operations.

```sql
CREATE CQ DeleteOnly
INSERT INTO DeleteStream
SELECT *
FROM CDCStream
WHERE METADATA(OperationName)='DELETE';
```

---
# Date Functions:

Current timestamp:

```sql
CREATE CQ AuditStream
INSERT INTO AuditOutput
SELECT
    EMP_ID,
    CURRENT_TIMESTAMP AS LOAD_TIME
FROM EmployeeStream;
```

---
# Creating a Target

Example BigQuery Target:

```sql
CREATE TARGET BigQueryTarget
USING BigQueryWriter (
    ProjectID:'my-project',
    Dataset:'employee'
)
INPUT FROM EmployeeStream;
```

---
# Complete End-to-End TQL Pipeline:

### Oracle → Filter → BigQuery

```sql
CREATE SOURCE OracleSource
USING OracleReader (
    ConnectionURL:'jdbc:oracle:thin:@server:1521:ORCL',
    Username:'admin',
    Password:'password'
)
OUTPUT TO EmployeeStream;

CREATE CQ HighSalaryEmployees
INSERT INTO FilteredStream
SELECT *
FROM EmployeeStream
WHERE SALARY > 50000;

CREATE TARGET BigQueryTarget
USING BigQueryWriter (
    ProjectID:'gcp-project',
    Dataset:'employee'
)
INPUT FROM FilteredStream;
```

Flow:

```text
Oracle
   |
OracleReader
   |
EmployeeStream
   |
HighSalaryEmployees CQ
   |
FilteredStream
   |
BigQueryWriter
   |
BigQuery
```

---
# Common TQL Keywords:
| Keyword           | Purpose               |
| ----------------- | --------------------- |
| CREATE SOURCE     | Define source         |
| CREATE STREAM     | Create stream         |
| CREATE CQ         | Create transformation |
| CREATE TARGET     | Define target         |
| SELECT            | Retrieve fields       |
| WHERE             | Filter data           |
| GROUP BY          | Aggregate             |
| JOIN              | Join streams          |
| COUNT             | Count records         |
| CURRENT_TIMESTAMP | Current time          |

---
### What is TQL?

TQL (Transformation Query Language) is Striim's SQL-like language used to create data pipelines, perform stream transformations, filtering, aggregations, joins, and route data between sources and targets.

### What is a CQ in TQL?

A Continuous Query (CQ) is a continuously running transformation that processes streaming data in real time.

### How do you filter CDC events?

```sql
WHERE METADATA(OperationName)='INSERT'
```

or

```sql
WHERE METADATA(OperationName)='UPDATE'
```

---
# Quick Revision

```text
TQL Components
--------------
CREATE SOURCE
CREATE STREAM
CREATE CQ
CREATE TARGET

Common Operations
-----------------
SELECT
WHERE
GROUP BY
JOIN
COUNT
UPPER
CURRENT_TIMESTAMP

CDC Filtering
-------------
INSERT
UPDATE
DELETE
```

For interviews, remember:

**Source → Stream → CQ → Target**

and **CQ (Continuous Query)** is where most TQL transformations and filtering logic are implemented.
---
---
# Advanced TQL Writing in Striim:
Advanced TQL is used when you need to perform:

* Complex Filters
* Stream Joins
* Aggregations
* Window Functions
* CDC Event Processing
* Data Enrichment
* Real-Time Analytics

---
# Advanced TQL Architecture:
```text id="c1jv3f"
Oracle CDC
    |
OrderStream
    |
Advanced CQ
    |
AnalyticsStream
    |
BigQuery
```

---
# 1. Complex Filtering:
### Multiple Conditions

```sql
CREATE CQ HighValueOrders
INSERT INTO FilteredOrders
SELECT *
FROM OrderStream
WHERE ORDER_AMOUNT > 10000
AND STATUS='APPROVED'
AND COUNTRY='INDIA';
```

### Output

```text
Only approved Indian orders above 10000
```

---
# 2. IN Operator:
```sql
CREATE CQ RegionOrders
INSERT INTO RegionStream
SELECT *
FROM OrderStream
WHERE REGION IN ('APAC','EMEA');
```

---
# 3. LIKE Operator:
```sql
CREATE CQ CustomerFilter
INSERT INTO CustomerStream
SELECT *
FROM CustomerData
WHERE CUSTOMER_NAME LIKE 'J%';
```

Output:

```text
Jagadeesh
John
James
```

---
# 4. Stream Joins:
## Employee + Department

### Employee Stream

```text
EMP_ID
EMP_NAME
DEPT_ID
```

### Department Stream

```text
DEPT_ID
DEPT_NAME
```

### Join Query

```sql
CREATE CQ EmployeeDeptCQ
INSERT INTO EmployeeDeptStream
SELECT
E.EMP_ID,
E.EMP_NAME,
D.DEPT_NAME
FROM EmployeeStream E
JOIN DepartmentStream D
ON E.DEPT_ID = D.DEPT_ID;
```

### Output

```text
101 | Jagadeesh | IT
102 | John      | HR
```

---
# 5. Multi-Stream Join:

```sql
CREATE CQ OrderCustomerCQ
INSERT INTO AnalyticsStream
SELECT
O.ORDER_ID,
C.CUSTOMER_NAME,
P.PRODUCT_NAME
FROM OrderStream O
JOIN CustomerStream C
ON O.CUSTOMER_ID = C.CUSTOMER_ID
JOIN ProductStream P
ON O.PRODUCT_ID = P.PRODUCT_ID;
```

---
# 6. Aggregations:

## Count

```sql
CREATE CQ EmployeeCount
INSERT INTO CountStream
SELECT
COUNT(*) AS TOTAL_EMPLOYEES
FROM EmployeeStream;
```

---
## Sum:

```sql
CREATE CQ SalesTotal
INSERT INTO SalesStream
SELECT
SUM(AMOUNT) AS TOTAL_SALES
FROM OrderStream;
```

---
## Average:

```sql
CREATE CQ AvgSalary
INSERT INTO AvgSalaryStream
SELECT
AVG(SALARY) AS AVG_SALARY
FROM EmployeeStream;
```

---
## Maximum:

```sql
CREATE CQ MaxSalary
INSERT INTO MaxSalaryStream
SELECT
MAX(SALARY)
FROM EmployeeStream;
```

---
## Minimum:

```sql
CREATE CQ MinSalary
INSERT INTO MinSalaryStream
SELECT
MIN(SALARY)
FROM EmployeeStream;
```

---
# 7. GROUP BY:

Count employees per department.

```sql
CREATE CQ DeptCount
INSERT INTO DeptStatsStream
SELECT
DEPARTMENT,
COUNT(*) AS EMP_COUNT
FROM EmployeeStream
GROUP BY DEPARTMENT;
```

### Output:
```text
IT       50
HR       20
Finance  10
```

---
# 8. Window Functions:

Windowing is very important in streaming applications.

Instead of aggregating forever, Striim processes data within a time window.

---
## Tumbling Window:

Every 5 minutes.

```sql
CREATE CQ FiveMinuteSales
INSERT INTO SalesSummary
SELECT
COUNT(*) AS ORDERS,
SUM(AMOUNT) AS SALES
FROM OrderStream
GROUP BY TIME_WINDOW(MINUTE,5);
```

### Example:
```text
10:00 - 10:05
Orders = 100

10:05 - 10:10
Orders = 120
```

---
## Sliding Window:

Last 10 minutes continuously.

```sql
CREATE CQ SlidingSales
INSERT INTO SalesWindow
SELECT
COUNT(*)
FROM OrderStream
GROUP BY TIME_WINDOW(MINUTE,10);
```

Useful for real-time dashboards.

---
# 9. CDC Event Filtering:

Capture only Inserts.

```sql
CREATE CQ InsertOnly
INSERT INTO InsertStream
SELECT *
FROM CDCStream
WHERE META(CDCStream,'OperationName')='INSERT';
```

---

Capture only Updates.

```sql
CREATE CQ UpdateOnly
INSERT INTO UpdateStream
SELECT *
FROM CDCStream
WHERE META(CDCStream,'OperationName')='UPDATE';
```

---

Capture only Deletes.

```sql
CREATE CQ DeleteOnly
INSERT INTO DeleteStream
SELECT *
FROM CDCStream
WHERE META(CDCStream,'OperationName')='DELETE';
```

---
# 10. Data Enrichment:

Add additional information.

### Customer Stream

```text
CUSTOMER_ID
CUSTOMER_NAME
```

### Order Stream

```text
ORDER_ID
CUSTOMER_ID
AMOUNT
```

### Enrichment Query

```sql
CREATE CQ EnrichedOrders
INSERT INTO OrderAnalytics
SELECT
O.ORDER_ID,
O.AMOUNT,
C.CUSTOMER_NAME
FROM OrderStream O
JOIN CustomerStream C
ON O.CUSTOMER_ID=C.CUSTOMER_ID;
```

---
# 11. Real-Time Fraud Detection Example:

```sql
CREATE CQ FraudDetection
INSERT INTO FraudStream
SELECT *
FROM TransactionStream
WHERE AMOUNT > 100000;
```

Flow:

```text
Transactions
      |
FraudDetection CQ
      |
FraudStream
      |
Kafka Alert
```

---
# 12. End-to-End Advanced Pipeline:

```text
Oracle CDC
    |
OracleReader
    |
OrderStream
    |
Join CustomerStream
    |
Filter High Value Orders
    |
Aggregate Sales
    |
BigQuery
```

### TQL

```sql
CREATE CQ HighValueAnalytics
INSERT INTO AnalyticsStream
SELECT
C.CUSTOMER_NAME,
COUNT(*) AS TOTAL_ORDERS,
SUM(O.AMOUNT) AS TOTAL_SALES
FROM OrderStream O
JOIN CustomerStream C
ON O.CUSTOMER_ID=C.CUSTOMER_ID
WHERE O.AMOUNT > 10000
GROUP BY C.CUSTOMER_NAME;
```

---
# Performance Best Practices:

### Use Filters Early

```sql
WHERE AMOUNT > 10000
```

before joins.

---
### Avoid Unnecessary Columns:

```sql
SELECT CUSTOMER_ID,
AMOUNT
```

instead of:

```sql
SELECT *
```

---
### Use Windowing:

For streaming analytics:

```sql
TIME_WINDOW(MINUTE,5)
```

---
### Monitor CQ Latency:

Check:

```text
Events/sec
CQ Processing Time
Lag
```

from Striim Dashboard.

---
### What are advanced TQL features?

* Joins
* Aggregations
* Group By
* Window Functions
* CDC Metadata Filtering
* Data Enrichment

### What is a Window Function?
A window function performs aggregations over a specific time interval rather than over the entire stream.

Example:

```sql
GROUP BY TIME_WINDOW(MINUTE,5)
```

### How do you join streams in TQL?
```sql
JOIN DepartmentStream D
ON E.DEPT_ID=D.DEPT_ID
```

---
> Advanced TQL is used to perform complex real-time data transformations such as joins, filtering, aggregations, window-based calculations, and CDC event processing. Continuous Queries (CQ) allow streaming analytics by combining multiple streams, applying business rules, and generating enriched datasets that can be delivered to targets such as Kafka, BigQuery, and Snowflake.

---
# Custom Functions (UDFs) in Striim TQL:

## What are UDFs?
**UDF (User Defined Function)** allows you to create your own custom logic in Java and use it inside TQL queries.

When built-in TQL functions such as:

```text
UPPER()
LOWER()
COUNT()
SUM()
AVG()
```

are not sufficient, you can create a custom Java function and call it from TQL.

---
# Why Use UDFs?

Common use cases:

✅ Data masking

✅ Data validation

✅ String manipulation

✅ Date conversion

✅ Business rule implementation

✅ Custom calculations

---
# Architecture

```text
Source Stream
      |
      v
     TQL
      |
      v
  Custom UDF
      |
      v
Target Stream
```

Example:

```text
EmployeeStream
      |
      v
MaskEmailUDF()
      |
      v
MaskedStream
```

---
# Example 1: Email Masking UDF

### Java Code

```java
package com.company.udf;

public class MaskEmail {

    public static String mask(String email) {

        if(email == null)
            return null;

        int index = email.indexOf("@");

        if(index > 2) {
            return email.substring(0,2) +
                   "****" +
                   email.substring(index);
        }

        return email;
    }
}
```

---
# Build JAR

Compile:

```bash
javac MaskEmail.java
```

Package:

```bash
jar cvf custom-udf.jar com/
```

---
# Deploy JAR

Copy JAR to Striim server:

```bash
<Striim Installation>/lib
```

Example:

```bash
/opt/striim/lib/custom-udf.jar
```

Restart Striim if required.

---

# Register Function in TQL

```sql
CREATE FUNCTION MaskEmail
RETURNS String
LANGUAGE JAVA
NAME 'com.company.udf.MaskEmail.mask';
```

---
# Use Function in CQ

```sql
CREATE CQ MaskCustomerEmail
INSERT INTO CustomerMaskedStream
SELECT
    CUSTOMER_ID,
    MaskEmail(EMAIL) AS EMAIL
FROM CustomerStream;
```

---

### Input

```text
101,jagadeesh@gmail.com
```

### Output

```text
101,ja****@gmail.com
```

---
# Example 2: PAN Card Masking

Java:

```java
public class MaskPAN {

    public static String mask(String pan) {

        return "XXXXX" + pan.substring(5);
    }
}
```

Register:

```sql
CREATE FUNCTION MaskPAN
RETURNS String
LANGUAGE JAVA
NAME 'com.company.udf.MaskPAN.mask';
```

Usage:

```sql
SELECT
CUSTOMER_ID,
MaskPAN(PAN_NUMBER)
FROM CustomerStream;
```

---
# Example 3: Age Calculation

Java:

```java
public class AgeCalculator {

    public static int calculateAge(int birthYear) {

        return 2026 - birthYear;
    }
}
```

Register:

```sql
CREATE FUNCTION CalculateAge
RETURNS Integer
LANGUAGE JAVA
NAME 'com.company.udf.AgeCalculator.calculateAge';
```

Usage:

```sql
SELECT
EMP_ID,
CalculateAge(BIRTH_YEAR)
FROM EmployeeStream;
```

---
# Example 4: Data Validation

Validate mobile number.

Java:

```java
public class ValidatePhone {

    public static boolean isValid(String phone) {

        return phone.matches("[0-9]{10}");
    }
}
```

TQL:

```sql
CREATE FUNCTION ValidatePhone
RETURNS Boolean
LANGUAGE JAVA
NAME 'com.company.udf.ValidatePhone.isValid';
```

Usage:

```sql
CREATE CQ ValidCustomers
INSERT INTO ValidStream
SELECT *
FROM CustomerStream
WHERE ValidatePhone(PHONE)=true;
```

---
# Example 5: Currency Conversion

Java:

```java
public class CurrencyConverter {

    public static double usdToInr(double amount) {

        return amount * 85;
    }
}
```

TQL:

```sql
CREATE FUNCTION USDToINR
RETURNS Double
LANGUAGE JAVA
NAME 'com.company.udf.CurrencyConverter.usdToInr';
```

Usage:

```sql
SELECT
ORDER_ID,
USDToINR(AMOUNT)
FROM OrderStream;
```

---
# End-to-End Example

```text
Oracle
   |
OracleReader
   |
CustomerStream
   |
MaskEmail()
   |
CustomerMaskedStream
   |
BigQueryWriter
   |
BigQuery
```

CQ:

```sql
CREATE CQ CustomerMasking
INSERT INTO CustomerMaskedStream
SELECT
    CUSTOMER_ID,
    MaskEmail(EMAIL) AS EMAIL
FROM CustomerStream;
```

---
# Best Practices:

### Keep UDFs Lightweight

Good:

```java
String manipulation
Validation
Simple calculations
```

Avoid:

```java
Database calls
HTTP API calls
Long-running logic
```

---
### Handle Null Values

Always check:

```java
if(value == null)
```

to prevent runtime errors.

---
### Make Methods Static

```java
public static String mask(...)
```

This is the preferred pattern.

---
### Optimize Performance:
UDF executes for every event.

Example:

```text
10,000 events/sec
```

A slow UDF can become a bottleneck.

---
# Limitations:
* UDFs require Java development.
* Poorly written UDFs can increase latency.
* JAR deployment may require restart/reload.
* Debugging is harder than standard TQL.

---
### What is a UDF in Striim?

A UDF (User Defined Function) is a custom Java function that can be registered and invoked inside TQL queries to implement custom business logic that is not available through built-in TQL functions.

---
### Why use UDFs?
To perform:

* Data masking
* Validation
* Custom calculations
* Business-specific transformations

---
### How do you use a UDF?
1. Write Java code.
2. Build a JAR file.
3. Deploy the JAR to Striim.
4. Register the function in TQL.
5. Call it inside a CQ.

---
> Custom Functions (UDFs) in Striim are Java-based functions that extend TQL capabilities. They are packaged into JAR files, deployed to the Striim environment, registered using CREATE FUNCTION, and then invoked within Continuous Queries (CQ) to perform custom transformations, validations, masking, or calculations on streaming data.**
