---
title: Change history for customization docs
description: Provides a record of the major changes that were made in the Customize section of the Windows 10 partner documentation.
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 07E710A3-3368-476E-9EC1-12AA67B52498
ms.author: alhopper
ms.date: 1/25/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---
# Change history for customization docs

The following tables record the major changes that were made in the **Customize** section of the Windows 10 partner documentation since Windows 10, version 1607 was released.

## January 2018

| Topic                                          | Description                                                                                        |
|:-----------------------------------------------|:---------------------------------------------------------------------------------------------------|
| [OEM registration pages](desktop/oem-registration-pages.md) | Updated. New screenshots and XML sample, clarifications on how the Oobe.xml elements relate to registration page fields, clarifications on collecting encrypted customer data.                                                             |

## December 2017

| Topic                                          | Description                                                                                        |
|:-----------------------------------------------|:---------------------------------------------------------------------------------------------------|
| [Windows updates during OOBE](desktop/windows-updates-during-oobe.md) | New. Describes how both critical and non-critical Windows and driver updates are downloaded during a user's Out of Box Experience. |
| [Exclusive apps](preinstall/exclusive-apps.md) | New. Guidance on how OEMs can work with software developers to target OEM devices for apps to appear exclusively on, based on the OEM IDs set in the registry.    |
| [Hibernate Once Resume Many](enterprise/hibernate-once-resume-many-horm-.md)  | Updated to note that HORM (a feature of Unified Write Filter) can now be used on UEFI devices starting in Windows 10, version 1709.    |

## November 2017

| Topic                                          | Description                                                                                        |
|:-----------------------------------------------|:---------------------------------------------------------------------------------------------------|
| [Customize OOBE](desktop/customize-oobe.md)    | Updated with recommendation for setting the default volume level during OOBE.                      |
| [Connect users to the network during OOBE](desktop/connect-to-network.md) | Updated with clarifications on how Cellular and Wi-Fi connections are used during OOBE, and the types of updates that download during OOBE.   |
| [Keyboard Filter](enterprise/keyboardfilter.md)      | Updated to note that Keyboard Filter is not supported in a remote desktop session.           |
| [Unattend Setting: FirewallGroups](desktop/unattend/networking-mpssvc-svcfirewallgroups.md)   | Updated with guidance on how to obtain the correct FirewallGroup-Group value using PowerShell.    |

## October 17, 2017

| Topic                                      | Description                                                                                        |
|:-------------------------------------------|:---------------------------------------------------------------------------------------------------|
| [Customize the Get Help app](desktop/customize-get-help-app.md)    | New. Learn how to add your support app or website to Window's self-service Get Help app, to provide customers with an easy-to-find way to reach out. |
| [Customize the Windows performance power slider](desktop/customize-power-slider.md) | New. The Windows performance power slider enables customers to trade performance of their system for longer battery life. You can configure the default slider mode, and the power settings engaged behind the scenes. |
| [Customize a SAR mapping table](desktop/customize-sar-mapping-table.md) | New. Configure and store a Specific Absorption Rate (SAR) table for mobile broadband modems in the registry. |
| [Customize the Start layout](desktop/customize-start-layout.md) | New. Customize the size of the start layout, and add your own tiles to it. |
| [Create a Kiosk Experience](enterprise/create-a-kiosk-image.md) | Updated with guidance on providing a multi-app kiosk experience. This functionality is new in Windows 10 version 1709. |
| [Adaptive hibernate](power-settings/adaptive-hibernate.md) | Updated. In Windows 10 version 1709, user usage prediction no longer triggers Hibernate. Also updated to include default values of hibernate triggers. |
| [Predefined key combinations](enterprise/predefined-key-combinations.md) | Updated with keyboard shortcut changes introduced in Windows 10 version 1709. |
| [OOBE.xml](desktop/oobexml.md) | Updated. In Windows 10 version 1709, `timezone` is now available to set in OOBE.xml |
| [Changed answer file settings for Windows 10 version 1709](desktop/unattend/changed-answer-file-settings-for-windows-10-build-1709.md) | Learn about the Unattend settings that have been added, deprecated, and removed in the most recent version of Windows. |

## September 27, 2017

| Topic                                      | Description                                                                                        |
|:-------------------------------------------|:---------------------------------------------------------------------------------------------------|
| [Customize the Out of Box Experience](desktop/customize-oobe.md) | New. Guidance on how to customize elements of the Out of Box Experience (OOBE), such as setting default values, adding registration screens, and providing support for unpaired mice and keyboards. |

## March 24, 2017

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>New or updated topic</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="even">
<td><p>[Microsoft-Windows-TPM-Tasks-ClearTpm](desktop/unattend/microsoft-windows-tpm-tasks-cleartpm.md)</p></td>
<td><p>New. Specifies whether to clear the Trusted Platform Module (TPM) during Windows setup. Clearing the TPM prevents an issue in earlier versions that kept some Windows features from working if the TPM was incorrectly set.</p></td>
</tr>
<tr class="odd">
<td><p>[Microsoft-Windows-TwinUI-Hide](desktop/unattend/microsoft-windows-twinui-hide.md)</p></td>
<td><p>New. Specifies whether to hide the link to an advanced settings app in the Pen and Windows Ink Settings page.</p></td>
</tr>
<tr class="even">
<td><p>[Preinstallable apps for Windows 10 Mobile](preinstall/preinstallable-apps-for-window-10-for-phones.md)</p></td>
<td><p>Updated. Uses imggen.cmd to build the mobile image because ICD no longer includes support for image creation. </p></td>
</tr>
</tbody>
</table>

## March 15, 2017

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>New or updated topic</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>[Customize the Country and Operator Settings Asset](desktop/customize-cosa.md)</p></td>
<td><p>New. When a SIM is inserted in a COSA-enabled Windows-based device, the provisioning framework attempts to establish a cellular connection by searching for the matching profile and APN in COSA.</p></td>
</tr>

</tbody>
</table>

## October 6, 2016


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>New or updated topic</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>[Customize the taskbar](desktop/customize-the-taskbar.md)</p></td>
<td><p>New. Starting in Windows 10, version 1607, you can pin up to three additional apps to the taskbar by adding a taskbar layout modification file, for example, TaskbarLayoutModification.xml. You can specify different taskbar configurations based on SKU, device locale, or region.</p></td>
</tr>
<tr class="even">
<td><p>[Set dark mode](desktop/set-dark-mode.md)</p></td>
<td><p>New. Windows 10, build 1607 exposes a new personalization setting for end users, allowing them to express preference whether to see applications which support the setting in a dark or light mode.</p></td>
</tr>
</tbody>
</table>
