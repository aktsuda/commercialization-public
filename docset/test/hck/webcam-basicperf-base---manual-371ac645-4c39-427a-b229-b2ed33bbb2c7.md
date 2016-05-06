---
author: joshbax-msft
title: Webcam BasicPerf base (Manual)
description: Webcam BasicPerf base (Manual)
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 9a118847-974b-45d7-9d43-568d84dac3c8
---

# Webcam BasicPerf base (Manual)


This test verifies that the webcam latency meets the requirements detailed below.

## Test details


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>Associated requirements</strong></p></td>
<td><p>Device.Streaming.Webcam.Base.AVStreamWDMAndInterfaceRequirements Device.Streaming.Webcam.Base.BasicPerf Device.Streaming.Webcam.Base.DirectShowAndMediaFoundation Device.Streaming.Webcam.Base.KSCategoryVideoCameraRegistration Device.Streaming.Webcam.Base.MultipleClientAppSupport Device.Streaming.Webcam.Base.ReportingCameraLocation Device.Streaming.Webcam.Base.SurpriseRemoval Device.Streaming.Webcam.Base.UsageIndicator Device.Streaming.Webcam.Base.WindowsCaputureInfrastructureCameraSupport Device.Streaming.Webcam.H264.H264Support</p>
<p>[See the device hardware requirements.](http://go.microsoft.com/fwlink/p/?linkid=254483)</p></td>
</tr>
<tr class="even">
<td><p><strong>Platforms</strong></p></td>
<td><p>Windows RT (ARM-based) Windows 8 (x64) Windows 8 (x86) Windows RT 8.1 Windows 8.1 x64 Windows 8.1 x86</p></td>
</tr>
<tr class="odd">
<td><p><strong>Expected run time</strong></p></td>
<td><p>~2 minutes</p></td>
</tr>
<tr class="even">
<td><p><strong>Categories</strong></p></td>
<td><p>Certification Reliability</p></td>
</tr>
<tr class="odd">
<td><p><strong>Type</strong></p></td>
<td><p>Manual</p></td>
</tr>
</tbody>
</table>

 

## Running the test


Before you run the test, complete the test setup as described in the test requirements: [Webcam Testing Prerequisites](webcam-testing-prerequisites.md).

During the test, a command prompt window appears on the test computer. You must respond to the prompts displayed. During the test, you must point the webcam at the screen. The test succeeds if the webcam captures an image on screen within the acceptable latency period.

For internal cameras:

-   A mirror should reflect the monitor to the internal camera.

-   A speaker must be close to the camera microphone.

For external cameras:

-   The external camera needs to be pointed to the monitor.

-   A speaker must be close to the camera microphone.

**Note**  
It is acceptable to attach a HDMI Audio playback endpoint for this test; however, the test does not explicitly test for this condition.

 

## Troubleshooting


For troubleshooting information, see [Troubleshooting Device.Streaming Testing](troubleshooting-devicestreaming-testing.md).

If this test fails, review the test log from Windows Hardware Certification Kit (Windows HCK) Studio.

## More information


### Parameters

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>WDKDeviceID</p></td>
<td><p>Specifies the Plug and Play (PnP) ID of the device to be tested.</p>
<p>Example: \\?\root#media#0001#{65e8773d-8f56-11d0-a3b9-00a0c9223196}\global</p></td>
</tr>
</tbody>
</table>

 

### Command syntax

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>Command option</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><strong>MFMessenger.exe -m color -h “[WDKDeviceID]”</strong></p></td>
<td><p>Runs the test.</p></td>
</tr>
</tbody>
</table>

 

### File list

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>File</th>
<th>Location</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>MFMessenger.exe</p></td>
<td><p><em>&lt;testbinroot&gt;</em>\nttest\multimediatest\Performance\MFMessenger\</p></td>
</tr>
<tr class="even">
<td><p>audiostreaming.dll</p></td>
<td><p><em>&lt;testbinroot&gt;</em>\nttest\multimediatest\avcore\audio\bin\</p></td>
</tr>
</tbody>
</table>

 

 

 





