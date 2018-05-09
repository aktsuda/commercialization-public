---
title: Step 1 Install Controller and Studio on the test server
description: Step 1 Install Controller and Studio on the test server
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: d5f43ad7-4ebb-4523-b3d9-b324ef44632b
author: aahi
ms.author: aahi
ms.date: 10/15/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# Step 1: Install Controller and Studio on the test server

>[!WARNING]
>  The Windows Hardware Lab Kit should only be installed on machines that are dedicated solely for testing purposes. Do not install any HLK component on a machine that is outside of a dedicated testing environment.

In this step, you install Windows HLK software on the designated test server. The setup program installs the Windows HLK Controller and Studio, in addition to other resources.

The test server should be preinstalled with Windows Server 2012, Windows Server 2012 R2, or Windows Server 2016.

If you are upgrading your HLK environment to a later version, you must first uninstall the previous version of the HCK or HLK software from the test server and any connected test clients.

 

## <span id="To_install_Windows_HLK__do_the_following_"></span><span id="to_install_windows_hlk__do_the_following_"></span><span id="TO_INSTALL_WINDOWS_HLK__DO_THE_FOLLOWING_"></span>To install Windows HLK, do the following:


****
1.  Download the correct HLK version for the devices you wish to  test. See the [Windows Hardware Lab Kit](https://docs.microsoft.com/windows-hardware/test/hlk/windows-hardware-lab-kit) topic for information about selecting the correct HLK.

    If you are downloading directly onto your server, you must disable the IE Enhanced Security Configuration (IE ESC).

2.  When prompted, select **Run**.

    >[!WARNING]
    >  Don't select the **Save** option. The **Save** option only downloads the setup file and not the complete kit.

3.  When the **Specify Location** screen appears, choose the appropriate option:

    1.  Install option - choose **Install the Windows Hardware Lab Kit to this computer**, and then choose **Install** .

    2.  Download option - choose **Download the Windows Hardware Lab Kit for installation on a separate computer**, and then choose **Next**.

4.  Select the **Windows Hardware Lab Kit -- Controller + Studio** option.

    If you are installing directly, you must open a port on your server. Choose **Yes**, to allow the installation to open a port.

5.  When the **Join the Customer Experience Improvement Program (CEIP)** screen appears, choose **Yes** or **No**, and then choose **Next**. If your network isn't connected to the Internet, choose **No**.

     

6.  Review the License Agreement, and then choose **Accept** to proceed.

    If you selected the download option, copy your download to your test server. Run HLKSetup.exe and repeat the installation instructions from **Step 3** for installing to this computer.

    Installation takes about 45 minutes.

    >[!NOTE]
    >  If Microsoft .NET Framework 4.5 isn't already installed on the computer, follow the prompts to install it. After the computer restarts, you must repeat the installation instructions from **Step 1** for installing to this computer.

## <span id="related_topics"></span>Related topics


[HLK Studio](../user/hlk-studio.md)

[Install a remote HLK Studio](../user/install-a-remote-hlk-studio.md)

[Install standalone HLK Studio](../user/install-standalone-hlk-studio.md)

 

 







