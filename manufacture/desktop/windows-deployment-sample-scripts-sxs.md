---
author: themar
Description: 'The following scripts are used in the lab. It may be helpful to create these all at once, or to download the samples from the web.'
ms.assetid: 621503da-e74f-4eef-8315-72c8be67747a
MSHAttr: 'PreferredLib:/library/windows/hardware'
title: Sample scripts
ms.author: themar
ms.date: 05/02/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# Sample scripts

[Download the sample scripts used in this lab](http://download.microsoft.com/download/3/F/2/3F2646EF-D589-498C-9F07-DE5549BE018E/USB-B.zip) 

Copy these scripts to the root of your storage USB drive.  Refer to this page to understand what's in the scripts.

**To keep moving with the labs, it's OK to skip the rest of this topic**. Come back later when you want to understand what's going on or to make changes: we designed these scripts so that you can modify them to fit your needs.

> [!div class="nextstepaction"]
> [Install Windows PE](install-windows-pe-sxs.md)

## <span id="Image_deployment_scripts"></span><span id="image_deployment_scripts"></span><span id="IMAGE_DEPLOYMENT_SCRIPTS"></span>Image deployment scripts

The following scripts set up Windows devices by using either a WIM or an FFU image file, and then give the option to configure push-button reset features.

The following files make up the deployment scripts:
- ApplyImage.bat
- ApplyRecovery.bat
- CreatePartitions-BIOS.txt
- CreatePartitions-BIOS-FFU.txt
- CreatePartitions-UEFI.txt
- CreatePartitions-UEFI-FFU.txt
- HideRecoveryPartitions-BIOS.txt
- HideRecoveryPartitions-UEFI.txt
- CreateRecoveryPartitions-BIOS.txt
- CreateRecoveryPartitions-UEFI.txt

### ApplyImage.bat

Use this script applies a Windows image to a new device.

**Note:** If you copy and paste the contents below to create a .bat file, you may get an error when detecting firmware. For firmware detection to succeed, ensure that the lines that begin `for /f "tokens=2* delims=	 " %%A` has a tab followed by a space in between `delims=` and `" %%A`.

```
@echo Apply-Image.bat
@echo     Run from the reference device in the WinPE environment.
@echo.
@echo     This script erases the primary hard drive and applies a new image.
@echo.
@echo	  Make sure that this script is run from the folder that contains the
@echo	  supporting scripts
@echo.
@echo UPDATE (November 2017)
@echo * Added support for FFU deployments.	
@echo.
@echo UPDATE (JULY 2016):
@echo * This script stops just after applying the image.
@echo   This gives you an opportunity to add siloed provisioning packages (SPPs)
@echo   so that you can include them in your recovery tools.
@echo.
@echo   After the script is complete, use apply-recovery.bat to finish
@echo   setting up the recovery tools.
@echo.
@echo * This script creates a now includes support for the /EA variables for quicker
@echo   image capture and recovery.
@echo.
@echo * This script now includes support for the /EA variables for quicker
@echo   image capture and recovery.
@echo.
@echo * This script now checks to see if you're booted into Windows PE.
@echo.
@if not exist X:\Windows\System32 echo ERROR: This script is built to run in Windows PE.
@if not exist X:\Windows\System32 goto END
@if %1.==. echo ERROR: To run this script, add a path to a Windows image file.
@if %1.==. echo Example: ApplyImage D:\WindowsWithFrench.wim
@if %1.==. goto END
@echo *********************************************************************
@echo  == Setting high-performance power scheme to speed deployment ==
@call powercfg /s 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c
@echo *********************************************************************
@echo Checking to see the type of image being deployed
@if "%~x1" == ".wim" (GOTO WIM)
@if "%~x1" == ".ffu" (GOTO FFU)
@echo *********************************************************************
@if not "%~x1" == ".ffu". if not "%~x1" == ".wim" echo Please use this script with a WIM or FFU image.
@if not "%~x1" == ".ffu". if not "%~x1" == ".wim" GOTO END
:WIM
@echo Starting WIM Deployment
@echo *********************************************************************
@echo Checking to see if the PC is booted in BIOS or UEFI mode.
wpeutil UpdateBootInfo
for /f "tokens=2* delims=	 " %%A in ('reg query HKLM\System\CurrentControlSet\Control /v PEFirmwareType') DO SET Firmware=%%B
@echo            Note: delims is a TAB followed by a space.
@if x%Firmware%==x echo ERROR: Can't figure out which firmware we're on.
@if x%Firmware%==x echo        Common fix: In the command above:
@if x%Firmware%==x echo             for /f "tokens=2* delims=	 "
@if x%Firmware%==x echo        ...replace the spaces with a TAB character followed by a space.
@if x%Firmware%==x goto END
@if %Firmware%==0x1 echo The PC is booted in BIOS mode. 
@if %Firmware%==0x2 echo The PC is booted in UEFI mode. 
@echo *********************************************************************
@echo Do you want to create a Recovery partition?
@echo    (If you're going to be working with FFUs, and need 
@echo     to expand the Windows partition after applying the FFU, type N). 
@SET /P RECOVERY=(Y or N):
@if %RECOVERY%.==y. set RECOVERY=Y
@echo Formatting the primary disk...
@if %Firmware%==0x1 echo    ...using BIOS (MBR) format and partitions.
@if %Firmware%==0x2 echo    ...using UEFI (GPT) format and partitions. 
@echo CAUTION: All the data on the disk will be DELETED.
@SET /P READY=Erase all data and continue? (Y or N):
@if %READY%.==y. set READY=Y
@if not %READY%.==Y. goto END
@if %Firmware%.==0x1. if %RECOVERY%.==Y. diskpart /s CreatePartitions-BIOS.txt
@if %Firmware%.==0x1. if not %RECOVERY%.==Y. diskpart /s CreatePartitions-BIOS-FFU.txt
@if %Firmware%.==0x2. if %RECOVERY%.==Y. diskpart /s CreatePartitions-UEFI.txt
@if %Firmware%.==0x2. if not %RECOVERY%.==Y. diskpart /s CreatePartitions-UEFI-FFU.txt
@echo *********************************************************************
@echo  == Apply the image to the Windows partition ==
@SET /P COMPACTOS=Deploy as Compact OS? (Y or N):
@if %COMPACTOS%.==y. set COMPACTOS=Y
@echo Does this image include Extended Attributes?
@echo    (If you're not sure, type N).
@SET /P EA=(Y or N):
@if %EA%.==y. set EA=Y
@if %COMPACTOS%.==Y.     if %EA%.==Y.     dism /Apply-Image /ImageFile:%1 /Index:1 /ApplyDir:W:\ /Compact /EA
@if not %COMPACTOS%.==Y. if %EA%.==Y.     dism /Apply-Image /ImageFile:%1 /Index:1 /ApplyDir:W:\ /EA
@if %COMPACTOS%.==Y.     if not %EA%.==Y. dism /Apply-Image /ImageFile:%1 /Index:1 /ApplyDir:W:\ /Compact
@if not %COMPACTOS%.==Y. if not %EA%.==Y. dism /Apply-Image /ImageFile:%1 /Index:1 /ApplyDir:W:\
@echo *********************************************************************
@echo == Copy boot files to the System partition ==
W:\Windows\System32\bcdboot W:\Windows /s S:
@echo *********************************************************************
@echo   Next steps:
@echo   * Add Windows Classic apps (optional):
@echo       DISM /Apply-SiloedPackage /ImagePath:W:\ 
@echo            /PackagePath:"D:\App1.spp" /PackagePath:"D:\App2.spp"  ...
@echo.
@echo   * Configure the recovery partition with ApplyRecovery.bat
@echo.      
@echo   * Reboot:
@echo       exit
@GOTO END
:FFU
@echo Starting FFU Deployment
@echo list disk > x:\listdisks.txt
@echo exit >> x:\listdisks.txt
@diskpart /s x:\listdisks.txt
@del x:\listdisks.txt
@echo Enter the disk number of the drive where you're going to deploy your FFU (usually 0).
@SET /P DISKNUMBER=(Enter the Disk Number from above):
@echo This will remove all data from disk %DISKNUMBER%. Continue?
@SET /P ERASEALL=(Y or N):
@if %ERASEALL%.==y. set ERASEALL=Y
@if %ERASEALL%==Y DISM /apply-ffu /ImageFile=%1 /ApplyDrive:\\.\PhysicalDrive%DISKNUMBER%
@if not %ERASEALL%==Y GOTO END
@echo FFU applied. Would you like to configure the recovery partition?
@SET /P CONFIGRECOVERY=(Y or N):
@if %CONFIGRECOVERY%.==y. SET CONFIGRECOVERY=Y
@if %CONFIGRECOVERY%==Y ApplyRecovery.bat
@if not %CONFIGRECOVERY%==Y GOTO END
:END
```

ApplyImage.bat relies on the following DiskPart scripts, which must be placed in the same folder:

### <span id="CreatePartitions-_firmware_.txt"></span>CreatePartitions scripts

Use these scripts together with DiskPart to format and set up the hard disk partitions for Windows, including recovery tools. Adjust the partition sizes to fill the drive as necessary.

#### <span id="CreatePartitions-UEFI.txt"></span> CreatePartitions-UEFI.txt

Creates the System, MSR, Windows, and recovery tools partitions for UEFI-based PCs.

This script temporarily assigns these drive letters: System=S, Windows=W, and Recovery=R. The MSR partition doesn't get a letter. The letter W is used to avoid potential drive letter conflicts. After the device reboots, the Windows partition is assigned the letter C, and the other partitions don’t receive drive letters.

The Recovery partition must be the partition after the Windows partition to ensure winre.wim can be kept up-to-date during life of the device.

The following diagram shows the resulting partition configuration:

![diagram of default partition layout: system, msr, windows, and recovery](images/dep-win10-partitions-uefi.png)

```
rem == CreatePartitions-UEFI.txt ==
rem == These commands are used with DiskPart to
rem    create four partitions
rem    for a UEFI/GPT-based PC.
rem    Adjust the partition sizes to fill the drive
rem    as necessary. ==
select disk 0
clean
convert gpt
rem == 1. System partition =========================
create partition efi size=100
rem    ** NOTE: For Advanced Format 4Kn drives,
rem               change this value to size = 260 ** 
format quick fs=fat32 label="System"
assign letter="S"
rem == 2. Microsoft Reserved (MSR) partition =======
create partition msr size=16
rem == 3. Windows partition ========================
rem ==    a. Create the Windows partition ==========
create partition primary 
rem ==    b. Create space for the recovery tools ===
shrink minimum=500
rem       ** NOTE: Update this size to match the
rem                size of the recovery tools 
rem                (winre.wim)                    **
rem ==    c. Prepare the Windows partition ========= 
format quick fs=ntfs label="Windows"
assign letter="W"
rem === 4. Recovery partition ======================
create partition primary
format quick fs=ntfs label="Recovery"
assign letter="R"
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"
gpt attributes=0x8000000000000001
list volume
exit
```

#### <span id="CreatePartitions-UEFI-FFU.txt"></span> CreatePartitions-UEFI-FFU.txt

This script is based off of CreatePartitions-UEFI.txt, but it does not create a recovery partition. This is so that the Windows partition is the last partition on the drive and can be expanded. If this script is used, the recovery partition can be configured later with ApplyRecovery.bat.

```
rem == CreatePartitions-UEFI-FFU.txt ==
rem == These commands are used with DiskPart to
rem    create four partitions
rem    for a UEFI/GPT-based PC.
rem    Adjust the partition sizes to fill the drive
rem    as necessary. ==
select disk 0
clean
convert gpt
rem == 1. System partition =========================
create partition efi size=100
rem    ** NOTE: For Advanced Format 4Kn drives,
rem               change this value to size = 260 ** 
format quick fs=fat32 label="System"
assign letter="S"
rem == 2. Microsoft Reserved (MSR) partition =======
create partition msr size=16
rem == 3. Windows partition ========================
rem ==    a. Create the Windows partition ==========
create partition primary 
rem ==    c. Prepare the Windows partition ========= 
format quick fs=ntfs label="Windows"
assign letter="W"
list volume
exit
```

#### <span id="CreatePartitions-BIOS.txt"></span> CreatePartitions-BIOS.txt

Creates the System, Windows, and recovery tools partitions for BIOS-based PCs.

This script temporarily assigns these drive letters: System=S, Windows=W, and Recovery=R. The letter W is used to avoid potential drive letter conflicts. After the device reboots, the Windows partition is assigned the letter C, and the other partitions don’t receive drive letters.

The Recovery partition must be the partition after the Windows partition to ensure winre.wim can be kept up-to-date during life of the device.

The following diagram shows the resulting partition configuration:

![diagram of default partition layout: system, windows, and recovery](images/dep-win10-partitions-bios.png)

```
rem == CreatePartitions-BIOS.txt ==
rem == These commands are used with DiskPart to
rem    create three partitions
rem    for a BIOS/MBR-based computer.
rem    Adjust the partition sizes to fill the drive
rem    as necessary. ==
select disk 0
clean
rem == 1. System partition ======================
create partition primary size=100
format quick fs=ntfs label="System"
assign letter="S"
active
rem == 2. Windows partition =====================
rem ==    a. Create the Windows partition =======
create partition primary
rem ==    b. Create space for the recovery tools  
shrink minimum=500
rem       ** NOTE: Update this size to match the
rem                size of the recovery tools 
rem                (winre.wim)                 **
rem ==    c. Prepare the Windows partition ====== 
format quick fs=ntfs label="Windows"
assign letter="W"
rem == 3. Recovery partition ====================
create partition primary
format quick fs=ntfs label="Recovery image"
assign letter="R"
set id=27
list volume
exit
```

#### <span id="CreatePartitions-BIOS-FFU.txt"></span> CreatePartitions-BIOS-FFU.txt

This script is based off of CreatePartitions-BIOS.txt, but it doesn't create a recovery partition. This is so that the Windows partition is the last partition on the drive and can be expanded. If this script is used, the recovery partition can be configured later with ApplyRecovery.bat.

```
rem == CreatePartitions-BIOS-FFU.txt ==
rem == These commands are used with DiskPart to
rem    create three partitions
rem    for a BIOS/MBR-based computer.
rem    Adjust the partition sizes to fill the drive
rem    as necessary. ==
select disk 0
clean
rem == 1. System partition ======================
create partition primary size=100
format quick fs=ntfs label="System"
assign letter="S"
active
rem == 2. Windows partition =====================
rem ==    a. Create the Windows partition =======
create partition primary
rem ==    c. Prepare the Windows partition ====== 
format quick fs=ntfs label="Windows"
assign letter="W"
list volume
exit
```

### ApplyRecovery.bat

Use this script to prepare the Windows recovery partition. This script is called by ApplyImage.bat, but can also be run on its own.

**Note:** If you copy and paste the contents below to create a .bat file, you may get an error when detecting firmware. For firmware detection to succeed, ensure that the lines that begin `for /f "tokens=2* delims=	 " %%A` has a tab followed by a space in between `delims=` and `" %%A`.

```
@echo == ApplyRecovery.bat ==
@rem *********************************************************************
@echo Checking to see if the PC is booted in BIOS or UEFI mode.
wpeutil UpdateBootInfo
for /f "tokens=2* delims=  " %%A in ('reg query HKLM\System\CurrentControlSet\Control /v PEFirmwareType') DO SET Firmware=%%B
@echo            Note: delims is a TAB followed by a space.
@if x%Firmware%==x echo ERROR: Can't figure out which firmware we're on.
@if x%Firmware%==x echo        Common fix: In the command above:
@if x%Firmware%==x echo             for /f "tokens=2* delims=	 "
@if x%Firmware%==x echo        ...replace the spaces with a TAB character followed by a space.
@if x%Firmware%==x goto END
@if %Firmware%==0x1 echo The PC is booted in BIOS mode. 
@if %Firmware%==0x2 echo The PC is booted in UEFI mode. 
@echo  *********************************************************************
@echo Do you already have a recovery partition on this disk? (Y or N):
@SET /P RECOVERYEXIST=(Y or N):
@if %RECOVERYEXIST%.==y. set RECOVERYEXIST=Y
@if %RECOVERYEXIST%.==Y. GOTO COPYTOTOOLSPARTITION
@if not %RECOVERYEXIST%.==Y. GOTO CREATEFFURECOVERY
@echo  *********************************************************************
:COPYTOTOOLSPARTITION
@echo  == Copy the Windows RE image to the Windows RE Tools partition ==
md R:\Recovery\WindowsRE
xcopy /h W:\Windows\System32\Recovery\Winre.wim R:\Recovery\WindowsRE\
@echo  *********************************************************************
@echo  == Register the location of the recovery tools ==
W:\Windows\System32\Reagentc /Setreimage /Path R:\Recovery\WindowsRE /Target W:\Windows
@echo  *********************************************************************
@IF EXIST W:\Recovery\Customizations\USMT.ppkg (GOTO CUSTOMDATAIMAGEWIM) else goto HIDEWIMRECOVERYTOOLS
:CUSTOMDATAIMAGEWIM
@echo  == If Compact OS, single-instance the recovery provisioning package ==
@echo.     
@echo	  *Note: this step only works if you created a ScanState package called
@echo	   USMT.ppkg as directed in the OEM Deployment lab. If you aren't
@echo	   following the steps in the lab, choose N.
@echo.      
@echo     Options: N: No
@echo              Y: Yes
@echo              D: Yes, but defer cleanup steps to first boot.
@echo                 Use this if the cleanup steps take more than 30 minutes.
@echo                 defer the cleanup steps to the first boot.
@SET /P COMPACTOS=Deploy as Compact OS? (Y, N, or D):
@if %COMPACTOS%.==y. set COMPACTOS=Y
@if %COMPACTOS%.==d. set COMPACTOS=D
@if %COMPACTOS%.==Y. dism /Apply-CustomDataImage /CustomDataImage:W:\Recovery\Customizations\USMT.ppkg /ImagePath:W:\ /SingleInstance
@if %COMPACTOS%.==D. dism /Apply-CustomDataImage /CustomDataImage:W:\Recovery\Customizations\USMT.ppkg /ImagePath:W:\ /SingleInstance /Defer
@echo  *********************************************************************
:HIDEWIMRECOVERYTOOLS
@echo == Hiding the recovery tools partition
if %Firmware%==0x1 diskpart /s %~dp0HideRecoveryPartitions-BIOS.txt
if %Firmware%==0x2 diskpart /s %~dp0HideRecoveryPartitions-UEFI.txt
@echo *********************************************************************
@echo == Verify the configuration status of the images. ==
W:\Windows\System32\Reagentc /Info /Target W:\Windows
@echo    (Note: Windows RE status may appear as Disabled, this is OK.)
@echo *********************************************************************
@echo      All done!
@echo      Disconnect the USB drive from the reference device.
@echo      Type exit to reboot.
@echo.
GOTO END
:CREATEFFURECOVERY
@echo *********************************************************************
@echo == Creating the recovery tools partition
@if %Firmware%==0x1 diskpart /s CreateRecoveryPartitions-BIOS.txt
@if %Firmware%==0x2 diskpart /s CreateRecoveryPartitions-UEFI.txt
@echo finding the Windows Drive
@echo  *********************************************************************
@IF EXIST C:\Windows SET windowsdrive=C:\
@IF EXIST D:\Windows SET windowsdrive=D:\
@IF EXIST E:\Windows SET windowsdrive=E:\
@IF EXIST W:\Windows SET windowsdrive=W:\
@echo The Windows drive is %windowsdrive%
md R:\Recovery\WindowsRE
@echo  *********************************************************************
@echo Finding Winre.wim
@IF EXIST %windowsdrive%Recovery\WindowsRE\winre.wim SET recoveryfolder=%windowsdrive%Recovery\WindowsRE\
@IF EXIST %windowsdrive%Windows\System32\Recovery\winre.wim SET recoveryfolder=%windowsdrive%Windows\System32\Recovery\
@echo  *********************************************************************
@echo copying Winre.wim
xcopy /h %recoveryfolder%Winre.wim R:\Recovery\WindowsRE\
@echo  *********************************************************************
@echo  == Register the location of the recovery tools ==
%windowsdrive%Windows\System32\Reagentc /Setreimage /Path R:\Recovery\WindowsRE /Target %windowsdrive%Windows
@echo  *********************************************************************
@IF EXIST W:\Recovery\Customizations\USMT.ppkg (GOTO CUSTOMDATAIMAGEFFU) else goto HIDERECOVERYTOOLSFFU
:CUSTOMDATAIMAGEFFU
@echo  == If Compact OS, single-instance the recovery provisioning package ==
@echo.     
@echo	  *Note: this step only works if you created a ScanState package called
@echo	   USMT.ppkg as directed in the OEM Deployment lab. If you aren't
@echo	   following the steps in the lab, choose N.
@echo.
@echo     Options: N: No
@echo              Y: Yes
@echo              D: Yes, but defer cleanup steps to first boot.
@echo                 Use this if the cleanup steps take more than 30 minutes.
@echo                 defer the cleanup steps to the first boot.
@SET /P COMPACTOS=Deploy as Compact OS? (Y, N, or D):
@if %COMPACTOS%.==y. set COMPACTOS=Y
@if %COMPACTOS%.==d. set COMPACTOS=D
@if %COMPACTOS%.==Y. dism /Apply-CustomDataImage /CustomDataImage:%windowsdrive%Recovery\Customizations\USMT.ppkg /ImagePath:%windowsdrive% /SingleInstance
@if %COMPACTOS%.==D. dism /Apply-CustomDataImage /CustomDataImage:%windowsdrive%Recovery\Customizations\USMT.ppkg /ImagePath:%windowsdrive% /SingleInstance /Defer
:HIDERECOVERYTOOLSFFU
@rem *********************************************************************
@echo == Hiding the recovery tools partition
@if %Firmware%==0x1 diskpart /s HideRecoveryPartitions-BIOS.txt
@if %Firmware%==0x2 diskpart /s HideRecoveryPartitions-UEFI.txt
@echo *********************************************************************
@echo == Verify the configuration status of the images. ==
%windowsdrive%Windows\System32\Reagentc /Info /Target %windowsdrive%Windows
@echo    (Note: Windows RE status may appear as Disabled, this is OK.)
@echo *********************************************************************
@echo      All done!
@echo      Disconnect the USB drive from the reference device.
@echo      Type exit to reboot.
@GOTO END
:END
```

ApplyRecovery.bat relies on the following  DiskPart scripts, which must be placed in the same folder:

#### CreateRecoveryPartitions-UEFI.txt

```
rem == CreateRecoveryPartitions-UEFI.txt ==
select disk 0
select partition 3
assign letter="W"
rem == extend the Windows partition ==
shrink minimum=500
extend
rem ==    b. Create space for the recovery tools  
shrink minimum=500
rem       ** NOTE: Update this size to match the
rem                size of the recovery tools 
rem                (winre.wim)                 **
rem === Create Recovery partition ======================
create partition primary
format quick fs=ntfs label="Recovery"
assign letter="R"
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"
gpt attributes=0x8000000000000001
list volume
exit
```

#### CreateRecoveryPartitions-BIOS.txt

```
rem == CreateRecoveryPartitions-BIOS.txt ==
select disk 0
select partition 2
assign letter="W"
rem == extend the Windows partition ==
shrink minimum=500
extend
rem ==    b. Create space for the recovery tools  
shrink minimum=500
rem       ** NOTE: Update this size to match the
rem                size of the recovery tools 
rem                (winre.wim)                 **
rem ==    c. Prepare the Recovery partition ====== 
select disk 0
create partition primary
format quick fs=ntfs label="Recovery image"
assign letter="R"
set id=27
list volume
exit
```

#### HideRecoveryPartitions-UEFI.txt

```
rem === HideRecoveryPartitions-UEFI.txt ===
select disk 0
select partition 4
set id=de94bba4-06d1-4d40-a16a-bfd50179d6ac
gpt attributes=0x8000000000000001
remove
list volume
```

#### HideRecoveryPartitions-BIOS.txt

```
rem === HideRecoveryPartitions-BIOS.txt ===
select disk 0
select partition 3
set id=27
remove
list volume
```


## <span id="Start_Layout"></span><span id="layoutmodification.xml"></span><span id="LAYOUTMODIFICATION.XML"></span>Start layout (LayoutModification.xml)


The Start tile layout in Windows 10 provides OEMs the ability to append tiles to the default Start layout to include Web links, secondary tiles, Windows apps, and Windows desktop applications. OEMs can use this layout to make it applicable to multiple regions or markets without duplicating a lot of the work. In addition, OEMs can add up to three default apps to the frequently used apps section in the system area, which delivers sytem-driven lists o the user including important or frequently accessed system locations and recently installed apps.

To take advantage of all these new features and have the most robust and complete Start customization experience for Windows 10, consider creating a LayoutModification.xml file. This file specifies how the OEM tiles should be laid out in Start. For more information about how to customize the new Start layout, see the topic [Customize the Windows 10 Start screen](https://msdn.microsoft.com/library/windows/hardware/mt170651) in the Windows 10 Partner Documentation.

Sample **LayoutModification.xml**:

```
<LayoutModificationTemplate
    xmlns="http://schemas.microsoft.com/Start/2014/LayoutModification"
    xmlns:defaultlayout="http://schemas.microsoft.com/Start/2014/FullDefaultLayout"
    xmlns:start="http://schemas.microsoft.com/Start/2014/StartLayout"
    Version="1">
  <RequiredStartGroupsCollection>
    <RequiredStartGroups
      Region="DE|ES|FR|GB|IT|US">
      <AppendGroup
        Name="Fabrikam Group 1">
        <start:Tile
          AppUserModelID="Microsoft.Office.Word_8wekyb3d8bbwe!microsoft.word"
          Size="2x2"
          Row="0"
          Column="0"/>
        <start:DesktopApplicationTile
          DesktopApplicationID="Microsoft.Windows.Explorer"
          Size="2x2"
          Row="0"
          Column="2"/>
        <start:Tile
          AppUserModelID="Microsoft.Office.Excel_8wekyb3d8bbwe!microsoft.excel"
          Size="2x2"
          Row="0"
          Column="4"/>
      </AppendGroup>    
      <AppendGroup
        Name="Fabrikam Group 2">
        <start:Tile
          AppUserModelID="Microsoft.Reader_8wekyb3d8bbwe!Microsoft.Reader"
          Size="2x2"
          Row="0"
          Column="0"/>
        <start:DesktopApplicationTile
          DesktopApplicationID="http://www.bing.com/"
          Size="2x2"
          Row="0"
          Column="2"/>
        <start:DesktopApplicationTile
          DesktopApplicationLinkPath="%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs\Accessories\Paint.lnk"
          Size="2x2"
          Row="0"
          Column="4"/>
      </AppendGroup>
    </RequiredStartGroups>
    <RequiredStartGroups>
      <AppendGroup
        Name="Fabrikam Group 1">
        <start:Tile
          AppUserModelID="Microsoft.Office.Word_8wekyb3d8bbwe!microsoft.word"
          Size="2x2"
          Row="0"
          Column="0"/>
        <start:SecondaryTile
          AppUserModelID="Microsoft.Windows.Spartan_cw5n1h2txyewy!Microsoft.Spartan.Spartan"
          TileID="FabrikamWeblinkTile"
          Arguments="http://www.fabrikam.com"
          DisplayName="Fabrikam"
          Square150x150LogoUri="ms-appx:///Assets/SpartanMedium.png"
          ShowNameOnSquare150x150Logo="true"
          BackgroundColor="#FF112233"
          Size="2x2"
          Row="0"
          Column="2"/>
      </AppendGroup>    
    </RequiredStartGroups>
  </RequiredStartGroupsCollection> 
  <TopMFUApps>
    <Tile AppUserModelID="Microsoft.WindowsCalculator_8wekyb3d8bbwe!App" />
  </TopMFUApps>  
</LayoutModificationTemplate>
```

## <span id="microphone_settings__speechsetting.cmd_"></span><span id="MICROPHONE_SETTINGS__SPEECHSETTING.CMD_"></span>Microphone settings (SpeechSetting.cmd)


Use this script to tune your device’s microphone to help maximize speech accuracy for features like Cortana. To learn how to test for the appropriate values for your device, see [Cortana Device Test Setup](https://msdn.microsoft.com/library/windows/hardware/dn957009) in the Hardware WEG.

### SpeechSetting.cmd

```
@echo off
@echo.
@echo This script will set the Device.SpeechRecognition.DefaultMicGain values.
@echo.
 
setlocal
setlocal ENABLEDELAYEDEXPANSION
 
if "%1"=="" goto :InputPrompt
 call :tohex %1
@echo %_DECVAL% == 0x%_HEXVAL%
set /a Percentage=%_DECVAL%/100
 
set RegPath=HKLM\Software\Microsoft\Speech_OneCore\AudioInput\MicWiz
set RegKey=DefaultDefaultMicGain
set RegValue=0x%_HEXVAL%
 
@echo reg add %RegPath% /v %RegKey% /t REG_DWORD /d %RegValue% /f
reg add %RegPath% /v %RegKey% /t REG_DWORD /d %RegValue% /f
if %errorlevel% NEQ 0 echo Reg Add function failed. && goto :Error
@echo.
@echo Successfully set the MicGain value to %Percentage% percent.
@echo.
goto :end
 
:ToHex
     set _DECVAL=%1
     set _HEXVAL=
     set _VAL=%1
     if %1 LSS 0 (
         REM break the number into two parts so that we can output the
         REM full value within the bounds of a 32 bit signed value
         set /A _offset="-(-2147483647 - !_VAL!) + 2"
         set /A _VAL="!_offset! / 16 + 0x7FFFFFF"
         set /A _P="!_offset! %% 16 + 0xF"
 
         if !_P! GEQ 16 (
         set /A _VAL="!_VAL! + 1"
         set /A _P="!_P! %% 16"
         )
         if !_P! LEQ 9 set _HEXVAL=!_P!!_HEXVAL!
         if "!_P!" == "10" set _HEXVAL=A!_HEXVAL!
         if "!_P!" == "11" set _HEXVAL=B!_HEXVAL!
         if "!_P!" == "12" set _HEXVAL=C!_HEXVAL!
         if "!_P!" == "13" set _HEXVAL=D!_HEXVAL!
         if "!_P!" == "14" set _HEXVAL=E!_HEXVAL!
         if "!_P!" == "15" set _HEXVAL=F!_HEXVAL!
     )
 
     :hexloop
     set /A _P="%_VAL% %% 16"
     if %_P% LEQ 9 set _HEXVAL=%_P%%_HEXVAL%
     if "%_P%" == "10" set _HEXVAL=A%_HEXVAL%
     if "%_P%" == "11" set _HEXVAL=B%_HEXVAL%
     if "%_P%" == "12" set _HEXVAL=C%_HEXVAL%
     if "%_P%" == "13" set _HEXVAL=D%_HEXVAL%
     if "%_P%" == "14" set _HEXVAL=E%_HEXVAL%
     if "%_P%" == "15" set _HEXVAL=F%_HEXVAL%
     set /A _VAL="%_VAL% / 16"
     if "%_VAL%" == "0" goto :endloop
     goto :hexloop
 
:InputPrompt
      @echo.
      @echo Incorrect Usage.
      @echo.
      @echo Please input a decimal value as a parameter.
      @echo.
      @echo Example:
      @echo SpeechSetting.cmd 4200
      @echo.
      @echo This example sets the MicGain as 42 percent which is 0x1068.
      @echo.
goto :end
 
:Error
@echo.
@echo Error occurred.
@echo.
goto :end
 
:endloop
     set _offset=
     set _P=
     set _VAL=
goto :eof

:end 
```

## <span id="Remove_Windows_apps"></span><span id="remove_windows_apps"></span><span id="REMOVE_WINDOWS_APPS"></span>Remove Windows apps


When you add language packs to an image, you’ll need to remove and reinstall each of your Windows apps to make sure they include the language assets.

Here's two scripts, one which can be used to remove the apps from an offline image, and another that can be used in audit mode to remove apps from a running image:

### Remove\_apps\_in\_offline\_image.cmd

This script assumes the file name is install.wim, that the script is being run from the same folder as install.wim, that the index being modified is \#1, and that there are no other Windows images in other index locations (\#2, \#3) that need to be preserved.

```
@echo off
@rem Run this script from the same folder that includes install.wim 
cls
md mount
dism /mount-image /mountdir:mount /imagefile:install.wim /index:1
if exist appx.txt del appx.txt
if exist applist.txt del applist.txt
if exist appremove.cmd del appremove.cmd
dism /image:mount /get-provisionedappxpackages > appx.txt
findstr /c:"PackageName" appx.txt > applist.txt
setlocal enabledelayedexpansion
set INTEXTFILE=applist.txt
set OUTTEXTFILE=appremove.cmd
set SEARCHTEXT=PackageName : 
set REPLACETEXT=dism /image:mount /remove-provisionedappxpackage /packagename:
set OUTPUTLINE=

for /f "tokens=1,* delims=¶" %%A in ( '"type %INTEXTFILE%"') do (
SET string=%%A
SET modified=!string:%SEARCHTEXT%=%REPLACETEXT%!

echo !modified! >> %OUTTEXTFILE%
)
del appx.txt
del applist.txt
call appremove.cmd
dism /unmount-image /mountdir:mount /commit
echo "Check to make sure operations completed successfully. If not, press Ctrl+C."
pause
ren install.wim install-temp.wim
dism /export-image /sourceimagefile:install-temp.wim /sourceindex:1 /destinationimagefile:install.wim
del install-temp.wim
```

### Remove\_apps\_in\_audit\_mode.cmd

This script assumes you’re running in audit mode on the reference PC.

```
@echo off
@rem Use MOUNT folder in folder where script is run 
cls
dism /mount-image /mountdir:mount /imagefile:install.wim /index:1
if exist appx.txt del appx.txt
if exist applist.txt del applist.txt
if exist appremove.cmd del appremove.cmd
dism /image:mount /get-provisionedappxpackages > appx.txt
findstr /c:"PackageName" appx.txt > applist.txt
setlocal enabledelayedexpansion
set INTEXTFILE=applist.txt
set OUTTEXTFILE=appremove.cmd
set SEARCHTEXT=PackageName : 
set REPLACETEXT=dism /image:mount /remove-provisionedappxpackage /packagename:
set OUTPUTLINE=

for /f "tokens=1,* delims=¶" %%A in ( '"type %INTEXTFILE%"') do (
SET string=%%A
SET modified=!string:%SEARCHTEXT%=%REPLACETEXT%!

echo !modified! >> %OUTTEXTFILE%
)
del appx.txt
del applist.txt
call appremove.cmd
dism /unmount-image /mountdir:mount /commit
ren install.wim install-temp.wim
dism /export-image /sourceimagefile:install-temp.wim /sourceindex:1 /destinationimagefile:install.wim
del install-temp.wim
```

## <span id="BootToAudit"></span><span id="boottoaudit"></span><span id="BOOTTOAUDIT"></span>BootToAudit


Add an answer file to the Windows image in C:\\mount\\windows\\Windows\\Panther\\unattend.xml to instruct it to boot into audit mode. You can create this answer file in Windows System Image Manager.

### BootToAudit-x64

```
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
<!-- BootToAudit-x64.xml -->
    <settings pass="oobeSystem">
        <component name="Microsoft-Windows-Deployment" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <Reseal>
                <Mode>Audit</Mode>
            </Reseal>
        </component>
    </settings>
</unattend>
```

### BootToAudit-x86

```
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
<!-- BootToAudit-x86.xml -->
    <settings pass="oobeSystem">
        <component name="Microsoft-Windows-Deployment" processorArchitecture="x86" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <Reseal>
                <Mode>Audit</Mode>
            </Reseal>
        </component>
    </settings>
</unattend>
```

## <span id="Keeping_Windows_settings_through_a_recovery"></span><span id="keeping_windows_settings_through_a_recovery"></span><span id="KEEPING_WINDOWS_SETTINGS_THROUGH_A_RECOVERY"></span>Keeping Windows settings through a recovery

Windows doesn't automatically save settings created through unattend.xml setup files, nor Windows Start Menu customizations created with LayoutModification.xml during a full-system reset, nor first-login info from oobe.xml.

To make sure your customizations are saved, that includes steps to put the unattend.xml, LayoutModification.xml, and oobe.xml files back into place. Here's some sample scripts that show how to retain these settings and put them back into the right spots. Save copies of unattend.xml, LayoutModification.xml, oobe.xml, plus these two text files: ResetConfig.xml and EnableCustomizations.cmd, in C:\\Recovery\\OEM\\:

### ResetConfig.xml

```
<?xml version="1.0" encoding="utf-8"?>
<!-- ResetConfig.xml -->
<Reset>
  <Run Phase="BasicReset_AfterImageApply">
    <Path>EnableCustomizations.cmd</Path>
    <Duration>2</Duration>
  </Run>
  <Run Phase="FactoryReset_AfterImageApply">
    <Path>EnableCustomizations.cmd</Path>
    <Duration>2</Duration>
  </Run>
</Reset>
```

### EnableCustomizations.cmd

```
rem EnableCustomizations.cmd

rem Set the variable %TARGETOS%      (Typically this is C:\Windows)
for /F "tokens=1,2,3 delims= " %%A in ('reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RecoveryEnvironment" /v TargetOS') DO SET TARGETOS=%%C

rem Set the variable %TARGETOSDRIVE% (Typically this is C:)
for /F "tokens=1 delims=\" %%A in ('Echo %TARGETOS%') DO SET TARGETOSDRIVE=%%A

rem Add back Windows settings, Start menu, and OOBE.xml customizations
copy "%TARGETOSDRIVE%\Recovery\OEM\Unattend.xml" "%TARGETOS%\Panther\Unattend.xml" /y
copy "%TARGETOSDRIVE%\Recovery\OEM\LayoutModification.xml" "%TARGETOSDRIVE%\Users\Default\AppData\Local\Microsoft\Windows\Shell\LayoutModification.xml" /y
xcopy "%TARGETOSDRIVE%\Recovery\OEM\OOBE\Info" "%TARGETOS%\System32\Info\" /s

rem Recommended: Create a pagefile for devices with 1GB or less of RAM.
wpeutil CreatePageFile /path=%TARGETOSDRIVE%\PageFile.sys /size=256
```

To learn more about using extensibility points for push-button reset, see [Add a script to push-button reset features](http://go.microsoft.com/fwlink/?LinkId=618946).

## <span id="Reinstall_Windows_inbox_apps"></span>Reinstall Windows inbox apps

Reinstall Windows apps after adding a new language. You can reinstall the apps without removing them first. 

### ReinstallInboxApps-x64.cmd

```
DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.3DBuilder_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.3DBuilder_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsAlarms_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsAlarms_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.BingWeather_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.BingWeather_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsCalculator_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsCalculator_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsCamera_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsCamera_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsFeedbackHub_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsFeedbackHub_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.Getstarted_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.Getstarted_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsMaps_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsMaps_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.Messaging_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.Messaging_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.MicrosoftOfficeHub_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.MicrosoftOfficeHub_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx
DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.Office.OneNote_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.Office.OneNote_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsCommunicationsApps_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsCommunicationsApps_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.People_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.People_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.Windows.Photos_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.Windows.Photos_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.StorePurchaseApp_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.StorePurchaseApp_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /add-ProvisionedAppxPackage /image:c:\mount\windows /packagepath:e:\apps\amd64\Microsoft.MicrosoftSolitaireCollection_8wekyb3d8bbwe.appxbundle /licensepath:e:\apps\amd64\Microsoft.MicrosoftSolitaireCollection_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.6.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.6.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.6.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.6.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.Advertising.Xaml.x64.10.0.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.Advertising.Xaml.x86.10.0.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.Services.Store.Engagement.x64.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.Services.Store.Engagement.x86.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsSoundRecorder_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsSoundRecorder_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.WindowsStore_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.WindowsStore_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.XboxApp_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.XboxApp_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.XboxIdentityProvider_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.XboxIdentityProvider_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.ZuneMusic_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.ZuneMusic_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.ZuneVideo_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.ZuneVideo_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.SkypeApp_kzf8qxf38zg5c.appxbundle /LicensePath:e:\apps\amd64\Microsoft.SkypeApp_kzf8qxf38zg5c.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.OneConnect_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.OneConnect_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x64.1.3.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\amd64\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\amd64\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x64.14.00.appx /DependencyPackagePath:e:\apps\amd64\Microsoft.VCLibs.x86.14.00.appx
```

### ReinstallInboxApps-x86.cmd

```
DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.3DBuilder_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.3DBuilder_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsAlarms_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsAlarms_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.BingWeather_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.BingWeather_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsCalculator_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsCalculator_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsCamera_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsCamera_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsFeedbackHub_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsFeedbackHub_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.Getstarted_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.Getstarted_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsMaps_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsMaps_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.Messaging_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.Messaging_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.MicrosoftOfficeHub_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.MicrosoftOfficeHub_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.Office.OneNote_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.Office.OneNote_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsCommunicationsApps_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsCommunicationsApps_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.People_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.People_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.Windows.Photos_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.Windows.Photos_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.StorePurchaseApp_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.StorePurchaseApp_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.MicrosoftSolitaireCollection_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.MicrosoftSolitaireCollection_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.Advertising.Xaml.x86.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsSoundRecorder_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsSoundRecorder_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.WindowsStore_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.WindowsStore_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.XboxApp_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.XboxApp_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.XboxIdentityProvider_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.XboxIdentityProvider_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.ZuneMusic_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.ZuneMusic_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.ZuneVideo_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.ZuneVideo_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.SkypeApp_kzf8qxf38zg5c.appxbundle /LicensePath:e:\apps\x86\Microsoft.SkypeApp_kzf8qxf38zg5c.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.OneConnect_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.OneConnect_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Framework.x86.1.3.appx /DependencyPackagePath:e:\apps\x86\Microsoft.NET.Native.Runtime.x86.1.3.appx

DISM /Add-ProvisionedAppxPackage /Image:c:\mount\windows /PackagePath:e:\apps\x86\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.appxbundle /LicensePath:e:\apps\x86\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe.xml /DependencyPackagePath:e:\apps\x86\Microsoft.VCLibs.x86.14.00.appx
```


 