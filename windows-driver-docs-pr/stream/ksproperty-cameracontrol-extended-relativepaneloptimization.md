---
title: KSPROPERTY\_CAMERACONTROL\_EXTENDED\_RELATIVEPANELOPTIMIZATION
description: KSPROPERTY\_CAMERACONTROL\_EXTENDED\_RELATIVEPANELOPTIMIZATION is a property ID used to inform the driver of whether the camera is facing front or not, relative to the active display of the application.
keywords: ["KSPROPERTY_CAMERACONTROL_EXTENDED_RELATIVEPANELOPTIMIZATION Streaming Media Devices"]
topic_type:
- apiref
api_name:
- KSPROPERTY_CAMERACONTROL_EXTENDED_RELATIVEPANELOPTIMIZATION
api_location:
- Ksmedia.h
api_type:
- HeaderDef
ms.date: 08/05/2019
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
ms.custom: Vb
---

# KSPROPERTY\_CAMERACONTROL\_EXTENDED\_RELATIVEPANELOPTIMIZATION

**KSPROPERTY\_CAMERACONTROL\_EXTENDED\_RELATIVEPANELOPTIMIZATION** is a property ID used to inform the driver of whether the camera is facing front or not,
relative to the active display of the application.
Windows will set the property when the new WinRT API property PanelBasedOptimizationControl.Panel is set.
Samples for setting KSProperty controls can be found on GitHub here: https://github.com/microsoft/Windows-driver-samples/tree/master/avstream/avscamera

## Usage Summary Table

| Get | Set | Target | Property descriptor type | Property value type |
| --- | --- | --- | --- | --- |
| Yes | Yes | Filter | [KSPROPERTY](https://docs.microsoft.com/previous-versions/ff564262(v=vs.85)) | [KSCAMERA_EXTENDEDPROP_HEADER](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_header)|

## Remarks

The property request contains a [KSCAMERA_EXTENDEDPROP_HEADER](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_header) structure and a [KSCAMERA_EXTENDEDPROP_VALUE](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_value) structure.

The total property data size is `sizeof(KSCAMERA_EXTENDEDPROP_HEADER) + sizeof(KSCAMERA_EXTENDEDPROP_VALUE)`.
The **Size** member of [KSCAMERA_EXTENDEDPROP_HEADER](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_header) is set to this total property data size.

The following are flags that can be placed in the **KSCAMERA_EXTENDEDPROP_HEADER.Flags** and **KSCAMERA_EXTENDEDPROP_HEADER.Capability** fields.

| Relative Panel Optimization mode                           | Description                                                                      |
|------------------------------------------------------------|----------------------------------------------------------------------------------|
| KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_OFF     | Camera will use normal mode of operation                                         |
| KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_ON      | Camera will use optimization relative to a position described in the value field |
| KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_DYNAMIC | Camera location hint can be dynamically adjusted while streaming without glitching the stream |

**KSCAMERA_EXTENDEDPROP_RELATIVEPANELOPTIMIZATION** is always a synchronous control.

Any app can read the property but only apps that have opened the camera for exclusive access can write to the property value.
A suitable error code will be returned if attempts are made to write the property without having exclusive mode access.

In regards to mapping this DDI to the PanelBasedOptimizationControl,
the application using PanelBasedOptimizationControl will set the Panel value,
which Windows will internally use to program the [**KSCAMERA_EXTENDEDPROP_VALUE**](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_value) field of the payload.
The **Capability** and **Flags** field will be controlled by Windows.

If the driver receives a SET operation while the camera device is streaming and the flag **KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_DYNAMIC** is not set,
the driver will return a state-based error.

The table below contains the descriptions and requirements for the [KSCAMERA_EXTENDEDPROP_HEADER](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_header) structure fields when using the metadata control.

<table>
<colgroup>
<col width="30%" />
<col width="70%" />
</colgroup>
<thead>
<tr class="header">
<th>Member</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Version</p></td>
<td><p>This must be 1.</p></td>
</tr>
<tr class="even">
<td><p>PinId</p></td>
<td><p>KSCAMERA_EXTENDEDPROP_FILTERSCOPE (0xFFFFFFFF).</p></td>
</tr>
<tr class="odd">
<td><p>Size</p></td>
<td><p>This must be sizeof(KSCAMERA_EXTENDEDPROP_HEADER)+sizeof(KSCAMERA_EXTENDEDPROP_VALUE),</p></td>
</tr>
<tr class="even">
<td><p>Result</p></td>
<td><p>Indicates the error results of the last SET operation. If no SET operation has taken place, this must be 0.</p></td>
</tr>
<tr class="odd">
<td><p>Capability</p></td>
<td><p>Must be a bit wise OR of the supported <strong>KSCAMERA_EXTENDEDPROP_RELATIVEPANELOPTIMIZATION_XXX</strong> flags as defined above.</p></td>
</tr>
<tr class="even">
<td><p>Flags</p></td>
<td><p>This is a read/write field. This can be either <strong>KSCAMERA_EXTENDEDPROP_RELATIVEPANELOPTIMIZATION_ON</strong> or 
<strong>KSCAMERA_EXTENDEDPROP_RELATIVEPANELOPTIMIZATION_OFF</strong> flags defined above.</p></td>
</tr>
</tbody>
</table>

If **KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_ON** is specified in
the **Flags** field of the [**KSCAMERA\_EXTENDEDPROP\_HEADER**](https://docs.microsoft.com/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-tagkscamera_extendedprop_header),
the **Value.ul** field must specify the PLD for the relative direction the camera is currently facing.
This can be any of the enumeration values for ACPI PLD, but most frequently will be **Front**, **Back** or **Unknown**.

If **KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_OFF** is specified,
for SET operations, the **Value** field is ignored.

For GET operations, the driver must return the direction that the camera is currently programmed for.
If **KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_OFF** is specified,
or if no value has been set, the device’s default PLD must be returned.
If **KSCAMERA\_EXTENDEDPROP\_RELATIVEPANELOPTIMIZATION\_ON** is specified,
the most recently set value must be returned.

## Requirements

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td>Header</td>
<td>Ksmedia.h</td>
</tr>
</tbody>
</table>