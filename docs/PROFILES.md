# File formats of the Windows program output

The Windows program from the SD card offers 11 profiles in the UI: F1, F2, F3, F4, F5, F-360A, F-360B, F-360C, F-Mini11, F-Mini18, F-Mini20.

This table outlines the format differences and the magic number associated with each format.

**REMINDER** only F1 geometry has been implementation- and display-verified. The *non-F1* radial/canvas values are strong structural inferences from payload and all-white record patterns.
```
Profile   Magic  Hdr.  Payload  Slot    Radial  Canvas  Geometry
F1        EE31   124   137280   138880  116     232     Confirmed
F2        EE32   40    168480   170080  144     288     Strong
F3        EE33   60    234000   235600  200     400     Strong
F4        EE31   384   175500   177100  171     342     Strong
F5        EE31   460   156000   157600  133     266     Strong
F-360A    EE35   788   93600    95200   80      160     Strong
F-360B    EE36   256   168480   170080  144     288     Strong
F-360C    EE38   644   234000   235600  200     400     Strong
F-Mini11  EE37   908   27000    28600   ?       ?       Unknown
F-Mini18  EE34   504   54000    55600   ?       ?       Unknown
F-Mini20  EE39   528   64800    66400   ?       ?       Unknown
```

