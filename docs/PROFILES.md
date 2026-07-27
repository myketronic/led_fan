# File formats of the Windows program output

The Windows program from the SD card offers 11 profiles in the UI: F1, F2, F3, F4, F5, F-360A, F-360B, F-360C, F-Mini11, F-Mini18, F-Mini20.

This table outlines the format differences and the magic number associated with each format.
```
Profile	    Magic	    Frame payload  Radial resolution
F1	        EE31	    137280         116 → 232 px
F2	        EE32	    168480         144 → 288 px
F3	        EE33	    234000         200 → 400 px
F4	        EE31	    175500         150 → 300 px
F5	        EE31	    156000         approx. 132 → 264 px
F-360A	    EE35	    93600          80 → 160 px
F-360B	    EE36	    168480         144 → 288 px
F-360C	    EE38	    234000         200 → 400 px
F-Mini11    EE37	    27000          Mini-specific geometry
F-Mini18    EE34	    54000          Mini-specific geometry
F-Mini20    EE39	    64800          Mini-specific geometry
```
