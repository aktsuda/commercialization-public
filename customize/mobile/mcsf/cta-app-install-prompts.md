---
title: CTA app install prompts
description: To meet China Type Approval (CTA) requirements for devices shipping in China, OEMs must show a notification dialog to alert users when the app being downloaded does certain things.
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 5c83b11a-82f2-4359-94b3-5945cf807671
ms.author: alhopper
ms.date: 05/02/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# CTA app install prompts


To meet China Type Approval (CTA) requirements for devices shipping in China, OEMs must show a notification dialog to alert users when the app being downloaded does certain things.

**Note**  This is a legacy mobile setting and is only a requirement for China. It works on phones being upgraded to Windows 10 Mobile, but we recommend that you use the new [CountryAndRegion](https://docs.microsoft.com/en-us/windows/configuration/wcd/wcd-countryandregion) Windows provisioning setting instead.

 

The dialog must be shown when the app being downloaded does any of the following:

-   Invokes user data from a phone book

-   Uses recording from the Microsoft Store

-   Uses location data

<a href="" id="constraints---none"></a>**Constraints:** None  

<a href="" id="instructions-"></a>**Instructions:**  
1.  Create a customization answer file using the contents shown in the following code sample.

    ```XML
    <?xml version="1.0" encoding="utf-8" ?>  

    <ImageCustomizations xmlns="http://schemas.microsoft.com/embedded/2004/10/ImageUpdate"  
                         Name="CTAAppInstallPrompts"  
                         Description="Use to show a notification dialog to alert users when the app being downloaded invokes data from a 
                                      phone book, uses recording from the Windows Phone Store, or uses location data.
                                      This customization is only a requirement for China."  
                         Owner=""  
                         OwnerType="OEM"> 
      
      <Static>  

        <Settings Path="Zune/Settings">  
          <Setting Name="RequireExtendedCapabilityPrompts" Value="" />    
       </Settings>  

      </Static>

    </ImageCustomizations>
    ```

2.  Specify an `Owner`.

3.  Set the value of `RequireExtendedCapabilityPrompts` to one of the following:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th>Value</th>
    <th>Description</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td><p>1 or 'Yes'</p></td>
    <td><p>Shows a notification dialog when the user downloads an app from the Microsoft Store that supports the functionality described for the customization.</p></td>
    </tr>
    <tr class="even">
    <td><p>0 or 'No'</p></td>
    <td><p>Disables the feature.</p></td>
    </tr>
    </tbody>
    </table>

     

    If the setting is not set, the feature is not enabled.

<a href="" id="testing-"></a>**Testing:**  
To fully test this customization, the phone must also be configured to use [Microsoft Store for China](windows-phone-store-for-china.md).

1.  Flash the build containing this customization to a phone.

2.  Go to the Microsoft Store and download and install an app that can invoke the phone book.

3.  Open the app to access the device's native phone book.

4.  Verify that a notification dialog shows up.

5.  Download another app that uses recording or location data and verify that you see the notification appear when you first run the app.

## Related topics

[Prepare for Windows mobile development](https://docs.microsoft.com/en-us/windows-hardware/manufacture/mobile/preparing-for-windows-mobile-development)

[Customization answer file overview](https://docs.microsoft.com/en-us/windows-hardware/customize/mobile/mcsf/customization-answer-file)