# led_fan
Public domain still-image and video converters for reverse-engineered 3D hologram LED fan formats.

A commonly sold persistence of vision (POV) display device class are LED "fans".  One of the more popular brand for these POV display fans is "Missyou", who makes devices ranging from a sedate two-armed 23 cm diameter device, to a four-armed 100 cm diameter beast.  This project came about from a desire to operate the 23 cm model without using Windows-based software to convert video and images into the fan-compatible .bin file format.  

**PLEASE NOTE** that this project, the author, and the contents of this repository are completely and entirely unaffiliated with "Missyou".  This is not an endorsement of the company or product in any way.  This is a personal project only.


A single Missyou 23 cm device was tested: the fan itself has no markings to indicate a model or serial number.  The box label is marked "MS25-D1(W)", and also “Model: MS2.” The files on the supplied micro SD card start with the byte signature/magic "EE31".  The Windows-based software encoder package on the SD card defaults to model "F1", which produces a file format for this device (magic EE31) that played and displayed correctly on the device. Otherwise, there is no indication in the Windows software package that "F1" is the correct model designation for this device.  One corroboration that "F1" is the correct designation for this device was upon connection of the mobile app to the fan, which displayed "F1" in the upper-right corner and did not allow changing it.

Installation requirements consist of: at least Python 3.10, and Python Pillow (PIL).  The video converter uses the external programs ffmpeg and ffprobe.


# Conversion command examples

Convert a single image:

    missyou_still.py --mode fit-height sample_a.png sample_a.bin

Check how much space a converted video will be:

    missyou_video.py --estimate --fan-fps 8.86535 clock_test.mkv

Convert a video file with a target of 8.86535 FPS:

    missyou_video.py --fan-fps 8.86535 --mode fit-height clock_test.mkv clock_test-8_86535fps.bin

The conversion estimate is important, considering the fan device only seems to recognize FAT32, which supports a maxiumum single file size of 4GB. Technically 4GB - 1 byte.
