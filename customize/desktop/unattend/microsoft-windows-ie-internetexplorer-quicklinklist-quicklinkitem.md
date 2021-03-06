---
title: QuickLinkItem
description: QuickLinkItem
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: 2c525eb2-440e-4aef-bffd-f405d41b6622
ms.mktglfcycl: deploy
ms.sitesec: msdn
ms.author: alhopper
ms.date: 05/02/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-oem
---

# QuickLinkItem


`QuickLinkItem` specifies the unique ID, the friendly site name that is displayed on the **Favorites** toolbar, and an associated Uniform Resource Locator (URL).

## Child Elements


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>[QLID](microsoft-windows-ie-internetexplorer-quicklinklist-quicklinkitem-qlid.md)</p></td>
<td><p>Specifies a unique ID to associate with a <code>QuickLinkItem</code>.</p></td>
</tr>
<tr class="even">
<td><p>[QuickLinkName](microsoft-windows-ie-internetexplorer-quicklinklist-quicklinkitem-quicklinkname.md)</p></td>
<td><p>Specifies a friendly name for the <code>QuickLinkItem</code>, as it appears on the <strong>Favorites</strong> toolbar.</p></td>
</tr>
<tr class="odd">
<td><p>[QuickLinkUrl](microsoft-windows-ie-internetexplorer-quicklinklist-quicklinkitem-quicklinkurl.md)</p></td>
<td><p>Specifies a URL value to associate with a <code>QuickLinkItem</code>.</p></td>
</tr>
</tbody>
</table>

 

## Valid Passes


specialize

## Parent Hierarchy


[Microsoft-Windows-IE-InternetExplorer](microsoft-windows-ie-internetexplorer.md) | [QuickLinkList](microsoft-windows-ie-internetexplorer-quicklinklist.md) | **QuickLinkItem**

## Applies To


For the list of the supported Windows editions and architectures that this component supports, see [Microsoft-Windows-IE-InternetExplorer](microsoft-windows-ie-internetexplorer.md).

## XML Example


The following XML output shows how to configure a [QuickLinkList](microsoft-windows-ie-internetexplorer-quicklinklist.md).

```
<QuickLinkList>
   <QuickLinkItem>
      <QLID>0</QLID>
      <QuickLinkUrl>http://www.fabrikam.com/QL.htm</QuickLinkUrl>
      <QuickLinkName>Fabrikam Quick Link</QuickLinkName>
   </QuickLinkItem>
   <QuickLinkItem>
      <QLID>1</QLID>
      <QuickLinkUrl>http://www.fabrikam.com/QL2.htm</QuickLinkUrl>
      <QuickLinkName>Fabrikam Quick Link 2</QuickLinkName>
   </QuickLinkItem>
</QuickLinkList>
```

## Related topics


[QuickLinkList](microsoft-windows-ie-internetexplorer-quicklinklist.md)

 

 







