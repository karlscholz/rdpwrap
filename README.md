# Install - Quick Start Guide to Install RDPWrap on Windows 

1. Activate RDP on the Windows machine:
2. Make a safety copy of the `termsrv.dll` file in the `C:\Windows\System32` folder.
```powershell
Copy-Item -Path "C:\Windows\System32\termsrv.dll" -Destination "C:\Windows\System32\termsrv.dll.bak"
```
3. Unzip the RDPWrap.zip file from https://github.com/stascorp/rdpwrap/releases
4. Run the install.bat file as an administrator
5. Run the RDPConf.exe file as an administrator. It should display `[not supported]` in the `Listener state` field. Do the next part to update the `rdpwrap.ini` file, then it should display `Listening [fully supported]`.

# To update that the rdpwrap.ini file, for the newest version:

```powershell
Stop-Service termservice -Force
Invoke-WebRequest https://raw.githubusercontent.com/sebaxakerhtc/rdpwrap.ini/master/rdpwrap.ini -outfile "C:\Program Files\RDP Wrapper\rdpwrap.ini"
Start-Service termservice
```

# Automate this part so new versions are automatically updated:
1. Create a Powershell script:
```powershell
nano C:\Scripts\Update-RDPWrap.ps1
```

2. Paste the following code:
```powershell
# Stop Remote Desktop Service
Stop-Service termservice -Force

# Download the latest rdpwrap.ini
Invoke-WebRequest https://raw.githubusercontent.com/sebaxakerhtc/rdpwrap.ini/master/rdpwrap.ini -outfile "C:\Program Files\RDP Wrapper\rdpwrap.ini"

# Start Remote Desktop Service
Start-Service termservice
```

3. Create a Batch File to Run the Powershell Script:
```powershell
nano C:\Scripts\Update-RDPWrap.bat
```

4. Paste the following code:
```powershell
@echo off
powershell.exe -ExecutionPolicy Bypass -File "C:\Scripts\Update-RDPWrap.ps1"
```

5. Create a Service:
```powershell
sc create UpdateRDPWrapService binPath= "cmd.exe /c C:\Scripts\Update-RDPWrap.bat" start= auto
```

6. Set Service Recovery Options:
```powershell
sc failure UpdateRDPWrapService reset= 86400 actions= restart/60000/restart/60000/restart/60000
```

7. Start the Service:
```powershell
sc start UpdateRDPWrapService
```

# Check how many RDP sessions are currently active:

    qwinsta

