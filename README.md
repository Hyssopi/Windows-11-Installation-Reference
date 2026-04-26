# Windows 11 Installation Reference
Windows 11 Pro installation setup references.

## Before Installation
- Do not use Legacy Boot into USB, otherwise it will not fulfill system requirements to install Windows 11. You need to boot via UEFI.
- Do not connect the ethernet cable. You cannot change Personalize settings after Windows connects to the activation server.
- Do not enable Legacy USB otherwise it cannot read/boot the Windows installer in the USB flash drive. Legacy USB should be set to UEFI-only in the BIOS.

## During Installation
- Bypass Microsoft account creation when it asks you to connect to the internet.
    - `Shift+F10` to open Command Prompt.
    ```
    start ms-cxh:localonly
    ```
    To be able to create a local account.
- Do not create a password while making the account. It will ask you for security questions if you make a password now. Make a password later, which will not require security questions.

## After Installation
1. While you have no internet: change your Personalize settings
1. Personalize > Taskbar > When using multiple displays... is greyed out because graphics drivers aren't installed yet so Windows can only recognize one monitor for now. This is okay, we can fix this later.
1. Remove Lock Screen Weather widget
    1. Open Command Prompt:
    ```
    winget uninstall "windows web experience pack"
    ```
    ii. Go to Settings > Apps > Installed apps
    iii. Uninstall `Windows Package Manager Source (winget) V2`
1. When Personalize settings are all set and ready: connect the ethernet cable to go online
1. Windows Update to download all the drivers
1. Multiple monitors should now be recognized
1. Activation has been checked and now blocks you from changing Personalize settings
1. However, for some reason, you are still allowed to change the multiple displays settings in Personalize > Taskbar

## For VMs
- Personalization > Colors
    `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize`
    ```
    AppsUseLightTheme = 0
    ColorPrevalence = 0
    EnableTransparency = 0
    SystemUsesLightTheme = 0
    ```

- Personalization > Colors > Show accent color on title bars and window borders
    `HKEY_CURRENT_USER\Software\Microsoft\Windows\DWM`
    ```
    ColorPrevalence = 1
    ```

- Personalization > Dynamic Lighting > Use Dynamic Lighting on my devices
    `HKEY_CURRENT_USER\Software\Microsoft\Lighting`
    ```
    AmbientLightingEnabled = 0
    ```

## Miscellaneous Notes
- Disk drive commands
    - Command Prompt
    ```
    wmic diskdrive get * /format:list
    ```
    - Powershell
    ```
    Get-PhysicalDisk | Select FriendlyName, HealthStatus, OperationalStatus
    Get-PhysicalDisk | Get-StorageReliabilityCounter | Format-List
    Get-PhysicalDisk | Get-StorageHistory | Format-List
    Get-PhysicalDisk | Get-StorageAdvancedProperty
    Get-PhysicalDisk | Format-List -Force
    Get-PhysicalDisk | Format-List * -force
    Get-PhysicalDisk | Get-StorageSubSystem  | Format-List
    ```
- `VBSCRIPT` may be required to install `Epic Games Launcher`, in: Settings > System > Optional features > View features
