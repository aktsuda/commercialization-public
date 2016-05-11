---
title: Mobile Broadband
description: This topic covers recommendations for Mobile Broadband components in devices that run Windows 10.
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 4BB6A8C2-9D48-44FE-81D3-57895A0C7644
---

# Mobile Broadband


This topic covers recommendations for Mobile Broadband components in devices that run Windows 10.

## Hardware


Windows supports combinations of the GSM-based and CDMA-based mobile broadband technologies including LTE. The minimum connectivity capability for a Mobile Broadband device is 3G connectivity. 2G connectivity is optional. Devices for the various broadband technologies must follow the specifications of standards bodies and be certified by the preferred mobile operator.

-   [GSM/LTE](http://go.microsoft.com/fwlink/p/?LinkId=624986)
-   [CDMA](http://go.microsoft.com/fwlink/p/?LinkId=624987)

The following table summarizes the recommended mobile broadband hardware features.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Feature</td>
<td>Remarks</td>
</tr>
<tr class="even">
<td>Bus</td>
<td><p>USB-SSIC (USB 3.0)</p>
<p>USB-HSIC for surface mounted solutions</p>
<p>USB for connector mounted solutions (the device must continue to work while in USB selective suspend mode and capable of waking up the device)</p></td>
</tr>
<tr class="odd">
<td>Idle power</td>
<td>&lt;10 mW (Connected Standby platform)</td>
</tr>
<tr class="even">
<td>SIM Connector</td>
<td>User accessible</td>
</tr>
</tbody>
</table>

 

Embedded module solutions must always have mobile broadband functionality available without any USB hub design inside the chipset.

Idle mode power for 3GPP devices covers:

-   PDP context activated but no network traffic state
-   PDP context de-activated state

Idle mode power for 3GPP2 devices covers:

-   Connected state with no network traffic state
-   Disconnected state

## Firmware


USB based devices for GSM and CDMA technologies (3GPP/3GPP2 standards based) must be firmware compliant with the Mobile Broadband Interface Model specification. The USB Forum must certify these drives for compliance (when that certification becomes available for mobile broadband devices).

The following table summarizes the additional features, as specified by NDIS, that we recommend the firmware support.

|                       |                      |
|-----------------------|----------------------|
| Feature               | Remarks              |
| No pause on suspend   | Support              |
| Wake reason reporting | Support              |
| USB SS                | Support if USB-based |
| Radio Management      | Support              |
| Fast dormancy         | Support              |

 

## Drivers and software


Non-USB devices for GSM and CDMA use IHV developed Mobile broadband drivers. The drivers must comply with the Mobile Broadband Driver Model. IHV developed MB drivers must pass the tests for validating mobile broadband functionality, such as the [Windows Hardware Lab Kit (HLK)](https://msdn.microsoft.com/library/windows/hardware/dn930814.aspx) tests.

-   Devices based on the SPI peripheral bus need SPI driver support from the platform vendor. The SPI driver must comply with the features listed in [Simple Peripheral Bus (SPB)](simple-peripheral-bus--spb-.md).
-   USB devices must be compliant with the Microsoft USB driver stack and not require any IHV or third-party drivers for the mobile broadband functionality.

## Multi-device


1.  In case of MB+GNSS multi-function device, the GNSS driver can be an IHV driver as compliant with GNSS and power requirements. Please see GNSS requirements for more information.
2.  No serial port devices enumerated for the purposes of diagnostics, device management or firmware upgrade etc. in the final production image.
3.  Serial/AT command port, for the purposes of Mobile Network Operator (MNO) certification or OEM factory diagnostics/configuration should be supported through an IHV driver stack solution only. This IHV software stack / port drivers must be removed in the shipping image.
4.  No IHV software based function SKUs to enumerate (or not enumerate) other functions in case of multi-function MB device solutions.
5.  All functions, including any virtual functions (where needed) that are enumerated by the MB device need to be power-aware and respect Connected Standby and power states and cannot use legacy, non-power aware or non-PnP friendly solutions.

## Connection Manager


We recommend using Connection Manager. There is no additional Connection Manager software necessary for the operation of mobile broadband devices. Value-add Mobile Broadband Connection Managers, if implemented, must implement the [Mobile Broadband API](http://go.microsoft.com/fwlink/p/?LinkId=624991).

The mobile broadband driver or device must implement the power management optimizations as required by NDIS.

## Behavior in ACPI S3


If the platform supports ACPI S3 operating system power states, wireless devices shall not be removed from the bus upon entering S3.

## Mechanical


### SIM Card

If the device features a SIM card, we recommend the SIM card socket is accessible to the user to ease the activation process. If the device has a removable battery pack, the recommended location is inside the battery compartment.

### Antenna

-   In any direction, the gain of at least one antenna attached to the device should be better than -15dBi.
-   The directive gain in any direction should not be higher than 6dB.
-   The antenna efficiency should be better than 50%. Antenna efficiency includes losses due to impedance mismatch and the radiation efficiency.

## Related topics


[Mobile Broadband Interface Model (MBIM) specification](http://www.usb.org/developers)

[Windows.Devices.Sms namespace](http://go.microsoft.com/fwlink/p/?LinkId=227371)

[Windows.Networking.NetworkOperators namespace](http://go.microsoft.com/fwlink/p/?LinkId=227373)

[Mobile Broadband (MB) Design Guide](http://go.microsoft.com/fwlink/p/?LinkID=227334)

[Mobile Broadband (MB) Reference](http://go.microsoft.com/fwlink/p/?LinkId=227335)

[Overview of Mobile Broadband Windows Runtime API](http://go.microsoft.com/fwlink/p/?LinkId=624965)

[Device App Lifecycle](http://go.microsoft.com/fwlink/p/?LinkId=624966)

[Windows Hardware Compatibility Program](https://msdn.microsoft.com/library/windows/hardware/dn922588.aspx)

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bp_WEG_Hardware\p_weg_hardware%5D:%20Mobile%20Broadband%20%20RELEASE:%20%285/9/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")




