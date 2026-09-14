# Customer Master Merge & DB2 Synchronization

## Objective
The Customer Master Merge & DB2 Synchronization project is a Mainframe batch application developed to support customer migration after a bank acquisition. The application processes duplicate customer records from a sequential file and merges them into the existing CUSTOMER-MASTER VSAM KSDS using CUSTOMER-ID as the primary key. The successfully processed records are also synchronized with a DB2 CUSTOMER_MASTER table.

The project demonstrates practical Mainframe development concepts including COBOL batch processing, VSAM KSDS random access, READ/WRITE/REWRITE operations, DB2 INSERT/UPDATE processing, validation, error handling, audit processing, and batch statistics.

### Skills Learned

- COBOL batch application development.
- VSAM KSDS random access processing.
- Using READ, WRITE, and REWRITE operations.
- Sequential file processing.
- Embedded SQL programming in COBOL.
- DB2 INSERT and UPDATE processing.
- SQLCODE and DB2 error handling.
- VSAM file status code handling.
- Customer record validation and merge logic.
- NULL handling and indicator variables.
- Audit and error-report processing.
- Batch counters and processing statistics.
- JCL batch job processing.
- Understanding Mainframe upstream and downstream processing.

### Tools Used

- **COBOL** for developing the customer merge and migration logic.
- **JCL** for batch job execution and dataset management.
- **VSAM KSDS** for CUSTOMER-MASTER random access processing.
- **IBM DB2** for storing and synchronizing customer information.
- **Embedded SQL** for DB2 INSERT and UPDATE operations.
- **IBM z/OS** as the Mainframe operating environment.
- **Git & GitHub** for source code management and project documentation.

## Steps

Below are the key steps taken in the Customer Master Merge & DB2 Synchronization process:

### 1. Read Duplicate Customer Records

The DUPLICATE-CUSTOMERS sequential file is opened and processed record by record. Each record is validated before attempting to update the CUSTOMER-MASTER VSAM KSDS.

*Ref 1: Duplicate Customer Input*  
This section represents the duplicate customer records received during the customer migration process.

![Duplicate Customer Input](link-to-image)

### 2. Validate Customer Record

Each input record is validated against the defined business rules before processing.

The following validations are performed:

- CUSTOMER-ID cannot be blank.
- CUSTOMER-NAME cannot be blank.
- ACCOUNT-BALANCE cannot be negative.
- LAST-UPDATE-DATE must be valid.
- PAN-NUMBER must match when an existing customer is found.

*Ref 2: Customer Validation*  
This section demonstrates validation of customer input data before processing the VSAM record.

![Customer Validation](link-to-image)

### 3. Read CUSTOMER-MASTER VSAM KSDS

The CUSTOMER-MASTER VSAM KSDS is accessed randomly using CUSTOMER-ID as the primary key.

If the customer is not found with VSAM file status `23`, the application treats the record as a new customer and proceeds with the insert process.

If the customer exists, the application proceeds with the merge process.

*Ref 3: VSAM Random Read*  
This section demonstrates random access to the CUSTOMER-MASTER KSDS using CUSTOMER-ID.

![VSAM Random Read](link-to-image)

### 4. Insert New Customer

If the customer does not exist in CUSTOMER-MASTER, the new customer record is written to the VSAM KSDS.

The corresponding customer information is then inserted into the DB2 CUSTOMER_MASTER table.

An audit record is generated with ACTION = INSERT.

*Ref 4: Customer Insert Processing*  
This section demonstrates VSAM WRITE processing followed by DB2 INSERT processing.

![Customer Insert](link-to-image)

### 5. Merge Existing Customer

If the customer already exists, the existing and duplicate records are merged according to the business rules.

- Keep the longer CUSTOMER-NAME.
- Keep the ADDRESS from the record with the latest LAST-UPDATE-DATE.
- Replace MOBILE-NUMBER when the duplicate value is not blank.
- Add both ACCOUNT-BALANCE values.
- PAN-NUMBER must match.
- STATUS becomes `A` if either record has status `A`; otherwise it remains `I`.

The merged record is rewritten into the CUSTOMER-MASTER VSAM KSDS.

The corresponding DB2 CUSTOMER_MASTER record is then updated.

An audit record is generated with ACTION = MERGE.

*Ref 5: Customer Merge Processing*  
This section demonstrates the business rules used to merge the existing and duplicate customer records.

![Customer Merge](link-to-image)

### 6. Error and Audit Processing

If a validation or processing error occurs, the rejected record is written to ERROR-REPORT along with the reason for rejection.

Examples include:

- Invalid CUSTOMER-ID.
- Blank CUSTOMER-NAME.
- Negative ACCOUNT-BALANCE.
- Invalid LAST-UPDATE-DATE.
- PAN mismatch.
- VSAM errors.
- DB2 errors.

Every successful INSERT or MERGE generates a corresponding MERGE-AUDIT record containing the customer ID, old balance, new balance, merge date, and action.

*Ref 6: Audit and Error Processing*  
This section demonstrates audit record generation and rejected-record processing.

![Audit and Error Processing](link-to-image)

### 7. Processing Summary

At the end of the batch execution, the program displays the processing statistics.

The summary includes:

- Total Records Read
- Records Inserted
- Records Merged
- Records Rejected
- PAN Mismatches
- Audit Records Written
- Total Errors

*Ref 7: Batch Processing Summary*  
This section displays the final processing statistics generated by the COBOL batch program.

![Batch Processing Summary](link-to-image)
