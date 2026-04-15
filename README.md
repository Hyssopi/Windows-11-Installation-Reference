# Windows 11 Installation Reference
Windows 11 Pro post-installation setup references.

## Before Installation
- Do not use Legacy Boot into USB, otherwise it will not fulfill system requirements to install Windows 11. You need to boot via UEFI.
- Do not connect to ethernet. You cannot change Personalize settings after Windows connects to the activation server.

## During Installation
- Bypass Microsoft account creation when it asks you to connect to the internet.
`Shift+F10` to open command prompt.
```
start ms-cxh:localonly
```
To be able to create a local account.

## After Installation
1. While you have no internet: change your Personalize settings
2. Personalize > Taskbar > When using multiple displays... is greyed out because graphics drivers aren't installed yet so Windows can only recognize one monitor for now. This is okay, we can fix this later.

x (No longer works) To get rid of: Lock Screen Weather widget,
a. Go to `regedit`
b. Navigate to: `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lock Screen`
c. Select it and create DWORD: `LockScreenWidgetsEnabled` and set to 0
d. Restart PC

- (Optimal, might work) To get rid of: Lock Screen Weather widget,
a. With ethernet still disconnected, keep restarting PC
b. Settings > Personalization > Lock Screen > Lock screen status: None
c. For some reason, the Lock screen status keeps changing to "Weather" after a PC restart. Keep changing it back to "None" and then keep restarting PC until it stops changing
d. If not, then connect the ethernet cable to the PC but without internet (maybe disconnect the ethernet cable between the modem and router?)

- (Last resort) To get rid of: Lock Screen Weather widget,
a. Open cmd as administrator:
```
winget uninstall "windows web experience pack"
```

3. When Personalize settings are all set and ready: connect the ethernet cable to go online
4. Windows Update to download all the drivers
5. Multiple monitors should now be recognized
6. Activation has been checked and now blocks you from changing Personalize settings
7. However, for some reason, you are still allowed to change the multiple displays settings in Personalize > Taskbar


For VMs:
- Personalization > Colors
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
AppsUseLightTheme = 0
ColorPrevalence = 0
EnableTransparency = 0
SystemUsesLightTheme = 0

- Personalization > Colors > Show accent color on title bars and window borders
HKEY_CURRENT_USER\Software\Microsoft\Windows\DWM
ColorPrevalence = 1

- Personalization > Dynamic Lighting > Use Dynamic Lighting on my devices
HKEY_CURRENT_USER\Software\Microsoft\Lighting
AmbientLightingEnabled = 0
