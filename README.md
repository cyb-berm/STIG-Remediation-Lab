# 🔐 Windows 11 STIG Remediation Lab

A hands-on cybersecurity lab documenting the identification, analysis, and remediation of 10 Security Technical Implementation Guide (STIG) findings on a Windows 11 Azure VM. Each entry includes a description of the vulnerability, evidence of the non-compliant state, the remediation applied, and post-fix compliance verification.

---

## 📋 Lab Overview

| Detail | Info |
|---|---|
| **Platform** | Microsoft Azure (Windows 11 VM) |
| **STIG Benchmark** | Windows 11 STIG |
| **Remediation Methods** | Registry edits, PowerShell, Group Policy |
| **Progress** | 6 / 10 Complete |

---

## ✅ Progress Tracker

| # | STIG ID | Title | Status |
|---|---|---|---|
| 1 | WN11-AU-000500 | Application Event Log Max Size | ✅ Complete |
| 2 | WN11-AU-000510 | System Event Log Max Size | ✅ Complete |
| 3 | WN11-00-000175 | Secondary Logon Service Disabled | ✅ Complete |
| 4 | WN11-AC-000005 | Account Lockout Duration | ✅ Complete |
| 5 | WN11-AC-000010 | Account Lockout Threshold | ✅ Complete |
| 6 | WN11-AC-000015 | Logon Counter Reset Period | ✅ Complete |
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

> ⚠️ *Screenshots pending re-upload with permanent links.*

---

### 🔍 Step 2 — Identifying the Finding

The baseline scan flagged **WN11-AU-000500** as a failed finding. Drilling into the STIG detail confirmed the Application Event Log max size was configured below the required 32,768 KB minimum. Checking the registry directly confirmed the `MaxSize` value was either missing or set below the required threshold.

> ⚠️ *Screenshots pending re-upload with permanent links.*

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

> ⚠️ *Screenshots pending re-upload with permanent links.*

---

### ✔️ Step 4 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AU-000500 now shows **Passed** ✅

> ⚠️ *Screenshots pending re-upload with permanent links.*

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

## ✅ STIG 5 — WN11-AC-000010
### Account Lockout Threshold

<br>

### 📌 What Is This Finding?

Windows 11 must be configured to lock out an account after no more than **3 invalid logon attempts**. Allowing too many failed attempts before a lockout gives attackers more opportunities to guess credentials through brute-force or password-spray attacks.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Account Lockout Policy >> "Account lockout threshold"`
> to **3 or fewer invalid logon attempts**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000010** as a failed finding, indicating the lockout threshold was configured above the maximum allowed value of 3.

![STIG scan showing WN11-AC-000010 failed](https://github.com/user-attachments/assets/639906e4-2645-4434-8f88-2e37dcd5b5ed)

<br>

Reviewing the STIG detail confirmed the requirement and the non-compliant state.

![STIG detail explaining the lockout threshold requirement](https://github.com/user-attachments/assets/bbf4ca14-e581-414b-b408-bd5df23527af)

<br>

Inspecting the Account Lockout Policy confirmed the threshold was set to **10** — well above the maximum of 3.

![Account lockout threshold showing non-compliant value of 10](https://github.com/user-attachments/assets/db7eeb8a-0975-4cb9-b463-0309427d7cb7)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Account Lockout Policy**
3. Double-click **Account lockout threshold**
4. Set the value to **3** → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /lockoutthreshold:3
Write-Host "Account lockout threshold set to 3."
```

After running the script, the threshold was correctly updated to 3 invalid logon attempts.

![PowerShell script setting account lockout threshold to 3](https://github.com/user-attachments/assets/81bf1f02-e019-4892-ae48-652487ec735a)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000010 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/466b6eb7-8472-4069-87ff-20241f800f59)

---

## ✅ STIG 6 — WN11-AC-000015
### Logon Counter Reset Period

<br>

### 📌 What Is This Finding?

Windows 11 must be configured to reset the bad logon counter after a minimum of **15 minutes**. If the reset window is too short, an attacker can spread failed attempts across multiple windows to avoid triggering an account lockout — effectively bypassing the threshold control set in WN11-AC-000010.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Account Lockout Policy >> "Reset account lockout counter after"`
> to **15 minutes or greater**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000015** as a failed finding, indicating the logon counter reset period was set below the required 15-minute minimum.

![STIG scan showing WN11-AC-000015 failed](https://github.com/user-attachments/assets/9c63a464-cb80-4e10-b282-e3d74cff5687)

<br>

Reviewing the STIG detail confirmed the requirement and the remediation path.

![STIG detail explaining the reset counter requirement](https://github.com/user-attachments/assets/52fcab52-cebb-4903-b33a-6352334130e3)

<br>

Inspecting the Account Lockout Policy confirmed the counter reset was set to only **10 minutes** — below the required threshold.

![Account lockout reset counter showing non-compliant value of 10 minutes](https://github.com/user-attachments/assets/28614d77-68ac-435f-a805-dd4a15c9e33d)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Account Lockout Policy**
3. Double-click **Reset account lockout counter after**
4. Set the value to **15** minutes → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /lockoutwindow:15
Write-Host "Logon counter reset period set to 15 minutes."
```

After running the script, the reset period was correctly updated to 15 minutes.

![PowerShell script setting logon counter reset period to 15 minutes](https://github.com/user-attachments/assets/c06130f9-3ab8-42c7-98c1-71e56d3c7bd5)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000015 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/faefb0b0-2f73-4f81-bdec-8de003744ed9)

---

## 🔲 STIG 7 — WN11-AC-000020

For the next STIG, the scan states that the password history must be configured to 24 passwords remembered.

<img width="1458" height="292" alt="image" src="https://github.com/user-attachments/assets/5976262e-38e8-4db2-b702-dcac713ac5a9" />

In the provided image it shows how to correctly remediate this vulnerability. 

<img width="1512" height="359" alt="image" src="https://github.com/user-attachments/assets/45b99ce6-5172-4200-bd76-3f026145a8c7" />

As shown below the current password remember is at 0

<img width="1512" height="767" alt="image" src="https://github.com/user-attachments/assets/71a071da-2471-461d-8352-7f45d233eabe" />

There is a manual way to remediate the vulnerability with the following steps 

1.secpol.msc → Account Policies → Password Policy
2.Double-click Enforce password history
3.Set to 24 → OK

As you can see in the below image, I went ahead & remediated the issue with the follow power shell script which is more efficient & recommended. 

<img width="1117" height="621" alt="image" src="https://github.com/user-attachments/assets/657c7ca4-0ffc-43a1-94d5-a98b6490fb4a" />


After running a scan, as we can see with the provided image below the scan now says its passed & fixed.



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

- [DISA STIG Library](https://public.cyber.mil/stigs/)
- [Windows 11 STIG Overview](https://www.stigviewer.com/stig/windows_11/)
- [NIST NVD](https://nvd.nist.gov/)
