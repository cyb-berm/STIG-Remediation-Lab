# 🔐 Windows 11 STIG Remediation Lab

A hands-on cybersecurity lab documenting the identification, analysis, and remediation of 10 Security Technical Implementation Guide (STIG) findings on a Windows 11 Azure VM. Each entry includes a description of the vulnerability, evidence of the non-compliant state, the remediation applied, and post-fix compliance verification.

---

## 📋 Lab Overview

| Detail | Info |
|---|---|
| **Platform** | Microsoft Azure (Windows 11 VM) |
| **STIG Benchmark** | Windows 11 STIG |
| **Remediation Methods** | Registry edits, PowerShell, Group Policy |
| **Progress** | 2 / 10 Complete |

---

## ✅ Progress Tracker

| # | STIG ID | Title | Status |
|---|---|---|---|
| 1 | WN11-AU-000500 | Application Event Log Max Size | ✅ Complete |
| 2 | WN11-AU-000510 | System Event Log Max Size | ✅ Complete |
| 3 | *(coming soon)* | — | 🔲 Pending |
| 4 | *(coming soon)* | — | 🔲 Pending |
| 5 | *(coming soon)* | — | 🔲 Pending |
| 6 | *(coming soon)* | — | 🔲 Pending |
| 7 | *(coming soon)* | — | 🔲 Pending |
| 8 | *(coming soon)* | — | 🔲 Pending |
| 9 | *(coming soon)* | — | 🔲 Pending |
| 10 | *(coming soon)* | — | 🔲 Pending |

---

## 📁 STIG Entries

---

## ✅ STIG 1 — WN11-AU-000500
### Application Event Log Maximum Size

<br>

### 📌 What Is This Finding?

The Windows 11 Application Event Log must be configured to a minimum size of **32,768 KB (32 MB)**. If the log size is too small, older security-relevant events get overwritten before they can be reviewed or exported — creating gaps in audit coverage.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Administrative Templates >> Windows Components >> Event Log Service >> Application >> "Specify the maximum log file size (KB)"`
> to **Enabled**, with a Maximum Log Size of **32768 KB or greater**.
>
> *If the system sends audit records directly to an audit server, this finding is Not Applicable (NA) — must be documented with the ISSO.*

---

### 🖥️ Step 1 — Environment Setup

An Azure VM running Windows 11 was provisioned with firewalls disabled to allow a baseline STIG scan to run against the machine.

![Initial scan results](https://private-user-images.githubusercontent.com/301828066/636577288-a5a6f64e-7209-45aa-9c9d-8e5891753753.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NTc3Mjg4LWE1YTZmNjRlLTcyMDktNDVhYS05YzlkLThlNTg5MTc1Mzc1My5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT03MGNjMzAwMTE2OTQwNDgxNmJjYTM0ZDllNmUzZWJjNWYyY2FhZmRlMDI2NTRhNWM2YTliNGU3OGVlMjVjMGMxJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.AoFfCO5W6x7uHackOc3Am8JOVbVhyLoCNyWre_Y59IA)

---

### 🔍 Step 2 — Identifying the Finding

The baseline scan flagged **WN11-AU-000500** as a failed finding.

![STIG scan showing WN11-AU-000500 failed](https://private-user-images.githubusercontent.com/301828066/636646282-226fccf2-6884-400c-841a-01f731c166be.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ2MjgyLTIyNmZjY2YyLTY4ODQtNDAwYy04NDFhLTAxZjczMWMxNjZiZS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kYmRlYzU3OGU3N2E2M2E0N2UzYzZjNDU4MmE1ZmVlODEwYzc4ZWE3NDgxZGIwNTM0MTI2NGVhMDQxZGQ4YWY5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.Sbcff5FY0Tsur0tiMx2EaJ6Kd6hVSPSzSvH8O33Dbno)

<br>

Drilling into the STIG detail confirmed the Application Event Log max size was configured below the required 32,768 KB minimum.

![STIG detail view for WN11-AU-000500](https://private-user-images.githubusercontent.com/301828066/636647563-d0219a92-2de4-4c41-a352-85c7191230c7.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ3NTYzLWQwMjE5YTkyLTJkZTQtNGM0MS1hMzUyLTg1YzcxOTEyMzBjNy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0yYzEyNTk1ZWIzNDNmMDJlNWVjMjE2MzhhMGY1Y2ExYjk5ZTBkMGFmZWQwYmQ5NDI4ZjY3MzJmM2Y4YWZjODQ5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.RAzQMMz8Nh3BlHLw_53FY7WzStHGVum3RLDYDBaFAGo)

<br>

Checking the registry directly confirmed the `MaxSize` value was either missing or set below the required threshold.

![Registry showing non-compliant MaxSize value](https://private-user-images.githubusercontent.com/301828066/636647850-1c74a7d1-a009-42ca-a705-0fcbb4f3effd.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ3ODUwLTFjNzRhN2QxLWEwMDktNDJjYS1hNzA1LTBmY2JiNGYzZWZmZC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01Yjc5YWE0Y2FkYzdmODI5NjM2MjNlZjBlYzJiYWQ1ZjkyZWQ4YzZkYjVhODQyZDJiNGViZTA5M2E1NTA5NzdjJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.pdRfCZVzSoZyMrcLIfsOiYdOnrC4cn02jBGnnDMRVBE)

---

### 🛠️ Step 3 — Remediation

Two methods were used to resolve this finding.

<br>

#### Method 1 — Manual Registry Edit

Navigated to the registry path below and manually created the `MaxSize` DWORD value, setting it to `32768`:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application
"MaxSize" = dword:00008000
```

