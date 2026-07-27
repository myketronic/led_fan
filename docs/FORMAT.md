# The Missyou .bin image file format

All of the profiles examined use the same organization, basically.  So here: below, H is for the length of the preamble, P is the (profile-speicific) visual payload size, and S is P + 1600, the frame slot size, then N is the number of stored frames.

```
offset 0               Preamble (H bytes; begins with EE xx)
offset H + k * S       Visual payload for frame "k" (P bytes)
offset H + k * S + P   The auxilliary/ audio block (1600 bytes)
offset H + N * S       zeroes trailer (100000 bytes)
```

So the total file size = H + N * (P + 1600) + 100000

## The Preamble
The preamble begins with a two-byte magic value for a profile.  
* **NOTE** the "EE31" magic vlue is shared by "F1", "F4", and also "F5".

The remaining bytes show a structure, some kind of XOR-based pattern that doesn't seem to relate to the content of the image.  

So, using zero-based offsets:
* Offsets 0–1 are the profile magic, such as EE31.
* Offset 3 acts like a one-byte XOR mask, lets say "K".
* For every tested preamble, byte[i] XOR K at offsets 3 on shows the same deterministic byte stream - truncated at the selected preamble length.
* Preamble lengths are multiples of four.
* byte[2] XOR K varies with preamble length and maybe per profile. It could contain control information, maybe? Its exact meaning's undecoded.

For example, normalizing the red image file preamble produces:

```00 C3 C0 C2 00 C6 C0 C7 00 05 C0 04 ...```

Every other tested profile produces that same sequence over the shared length.

There's good evidence from two unrelated F1 files having 128-byte preambles. Their masks are different but XOR-normalizing them makes every byte from offset 2 on identical. So the preamble doesn't seem to describe the image content, but it also not just meaningless random padding.

Bottom line: Reusing a complete known-good preamble is a viable approach here.

Important: Because the preamble-generation and/or controller metadata encoded in it haven't been decoded, don't assume the header length is constant or that EE31 applies only to the F1 profile.

## Trailer
Every Windows-generated file examined ends with exactly 100000 zero bytes.  I've not tried removing or resizing the trailer.

## The visual payload

These are the fixed dimensions - for the F1/EE31 payload

```
Cartesian working canvas   232 x 232 RGB pixels
Angular positions          520 per revolution
Radial positions           116 per angular position
Color channels             3: they're serialized B-G-R
Channel precision          6-bit code: 0 through 63
Bitplanes                  6: most significant bit first
Record size                44 bytes
Records per frame          520 x 6 = 3,120
Visual payload             3,120 x 44 = 137,280 bytes
```

## Sampling geometry, Polar

The F1 format samples a 232x232 Cartesian images into 520 angular lines, clockwise.  Angle "0" points to the right.  In each angular line, the samples run from the *outer edge* toward the center.
```
center_x = center_y = 115.65
for angle_index i = 0 .. 519:
    angle  = -2*pi*i/520
    for radial_index j = 0 .. 115:
        radius = 116 - j
        x = floor(115.65 + radius*cos(angle))
        y = floor(115.65 + radius*sin(angle))
        x and y are clamped to 0 .. 231
```
This is how you do it: The non-integer center and radius rules reproduce the black image with the wite lower-right quadrant image exactly. Conventional center coordinates (like 115.5 or 116) don't.

## Record packing

Each angular position produces six consecutive 44-byte records: one for each bitplane. The bitplanes are emitted from channel-code bit 5 down to bit 0.
```
Bit range    Length    Meaning
0-3          4 bits    Zero padding.
4-6          3 bits    Outer radial pixel: blue, green, red.
7-9          3 bits    Next radial pixel: blue, green, red.
...          ...       Keep going inward for all 116 radial pixels.
349-351      3 bits    Innermost radial pixel: blue, green, red.
```
So this gives an exact count: 4 leading bits + 116 x 3 channel bits = 352 bits = 44 bytes. There is no trailing partial byte.

## Serialization order
```
payload = bytearray()
for angle_index in range(520):
    samples = 116 RGB pixels, ordered outer-to-inner
    codes = map each 8-bit R/G/B channel to a 6-bit code
    for bitplane in [5, 4, 3, 2, 1, 0]:
        bits = [0, 0, 0, 0]
        for each radial pixel:
            bits += [B[bitplane], G[bitplane], R[bitplane]]
        payload += pack_bits_msb_first(bits)
```

