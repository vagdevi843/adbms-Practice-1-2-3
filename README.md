# adbms-Practice-1-2-3
-- Drop table if exists
DROP TABLE IF EXISTS FeePayments;

-- Create table
CREATE TABLE FeePayments (
    payment_id INT PRIMARY KEY,
    student_name VARCHAR(100) NOT NULL,
    amount DECIMAL(10,2) CHECK (amount > 0),
    payment_date DATE NOT NULL
);

-- ============================
-- Part A: Successful Transaction
-- ============================
START TRANSACTION;
INSERT INTO FeePayments VALUES (1,'Ashish',5000.00,'2024-06-01');
INSERT INTO FeePayments VALUES (2,'Smaran',4500.00,'2024-06-02');
INSERT INTO FeePayments VALUES (3,'Vaibhav',5500.00,'2024-06-03');
COMMIT;

SELECT * FROM FeePayments;

-- ============================
-- Part B: Failed insertion rollback
-- ============================
START TRANSACTION;
INSERT INTO FeePayments VALUES (4,'Kiran',6000.00,'2024-06-05');
INSERT INTO FeePayments VALUES (1,'Ashish',-2000.00,'2024-06-06'); -- Violates PK + CHECK
ROLLBACK;

SELECT * FROM FeePayments;

-- ============================
-- Part C: Partial failure rollback
-- ============================
START TRANSACTION;
INSERT INTO FeePayments VALUES (5,'Sneha',4700.00,'2024-06-08');
INSERT INTO FeePayments VALUES (6,NULL,4800.00,'2024-06-09'); -- Violates NOT NULL
ROLLBACK;

SELECT * FROM FeePayments;

-- ============================
-- Part D: ACID compliance (successful commit)
-- ============================
START TRANSACTION;
INSERT INTO FeePayments VALUES (7,'Sneha',4700.00,'2024-06-08');
INSERT INTO FeePayments VALUES (8,'Arjun',4900.00,'2024-06-09');
COMMIT;

SELECT * FROM FeePayments;

-- ============================
-- EXTRA: Savepoint demo (partial rollback)
-- ============================
START TRANSACTION;
INSERT INTO FeePayments VALUES (9,'Rahul',5100.00,'2024-06-10');
SAVEPOINT sp1;

INSERT INTO FeePayments VALUES (10,'Meera',5200.00,'2024-06-11');
INSERT INTO FeePayments VALUES (10,'Duplicate',5300.00,'2024-06-12'); -- Primary Key violation

-- Roll back only to savepoint (undo Meera + Duplicate, keep Rahul)
ROLLBACK TO sp1;
COMMIT;

SELECT * FROM FeePayments;
