# IAM User Lifecycle Automation Lab

## Overview
Built on top of the hybrid identity environment to simulate the enterprise joiner-mover-leaver (JML) lifecycle using PowerShell automation. This lab addresses one of the most critical real-world IAM security problems — orphaned accounts — which are consistently cited in security breach reports as a top attack vector.

## Environment
- **Platform:** Windows Server 2022 Domain Controller (DC01)
- **Domain:** corp.lab / fernandohuizarjrgmail.onmicrosoft.com
- **Scripts location:** C:\Scripts\
- **Log location:** C:\Scripts\Logs\

## Scripts Built

### Onboarding Script — Onboard-User.ps1
Automates the complete new hire provisioning process.

**Parameters:**
- FirstName, LastName, Department, Title (mandatory)
- Manager (optional)

**What it does:**
- Auto-generates username (first initial + last name convention)
- Constructs UPN using Azure tenant domain
- Determines correct OU based on department input
- Creates user with all required AD attributes
- Sets temporary password (requires change on first login)
- Adds user to correct department security group (GRP-IT, GRP-HR, GRP-Sales, GRP-Finance)
- Optionally sets manager attribute
- Logs action with timestamp to onboarding-log.txt
- Triggers Entra Connect delta sync automatically

### Offboarding Script — Offboard-User.ps1
Automates the complete employee termination process in 6 sequential steps.

**Step 1 — Disable account**
Immediately prevents login using Disable-ADAccount

**Step 2 — Reset password**
Generates a random 16-character complex password using System.Web.Security.Membership — even if someone knew the old password they cannot use it

**Step 3 — Remove group memberships**
Removes user from all security groups except Domain Users

**Step 4 — Stamp audit description**
Updates the Description field with timestamp and admin name — creates an auditable record directly on the account object

**Step 5 — Hide from GAL**
Attempts to hide account from Global Address List using msExchHideFromAddressLists attribute

**Step 6 — Move to Disabled Users OU**
Moves account to Disabled Users OU using Move-ADObject
<img width="748" height="365" alt="image" src="https://github.com/user-attachments/assets/e8bca98a-7c07-4247-b28d-3d431d3665cf" />


After all steps logs action to offboarding-log.txt and triggers Entra Connect delta sync

## Audit Logging
Both scripts write structured log entries:
- **Onboarding format:** timestamp | action | username | department | title
- <img width="662" height="71" alt="image" src="https://github.com/user-attachments/assets/b882bc86-5307-4e31-a0de-2916ab7e9893" />
- **Offboarding format:** timestamp | action | username | note
- <img width="635" height="51" alt="image" src="https://github.com/user-attachments/assets/4513aa64-da59-4795-bd57-8cd821961c0a" />
- <img width="987" height="210" alt="image" src="https://github.com/user-attachments/assets/e001c636-165b-479a-9cd6-c36bb8230f99" />



In a production environment these logs would be shipped to a SIEM like Microsoft Sentinel or Splunk for security monitoring and compliance.

## Key Concepts Demonstrated
- Joiner-Mover-Leaver (JML) lifecycle automation
- Orphaned account prevention
- Least privilege access through department-specific group assignments
- Audit logging for compliance requirements
- PowerShell parameter handling and error checking
- Automated Entra Connect delta sync triggering
- Role-Based Access Control (RBAC)

## Technologies Used
`Active Directory` `PowerShell` `Microsoft Entra ID` `Entra Connect` `Windows Server 2022` `Identity and Access Management` `RBAC`
