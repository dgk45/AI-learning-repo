# Windows System Information Commands (CMD)

## 1. Windows Version

```cmd
ver
```

```cmd
systeminfo
```

---

# 2. Computer Name

```cmd
hostname
```

---

# 3. Current Logged-in User

```cmd
whoami
```

---

# 4. Operating System Details

```cmd
systeminfo
```

---

# 5. CPU Information

```cmd
wmic cpu get Name,NumberOfCores,NumberOfLogicalProcessors,MaxClockSpeed
```

---

# 6. RAM Information

```cmd
wmic memorychip get Capacity,Speed,Manufacturer
```

Total RAM

```cmd
systeminfo | findstr /C:"Total Physical Memory"
```

---

# 7. Motherboard Information

```cmd
wmic baseboard get Manufacturer,Product,Version,SerialNumber
```

---

# 8. BIOS Information

```cmd
wmic bios get Manufacturer,SMBIOSBIOSVersion,ReleaseDate,SerialNumber
```

---

# 9. Disk Information

```cmd
wmic diskdrive get Model,Size,InterfaceType,MediaType
```

Logical Drives

```cmd
wmic logicaldisk get Name,Size,FreeSpace
```

---

# 10. GPU Information

```cmd
wmic path win32_VideoController get Name,AdapterRAM,DriverVersion
```

---

# 11. Network Configuration

```cmd
ipconfig /all
```

---

# 12. MAC Address

```cmd
getmac
```

---

# 13. Active Network Connections

```cmd
netstat -ano
```

---

# 14. Running Processes

```cmd
tasklist
```

---

# 15. Running Services

```cmd
sc query
```

---

# 16. Installed Drivers

```cmd
driverquery
```

---

# 17. Installed Programs

```cmd
wmic product get Name,Version
```

---

# 18. Environment Variables

```cmd
set
```

---

# 19. PATH Variable

```cmd
echo %PATH%
```

---

# 20. Disk Usage

```cmd
fsutil volume diskfree C:
```

---

# 21. Windows Activation

```cmd
slmgr /xpr
```

---

# 22. Windows License Information

```cmd
slmgr /dlv
```

---

# 23. User Accounts

```cmd
net user
```

---

# 24. Local Groups

```cmd
net localgroup
```

---

# 25. Shared Folders

```cmd
net share
```

---

# 26. Startup Programs

```cmd
wmic startup get Caption,Command
```

---

# 27. Scheduled Tasks

```cmd
schtasks
```

---

# 28. Power Configuration

```cmd
powercfg /a
```

Battery Report

```cmd
powercfg /batteryreport
```

---

# 29. Windows Updates

```cmd
wmic qfe list
```

---

# 30. Firewall Status

```cmd
netsh advfirewall show allprofiles
```

---

# 31. Antivirus

```cmd
wmic /namespace:\\root\SecurityCenter2 path AntiVirusProduct get displayName
```

---

# 32. Current Date & Time

```cmd
date /t
```

```cmd
time /t
```

---

# 33. Time Zone

```cmd
tzutil /g
```

---

# 34. Ping Test

```cmd
ping google.com
```

---

# 35. DNS Lookup

```cmd
nslookup google.com
```

---

# 36. Route Table

```cmd
route print
```

---

# 37. ARP Table

```cmd
arp -a
```

---

# 38. Open Ports

```cmd
netstat -ab
```

---

# 39. Installed Hotfixes

```cmd
wmic qfe
```

---

# 40. Full System Report

```cmd
systeminfo
```

---

# 41. Export System Information

```cmd
systeminfo > systeminfo.txt
```

---

# 42. Export Installed Drivers

```cmd
driverquery > drivers.txt
```

---

# 43. Export Installed Programs

```cmd
wmic product get Name,Version > software.txt
```

---

# 44. Export Network Configuration

```cmd
ipconfig /all > network.txt
```

---

# 45. Export Running Processes

```cmd
tasklist > process.txt
```

---

# 46. Check Ollama Version

```cmd
ollama --version
```

---

# 47. List Ollama Models

```cmd
ollama list
```

---

# 48. Running Ollama Models

```cmd
ollama ps
```

---

# 49. Show Ollama Model Details

```cmd
ollama show qwen3:8b
```

---

# 50. Check Git Version

```cmd
git --version
```

---

# 51. Check Node.js Version

```cmd
node -v
```

---

# 52. Check npm Version

```cmd
npm -v
```

---

# 53. Check Python Version

```cmd
python --version
```

---

# 54. Check Java Version

```cmd
java -version
```

---

# 55. Check .NET SDK

```cmd
dotnet --info
```

---

# 56. Check VS Code Version

```cmd
code --version
```

---

# 57. Check PowerShell Version

```cmd
powershell $PSVersionTable.PSVersion
```

---

# 58. Check Winget Version

```cmd
winget --version
```

---

# 59. Check Docker Version

```cmd
docker --version
```

---

# 60. Check WSL Version

```cmd
wsl --version
```
---

# 61. Computer Manufacturer & Model

Displays the computer manufacturer and model.

```cmd
wmic csproduct get Vendor,Name
```

Example:

```text
Vendor      Name
Dell Inc.   Latitude 7450
```

> Note: On some systems, `Vendor` may return blank. If that happens, use:

```cmd
wmic computersystem get Manufacturer,Model
```

---

# 62. Detailed RAM Information

Displays each installed memory module.

```cmd
wmic memorychip get Capacity,Speed,Manufacturer,PartNumber
```

Example:

