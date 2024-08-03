# Oracle
Below is a comprehensive list of commands and techniques for managing Oracle systems, including Oracle Database, Oracle Cloud, Oracle Linux, and Oracle VM.

### Oracle Database

#### Basic SQL Commands

- **Connect to Oracle Database:**
  ```sql
  sqlplus username/password@//hostname:port/service_name
  ```

- **Select Data:**
  ```sql
  SELECT * FROM employees;
  ```

- **Insert Data:**
  ```sql
  INSERT INTO employees (id, name, age, department) VALUES (1, 'John Doe', 30, 'HR');
  ```

- **Update Data:**
  ```sql
  UPDATE employees SET age = 31 WHERE id = 1;
  ```

- **Delete Data:**
  ```sql
  DELETE FROM employees WHERE id = 1;
  ```

#### Data Definition Language (DDL)

- **Create Table:**
  ```sql
  CREATE TABLE employees (
      id NUMBER PRIMARY KEY,
      name VARCHAR2(100),
      age NUMBER,
      department VARCHAR2(50)
  );
  ```

- **Drop Table:**
  ```sql
  DROP TABLE employees;
  ```

- **Create Index:**
  ```sql
  CREATE INDEX idx_name ON employees (name);
  ```

- **Drop Index:**
  ```sql
  DROP INDEX idx_name;
  ```

- **Alter Table (Add Column):**
  ```sql
  ALTER TABLE employees ADD (salary NUMBER);
  ```

- **Alter Table (Modify Column):**
  ```sql
  ALTER TABLE employees MODIFY (salary NUMBER(10, 2));
  ```

- **Alter Table (Drop Column):**
  ```sql
  ALTER TABLE employees DROP COLUMN salary;
  ```

#### Data Manipulation Language (DML)

- **Merge Statement:**
  ```sql
  MERGE INTO employees e
  USING (SELECT id, name FROM new_employees) n
  ON (e.id = n.id)
  WHEN MATCHED THEN
    UPDATE SET e.name = n.name
  WHEN NOT MATCHED THEN
    INSERT (id, name) VALUES (n.id, n.name);
  ```

#### Data Control Language (DCL)

- **Grant Privileges:**
  ```sql
  GRANT SELECT, INSERT ON employees TO username;
  ```

- **Revoke Privileges:**
  ```sql
  REVOKE INSERT ON employees FROM username;
  ```

#### Transaction Control

- **Commit Transaction:**
  ```sql
  COMMIT;
  ```

- **Rollback Transaction:**
  ```sql
  ROLLBACK;
  ```

- **Savepoint:**
  ```sql
  SAVEPOINT savepoint_name;
  ```

- **Rollback to Savepoint:**
  ```sql
  ROLLBACK TO SAVEPOINT savepoint_name;
  ```

#### Advanced SQL Commands

- **Subqueries:**
  ```sql
  SELECT name FROM employees WHERE department_id = (SELECT id FROM departments WHERE name = 'HR');
  ```

- **Joins:**
  ```sql
  SELECT e.name, d.name AS department
  FROM employees e
  JOIN departments d ON e.department_id = d.id;
  ```

- **Union:**
  ```sql
  SELECT name FROM employees
  UNION
  SELECT name FROM contractors;
  ```

#### PL/SQL Programming

- **Declare and Execute PL/SQL Block:**
  ```sql
  DECLARE
      v_employee_name VARCHAR2(100);
  BEGIN
      SELECT name INTO v_employee_name FROM employees WHERE id = 1;
      DBMS_OUTPUT.PUT_LINE('Employee Name: ' || v_employee_name);
  END;
  ```

- **Create Function:**
  ```sql
  CREATE OR REPLACE FUNCTION get_employee_name (p_id NUMBER)
  RETURN VARCHAR2
  IS
      v_name VARCHAR2(100);
  BEGIN
      SELECT name INTO v_name FROM employees WHERE id = p_id;
      RETURN v_name;
  END;
  ```

- **Create Procedure:**
  ```sql
  CREATE OR REPLACE PROCEDURE update_employee_salary (p_id NUMBER, p_salary NUMBER)
  IS
  BEGIN
      UPDATE employees SET salary = p_salary WHERE id = p_id;
      COMMIT;
  END;
  ```

