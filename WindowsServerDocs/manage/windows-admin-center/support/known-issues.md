---
title: Windows Admin Center Known Issues
description: Windows Admin Center Known Issues (Project Honolulu)
ms.technology: manage
ms.topic: article
author: jwwool
ms.author: jeffrew
ms.localizationpriority: medium
ms.prod: windows-server-threshold
ms.date: 06/07/2019
---
# Windows Admin Center Known Issues

> Applies to: Windows Admin Center, Windows Admin Center Preview

If you encounter an issue not described on this page, please [let us know](http://aka.ms/WACfeedback).

## Lenovo XClarity Integrator

The previously disclosed incompatibility issue of the Lenovo XClarity Integrator extension and Windows Admin Center version 1904 is now resolved with Windows Admin Center version 1904.1. We highly recommend that you update to the latest supported version of Windows Admin Center.

- Lenovo XClarity Integrator extension version 1.1 is fully compatible with Windows Admin Center 1904.1. We highly recommend that you update to the latest version of Windows Admin Center and the Lenovo extension.
- For any reason, if you need to continue using Windows Admin Center 1809.5 for the time being, you may use XClarity Integrator 1.0.4 which will also be available in the Windows Admin Center Extension Feed until Windows Admin Center 1809.5 is no longer supported based on our [support policy](../support/index.md).

## Installer

- When installing Windows Admin Center using your own certificate, be mindful that if you copy the thumbprint from the certificate manager MMC tool, [it will contain an invalid character at the beginning.](https://support.microsoft.com/help/2023835/certificate-thumbprint-displayed-in-mmc-certificate-snap-in-has-extra) As a workaround, type the first character of the thumbprint, and copy/paste the rest.

- Using port below 1024 is not supported. In service mode, you may optionally configure port 80 to redirect to your specified port.

- If the Windows Update service (wuauserv) is stopped and disabled, the installer will fail. [19100629]

### Upgrade

- When upgrading Windows Admin Center in service mode from a previous version, if you use msiexec in quiet mode, you may encounter an issue where the inbound firewall rule for Windows Admin Center port is deleted.
  - To recreate the rule, perform the following command from an elevated PowerShell console, replacing \<port> with the port configured for Windows Admin Center (default 443.)

    ```powershell
    New-NetFirewallRule -DisplayName "SmeInboundOpenException" -Description "Windows Admin Center inbound port exception" -LocalPort <port> -RemoteAddress Any -Protocol TCP
    ```

## General

- If you have Windows Admin Center installed as a gateway on **Windows Server 2016** under heavy use, the service may crash with an error in the event log that contains ```Faulting application name: sme.exe``` and ```Faulting module name: WsmSvc.dll```. This is due to a bug that has been fixed in Windows Server 2019. The patch for Windows Server 2016 was included the February 2019 cumulative update, [KB4480977](https://www.catalog.update.microsoft.com/Search.aspx?q=4480977).

- If you have Windows Admin Center installed as a gateway and your connection list appears to be corrupted, perform the following steps:

   > [!WARNING]
   >This will delete the connection list and settings for all Windows Admin Center users on the gateway.

  1. Uninstall Windows Admin Center
  2. Delete the **Server Management Experience** folder under **C:\Windows\ServiceProfiles\NetworkService\AppData\Roaming\Microsoft**
  3. Reinstall Windows Admin Center

- If you leave the tool open and idle for a long period of time, you may get several **Error: The runspace state is not valid for this operation** errors. If this occurs, refresh your browser. If you encounter this, [send us feedback](http://aka.ms/WACfeedback).

- You may encounter a **500 Error** when refreshing pages with very long URLs. [12443710]

- In some tools, your browser's spell checker may mark certain field values as misspelled. [12425477]

- In some tools, command buttons may not reflect state changes immediately after being clicked, and the tool UI may not automatically reflect changes to certain properties. You can click **Refresh** to retrieve the latest state from the target server. [11445790]

- Tag filtering on connection list - if you select connections using the multiselect checkboxes, then filter your connection list by tags, the original selection persists so any action you select will apply to all the previously selected machines. [18099259]

- There may be minor variance between version numbers of OSS running in Windows Admin Center modules, and what is listed within the 3rd Party Software Notice.

### Extension Manager

- When you update Windows Admin Center, you must reinstall your extensions.
- If you add an extension feed that is inaccessible, there is no warning. [14412861]

## Browser Specific Issues

### Microsoft Edge

- In some cases, you may encounter long loading times when using Microsoft Edge to access a Windows Admin Center gateway over the Internet. This can occur on Azure VMs where the Windows Admin Center gateway is using a self-signed certificate. [13819912]

- When using Azure Active Directory as your identity provider and Windows Admin Center is configured with a self-signed or otherwise untrusted certificate, you cannot complete the AAD authentication in Microsoft Edge.  [15968377]

- If you have Windows Admin Center deployed as a service and you are using Microsoft Edge as your browser, connecting your gateway to Azure may fail after spawning a new browser window. Try to work around this issue by adding https://login.microsoftonline.com, https://login.live.com, and the URL of your gateway as trusted sites and allowed sites for pop-up blocker settings on your client side browser. For more guidance on fixing this in the [troubleshooting guide](troubleshooting.md#azure-features-dont-work-properly-in-edge). [17990376]

- If you have Windows Admin Center installed in desktop mode, the browser tab in Microsoft Edge won't display the favicon. [17665801]

### Google Chrome

- Prior to version 70 (released late October, 2018) Chrome had a [bug](https://bugs.chromium.org/p/chromium/issues/detail?id=423609) regarding the websockets protocol and NTLM authentication. This effects the following tools: Events, PowerShell, Remote Desktop.

- Chrome may pop-up multiple credential prompts, especially during the add connection experience in a **workgroup** (non-domain) environment.

- If you have Windows Admin Center deployed as a service, popups from the gateway URL need to be enabled for any Azure integration functionality to work. These services include Azure Network Adapter, Azure Update Management and Azure Site Recovery.

### Mozilla Firefox

Windows Admin Center is not tested with Mozilla Firefox, but most functionality should work.

- Windows 10 Installation: Mozilla Firefox has it's own certificate store, so you must import the ```Windows Admin Center Client``` certificate into Firefox to use Windows Admin Center on Windows 10.

## WebSocket compatibility when using a proxy service

Remote Desktop, PowerShell, and Events modules in Windows Admin Center utilize the WebSocket protocol, which is often not supported when using a proxy service. Websocket support in Azure AD Application Proxy compatibility is in [preview](https://blogs.technet.microsoft.com/applicationproxyblog/2018/03/28/limited-websocket-support-now-in-public-preview/) and looking for feedback on compatibility.

## Support for Windows Server versions before 2016 (2012 R2, 2012, 2008 R2)

> [!NOTE]
> Windows Admin Center requires PowerShell features that are not included in Windows Server 2012 R2, 2012, or 2008 R2. If you will manage Windows Server these with Windows Admin Center, you will need to install WMF version 5.1 or higher on those servers.

Type `$PSVersiontable` in PowerShell to verify that WMF is installed,
and that the version is 5.1 or higher.

If it is not installed, you can [download and install WMF 5.1](https://www.microsoft.com/en-us/download/details.aspx?id=54616).

## Role Based Access Control (RBAC)

- RBAC deployment will not succeed on machines that are configured to use Windows Defender Application Control (WDAC, formerly known as Code Integrity.) [16568455]

- To use RBAC in a cluster, you must deploy the configuration to each member node individually.

- When RBAC is deployed, you may get unauthorized errors that are incorrectly attributed to the RBAC configuration. [16369238]

## Server Manager solution

### Server Settings

- If you modify a setting, then try to navigate away without saving, the page will warn you about the unsaved changes, but continue to navigate away. You may end up in a state where the settings tab that is selected does not match the content of the page. [19905798] [19905787]

### Certificates

- Cannot import .PFX Encrypted Certificate in to current user store. [11818622]

### Devices

- When navigating through the table with your keyboard, the selection may jump to the top of the table group. [16646059]

### Events

- Events is effected by [websocket compatibility when using a proxy service.](#websocket-compatibility-when-using-a-proxy-service)

- You may get an error that references “packet size” when exporting large log files. [16630279]

  - To resolve this, use the following command in an elevated command prompt on the gateway machine: ```winrm set winrm/config @{MaxEnvelopeSizekb="8192"}```

### Files

- Uploading or downloading large files not yet supported. (\~100mb limit) [12524234]

### PowerShell

- PowerShell is effected by [websocket compatibility when using a proxy service](#websocket-compatibility-when-using-a-proxy-service)

- Pasting with a single right-click as in the desktop PowerShell console does not work. Instead you will get the browser's context menu, where you can select paste. Ctrl-V works as well.

- Ctrl-C to copy does not work, it will always send the Ctrl-C break command to the console. Copy from the right-click context menu works.

- When you make the Windows Admin Center window smaller, the terminal content will reflow, but when you make it larger again, the content may not return to it's previous state. If things get jumbled, you can try Clear-Host, or disconnect and reconnect using the button above the terminal.

### Registry Editor

- Search functionality not implemented. [13820009]

### Remote Desktop

- The Remote Desktop tool may fail to connect when managing Windows Server 2012. [20258278]

- When using the Remote Desktop to connect to a machine that is not Domain joined, you must enter your account in the ```MACHINENAME\USERNAME``` format.

- Some configurations can block Windows Admin Center's remote desktop client with group policy. If you encounter this, enable ```Allow users to connect remotely by using Remote Desktop Services``` under ```Computer Configuration/Policies/Administrative Templates/Windows Components/Remote Desktop Services/Remote Desktop Session Host/Connections```

- Remote Desktop is effected by [websocket compatibility.](#websocket-compatibility-when-using-a-proxy-service)

- The Remote Desktop tool does not currently support any text, image, or file copy/paste between the local desktop and the remote session.

- To do any copy/paste within the remote session, you can copy as normal (right click + copy or Ctrl+C), but paste requires right click + paste (Ctrl+V does NOT work)

- You cannot send the following key commands to the remote session
  - Alt+Tab
  - Function keys
  - Windows Key
  - PrtScn

- Remote App – After enabling the Remote App tool from Remote Desktop settings, the tool may not appear in the tool list when managing a Server with Desktop Experience. [18906904]

### Roles and Features

- When selecting roles or features with unavailable sources for install, they are skipped. [12946914]

- If you choose not to automatically reboot after role installation, we won't ask again. [13098852]

- If you do choose to automatically reboot, the reboot will occur before the status gets updated to 100%. [13098852]

### Storage

- Fetching Quota information may fail without an error notification (there will still be an error in the browser's console) [18962274]

- Down-level: DVD/CD/Floppy drives do not appear as volumes on down-level.

- Down-level: Some properties in Volumes and Disks are not available down-level so they appear unknown or blank in details panel.

- Down-level: When creating a new volume, ReFS only supports an allocation unit size of 64K on Windows 2012 and 2012 R2 machines. If a ReFS volume is created with a smaller allocation unit size on down-level targets, file system formatting will fail. The new volume will not be usable. The resolution is to delete the volume and use 64K allocation unit size.

### Updates

- After installing updates, install status may be cached and require a browser refresh.

- You may encounter the error: "Keyset does not exist" when attempting to set up Azure Update management. In this case, please try the following remediation steps on the managed node -
    1. Stop ‘Cryptographic Services' service.
    2. Change folder options to show hidden files (if required).
    3. Got to “%allusersprofile%\Microsoft\Crypto\RSA\S-1-5-18” folder and delete all its contents.
    4. Restart ‘Cryptographic Services' service.
    5. Repeat setting up Update Management with Windows Admin Center

### Virtual Machines

- When managing the virtual machines on a Windows Server 2012 host, the in-browser VM connect tool will fail to connect to the VM. Downloading the .rdp file to connect to the VM should still work. [20258278]

- Azure Site Recovery – If ASR is setup on the host outside of WAC, you will be unable to protect a VM from within WAC [18972276]

- Advanced features available in Hyper-V Manager such as Virtual SAN Manager, Move VM, Export VM, VM Replication are currently not supported.

### Virtual Switches

- Switch Embedded Teaming (SET): When adding NICs to a team, they must be on the same subnet.

## Computer Management Solution

The Computer Management solution contains a subset of the tools from the Server Manager solution, so the same known issues apply, as well as the following Computer Management solution specific issues:

- If you use a Microsoft Account ([MSA](https://account.microsoft.com/account/)) or if you use Azure Active Directory (AAD) to log on to you Windows 10 machine, you must specify "manage-as" credentials to manage your local machine [16568455]

- When you try to manage the localhost, you will be prompted to elevate the gateway process. If you click **no** in the User Account Control popup that follows, Windows Admin Center won't be able to display it again. In this case, exit the gateway process by right-clicking the Windows Admin Center icon in the system tray and choosing exit, then relaunch Windows Admin Center from the Start Menu.

- Windows 10 does not have WinRM/PowerShell remoting on by default
  
  - To enable management of the Windows 10 Client, you must issue the command ```Enable-PSRemoting``` from an elevated PowerShell prompt.

  - You may also need to update your firewall to allow connections from outside the local subnet with ```Set-NetFirewallRule -Name WINRM-HTTP-In-TCP -RemoteAddress Any```. For more restrictive networks scenarios, please refer to [this documentation](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-5.1).

## Failover Cluster Manager solution

- When managing a cluster, (either Hyper-Converged or traditional?) you may encounter a **shell was not found** error. If this happens either reload your browser, or navigate away to another tool and back. [13882442]

- An issue can occur when managing a down-level (Windows Server 2012 or 2012 R2) cluster that hasn't been configured completely. The fix for this issue is to ensure that the Windows feature **RSAT-Clustering-PowerShell** has been installed and enabled on **each member node** of the cluster. To do this with PowerShell, enter the command `Install-WindowsFeature -Name RSAT-Windows-PowerShell` on all the cluster nodes. [12524664]

- The Cluster may need to be added with the entire FQDN to be discovered correctly.

- When connecting to a cluster using Windows Admin Center installed as a gateway, and providing explicit username/password to authenticate, you must select **Use these credentials for all connections** so that the credentials are available to query the member nodes.

## Hyper-Converged Cluster Manager solution

- Some commands such as **Drives - Update firmware**, **Servers - Remove** and **Volumes - Open** are disabled and currently not supported.