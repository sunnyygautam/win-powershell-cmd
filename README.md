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
