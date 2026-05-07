# Windows 11 Installation Reference
Windows 11 Pro installation setup references.

## Before Installation
- Do not use Legacy Boot into USB, otherwise it will not fulfill system requirements to install Windows 11. You need to boot via UEFI.
- Do not connect the ethernet cable. You cannot change Personalize settings after Windows connects to the activation server.
- Do not enable Legacy USB otherwise it cannot read/boot the Windows installer in the USB flash drive. Legacy USB should be set to UEFI-only in the BIOS.
- It is possible to get a `0xc0000098` error when trying to boot a USB flash drive containing the Windows installer. This may be due to "Legacy Boot into USB" or "Legacy USB" not being disabled in the BIOS.

    However, you may still get that error even after you disable it. In that case, re-create the Windows installer in the USB flash drive using the same method as before (Media Creation Tool). It may be due to the USB flash drive being larger than 32 GB? Not sure, but re-creating the USB flash drive Windows installer with Media Creation Tool did resolve the issue.

    For it to work, it looks like the USB flash drive needs to be separated into two partitions when larger than 32 GB, for example for a 256 GB USB flash drive:

    1\. 32 GB, containing the installation files

    2\. 199 GB, unallocated

    Afterwards you have to go to Disk Management to wipe the USB flash drive and recombine the partitions.

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
1. When Personalize settings are all set and ready: connect the ethernet cable to go online
1. Windows Update to download all the drivers
1. Multiple monitors should now be recognized
1. Activation has been checked and now blocks you from changing Personalize settings
1. However, for some reason, you are still allowed to change the multiple displays settings in Personalize > Taskbar
1. Remove Lock Screen Weather widget
    1. Open Command Prompt:
    ```
    winget uninstall "windows web experience pack"
    ```
    2. Go to Settings > Apps > Installed apps
    3. Uninstall `Windows Package Manager Source (winget) V2`

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
    - PowerShell
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

## Setup GPU Paravirtualization on a Hyper-V VM
1. Disable VM checkpoints, if enabled.

1. Copy GPU drivers from Host to VM.

    On Host, copy:
    ```
    C:\Windows\System32\DriverStore\FileRepository\nv_dispig.inf_amd64_5d5c294bb8d17217
    ```
    Paste to VM:
    ```
    C:\Windows\System32\HostDriverStore\FileRepository\nv_dispig.inf_amd64_5d5c294bb8d17217
    ```
    Create folders on VM as needed.

1. On Host, copy all files starting with `nv` in:
    ```
    C:\Windows\System32
    ```
    Paste to VM in:
    ```
    C:\Windows\System32
    ```

1. On VM: Shut down

1. On Host: Open PowerShell as administrator

1. On Host: Run script to add GPU partition to VM.

    Modify $vm to VM name in Hyper-V Manager:
    ```
    $vm = "t-VM-01"
    if (Get-VMGpuPartitionAdapter -VMName $vm -ErrorAction SilentlyContinue) {
        Remove-VMGpuPartitionAdapter -VMName $vm
    }
    Set-VM -GuestControlledCacheTypes $true -VMName $vm
    Set-VM -LowMemoryMappedIoSpace 1Gb -VMName $vm
    Set-VM -HighMemoryMappedIoSpace 32Gb -VMName $vm
    Add-VMGpuPartitionAdapter -VMName $vm
    ```
    Paste and run in PowerShell.

1. On Host: Start VM in Hyper-V Manager

1. On VM: Check Device Manager and Task Manager. Should have the graphics card shown.

    However, if the graphics card shown is the integrated GPU (for example: "AMD Radeon(TM) Graphics") then we must find and add the second GPU.

1. On VM: Shut down

1. On Host, run in PowerShell to get the GPU InstancePath:
    ```
    Get-VMHostPartitionableGpu | Select-Object Name
    ```
    Copy the second name.

1. On Host, run in PowerShell:

    Replace the `InstancePath` with the second name in the previous step.
    ```
    Remove-VMGpuPartitionAdapter -VMName $vm
    Add-VMGpuPartitionAdapter -VMName $vm -InstancePath "\\?\PCI#VEN_10DE&DEV_2684&SUBSYS_162C10DE&REV_A1#4&2255b23f&0&0008#{06c3a105-2487-4369-923f-275c6c06830d}\GPUPARAV"
    ```

1. On Host: Start VM in Hyper-V Manager

1. On VM: Check Device Manager and Task Manager. Should have the correct graphics card shown.

- Reference:
    - https://www.youtube.com/watch?v=XLLcc29EZ_8
    - https://www.youtube.com/watch?v=KDc8lbE2I6I
