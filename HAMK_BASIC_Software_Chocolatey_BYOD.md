# Installation of basic programs (HAMK BYOD) in students' personal devices (Windows 10/11). 

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
* Run the following command in Powershell to install CHOCOLATEY
```
Set-ExecutionPolicy Bypass -Scope Process -Force; iwr https://community.chocolatey.org/install.ps1 -UseBasicParsing | iex
```

## Step 2: Programs/Apps Installation
### The list of apss that you are going to install is as below: <br>
> powershell-core, git, vscode, putty, greenshot, notepadplusplus, winscp, 7zip,  paint.net, windirstat, zoom, sudo, vmrc, vmware-horizon-client, github-desktop & obs-studio
### To install the above mentioned programs run the following script
```
choco install powershell-core  git vscode putty greenshot notepadplusplus winscp 7zip paint.net windirstat zoom sudo vmrc vmware-horizon-client github-desktop obs-studio -y

```

## Step 3: Installing some useful VScode addons 
* Restart your Powershell session 
* Run the following command 
```
code --install-extension ms-vscode.powershell
code --install-extension vsls-contrib.gistfs
code --install-extension hashicorp.terraform
code --install-extension ms-vscode-remote.remote-containers
code --install-extension ms-azuretools.vscode-docker
code --install-extension ms-vscode-remote.vscode-remote-extensionpack
```
