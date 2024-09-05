# Prerequisites
- LDPlayer
- Git
- SQL Server Express 2022
- SSMS (SQL Server Management Studio 20)
- mitmproxy
- WireGuard
- Blue Archive JP
- SCHALE.GameServer

> [!CAUTION]  
> **Make sure you've done the tutorial and have downloaded all the data**

# Steps 1 [Setup LDPlayer]
1. Make the emulator writeable system, enable root and enable local connection for ADB
![image](https://github.com/user-attachments/assets/23160474-3021-4532-9e51-a8adac4e2e02)
![image](https://github.com/user-attachments/assets/f1db6845-df2e-4844-9deb-e0b2ca406bbb)
2. Restart the emulator

# Steps 2 [Create System CA]
1. Go to `C:\Users\<YOUR USER ACCOUNT>\.mitmproxy` and git bash

![image](https://github.com/user-attachments/assets/956d9c68-647f-4b71-a266-5b716d1005f9)

2. Type this into git bash
```bash
hashed_name=`openssl x509 -inform PEM -subject_hash_old -in mitmproxy-ca-cert.cer | head -1` && cp mitmproxy-ca-cert.cer $hashed_name.0
```

3. It should be generating `[8-character alphanumeric].0`
![image](https://github.com/user-attachments/assets/7fa05b59-5362-45c9-a49e-dcdf568097dd)

4. Copy that `[8-character alphanumeric].0` into LDPlayer folder
# Steps 3 [Applied CA to Emulator]
1. Open cmd from LDPlayer folder that contains both `[8-character alphanumeric].0` and `adb.exe`
2. Run the following commands in sequence
```bash
adb root
adb remount
adb push [8-character alphanumeric].0 /system/etc/security/cacerts
adb shell chmod 664 /system/etc/security/cacerts/<8-character alphanumeric.0>
```
3. Restart the emulator

# Steps 4 [Setting up Mitmproxy] 
1. Open cmd and type `ipconfig`
2. Find "IPv4 Address" and copy the Address (You can close this cmd)

![image](https://github.com/user-attachments/assets/13228d54-ab6c-49d0-b961-22159bfaf07e)

3. Go to `SCHALE.GameServer/Scripts/redirect_server_mitmproxy`
4. Open the `redirect_server.py` and change
```py
SERVER_HOST = 'YOUR_SERVER_HERE'
```
to
```py
SERVER_HOST = '192.168.x.x' # Your IPv4 Address
```
Example:

![image](https://github.com/user-attachments/assets/4f2281b7-5420-4e65-aaec-9b841b27a629)

5. Save and now open cmd on this current directory
6. Run this command and provide your IPv4 Address after `--ignore`
```bash
mitmweb -m wireguard --no-http2 -s redirect_server.py --set termlog_verbosity=warn --ignore [Your IP address]
```
7. Wait and it will pop up an new tab contains mitmproxy with QR Code (used for later)

# Steps 5 [Setting up Wireguard]
1. Open Wireguard on your Emulator.
2. Scan the QR Code using Wireguard.
3. Name the wireguard connection whatever you want.
4. Enable the connection.

# Steps 6 [Setting up SCHALE.GameServer]
1. Go to `SCHALE.GameServer/SCHALE.GameServer`
2. Open cmd on this current folder
3. Type `dotnet run`
4. Wait until its listening to `0.0.0.0`

# Steps 7 [Setting Up SQL Server Management Studio (SSMS)]
1. Open SSMS
2. Check the `Trust Server Certificate`
3. Connect and close the SSMS

# Last Steps [Checking]
> [!CAUTION]  
> Make sure mitmproxy cmd, SCHALE.GameServer cmd, and Wireguard connection is on.
1. Play the game

# References
- [Install System CA Certificate on Android Emulator](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/)