- **Create Trigger:**
  ```sql
  CREATE OR REPLACE TRIGGER trg_after_insert
  AFTER INSERT ON employees
  FOR EACH ROW
  BEGIN
      DBMS_OUTPUT.PUT_LINE('New Employee Inserted: ' || :NEW.name);
  END;
  ```

#### Performance Tuning

- **Explain Plan:**
  ```sql
  EXPLAIN PLAN FOR SELECT * FROM employees WHERE department_id = 10;
  SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
  ```

- **Use Index Hint:**
  ```sql
  SELECT /*+ INDEX(employees idx_name) */ name FROM employees WHERE name = 'John Doe';
  ```

- **Gather Statistics:**
  ```sql
  EXEC DBMS_STATS.GATHER_TABLE_STATS('SCHEMA_NAME', 'TABLE_NAME');
  ```

#### Backup and Recovery

- **Export Data (Data Pump):**
  ```bash
  expdp username/password@//hostname:port/service_name DIRECTORY=dpump_dir1 DUMPFILE=table.dmp TABLES=employees
  ```

- **Import Data (Data Pump):**
  ```bash
  impdp username/password@//hostname:port/service_name DIRECTORY=dpump_dir1 DUMPFILE=table.dmp TABLES=employees
  ```

#### Security Management

- **Create User:**
  ```sql
  CREATE USER new_user IDENTIFIED BY password;
  ```

- **Drop User:**
  ```sql
  DROP USER new_user CASCADE;
  ```

- **Grant Role to User:**
  ```sql
  GRANT connect, resource TO new_user;
  ```

- **Revoke Role from User:**
  ```sql
  REVOKE resource FROM new_user;
  ```

#### Data Warehousing and Analytics

- **Create Materialized View:**
  ```sql
  CREATE MATERIALIZED VIEW emp_mv AS
  SELECT department_id, COUNT(*) AS emp_count
  FROM employees
  GROUP BY department_id;
  ```

- **Refresh Materialized View:**
  ```sql
  BEGIN
      DBMS_MVIEW.REFRESH('emp_mv', 'C');
  END;
  ```

- **Create OLAP Cube:**
  ```sql
  CREATE CUBE sales_cube
  DIMENSION BY (time_dim, product_dim, store_dim)
  MEASURE BY (sales_amount, sales_quantity)
  USING sales_fact_table;
  ```

- **Query OLAP Cube:**
  ```sql
  SELECT time_dim.year, SUM(sales_amount)
  FROM sales_cube
  GROUP BY time_dim.year;
  ```

#### Advanced Oracle Techniques

##### Partitioning

- **Create Partitioned Table:**
  ```sql
  CREATE TABLE employees (
      id NUMBER,
      name VARCHAR2(100),
      hire_date DATE
  )
  PARTITION BY RANGE (hire_date) (
      PARTITION p1 VALUES LESS THAN (TO_DATE('01-JAN-2020', 'DD-MON-YYYY')),
      PARTITION p2 VALUES LESS THAN (TO_DATE('01-JAN-2021', 'DD-MON-YYYY')),
      PARTITION p3 VALUES LESS THAN (MAXVALUE)
  );
  ```

- **Add Partition:**
  ```sql
  ALTER TABLE employees
  ADD PARTITION p4 VALUES LESS THAN (TO_DATE('01-JAN-2022', 'DD-MON-YYYY'));
  ```

##### Advanced PL/SQL

- **Bulk Collect:**
  ```sql
  DECLARE
      TYPE t_emp IS TABLE OF employees%ROWTYPE;
      l_emp t_emp;
  BEGIN
      SELECT * BULK COLLECT INTO l_emp FROM employees;
  END;
  ```

- **Forall Statement:**
  ```sql
  DECLARE
      TYPE t_ids IS TABLE OF employees.id%TYPE;
      l_ids t_ids := t_ids(1, 2, 3);
  BEGIN
      FORALL i IN l_ids.FIRST..l_ids.LAST
          DELETE FROM employees WHERE id = l_ids(i);
      COMMIT;
  END;
  ```

- **Dynamic SQL:**
  ```sql
  EXECUTE IMMEDIATE 'CREATE TABLE temp_table (id NUMBER)';
  ```

#### Oracle RAC and Data Guard

##### Oracle Real Application Clusters (RAC)