```text
Capacity        Speed  Manufacturer  PartNumber
8589934592      3200   Samsung       M471A1K43EB1-CWE
8589934592      3200   Samsung       M471A1K43EB1-CWE
```

Capacity is shown in bytes.

---

# 63. RAM Serial Number

```cmd
wmic memorychip get SerialNumber
```

---

# 64. RAM Bank Information

```cmd
wmic memorychip get BankLabel,DeviceLocator,Capacity
```

---

# 65. Processor Name

```cmd
wmic cpu get Name
```

---

# 66. Processor Architecture

```cmd
wmic os get OSArchitecture
```

---

# 67. BIOS Serial Number

```cmd
wmic bios get SerialNumber
```

---

# 68. System UUID

```cmd
wmic csproduct get UUID
```

---

# 69. Manufacturer & Model

```cmd
wmic computersystem get Manufacturer,Model
```

---

# 70. Complete Computer System Information

```cmd
wmic computersystem get *
```
---

# 71. Computer Manufacturer, Model & Domain

```cmd
wmic computersystem get Manufacturer,Model,Domain
```

---

# 72. Computer Name

```cmd
wmic computersystem get Name
```

---

# 73. Current Logged-in User

```cmd
wmic computersystem get UserName
```

---

# 74. Total Physical Memory

```cmd
wmic computersystem get TotalPhysicalMemory
```

---

# 75. Number of Processors

```cmd
wmic computersystem get NumberOfProcessors
```

---

# 76. Number of Logical Processors

```cmd
wmic cpu get NumberOfLogicalProcessors
```

---

# 77. CPU Core Count

```cmd
wmic cpu get NumberOfCores
```

---

# 78. CPU Max Clock Speed

```cmd
wmic cpu get MaxClockSpeed
```

---

# 79. CPU Current Clock Speed

```cmd
wmic cpu get CurrentClockSpeed
```

---

# 80. CPU Processor ID

```cmd
wmic cpu get ProcessorId
```

---

# 81. CPU Caption

```cmd
wmic cpu get Caption
```

---

# 82. CPU Device ID

```cmd
wmic cpu get DeviceID
```

---

# 83. CPU Status

```cmd
wmic cpu get Status
```

---

# 84. Installed RAM Modules

```cmd
wmic memorychip get BankLabel,Capacity,Speed
```

---

# 85. RAM Memory Type

```cmd
wmic memorychip get MemoryType
```

---

# 86. RAM Configured Clock Speed

```cmd
wmic memorychip get ConfiguredClockSpeed
```

---

# 87. RAM Form Factor

```cmd
wmic memorychip get FormFactor
```

---

# 88. RAM Device Locator

```cmd
wmic memorychip get DeviceLocator
```

---

# 89. RAM SMBIOS Memory Type

```cmd
wmic memorychip get SMBIOSMemoryType
```

---

# 90. Motherboard Details

```cmd
wmic baseboard get Manufacturer,Product,Version
```

---

# 91. Motherboard Serial Number

```cmd
wmic baseboard get SerialNumber
```

---

# 92. BIOS Version

```cmd
wmic bios get SMBIOSBIOSVersion
```

---

# 93. BIOS Release Date

```cmd
wmic bios get ReleaseDate
```

---

# 94. BIOS Manufacturer

```cmd
wmic bios get Manufacturer
```

---

# 95. BIOS Name

```cmd
wmic bios get Name
```

---

# 96. BIOS Status

```cmd
wmic bios get Status
```

---

# 97. Operating System Name

```cmd
wmic os get Caption
```

---

# 98. OS Version

```cmd
wmic os get Version
```

---

# 99. OS Build Number

```cmd
wmic os get BuildNumber
```

---

# 100. OS Install Date

```cmd
wmic os get InstallDate
```

---

# 101. Last Boot Time

```cmd
wmic os get LastBootUpTime
```

---

# 102. Local Time

```cmd
wmic os get LocalDateTime
```

---

# 103. Boot Device

```cmd
wmic os get BootDevice
```

---

# 104. Windows Directory

```cmd
wmic os get WindowsDirectory
```

---

# 105. System Drive

```cmd
wmic os get SystemDrive
```

---

# 106. System Directory

```cmd
wmic os get SystemDirectory
```

---

# 107. Page File Size

```cmd
wmic pagefile list full
```

---

# 108. Disk Model

```cmd
wmic diskdrive get Model
```

---

# 109. Disk Serial Number

```cmd
wmic diskdrive get SerialNumber
```

---

# 110. Disk Interface Type

```cmd
wmic diskdrive get InterfaceType
```

---

# 111. Disk Media Type

```cmd
wmic diskdrive get MediaType
```

---

# 112. Disk Partitions

```cmd
wmic partition get Name,Size,Type
```

---

# 113. Logical Drives

```cmd
wmic logicaldisk get Name,FileSystem,FreeSpace,Size
```

---

# 114. Volume Information

```cmd
wmic volume get DriveLetter,Label,Capacity,FreeSpace
```

---

# 115. Video Controller Name

```cmd
wmic path win32_VideoController get Name
```

---

# 116. GPU Driver Version

```cmd
wmic path win32_VideoController get DriverVersion
```

---

# 117. GPU RAM

```cmd
wmic path win32_VideoController get AdapterRAM
```

---

# 118. Sound Devices

```cmd
wmic sounddev get Name,Manufacturer
```

---

# 119. Network Adapter Name

```cmd
wmic nic get Name
```

---

# 120. Enabled Network Adapters

```cmd
wmic nic where "NetEnabled=true" get Name,Speed,MACAddress
```