![Manual registry edit showing MaxSize being set](https://private-user-images.githubusercontent.com/301828066/636649450-4e4cf478-f992-4e3b-9cf7-6d1880957268.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ5NDUwLTRlNGNmNDc4LWY5OTItNGUzYi05Y2Y3LTZkMTg4MDk1NzI2OC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0wYmQ0ZDczY2Y1MmRkZjUzYjBkMmFiN2M0ZGY3ZDg1NDYyMGE0Mjc3NWVjMjcxZDgyZjlmNGIxMDk2ZDZiZTc1JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.eq-CFpFf5tE1WVpxl9F4DjQOLBzC_9jXFPNjiRceyIA)

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A more efficient and repeatable approach. Run the following script as Administrator — it creates the key if it doesn't exist and sets the correct value in one step:

```powershell
# Requires elevation (Run as Administrator)
$regPath = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application'

if (-not (Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

Set-ItemProperty -Path $regPath -Name 'MaxSize' -Value 0x8000 -Type DWord

Write-Host "Applied: MaxSize = 0x8000 (32768 KB) at $regPath"
```

After the script ran, the registry key and `MaxSize` value were correctly created at `32768 KB (0x8000)`.

![Registry after script execution confirming MaxSize created](https://private-user-images.githubusercontent.com/301828066/636649874-6e10c747-5f68-45db-88d8-a7fe82defd42.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ5ODc0LTZlMTBjNzQ3LTVmNjgtNDVkYi04OGQ4LWE3ZmU4MmRlZmQ0Mi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT02ZjZiNjlmNzE4NDRhNzcyZWRmYWEwYTQ3MGI3MzI2ZWY5Y2I4MzZiZGQwODIyOTUzMDgxYTZkMTFjZjBlMTI1JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.yrtWiPL4nSQaXEiHdl-Y_mO0APGJxFEQBsi5qjKTYmo)

---

### ✔️ Step 4 — Verification

A follow-up STIG scan was run to confirm the finding was resolved.

![Post-remediation scan result](https://private-user-images.githubusercontent.com/301828066/636650581-e342d28d-7d5b-48e3-871a-0f4176d5e187.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjUwNTgxLWUzNDJkMjhkLTdkNWItNDhlMy04NzFhLTBmNDE3NmQ1ZTE4Ny5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1jMDU4ZWMzOTJiZjNmZTAyMGZkNGQzNWY2NGYwZjcxNWE0YmYyMTU5YTBjNjcwNTY2MzgyMjI3MDQ3NmFmY2MwJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.HiA8FUKXjZkPbLMIgwimCDbMNxAYDj0TDITFrTLagiY)

Compliance status for WN11-AU-000500 now shows **Passed** ✅

![Final scan confirming compliance passed](https://private-user-images.githubusercontent.com/301828066/636650806-e8edd225-7000-43aa-96c7-c33d8b0ce1d2.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjUwODA2LWU4ZWRkMjI1LTcwMDAtNDNhYS05NmM3LWMzM2Q4YjBjZTFkMi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwODE2JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDgxNlQxNDQ1MDlaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01NDEwYjdlM2MxNWUxNjE4YTIxODUyNzVkNDVjODEzZjk3OWIyMjVkMGI3NDcyNmY0ODg1OWFkODczNDI2Nzc1JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZyZXNwb25zZS1jb250ZW50LXR5cGU9aW1hZ2UlMkZwbmcifQ.hgrpjM4wbQlFf3S5Hre18W6n-4mS6RQwT3IZoWY9j9M)

---

## ✅ STIG 2 — WN11-AU-000510
### System Event Log Maximum Size

<br>

### 📌 What Is This Finding?

The Windows 11 System Event Log must be configured to a minimum size of **32,768 KB (32 MB)**. Just like the Application log, an undersized System log causes older security-relevant events to be overwritten before they can be reviewed — breaking audit continuity.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Administrative Templates >> Windows Components >> Event Log Service >> System >> "Specify the maximum log file size (KB)"`
> to **Enabled**, with a Maximum Log Size of **32768 KB or greater**.
>
> *If the system sends audit records directly to an audit server, this finding is Not Applicable (NA) — must be documented with the ISSO.*

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AU-000510** as a failed finding. Inspecting the System Event Log properties confirmed the current max size was below the required 32,768 KB threshold.

![STIG scan showing WN11-AU-000510 failed and System log size non-compliant](https://github.com/user-attachments/assets/d6b527f9-30c4-4b9e-92c2-dd86f9e07b59)

<br>

Drilling into the Event Viewer confirmed the System log `MaxSize` was not meeting the required minimum.

![System Event Log properties showing non-compliant size](https://github.com/user-attachments/assets/15cde01b-9ab8-4e3c-9357-b9521c2bcc89)

---

### 🛠️ Step 2 — Remediation

A PowerShell script was used to apply the fix. Run the following as Administrator — it creates the registry key if it doesn't exist and sets `MaxSize` to the required value in one step:

```powershell
# Requires elevation (Run as Administrator)
$regPath = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\System'

if (-not (Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

Set-ItemProperty -Path $regPath -Name 'MaxSize' -Value 0x8000 -Type DWord

Write-Host "Applied: System log MaxSize = 32768 KB"
```

After the script ran, the registry key was created and `MaxSize` was correctly set to `32768 KB (0x8000)`.

![PowerShell script execution and registry confirming MaxSize applied](https://github.com/user-attachments/assets/36ba9989-6349-4927-a9aa-6695c303ac0d)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AU-000510 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/f646ae14-4813-4893-b21a-91fc289065dc)

---

## 🔲 STIG 3 — WN11-00-000175 

After running a scan, heres the specific vulnerability  

---
As shown below in order to remediate this vulnerability, we must configure the "Secondary Logon" service "Startup Type" to "Disabled".
## 🔲 STIG 4 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 5 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 6 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 7 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 8 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 9 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 🔲 STIG 10 — *(Coming Soon)*

> *Fill in the STIG ID, title, finding details, remediation steps, and screenshots when ready.*

---

## 📚 Resources

- [DISA STIG Library](https://public.cyber.mil/stigs/)
- [Windows 11 STIG Overview](https://www.stigviewer.com/stig/windows_11/)
- [NIST NVD](https://nvd.nist.gov/)




