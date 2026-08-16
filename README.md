# Remediating / implementing 10-STIGS

1st STIG we will be remediating is STIG ID: WN11-AU-000500 

<img width="1868" height="890" alt="image" src="https://github.com/user-attachments/assets/a5a6f64e-7209-45aa-9c9d-8e5891753753" />
Within azure, i created a VM & disabled the firewalls & ran a scan & ended with these results

<img width="1858" height="854" alt="Screenshot 2026-08-15 at 12 11 23 PM" src="https://github.com/user-attachments/assets/226fccf2-6884-400c-841a-01f731c166be" />
Here is the current STIG ID : WN11-AU-00500 that we are going to remediate. 

<img width="1906" height="614" alt="Screenshot 2026-08-15 at 1 42 00 PM" src="https://github.com/user-attachments/assets/d0219a92-2de4-4c41-a352-85c7191230c7" />
as stated, to fix the issue " If the system is configured to send audit records directly to an audit server, this is NA. This must be documented with the ISSO.

Configure the policy value for Computer Configuration >> Administrative Templates >> Windows Components >> Event Log Service >> Application >> "Specify the maximum log file size (KB)" to "Enabled" with a "Maximum Log Size (KB)" of "32768" or greater."

<img width="1918" height="909" alt="Screenshot 2026-08-15 at 1 40 52 PM" src="https://github.com/user-attachments/assets/1c74a7d1-a009-42ca-a705-0fcbb4f3effd" />
here you can see in the provided image that the Max log size is not above  32768 KB or greater.

<img width="1917" height="911" alt="Screenshot 2026-08-15 at 2 11 51 PM" src="https://github.com/user-attachments/assets/4e4cf478-f992-4e3b-9cf7-6d1880957268" />
one way to remediate this issue is to manually go into the registry & create the necessary folders & also naming file MaxSize. While setting the file size to at-least 32768 kb or greater.

<img width="1370" height="798" alt="Screenshot 2026-08-15 at 4 35 41 PM" src="https://github.com/user-attachments/assets/6e10c747-5f68-45db-88d8-a7fe82defd42" />
a more efficient way to remediate the situation is using the following script.

$regPath = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Application'

if (-not (Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

Set-ItemProperty -Path $regPath -Name 'MaxSize' -Value 0x8000 -Type DWord

Write-Host "Applied: MaxSize = 0x8000 (32768 KB) at $regPath"

<img width="1783" height="750" alt="Screenshot 2026-08-15 at 4 37 35 PM" src="https://github.com/user-attachments/assets/e342d28d-7d5b-48e3-871a-0f4176d5e187" />
as shown, after the script was ran the following folders were created with the file named "MaxSize" with a size of 32768 kb.

<img width="1506" height="399" alt="Screenshot 2026-08-15 at 2 45 55 PM" src="https://github.com/user-attachments/assets/e8edd225-7000-43aa-96c7-c33d8b0ce1d2" />
& to finalize it, i ran another scan to see if the following vulnerability was fixed. As shown it says the compliance is passed.   











