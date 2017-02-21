---
title: Querying TV Connector and Copy Protection Hardware
description: Querying TV Connector and Copy Protection Hardware
ms.assetid: 7812a3ba-42f1-4872-bfe8-08933802f0c1
keywords: ["TV connector WDK video miniport", "copy protection WDK video miniport , querying"]
---

# Querying TV Connector and Copy Protection Hardware


## <span id="ddk_querying_tv_connector_and_copy_protection_hardware_gg"></span><span id="DDK_QUERYING_TV_CONNECTOR_AND_COPY_PROTECTION_HARDWARE_GG"></span>


A video miniport driver for an adapter that has a TV connector must process the [**IOCTL\_VIDEO\_HANDLE\_VIDEOPARAMETERS**](https://msdn.microsoft.com/library/windows/hardware/ff567805) request in its [*HwVidStartIO*](https://msdn.microsoft.com/library/windows/hardware/ff567367) function. When the IOCTL request is IOCTL\_VIDEO\_HANDLE\_VIDEOPARAMETERS, the **InputBuffer** member of the [**VIDEO\_REQUEST\_PACKET**](https://msdn.microsoft.com/library/windows/hardware/ff570547) structure points to a [**VIDEOPARAMETERS**](https://msdn.microsoft.com/library/windows/hardware/ff570173) structure. The **dwCommand** member of that VIDEOPARAMETERS structure specifies whether the miniport driver must provide information about the TV connector (VP\_COMMAND\_GET) or apply specified settings to the TV connector (VP\_COMMAND\_SET).

When the **dwCommand** member of the VIDEOPARAMETERS structure is VP\_COMMAND\_GET, the miniport driver must do the following:

-   Verify the **Guid** member of the VIDEOPARAMETERS structure.

-   For each capability that the TV connector supports, set the corresponding flag in the **dwFlags** member of the VIDEOPARAMETERS structure.

-   For each flag set in the **dwFlags** member, assign values to the corresponding members of the VIDEOPARAMETERS structure to indicate the capabilities and current settings associated with that flag. See the [**VIDEOPARAMETERS**](https://msdn.microsoft.com/library/windows/hardware/ff570173) reference page for a list of structure members that correspond to a given flag.

The **dwMode** member of the VIDEOPARAMETERS structure specifies whether the TV output is optimized for video playback or for displaying the Windows desktop. A value of VIDEO\_MODE\_TV\_PLAYBACK specifies that the TV output is optimized for video playback (that is, flicker filter is disabled and overscan is enabled). A value of VIDEO\_MODE\_WIN\_GRAPHICS specifies that the TV output is optimized for Windows graphics (that is, maximum flicker filter is enabled and overscan is disabled).

In response to VP\_COMMAND\_GET, the miniport driver must set the VP\_FLAGS\_TV\_MODE flag in **dwFlags** and must set the VP\_MODE\_WIN\_GRAPHICS bit in **dwAvailableModes**. Setting the VP\_MODE\_TV\_PLAYBACK bit in **dwAvailableModes** is optional. In addition, the miniport driver must set the VP\_FLAGS\_MAX\_UNSCALED flag in **dwFlags** and must assign values to the corresponding members of the VIDEOPARAMETERS structure.

In response to VP\_COMMAND\_GET, if the TV output is currently disabled, the miniport driver should set **dwMode** to 0, set **dwTVStandard** to VP\_STANDARD\_WIN\_VGA, and set **dwAvailableTVStandard** to VP\_STANDARD\_WIN\_VGA.

Example 1: An adapter supports TV output, which is currently disabled. The miniport driver must do the following in response to VP\_COMMAND\_GET:

-   In **dwFlags**, set VP\_FLAGS\_TV\_MODE, VP\_FLAGS\_TV\_STANDARD, and all other flags that represent capabilities supported by the TV connector.

-   Set **dwMode** to 0.

-   In **dwAvailableModes**, set VP\_MODE\_WIN\_GRAPHICS. If the hardware supports VP\_MODE\_TV\_PLAYBACK, set that bit also.

-   Set **dwTVStandard** to VP\_TV\_STANDARD\_WIN\_VGA.

-   In **dwAvailableTVStandard**, set all bits that represent TV standards supported by the TV connector.

-   For all flags set in **dwFlags** (other than VP\_FLAGS\_TV\_MODE and VP\_FLAGS\_TV\_STANDARD, which have already been discussed), assign values to the corresponding members of the VIDEOPARAMETERS structure.

Example 2: To enable TV output, the caller (not the miniport driver) should do the following:

-   In **dwFlags**, set VP\_FLAGS\_TV\_MODE and VP\_FLAGS\_TV\_STANDARD. Clear all other flags.

-   Set **dwMode** to either VP\_MODE\_WIN\_GRAPHICS or VP\_MODE\_TV\_PLAYBACK. Do not set both bits.

-   Set **dwTvStandard** to the desired standard (for example VP\_TV\_STANDARD\_NTSC\_M). Do not set any other bits in **dwTvStandard**.

Example 3: To disable TV output, the caller (not the miniport driver) should do the following:

-   In **dwFlags**, set VP\_FLAGS\_TV\_MODE and VP\_FLAGS\_TV\_STANDARD. Clear all other flags.

-   Set **dwMode** to 0.

-   In **dwTvStandard**, set VP\_TV\_STANDARD\_WIN\_VGA. Clear all other bits in **dwTvStandard**.

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[display\display]:%20Querying%20TV%20Connector%20and%20Copy%20Protection%20Hardware%20%20RELEASE:%20%282/10/2017%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")



