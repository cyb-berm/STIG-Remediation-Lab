# 🔐 Windows 11 STIG Remediation Lab

A hands-on cybersecurity lab documenting the identification, analysis, and remediation of 10 Security Technical Implementation Guide (STIG) findings on a Windows 11 Azure VM. Each entry includes a description of the vulnerability, evidence of the non-compliant state, the remediation applied, and post-fix compliance verification.

---

## 📋 Lab Overview

| Detail | Info |
|---|---|
| **Platform** | Microsoft Azure (Windows 11 VM) |
| **STIG Benchmark** | Windows 11 STIG |
| **Remediation Methods** | Registry edits, PowerShell, Group Policy |
| **Progress** | 10 / 10 Complete ✅ |

---

## ✅ Progress Tracker

| # | STIG ID | Title | Status |
|---|---|---|---|
| 1 | [WN11-AU-000500](#-stig-1--wn11-au-000500) | Application Event Log Max Size | ✅ Complete |
| 2 | [WN11-AU-000510](#-stig-2--wn11-au-000510) | System Event Log Max Size | ✅ Complete |
| 3 | [WN11-00-000175](#-stig-3--wn11-00-000175) | Secondary Logon Service Disabled | ✅ Complete |
| 4 | [WN11-AC-000005](#-stig-4--wn11-ac-000005) | Account Lockout Duration | ✅ Complete |
| 5 | [WN11-AC-000010](#-stig-5--wn11-ac-000010) | Account Lockout Threshold | ✅ Complete |
| 6 | [WN11-AC-000015](#-stig-6--wn11-ac-000015) | Logon Counter Reset Period | ✅ Complete |
| 7 | [WN11-AC-000020](#-stig-7--wn11-ac-000020) | Password History | ✅ Complete |
| 8 | [WN11-AC-000030](#-stig-8--wn11-ac-000030) | Minimum Password Age | ✅ Complete |
| 9 | [WN11-AC-000035](#-stig-9--wn11-ac-000035) | Minimum Password Length | ✅ Complete |
| 10 | [WN11-AC-000040](#-stig-10--wn11-ac-000040) | Password Complexity | ✅ Complete |

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

<img width="1858" height="854" alt="Screenshot 2026-08-15 at 12 11 23 PM" src="https://github.com/user-attachments/assets/308d0521-655b-49d7-bc1a-c4880802f2f9" />


---

### 🔍 Step 2 — Identifying the Finding

The baseline scan flagged **WN11-AU-000500** as a failed finding. Drilling into the STIG detail confirmed the Application Event Log max size was configured below the required 32,768 KB minimum. Checking the registry directly confirmed the `MaxSize` value was either missing or set below the required threshold.

<img width="1906" height="614" alt="Screenshot 2026-08-15 at 1 42 00 PM" src="https://github.com/user-attachments/assets/268be8a1-a82b-4cad-afb7-36924e7016fa" />

<img width="1918" height="909" alt="Screenshot 2026-08-15 at 1 40 52 PM" src="https://github.com/user-attachments/assets/7f723f52-a647-40ed-8a20-0c882e16a768" />

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

<img width="1783" height="750" alt="Screenshot 2026-08-15 at 4 37 35 PM" src="https://github.com/user-attachments/assets/c5380b01-22f6-4045-bc3a-01ea20d21d09" />


---

### ✔️ Step 4 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AU-000500 now shows **Passed** ✅

> <img width="1506" height="399" alt="Screenshot 2026-08-15 at 2 45 55 PM" src="https://github.com/user-attachments/assets/65290167-d625-48bb-ba99-cf2d5b065b6d" />


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

## ✅ STIG 7 — WN11-AC-000020
### Password History

<br>

### 📌 What Is This Finding?

Windows 11 must be configured to remember a minimum of **24 previously used passwords**. Without adequate password history enforcement, users can cycle through a small set of passwords — undermining the effectiveness of password rotation policies and making credential-based attacks easier.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Password Policy >> "Enforce password history"`
> to **24 or more passwords remembered**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000020** as a failed finding, indicating the password history was not configured to the required minimum of 24.

![STIG scan showing WN11-AC-000020 failed](https://github.com/user-attachments/assets/5976262e-38e8-4db2-b702-dcac713ac5a9)

<br>

Reviewing the STIG detail confirmed the requirement and the remediation path.

![STIG detail explaining the password history requirement](https://github.com/user-attachments/assets/45b99ce6-5172-4200-bd76-3f026145a8c7)

<br>

Inspecting the Password Policy confirmed the history was set to **0** — meaning no password history was being enforced at all.

![Password policy showing enforce password history set to 0](https://github.com/user-attachments/assets/71a071da-2471-461d-8352-7f45d233eabe)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Password Policy**
3. Double-click **Enforce password history**
4. Set the value to **24** → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /uniquepw:24
Write-Host "Password history set to 24 passwords remembered."
```

After running the script, the password history was correctly updated to 24.

![PowerShell script setting password history to 24](https://github.com/user-attachments/assets/657c7ca4-0ffc-43a1-94d5-a98b6490fb4a)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000020 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/21b23477-75b2-421d-8d85-718f0c2bfba8)

---

## ✅ STIG 8 — WN11-AC-000030
### Minimum Password Age

<br>

### 📌 What Is This Finding?

Windows 11 must be configured with a minimum password age of **at least 1 day**. Without this control, users can immediately change their password multiple times in succession to cycle back to a previously used one — completely bypassing password history enforcement.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Password Policy >> "Minimum password age"`
> to **1 or more days**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000030** as a failed finding, indicating the minimum password age was not configured to at least 1 day.

![STIG scan showing WN11-AC-000030 failed](https://github.com/user-attachments/assets/23bec646-6c8a-4f90-a61d-c7336afb0b1d)

<br>

Reviewing the STIG detail confirmed the requirement and the remediation path.

![STIG detail explaining the minimum password age requirement](https://github.com/user-attachments/assets/afe9ec52-6b7a-42c8-84eb-347645e8ef5b)

<br>

Inspecting the Password Policy confirmed the minimum password age was set to **0 days** — meaning passwords could be changed immediately and repeatedly.

![Password policy showing minimum password age set to 0](https://github.com/user-attachments/assets/6701f26e-7946-4f51-9788-377eb092ebab)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Password Policy**
3. Double-click **Minimum password age**
4. Set the value to **1** day → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /minpwage:1
Write-Host "Minimum password age set to 1 day."
```

After running the script, the minimum password age was correctly updated to 1 day.

![PowerShell script setting minimum password age to 1 day](https://github.com/user-attachments/assets/0eb9efac-bc99-4ff5-bb4d-07499b0f26c9)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000030 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/e579ddcc-117b-4900-bdc8-3514d9e606bc)

---

## ✅ STIG 9 — WN11-AC-000035
### Minimum Password Length

<br>

### 📌 What Is This Finding?

Windows 11 must enforce a minimum password length of **14 characters**. Shorter passwords are significantly more vulnerable to brute-force attacks — longer passwords exponentially increase the number of possible combinations an attacker must try.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Password Policy >> "Minimum password length"`
> to **14 characters or more**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000035** as a failed finding, indicating the minimum password length was below the required 14 characters.

![STIG scan showing WN11-AC-000035 failed](https://github.com/user-attachments/assets/fe620c2e-6fab-436e-9163-6e374c4ad0a9)

<br>

Reviewing the STIG detail confirmed the requirement and the remediation path.

![STIG detail explaining the minimum password length requirement](https://github.com/user-attachments/assets/0cd50e57-51d6-4ebc-93a1-40c30ddc0124)

<br>

Inspecting the Password Policy confirmed the minimum password length was set below the required 14-character minimum.

![Password policy showing non-compliant minimum password length](https://github.com/user-attachments/assets/4c921216-30ea-43d6-907a-9256019fc744)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Password Policy**
3. Double-click **Minimum password length**
4. Set the value to **14** → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
net accounts /minpwlen:14
Write-Host "Minimum password length set to 14 characters."
```

After running the script, the minimum password length was correctly updated to 14 characters.

![PowerShell script setting minimum password length to 14](https://github.com/user-attachments/assets/d8f5a6ae-6a8f-4950-a243-7593b0a270ce)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000035 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/110f04d0-625b-4a21-be65-099a9357c9bc)

---

## ✅ STIG 10 — WN11-AC-000040
### Password Complexity

<br>

### 📌 What Is This Finding?

Windows 11 must have the built-in Microsoft **password complexity filter enabled**. Without complexity requirements, users can set simple, easily guessable passwords — significantly weakening the organization's authentication security even when minimum length is enforced.

> **STIG Requirement:**
> Configure the policy value for:
> `Computer Configuration >> Windows Settings >> Security Settings >> Account Policies >> Password Policy >> "Password must meet complexity requirements"`
> to **Enabled**.

---

### 🔍 Step 1 — Identifying the Finding

The STIG scan flagged **WN11-AC-000040** as a failed finding, indicating password complexity requirements were not enabled.

![STIG scan showing WN11-AC-000040 failed](https://github.com/user-attachments/assets/a99c6fa0-c5e8-467a-b9d4-34ae9a2b7b6d)

<br>

Reviewing the STIG detail confirmed the requirement and the remediation path.

![STIG detail explaining the password complexity requirement](https://github.com/user-attachments/assets/9a7d72c7-343c-47cd-a317-60d1b597cb1b)

<br>

Inspecting the Password Policy confirmed the complexity filter was set to **Disabled** — meaning no character-type requirements were being enforced.

![Password policy showing complexity requirements disabled](https://github.com/user-attachments/assets/9ca3a5c0-b78c-4e0c-94de-e0c66351fba5)

---

### 🛠️ Step 2 — Remediation

Two methods can be used to resolve this finding.

<br>

#### Method 1 — Manual via Local Security Policy (secpol.msc)

1. Press `Win + R` → type `secpol.msc` → press **Enter**
2. Navigate to **Account Policies → Password Policy**
3. Double-click **Password must meet complexity requirements**
4. Set to **Enabled** → click **OK**

<br>

#### Method 2 — PowerShell Script *(Recommended)*

A faster and repeatable approach. Run the following as Administrator:

```powershell
# Requires elevation (Run as Administrator)
secedit /export /cfg C:\secpol.cfg
(Get-Content C:\secpol.cfg) -replace 'PasswordComplexity = 0','PasswordComplexity = 1' | Set-Content C:\secpol.cfg
secedit /configure /db secedit.sdb /cfg C:\secpol.cfg /quiet
Remove-Item C:\secpol.cfg
Write-Host "Password complexity enabled."
```

After running the script, the password complexity filter was correctly set to Enabled.

![PowerShell script enabling password complexity](https://github.com/user-attachments/assets/840e8b5b-fe04-45e0-838d-157772e6f81a)

---

### ✔️ Step 3 — Verification

A follow-up STIG scan was run to confirm the finding was resolved. Compliance status for WN11-AC-000040 now shows **Passed** ✅

![Post-remediation scan confirming compliance passed](https://github.com/user-attachments/assets/04b7e534-6820-4def-86be-ece65ad56e61)

---

## 📚 Resources

- [DISA STIG Library](https://public.cyber.mil/stigs/)
- [Windows 11 STIG Overview](https://www.stigviewer.com/stig/windows_11/)
- [NIST NVD](https://nvd.nist.gov/)
