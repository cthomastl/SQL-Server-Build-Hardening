# SQL-Server-Modernization-Security-Hardening-Lab

Project Overview
This project documents the end-to-end configuration of a SQL Server environment (dbsql01) joined to an Active Directory domain (oakmont.org). The lab focuses on transitioning from a local administrator setup to a Principle of Least Privilege model, simulating a professional migration for a "Next Gen" application.

1. Database Schema & Relational Design
The project began with the creation of a relational HR database. I translated legacy syntax into T-SQL to ensure proper identity management and primary key constraints.

<img width="733" height="443" alt="DBQuery" src="https://github.com/user-attachments/assets/9ec08c2c-db0a-42d7-8ea9-93985d791422" />


Relational Integrity: Designed a schema containing Departments, Employees, and Performance_Reviews.

Data Ingestion: Populated the tables with sample records to test the data types and constraints.

2. Querying & Validation
To confirm the database was functional for application use, I developed an INNER JOIN query. This produced a unified view of employee details alongside their respective department names.

<img width="566" height="441" alt="JoinStatementQuery" src="https://github.com/user-attachments/assets/2f855135-0d44-4c60-a981-5ae51afca37a" />


3. Active Directory Integration
Following "Best Practices," I moved identity management from the local server to the Domain Controller.

Service Account Creation: Created svc_sql within the oakmont.org domain.

Workstation Hardening: Restricted the svc_sql account so it is only authorized to log onto the dbsql01 server, preventing lateral movement in the network.

<img width="508" height="344" alt="ServiceAccountInAD" src="https://github.com/user-attachments/assets/f7ba58ba-f1e1-414d-8a8a-792303f0b08d" />
<img width="466" height="340" alt="LogOnto" src="https://github.com/user-attachments/assets/c2e902cc-4c15-41b5-ada0-41236245975d" />


4. SQL Security Provisioning
I mapped the AD identity into SQL Server to replace the default Administrator login.

<img width="665" height="442" alt="addedUserinSQL" src="https://github.com/user-attachments/assets/6bf8a5a6-3e91-459d-89bd-2ecb0ec77037" />


Identity Verification: Verified the session identity using select suser_sname(); to ensure the server recognized the correct Windows credentials.

Role Assignment: Configured the svc_sql account with db_datareader permissions while explicitly denying db_owner or db_datawriter access.

5. Security Testing (The "Drop Test")
The final phase was to verify that the restricted service account could not maliciously or accidentally damage the database.

<img width="950" height="500" alt="Signedinassvcsqlcantdelete" src="https://github.com/user-attachments/assets/2617ce03-1674-46b0-a6d7-4171b97b55cf" />


Expected Failure: Logged in as svc_sql, I attempted to delete the Employees table via the SSMS GUI.

Result: SQL Server blocked the action with a "Permission Error 3701," proving that the Least Privilege model was successfully enforced.

Technical Environment
OS: Windows Server 2022 (Domain Joined)

Database: SQL Server 2022 (Instance: dbsql01)

Network: Port 1433 opened via Advanced Firewall Rules to allow remote management

<img width="518" height="388" alt="FireWallRuleForSQLPort" src="https://github.com/user-attachments/assets/210d495b-7599-4838-8838-5ddaa78debe5" />

Key Troubleshooting: Domain Connectivity
During the domain join process, the server initially failed to communicate with oakmont.org.

Issue: The server was using a dynamic IP and default DNS, which could not resolve the Domain Controller's address.

Fix: Manually updated the IPv4 Adapter Settings to a Static IP and pointed the Preferred DNS directly to the Domain Controller IP.

Result: Successfully established the "Trust Relationship" allowing domain user logins.
<img width="348" height="311" alt="StaticIPinAdapterSettings" src="https://github.com/user-attachments/assets/5b547e51-f24d-4dc2-b36e-334b4cf198a1" />


