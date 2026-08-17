# 🔐 Windows 11 STIG Remediation Lab

A hands-on cybersecurity lab documenting the identification, analysis, and remediation of 10 Security Technical Implementation Guide (STIG) findings on a Windows 11 Azure VM. Each entry includes a description of the vulnerability, evidence of the non-compliant state, the remediation applied, and post-fix compliance verification.

---

## 📋 Lab Overview

| Detail | Info |
|---|---|
| **Platform** | Microsoft Azure (Windows 11 VM) |
| **STIG Benchmark** | Windows 11 STIG |
| **Remediation Methods** | Registry edits, PowerShell, Group Policy |
| **Progress** | 4 / 10 Complete |

---

## ✅ Progress Tracker

| # | STIG ID | Title | Status |
|---|---|---|---|
| 1 | WN11-AU-000500 | Application Event Log Max Size | ✅ Complete |
| 2 | WN11-AU-000510 | System Event Log Max Size | ✅ Complete |
| 3 | WN11-00-000175 | Secondary Logon Service Disabled | ✅ Complete |
| 4 | WN11-AC-000005 | Account Lockout Duration | ✅ Complete |
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

![Initial scan results](https://private-user-images.githubusercontent.com/301828066/636577288-a5a6f64e-7209-45aa-9c9d-8e5891753753.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NTc3Mjg4LWE1YTZmNjRlLTcyMDktNDVhYS05YzlkLThlNTg5MTc1Mzc1My5wbmciLCJzaWduYXR1cmUiOiJBb0ZmQ081VzZ4N3VIYWNrT2MzQW04Sk9WYmh5TG9DTnlXcmVfWTU5SUEifQ.AoFfCO5W6x7uHackOc3Am8JOVbVhyLoCNyWre_Y59IA)

---

### 🔍 Step 2 — Identifying the Finding

The baseline scan flagged **WN11-AU-000500** as a failed finding.

![STIG scan showing WN11-AU-000500 failed](https://private-user-images.githubusercontent.com/301828066/636646282-226fccf2-6884-400c-841a-01f731c166be.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ2MjgyLTIyNmZjY2YyLTY4ODQtNDAwYy04NDFhLTAxZjczMWMxNjZiZS5wbmciLCJzaWduYXR1cmUiOiJkYmRlYzU3OGU3N2E2M2E0N2UzYzZjNDU4MmE1ZmVlODEwYzc4ZWE3NDgxZGIwNTM0MTI2NGVhMDQxZGQ4YWY5In0.Sbcff5FY0Tsur0tiMx2EaJ6Kd6hVSPSzSvH8O33Dbno)

<br>

Drilling into the STIG detail confirmed the Application Event Log max size was configured below the required 32,768 KB minimum.

![STIG detail view for WN11-AU-000500](https://private-user-images.githubusercontent.com/301828066/636647563-d0219a92-2de4-4c41-a352-85c7191230c7.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ3NTYzLWQwMjE5YTkyLTJkZTQtNGM0MS1hMzUyLTg1YzcxOTEyMzBjNy5wbmciLCJzaWduYXR1cmUiOiJSQXpRTU16OE5oM0JsSHdfNTNGWTdXelN0SEdWdW0zUkxZREJhRkFHbyJ9.RAzQMMz8Nh3BlHLw_53FY7WzStHGVum3RLDYDBaFAGo)

<br>

Checking the registry directly confirmed the `MaxSize` value was either missing or set below the required threshold.

![Registry showing non-compliant MaxSize value](https://private-user-images.githubusercontent.com/301828066/636647850-1c74a7d1-a009-42ca-a705-0fcbb4f3effd.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ3ODUwLTFjNzRhN2QxLWEwMDktNDJjYS1hNzA1LTBmY2JiNGYzZWZmZC5wbmciLCJzaWduYXR1cmUiOiJwZFJmQ1pWelNvWnlNcmNMSWZzT2lZZE9uckM0Y24wMkpHbm5ETVJWQNSVRCJ9.pdRfCZVzSoZyMrcLIfsOiYdOnrC4cn02jBGnnDMRVBE)

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

![Manual registry edit showing MaxSize being set](https://private-user-images.githubusercontent.com/301828066/636649450-4e4cf478-f992-4e3b-9cf7-6d1880957268.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ5NDUwLTRlNGNmNDc4LWY5OTItNGUzYi05Y2Y3LTZkMTg4MDk1NzI2OC5wbmciLCJzaWduYXR1cmUiOiIwYmQ0ZDczY2Y1MmRkZjUzYjBkMmFiN2M0ZGY3ZDg1NDYyMGE0Mjc3NWVjMjcxZDgyZjlmNGIxMDk2ZDZiZTc1In0.eq-CFpFf5tE1WVpxl9F4DjQOLBzC_9jXFPNjiRceyIA)

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

![Registry after script execution confirming MaxSize created](https://private-user-images.githubusercontent.com/301828066/636649874-6e10c747-5f68-45db-88d8-a7fe82defd42.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjQ5ODc0LTZlMTBjNzQ3LTVmNjgtNDVkYi04OGQ4LWE3ZmU4MmRlZmQ0Mi5wbmciLCJzaWduYXR1cmUiOiI2ZjZiNjlmNzE4NDRhNzcyZWRmYWEwYTQ3MGI3MzI2ZWY5Y2I4MzZiZGQwODIyOTUzMDgxYTZkMTFjZjBlMTI1In0.yrtWiPL4nSQaXEiHdl-Y_mO0APGJxFEQBsi5qjKTYmo)

---

### ✔️ Step 4 — Verification

A follow-up STIG scan was run to confirm the finding was resolved.

![Post-remediation scan result](https://private-user-images.githubusercontent.com/301828066/636650581-e342d28d-7d5b-48e3-871a-0f4176d5e187.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjUwNTgxLWUzNDJkMjhkLTdkNWItNDhlMy04NzFhLTBmNDE3NmQ1ZTE4Ny5wbmciLCJzaWduYXR1cmUiOiJjMDU4ZWMzOTJiZjNmZTAyMGZkNGQzNWY2NGYwZjcxNWE0YmYyMTU5YTBjNjcwNTY2MzgyMjI3MDQ3NmFmY2MwIn0.HiA8FUKXjZkPbLMIgwimCDbMNxAYDj0TDITFrTLagiY)

Compliance status for WN11-AU-000500 now shows **Passed** ✅

![Final scan confirming compliance passed](https://private-user-images.githubusercontent.com/301828066/636650806-e8edd225-7000-43aa-96c7-c33d8b0ce1d2.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3ODY4OTE4MDksIm5iZiI6MTc4Njg5MTUwOSwicGF0aCI6Ii8zMDE4MjgwNjYvNjM2NjUwODA2LWU4ZWRkMjI1LTcwMDAtNDNhYS05NmM3LWMzM2Q4YjBjZTFkMi5wbmciLCJzaWduYXR1cmUiOiI1NDEwYjdlM2MxNWUxNjE4YTIxODUyNzVkNDVjODEzZjk3OWIyMjVkMGI3NDcyNmY0ODg1OWFkODczNDI2Nzc1In0.hgrpjM4wbQlFf3S5Hre18W6n-4mS6RQwT3IZoWY9j9M)

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

## ✅ STIG 3 — WN11-00-000175
### Secondary Logon Service Disabled

<br>

### 📌 What Is This Finding?

The Windows 11 **Secondary Logon** service allows processes to be started under alternate credentials, which can be exploited by attackers to escalate privileges or run malicious processes under a different user context. The STIG requires this service to be fully disabled to reduce the attack surface.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> System Services >> Secondary Logon`
> to **Startup Type: Disabled**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-00-000175** as a failed finding, indicating the Secondary Logon service was not disabled.

![STIG scan showing WN11-00-000175 failed](https://github.com/user-attachments/assets/8b320c9d-4c38-44ad-903f-0ba0480ff4af)

<br>

Reviewing the service confirmed it was running with a startup type other than Disabled — leaving the vulnerability open.

![Secondary Logon service showing non-compliant startup type](https://github.com/user-attachments/assets/e3c126f3-1113-41c7-84cb-5b158ffee6b5)

---

### 🛠️ Step 2 — Remediation

Two methods were used to resolve this finding.

<br>

#### Method 1 — Manual via Services (services.msc)

1. Press `Win + R` → type `services.msc` → press **Enter**
2. Scroll down to **Secondary Logon**
3. Right-click → **Properties**
4. Set **Startup type** to **Disabled** → click **Stop** → click **Apply**

![services.msc showing Secondary Logon set to Disabled](https://github.com/user-attachments/assets/4872814b-7457-45c6-a208-44197952affc)

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
Set-Service -Name seclogon -StartupType Disabled
Stop-Service -Name seclogon -Force
Write-Host "Secondary Logon service disabled."
```

After the script ran, the service was stopped and its startup type was correctly set to Disabled.

![PowerShell script disabling Secondary Logon service](https://github.com/user-attachments/assets/d097e783-759a-4d3f-8461-649285ec88f3)

<br>

Verifying in `services.msc` confirmed the change was applied correctly.

![services.msc confirming Secondary Logon is Disabled](https://github.com/user-attachments/assets/dff218ea-127b-421c-8076-303bfdd5292c)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-00-000175 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/19c59f1f-3e7e-4f0b-a789-536011433704)

---

## ✅ STIG 4 — WN11-AC-000005
### Account Lockout Duration

<br>

### 📌 What Is This Finding?

Windows 11 must be configured to lock out accounts for a minimum of **15 minutes** after a set number of failed logon attempts. Without an adequate lockout duration, attackers can repeatedly attempt credentials in a short window — making brute-force and password-spray attacks more viable.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Account Lockout Policy >> "Account lockout duration"`
> to **15 minutes or greater**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000005** as a failed finding, indicating the account lockout duration was below the required 15-minute minimum.

![STIG scan showing WN11-AC-000005 failed](https://github.com/user-attachments/assets/766e90a0-9b41-459c-b894-d2c215410130)

<br>

Reviewing the Account Lockout Policy confirmed the duration was set to only **10 minutes** — below the required threshold.

![Account lockout policy showing duration set to 10 minutes](https://github.com/user-attachments/assets/acc546dc-0228-45b4-ad7f-62a697692e9b)

<br>

Inspecting the policy setting directly confirmed the non-compliant value.

![Account lockout duration policy detail showing 10 minutes](https://github.com/user-attachments/assets/a910fa5b-4fb3-4cef-9017-e3158be9f3e3)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Account Lockout Policy**
3. Double-click **Account lockout duration**
4. Set the value to **15** minutes → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /lockoutduration:15
Write-Host "Account lockout duration set to 15 minutes."
```

After running the script, the lockout duration was correctly updated to 15 minutes.

![PowerShell script setting account lockout duration to 15 minutes](https://github.com/user-attachments/assets/34257cb4-906c-4d21-8f1c-12d2e07ce44e)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000005 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/ed50c7bc-84e7-4c98-9c5c-290005af42d9)

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