- **Check Cluster Status:**
  ```bash
  srvctl status database -d mydb
  ```

- **Start Cluster:**
  ```bash
  srvctl start database -d mydb
  ```

- **Stop Cluster:**
  ```bash
  srvctl stop database -d mydb
  ```

##### Oracle Data Guard

- **Switch to Standby:**
  ```sql
  ALTER DATABASE COMMIT TO SWITCHOVER TO STANDBY;
  ```

- **Switch to Primary:**
  ```sql
  ALTER DATABASE COMMIT TO SWITCHOVER TO PRIMARY;
  ```

- **Enable Data Guard Broker:**
  ```sql
  ALTER SYSTEM SET dg_broker_start=true;
  ```

---

### Oracle Cloud Infrastructure (OCI)

#### OCI CLI Commands

- **Install OCI CLI:**
  ```bash
  bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
  ```

- **Configure OCI CLI:**
  ```bash
  oci setup config
  ```

- **List All Compartments:**
  ```bash
  oci
  ```

```bash
  oci iam compartment list
  ```

- **Create a Compute Instance:**
  ```bash
  oci compute instance launch --availability-domain "xyz" --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --shape "VM.Standard2.1" --image-id "ocid1.image.oc1..exampleuniqueID" --subnet-id "ocid1.subnet.oc1..exampleuniqueID" --display-name "myInstance"
  ```

- **List Compute Instances:**
  ```bash
  oci compute instance list --compartment-id "ocid1.compartment.oc1..exampleuniqueID"
  ```

- **Terminate a Compute Instance:**
  ```bash
  oci compute instance terminate --instance-id "ocid1.instance.oc1..exampleuniqueID"
  ```

- **Create a Block Volume:**
  ```bash
  oci bv volume create --availability-domain "xyz" --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --size-in-gbs 50 --display-name "myVolume"
  ```

- **Attach a Block Volume:**
  ```bash
  oci compute volume-attachment create --instance-id "ocid1.instance.oc1..exampleuniqueID" --volume-id "ocid1.volume.oc1..exampleuniqueID" --type "iscsi"
  ```

- **Detach a Block Volume:**
  ```bash
  oci compute volume-attachment detach --volume-attachment-id "ocid1.volumeattachment.oc1..exampleuniqueID"
  ```

- **Create a VCN (Virtual Cloud Network):**
  ```bash
  oci network vcn create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --cidr-block "10.0.0.0/16" --display-name "myVCN"
  ```

- **Create a Subnet:**
  ```bash
  oci network subnet create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --vcn-id "ocid1.vcn.oc1..exampleuniqueID" --cidr-block "10.0.1.0/24" --availability-domain "xyz" --display-name "mySubnet"
  ```

- **Create a Load Balancer:**
  ```bash
  oci lb load-balancer create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --shape-name "100Mbps" --subnet-ids '["ocid1.subnet.oc1..exampleuniqueID"]' --display-name "myLoadBalancer"
  ```

- **Create a Database System:**
  ```bash
  oci db system launch --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --availability-domain "xyz" --db-name "myDB" --admin-password "YourPassword123!" --shape "VM.Standard2.1" --subnet-id "ocid1.subnet.oc1..exampleuniqueID" --display-name "myDBSystem"
  ```

#### OCI Security

- **Create a Policy:**
  ```bash
  oci iam policy create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --name "myPolicy" --statements '["Allow group Administrators to manage all-resources in compartment myCompartment"]'
  ```

- **List Policies:**
  ```bash
  oci iam policy list --compartment-id "ocid1.compartment.oc1..exampleuniqueID"
  ```

- **Create a User:**
  ```bash
  oci iam user create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --name "myUser" --description "New User"
  ```

- **Create a Group:**
  ```bash
  oci iam group create --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --name "myGroup" --description "New Group"
  ```

- **Add User to Group:**
  ```bash
  oci iam group add-user --user-id "ocid1.user.oc1..exampleuniqueID" --group-id "ocid1.group.oc1..exampleuniqueID"
  ```

### Oracle Linux

#### System Administration

- **Install Software Package:**
  ```bash
  sudo yum install package_name
  ```

- **Update System Packages:**
  ```bash
  sudo yum update
  ```

