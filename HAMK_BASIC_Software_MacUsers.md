# Installation of basic programs (HAMK BYOD) in students' personal devices (Mac Users)
The following guidelines utilizes the **[brew](https://brew.sh/) package manager** for **macOS** to install several applications that are used in different modules in HAMK. 
## Step 1: Install brew
### Requirements
* A 64-bit Intel CPU or Apple Silicon CPU 1
* macOS Catalina (10.15) (or higher) 2
* Command Line Tools (CLT) for Xcode (from xcode-select --install or https://developer.apple.com/download/all/) or Xcode 3

#### Install Xcode 
```
xcode-select --install
```
#### Install Brew
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

## Step 2: Programs/Apps Installation
### The list of apss that you are going to install is as below: <br>
> Vscode, vmware-horizon-client, disk-inventroy, git, github, obs, vmware-fusion & zoom.
### To install the above mentioned programs run the following script
```
brew install --cask visual-studio-code && brew install --cask vmware-horizon-client && brew install --cask disk-inventory-x && brew install --cask zoom && brew install git && brew install --cask github && brew install --cask obs && brew install --cask vmware-fusion
```