## Here are a few more highly effective PowerShell commands tailored for tracking down performance drops, checking system health, and managing resources on Windows.

## 💾 Storage & Disk Diagnostics
### 1. Identify Large Files Quickly
If you are running low on disk space, this command scans a specific folder (change C:\Users\Raj Kumar\Downloads to whatever path you want) and lists the top 20 largest files.

``` PowerShell
Get-ChildItem -Path "C:\Users\Raj Kumar\Downloads" -Recurse -File -ErrorAction SilentlyContinue | 
    Sort-Object Length -Descending | 
    Select-Object Name, @{Name="Size (GB)";Expression={[math]::Round($_.Length / 1GB, 2)}}, FullName -First 20
``` 
### 2. Check Drive Health status
Check the physical health operational status of your SSDs or HDDs directly from the storage controller.

``` PowerShell
Get-PhysicalDisk | Select-Object DeviceId, FriendlyName, MediaType, OperationalStatus, HealthStatus
``` 
## ⚙️ Advanced Process & Resource Triage
### 3. Track CPU-Hogging Processes
If your computer fans are spinning up loudly, this command isolates the top 10 processes consuming the most raw processor time.

``` PowerShell
Get-Process | Sort-Object CPU -Descending | 
    Select-Object Name, Id, @{Name="CPU Time (s)";Expression={[math]::Round($_.CPU, 2)}} -First 10
``` 
### 4. Find Unresponsive "Frozen" Applications
Quickly query the windowing subsystem to catch any running graphical applications that have stopped responding to the operating system loop.

``` PowerShell
Get-Process | Where-Object { $_.Responding -eq $false } | Select-Object Name, Id, Description
``` 
## 🩺 System Uptime & Stability
### 5. Check Exact System Uptime
Find out exactly how long your machine has been running since its last full reboot cycle.

``` PowerShell
Get-CimInstance Win32_OperatingSystem | 
    Select-Object @{Name="Last Boot Time";Expression={$_.LastBootUpTime}}, 
                  @{Name="Uptime";Expression={(Get-Date) - $_.LastBootUpTime}}
``` 
### 6. Pull Critical System Crashes (Blue Screens / BSODs)
Query the Windows Event Log for the last 5 critical unexpected shutdowns or system kernel errors without opening the slow Event Viewer GUI.

``` PowerShell
Get-WinEvent -FilterHashtable @{LogName='System'; Id=1001, 6008} -MaxEvents 5 | 
    Select-Object TimeCreated, Message | Format-List
``` 
## 🛠️ Service Management
### 7. View Resource-Heavy Third-Party Services
List all background services that are currently running, set to start automatically, and are not built by Microsoft. Great for catching bloated background updaters.

``` PowerShell
Get-WmiObject Win32_Service | 
    Where-Object { $_.State -eq "Running" -and $_.StartMode -eq "Auto" -and $_.PathName -notmatch "Windows|system32" } | 
    Select-Object Name, DisplayName, StartMode
``` 
