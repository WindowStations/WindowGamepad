# WindowGamepad
XInput mouse interface for "Xbox one" controllers and "Xbox adaptive controller".
https://youtu.be/t3ACJEYMcIM
![alt text](https://user-images.githubusercontent.com/39764372/137580156-46b20afb-0c2b-439e-a815-88c16d4cb897.png)

Allows users to quickly operate the mouse and manipulate Windows on the active desktop, appropriately handling desktop switches between instances.

Features:

* The application is aware of the desktop that is running and the desktop it started on.  Only one instance per desktop may be run at one time.
* Detection of the Window under the cursor.  Allowing manipulation of Windows, including closing, moving, resizing, pushing (to the back of the z-order), pulling (to the top of the z-order), maximizing, restoring, and minimizing.
* XInput events can be configured to trigger simulated mouse and keyboard events.
* A wide range of speed and acceleration settings can be applied to the simulated events.
* XInput buttons can be remapped to different functions.
* Thumbsticks and Triggers can be calibrated to ignore dead zones.
* XInput event handling for metro/store apps, "App support" can be disabled with the WTSDisconnectSession API.

To disconnect xinput to apps use WTSDisconnect.exe.  You can navigate app Windows with normal mouse simulation rather than the silly selector rectangle.

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




