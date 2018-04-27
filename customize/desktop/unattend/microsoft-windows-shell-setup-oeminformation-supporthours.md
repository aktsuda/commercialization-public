---
title: SupportHours
description: SupportHours
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 5fd623a0-6398-4d17-9203-b38568d7a502
ms.mktglfcycl: deploy
ms.sitesec: msdn
ms.author: alhopper
ms.date: 10/17/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---
# SupportHours

> [!Important]
> SupportHours is deprecated in Windows 10, version 1709.

`SupportHours` specifies the hours that support is available for the OEM.

## Values

| Value                   | Description                                                                           |
|:------------------------|:--------------------------------------------------------------------------------------|
| *hours*                 | **Deprecated in Windows 10, version 1709**. Specifies the hours that support is available for the OEM. *Hours* is a string with a maximum length of 256 characters.                                                                          |

This string type supports empty elements.

## Valid Passes

auditUser

generalize

offlineServicing

oobeSystem

specialize

## Parent Hierarchy

[Microsoft-Windows-Shell-Setup](microsoft-windows-shell-setup.md) | [OEMInformation](microsoft-windows-shell-setup-oeminformation.md) | **SupportHours**

## Applies To

For the list of the supported Windows editions and architectures that this component supports, see [Microsoft-Windows-Shell-Setup](microsoft-windows-shell-setup.md).

## XML Example

The following XML shows how to set OEM information.

```xml
<OEMInformation>
   <SupportProvider>Contoso Support</SupportProvider>
   <SupportAppURL>contoso-contact-support</SupportAppURL>
   <SupportURL>http://www.fabrikam.com/support</SupportURL>
</OEMInformation>
```

## Related topics

[OEMInformation](microsoft-windows-shell-setup-oeminformation.md)