- **Start/Stop a Service:**
  ```bash
  sudo systemctl start service_name
  sudo systemctl stop service_name
  ```

- **Enable/Disable a Service:**
  ```bash
  sudo systemctl enable service_name
  sudo systemctl disable service_name
  ```

- **Check System Status:**
  ```bash
  systemctl status
  ```

- **Check Disk Usage:**
  ```bash
  df -h
  ```

- **Check Memory Usage:**
  ```bash
  free -m
  ```

#### User Management

- **Create User:**
  ```bash
  sudo useradd newuser
  ```

- **Delete User:**
  ```bash
  sudo userdel newuser
  ```

- **Change User Password:**
  ```bash
  sudo passwd newuser
  ```

- **Add User to Group:**
  ```bash
  sudo usermod -aG groupname newuser
  ```

### Oracle VM

#### Oracle VM Manager CLI (OVMCLI)

- **Connect to Oracle VM Manager:**
  ```bash
  ssh admin@hostname
  ```

- **List Virtual Machines:**
  ```bash
  list vm
  ```

- **Create Virtual Machine:**
  ```bash
  create vm name=vmName repository=repoName server_pool=poolName
  ```

- **Start Virtual Machine:**
  ```bash
  start vm name=vmName
  ```

- **Stop Virtual Machine:**
  ```bash
  stop vm name=vmName
  ```

- **Delete Virtual Machine:**
  ```bash
  delete vm name=vmName
  ```

- **Create Virtual Disk:**
  ```bash
  create virtual_disk name=diskName repository=repoName size=20G
  ```

- **Attach Virtual Disk to VM:**
  ```bash
  create virtual_disk_mapping virtual_disk=diskName vm=vmName
  ```

### Oracle Enterprise Manager

#### Command Line Interface (EMCLI)

- **Install EMCLI:**
  ```bash
  emcli setup
  ```

- **Login to EMCLI:**
  ```bash
  emcli login -username=sysman
  ```

- **Discover Targets:**
  ```bash
  emcli discover_targets -hosts="host1,host2" -credentials=credentials_file
  ```

- **Create Monitoring Template:**
  ```bash
  emcli create_template -name="TemplateName" -type="monitoring"
  ```

- **Apply Monitoring Template:**
  ```bash
  emcli apply_template -name="TemplateName" -targets="target1,target2"
  ```

- **Create Incident Rule:**
  ```bash
  emcli create_rule_set -name="RuleSetName" -rules=rules_file
  ```

### Advanced Techniques

#### Oracle Database

- **Flashback Database:**
  ```sql
  FLASHBACK DATABASE TO SCN scn_number;
  ```

- **Using Oracle Data Pump for Full Database Export/Import:**
  ```bash
  expdp username/password FULL=Y DIRECTORY=dpump_dir1 DUMPFILE=full_db.dmp
  impdp username/password FULL=Y DIRECTORY=dpump_dir1 DUMPFILE=full_db.dmp
  ```

- **Cross-Platform Database Transport:**
  ```sql
  BEGIN
      DBMS_FILE_TRANSFER.GET_FILE(
          source_directory_object => 'SOURCE_DIR',
          source_file_name        => 'source_file',
          destination_directory_object => 'DEST_DIR',
          destination_file_name   => 'dest_file'
      );
  END;
  ```

#### Oracle Cloud Infrastructure (OCI)

- **Using OCI CLI with Scripts:**
  ```bash
  # Example: List all compute instances and output to a file
  oci compute instance list --compartment-id "ocid1.compartment.oc1..exampleuniqueID" > instances.txt
  ```

- **Automating OCI Tasks with Bash Scripts:**
  ```bash
  # Example: Stop all running instances in a compartment
  instances=$(oci compute instance list --compartment-id "ocid1.compartment.oc1..exampleuniqueID" --query "data [*].id" --raw-output)
  for instance in $instances; do
      oci compute instance action --instance-id $instance --action STOP
  done
  ```

### Summary

This comprehensive list covers a wide range of Oracle commands and techniques for managing Oracle Database, Oracle Cloud, Oracle Linux, and Oracle VM. It includes basic and advanced SQL commands, PL/SQL programming, Oracle RAC and Data Guard management, OCI CLI commands, Oracle VM Manager CLI, and more. These commands and techniques are essential for administrators and developers working with Oracle technologies.
