# File formats of the Windows program output

## Profiles 
The Windows program from the SD card offers 11 profiles in the UI: F1, F2, F3, F4, F5, F-360A, F-360B, F-360C, F-Mini11, F-Mini18, F-Mini20.

This table outlines the format differences and the magic number associated with each format. The header lengths below are values observed in this specific sample set - they aren't fixed profile constants.

**REMINDER** only F1 geometry has been implementation- and display-verified. The *non-F1* radial/canvas values are strong structural inferences from payload and all-white record patterns.  The model "F1" designation by the mobile app connection to the fan confirms that the fan identifies itself as "F1".

| Profile  | Magic | Obs. Hdr. | Payload | Slot   | Angular res. | Radial | Canvas | Geometry  |
|----------|-------|-----------|---------|--------|--------------|--------|--------|-----------|
| F1       | EE31  | 124       | 137280  | 138880 | 520          | 116    | 232    | Confirmed |
| F2       | EE32  | 40        | 168480  | 170080 | 520          | 144    | 288    | Strong    |
| F3       | EE33  | 60        | 234000  | 235600 | 520          | 200    | 400    | Strong    |
| F4       | EE31  | 384       | 175500  | 177100 | 450          | 171    | 342    | Strong    |
| F5       | EE31  | 460       | 156000  | 157600 | 520          | 133    | 266    | Strong    |
| F-360A   | EE35  | 788       | 93600   | 95200  | 520          | 80     | 160    | Strong    |
| F-360B   | EE36  | 256       | 168480  | 170080 | 520          | 144    | 288    | Strong    |
| F-360C   | EE38  | 644       | 234000  | 235600 | 520          | 200    | 400    | Strong    |
| F-Mini11 | EE37  | 908       | 27000   | 28600  | ?            | ?      | ?      | Unknown   |
| F-Mini18 | EE34  | 504       | 54000   | 55600  | ?            | ?      | ?      | Unknown   |
| F-Mini20 | EE39  | 528       | 64800   | 66400  | ?            | ?      | ?      | Unknown   |

* Payload and Slot are in bytes
* Slot = Payload + 1,600
* Radial is positions per angular line
* Canvas is the inferred Cartesian diameter

## How it was got

A video file was created at 20 FPS, starting with 1 second of completely filled black, then switching to 1 second of white, then back to 1 second of black.  This was converted via the Windows app into the 11 different profiles, each of which produced the following 60 stored frames:

* 19 black
* 20 white
* 21 black

## Known unknowns and unknown unknowns

Known unknowns: For every non-F1 profile, real devices are still required to verify orientation, sampling centers, angular ordering, and detailed record packing. Geometry for "Mini" profiles is unresolved.  Behavior of non-F1 profiles on the F1 device is unresolved.

Unknown unknowns: Probably, yes.

## Thank you for coming to my LED talk

Remember: The table above is for distinguishing profiles or payload layouts - not devices.  Again, you can clearly see that the same magic number EE31 itself is not enough to distinguish between different layouts of F1, F4, and F5.

You can't just pull radial geometry out of the source sequence by itself. The inference came from putting these together:
* The 100000-byte trailer
* The 1600-byte auxiliary block per frame
* The known 60-frame count
* Resulting fixed payload sizes
* Repeating all-white record patterns;
* The (assumed) six-bitplane organization for the non-F1 profiles

Specifically for F4 and F5, the leading padding and set-bit counts yield strong evidence for 171 and 133 radial positions. For profiles whose white payload is entirely 0xFF, record boundaries are less directly visible, so their 520-angle geometry stays an inference based on structure - rather than an equivalent direct observation.

