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