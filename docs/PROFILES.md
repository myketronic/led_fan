# File formats of the Windows program output

## Profiles 
The Windows program from the SD card offers 11 profiles in the UI: F1, F2, F3, F4, F5, F-360A, F-360B, F-360C, F-Mini11, F-Mini18, F-Mini20.

This table outlines the format differences and the magic number associated with each format. The header lengths below are values observed in this specific sample set - they aren't fixed profile constants.

**REMINDER** only F1 geometry has been implementation- and display-verified. The *non-F1* radial/canvas values are strong structural inferences from payload and all-white record patterns.  The model "F1" designation by the mobile app connection to the fan further cements this value.

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

So you can clearly see that the same magic number EE31 is shared between different device designations, and in itself is not sufficient to distinguish between different devices.

## How it was got

A video file was created at 20 FPS, starting with 1 second of completely filled white, then switching to 1 second of black, then back to 1 second of white.  This allows for strong inference with non-verified formats.

## Known unknowns and unknown unknowns




