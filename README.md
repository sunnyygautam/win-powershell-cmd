# Windows PowerShell Memory Diagnostics & Optimization Guide

A structured guide and reference repository containing robust PowerShell scripts for diagnosing operating system memory health, identifying resource-heavy processes, and aggregating multi-process memory consumption (e.g., Google Chrome).

---

## 📋 System Memory Diagnostic

The following script queries the Windows Management Instrumentation (WMI) engine via CIM instances to calculate total, free, and used physical memory parameters.

### PowerShell Script (`Get-SystemMemory.ps1`)
```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object `
    @{Name="Total Memory (GB)";Expression={[math]::Round($_.TotalVisibleMemorySize / 1MB, 2)}},
    @{Name="Free Memory (GB)";Expression={[math]::Round($_.FreePhysicalMemory / 1MB, 2)}},
    @{Name="Used Memory (GB)";Expression={[math]::Round(($_.TotalVisibleMemorySize - $_.FreePhysicalMemory) / 1MB, 2)}},
    @{Name="Memory Usage (%)";Expression={[math]::Round((($_.TotalVisibleMemorySize - $_.FreePhysicalMemory) / $_.TotalVisibleMemorySize) * 100, 2)}}
```
## 🔍 Process-Level Allocation Tracker
This command collects active runtime metrics, sorts them dynamically based on Working Set (WS) allocation parameters, and lists the top 20 consumers currently holding execution states.

### PowerShell Script (Get-TopProcesses.ps1)
```powershell
Get-Process | Sort-Object -Property WS -Descending | Select-Object -First 20 Name, Id, @{Name="RAM (MB)";Expression={[math]::Round($_.WS / 1MB, 2)}}
```
## 📊 Aggregated Application Grouping
Browsers using modern multi-process architectures (like Google Chrome or Microsoft Edge) split tabs, extensions, and engines across multiple Process IDs (PIDs). The command below pools these distinct runtime instances to profile total cumulative footprint.

### PowerShell Script (Get-AggregatedMemory.ps1)
```powershell
Get-Process | Group-Object Name | 
    Select-Object Name, 
    Count, 
    @{Name="Total RAM (MB)";Expression={[math]::Round(($_.Group | Measure-Object WS -Sum).Sum / 1MB, 2)}} | 
    Sort-Object "Total RAM (MB)" -Descending | Select-Object -First 10
```
