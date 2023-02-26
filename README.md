# WindowGamepad
XInput mouse interface for "Xbox one" controllers and "Xbox adaptive controller".
![alt text](https://user-images.githubusercontent.com/39764372/137580156-46b20afb-0c2b-439e-a815-88c16d4cb897.png)

Allows users to quickly operate the mouse and manipulate Windows on the active desktop, appropriately handling desktop switches between instances.

Features:

* Co-pilot with a second controller. Each gamepad can have its own settings and button mapping.
* The application is aware of the desktop that is running and the desktop it started on.  Only one instance per desktop may be run at one time.
* The application can be registered as as accessibility application, allowing it to run on the Winlogon desktop at boot, and at UAC (user account control) prompts.
* Detection of the Window under the cursor.  Allowing manipulation of Windows, including closing, moving, resizing, pushing (to the back of the z-order), pulling (to the top of the z-order), maximizing, restoring, and minimizing.
* XInput events can be configured to trigger simulated mouse and keyboard events.
* A wide range of speed and acceleration settings can be applied to the simulated events.
* XInput buttons can be remapped to different functions.
* Thumbsticks and Triggers can be calibrated to ignore dead zones.
* XInput event handling for metro/store apps can be disabled with the WTSDisconnectSession API.

To disconnect xinput to apps use WTSDisconnect.exe.  You can navigate app Windows with the mouse rather than the silly selector rectangle.

Code:


```
Const WTS_CURRENT_SESSION       As Long = -1
Const WTS_CURRENT_SERVER_HANDLE As Long = 0
Private Declare Function apiWTSDisconnectSession Lib "wtsapi32" Alias "WTSDisconnectSession" (ByVal hServer As Long, ByVal SessionID As Long, ByVal bWait As Boolean) As Boolean
Private Declare Function apiWTSOpenServer Lib "wtsapi32" Alias "WTSOpenServer" (ByVal pServerName As String) As Long
Private Declare Function apiWTSCloseServer Lib "wtsapi32" Alias "WTSCloseServer" (ByVal hServer As Long) As Boolean
Sub main()
   DissconnectSession
End Sub
Private Function DissconnectSession(Optional ByVal ServerName As String, Optional ByVal SessionID As Long) As Boolean
   On Error Resume Next
   Dim OpenedServer As Long
   OpenedServer = WTS_CURRENT_SERVER_HANDLE
   If ServerName <> "" Then OpenedServer = apiWTSOpenServer(ServerName)
   If SessionID = 0 Then SessionID = WTS_CURRENT_SESSION
   DissconnectSession = apiWTSDisconnectSession(OpenedServer, SessionID, False)
   Call apiWTSCloseServer(OpenedServer)
End Function
```

You can configure the driver to work on the Winlogon desktop at boot and at UAC prompts.

Registry editor code:
```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Accessibility\ATs\xinp]
"ApplicationName"=hex(2):57,00,69,00,6e,00,64,00,6f,00,77,00,47,00,61,00,6d,00,\
  65,00,70,00,61,00,64,00,00,00
"ATExe"="WindowGamepad.exe"
"CopySettingsToLockedDesktop"=dword:00000001
"Description"="Xinput Mouse"
"Profile"="<HCIModel><Accommodation type=\"severe dexterity\" /><Accommodation type=\"mild dexterity\" /></HCIModel>"
"SimpleProfile"="xinp"
"StartExe"=hex(2):45,00,3a,00,5c,00,57,00,69,00,6e,00,64,00,6f,00,77,00,53,00,\
  74,00,61,00,74,00,69,00,6f,00,6e,00,5c,00,57,00,69,00,6e,00,64,00,6f,00,77,\
  00,4c,00,61,00,75,00,6e,00,63,00,68,00,65,00,72,00,5c,00,57,00,69,00,6e,00,\
  64,00,6f,00,77,00,47,00,61,00,6d,00,65,00,70,00,61,00,64,00,2e,00,65,00,78,\
  00,65,00,00,00
"TerminateOnDesktopSwitch"=dword:00000000

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Accessibility\OOBE]
"Configuration"="xinp"
```

Be sure to edit the registry value for StartExe, after you import it into the registry, ie update the registry string rather than the hex.  If it fails to start you may have to apply the settings in Control Panel - Ease of Access Center -Change sign-in settings.  Check and apply the XInput entry.

![alt text](https://user-images.githubusercontent.com/39764372/221386742-ce3f41e8-7f5b-4bfb-a85b-f7ee7873ba53.png)

To uninstall from boot/uac:
```
Windows Registry Editor Version 5.00
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Accessibility\ATs\xinp]
```


