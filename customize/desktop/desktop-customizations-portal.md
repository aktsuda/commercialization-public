---
title: Customizations for desktop devices
description: Customizations for desktop devices allow you to change the UI and other settings for a desktop image.
ms.author: alhopper
ms.date: 10/17/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---
# Customizations for desktop devices

You have the following options to customize your image. Depending on which options you’d like to use, you’ll employ the associated method or choice of methods to apply the customization.

| Feature                             | Unattend                             | Modification file                             |
|:------------------------------------|:-------------------------------------|:----------------------------------------------|
| Taskbar                             | subset                               | TaskbarLayoutModification.xml                 |
| Start layout                        | subset                               | LayoutModification.xml                        |
| Out of Box Experience (OOBE)        | subset                               | OOBE.xml                                      |
| Darkmode                            | yes                                  | Unattend.xml                                  |
| Get Help app                        | yes                                  | Unattend.xml                                  |
| Colors                              | yes                                  | Unattend.xml                                  |
<!--
| Pen & Windows Ink Workspace         | subset                               | InkWorkspaceModification.xml                  |
-->

>[!Note]
> All desktop customization options listed above are supported in Windows 10 S. To learn more, see [Windows 10 S Manufacturing overview](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-10-s-overview).

## In this section

These are some common ways to customize your desktop device. You will also find the technical reference for Unattend and WSIM.

| Topic                                                   | Description                                                                                         |
|:--------------------------------------------------------|:----------------------------------------------------------------------------------------------------|
| [Customize the taskbar](customize-the-taskbar.md)       | You can pin up to three additional apps to the taskbar by adding a taskbar layout modification file, for example, TaskbarLayoutModification.xml. You can specify different taskbar configurations based on SKU, device locale, or region.                                |
| [Customize the Start layout](customize-start-layout.md) | Learn how to customize the size of the start menu, and add your own tiles to it. |
| [Customize OOBE](customize-oobe.md)                     | When customers turn on their Windows PCs for the first time, they will see the Windows Out of Box Experience (OOBE). Customize OOBE to determine how much work customers must do to complete the OOBE screens before they can enjoy their PCs running Windows 10. |
| [Customize the Windows power slider](customize-power-slider.md) | The Windows Performance Power slider enables end customers to quickly and intelligently trade performance of their system for longer battery life. You can set the default slider mode for both AC and DC, and configure the power settings and PPM options that are engaged in each power slider mode. |
| [Set dark mode](set-dark-mode.md)                       | This personalization setting for end users allows them to express preference whether to see applications which support the setting in a dark or light mode. You can set the dark mode as the default for apps using Unattend.    |
| [Customize the Get Help app](customize-get-help-app.md) | The Get Help app empowers customers to self-help with troubleshooters, instant answers, Microsoft support articles, and more, before contacting assisted support. You can customize the Get Help app to surface your support app or support website.                      |
| [Customize a Specific Absorption Rate mapping table](customize-sar-mapping-table.md) | You can configure and store a Specific Absorption Rate (SAR) table for mobile broadband modems in the registry. When a mobile broadband modem is connected to the Windows device, Windows automatically uses the table to map the mobile country code (MCC) of the modem's registered mobile operator (MO) to its appropriate SAR back-off index, and configures the modem with it.                               |
| [Windows SIM Technical Reference](wsim/windows-system-image-manager-technical-reference.md) | Settings reference for Windows System Image Manager.            |
| [Unattended Windows Setup Reference](unattend/index.md) | Settings reference for Unattend.                                                              |

## Related topics

[OEM deployment of Windows 10 for desktop editions](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/oem-deployment-of-windows-10-for-desktop-editions)

[Planning a Windows 10 S deployment](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-10-s-planning)

[Deployment options](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-deployment-options)
