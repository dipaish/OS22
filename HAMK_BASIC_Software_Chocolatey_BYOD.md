# Installation of basic programs (HAMK BYOD) in students' personal devices. 

The following guidelines utilizes the **chocolatey package manager** for **Windows** to install several applications that are used in different modules in HAMK. 

## Step 1: Install CHOCOLATEY

### Requirements
* Windows 7 or newer version of Windows Operating System (Recommended Windows 10 or 11)
* PowerShell v2+
* .NET Framework 4+ 

### Installation Process (with PowerShell)
* Start Powershell with Adminitrator rights (Run as Administrator)
> If you already have choco installed in your device, run the following command in your powershell 
```
rm C:\ProgramData\chocolatey -erroraction 'silentlycontinue'
```
* Run the following command in Powershell
```
Set-ExecutionPolicy Bypass -Scope Process -Force; iwr https://community.chocolatey.org/install.ps1 -UseBasicParsing | iex
```

# The list of programs that are installed: 