## The fun part: F1 color encoding

You don't want to just convert an 8-bit source with a simple linear shift: this creates severe posterization.  The Windows converter applies an 8-bit to 6-bit tone LUT.  The LUT effect: shadows are heavily compressed, conventional midtones range is compressed, then the curve rises above linear only the mid-high region (ex.: 192 maps to 56 vs 47 linearly)

The complete LUT:
```
Input +0  +1  +2  +3  +4  +5  +6  +7
00    00  00  00  00  00  00  00  00
08    00  00  00  00  00  00  00  00
10    00  00  00  00  00  00  00  00
18    00  00  00  00  00  00  00  00
20    00  00  00  00  00  00  00  00
28    00  00  00  00  00  00  00  00
30    00  00  00  00  00  00  00  00
38    01  01  01  01  01  01  01  01
40    01  01  01  01  01  01  02  02
48    02  02  02  02  02  03  03  03
50    03  03  03  03  04  04  04  04
58    04  05  05  05  05  06  06  06
60    06  06  07  07  07  07  08  08
68    08  08  09  09  09  0A  0A  0A
70    0B  0B  0B  0B  0C  0C  0D  0D
78    0E  0E  0E  0F  0F  10  10  10
80    11  11  12  12  13  13  14  14
88    15  16  16  17  18  18  19  19
90    1A  1B  1B  1D  1D  1D  1E  1F
98    20  21  22  23  24  24  25  26
A0    27  27  28  29  2A  2B  2C  2D
A8    2E  2E  2F  2F  30  30  30  31
B0    32  32  33  33  34  34  34  34
B8    35  36  36  36  36  37  38  38
C0    38  38  38  38  39  39  39  3A
C8    3A  3A  3A  3B  3B  3B  3B  3B
D0    3C  3C  3C  3C  3C  3C  3C  3D
D8    3D  3D  3D  3D  3E  3E  3E  3E
E0    3E  3E  3E  3E  3E  3E  3E  3E
E8    3F  3F  3F  3F  3F  3F  3F  3F
F0    3F  3F  3F  3F  3F  3F  3F  3F
F8    3F  3F  3F  3F  3F  3F  3F  3F
```

## Still image files

An F1 still-image file contains one frame slot followed by the standard trailer.

F1 still = preamble + 137280-byte payload + 1600 zero bytes + 100000 zero trailer

  with a 108-byte observed preamble:

size = 108 + 137280 + 1600 + 100000 = 238988 bytes

Valid Windows-generated stills have different preamble lengths and therefore (slightly) different total sizes. So, file size alone shouldn't be used to reject an otherwise structurally valid still.

## Recommended image preparation
    •  Flatten transparency over black.
    •  Convert to RGB.
    •  Produce a 232 x 232 canvas by fitting height, fitting width. Or just take a centered crop/sample.
    •  Apply the recovered Windows tone table before polar sampling and bitplane packing.

## Video

Video is just a sequence of complete still-frame payloads. No inter-frame compression is present: every stored frame consumes the *full profile payload* plus 1600 auxiliary bytes. As a result, file size grows linearly, and REALLY fast.

That means that, for the known-good F1 video preamble H = 992:

file size = 100992 + 138880*N bytes

  at 80/9 stored frames per second:

approximately 1234489 bytes/second (1.18 MiB/second)

* "Wait, 80/9 fps?"  Yes.  More on that in a bit.

## Sound and the 1,600-byte auxiliary block
Windows-generated silent video and the factory-provided "rose" animation fill this region with zeros. The factory-provided "Countdown+ T-rex" file fills it with a waveform centered near hexadecimal 80, consistent with unsigned 8-bit mono PCM.

That factory file contains 480 visual frames and 768,000 auxiliary bytes. Interpreting them as 48 kHz unsigned 8-bit PCM yields exactly 16 seconds of source audio: 480 x 1600 / 48000 = 16.

### Just Don't Do it:
The audio plays slowly and with severe stutter. A 1600-sample block represents 33.33 ms at 48 kHz, *but* rotor-paced slots arrive roughly every 112 ms. The factory file does technically demonstrate the field ... but without usable audio sync.

If you're writing a compatible silent encoder, it should continue writing 1600 zero bytes after every visual payload. Audio support with the F1 should be treated as *"experimental"* Maybe consider playing some independent audio started separately instead.

