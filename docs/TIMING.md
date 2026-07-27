# Explanation of rotor-paced playback

## There's no (effective) file-level frame rate

Seriously. Tests with Python-generated files, Windows-generated files, and factory-supplied files show that changing the source video frame rate does not make the fan honor that rate. The hardware just advances stored frames at a rate tied to rotor behavior.

Two factory samples independently exhibited a nominal rate near 80/9 frames per second:
* The 200-frame "rose.bin" loop lasted approximately 22.5 seconds
* The 480-frame "count down.bin" (countdown plus T-rex) loop lasted approximately 54 seconds.

Doing the math

Device calibration from a known frame count: actual frames per second = stored_frame_count / measured_loop_seconds

A nominal rate = 80/9 = 8.888888... frames per second

One-frame-per-revolution equivalent = (80/9)*60 = 533.333... RPM

The RPM value is an equivalent rate that assumes one stored-frame advance per rotor revolution. I didn't independently measure rotor speed and the one-frame-per-revolution relationship.

## Device-specific calibration (F1)

Consistency, wherefore art thou?  It is important to calibrate the device being used - just remember that calibration is going to be a representative average, not a ground-truth fixed number.

A 16000-frame, nominal 30-minute test lasted 1804.78 seconds on the tested unit, implying 8.86535 frames per second for that run. However, later runs showed angular wobble and different short-term rates, while one completed within about one second of the expected duration.

Interpretation: 80/9 is a useful nominal model value, and *not* a guaranteed device constant. Calibration can correct a stable average rate but it can't correct the observed angular phase jitter or run-to-run instability. Possible influences and not established causes are: supply voltage, temperature, and controller regulation.

## Source-frame conversion
A converter should sample source video timestamps at the chosen device rate. A 9 FPS source converted to approximately 8.87 FPS necessarily drops a source frame about every 7.4 seconds. This produces visible timing jitter of up to roughly 111 ms, but interestingly not multi-second cumulative drift when timestamps are correct. A higher-rate source, such as 20 FPS, reduces source quantization, and seems to reduce visible timing jitter.

## Summary
* Encoded frames contain no retained source timestamps; they are consumed sequentially.
* Expected playback duration: stored frame count / measured fan FPS

A calibration procedure should begin after the rotor reaches normal speed, measure a long known-frame-count sequence, and preferably be repeated to reveal run-to-run spread.

At 9 FPS → 8.86535 FPS, the relative frame-count difference is about one source frame every 7.43 seconds. This doesn't create cumulative duration drift when conversion follows timestamps.

A 20 FPS source reduces source-time quantization to at most about 50 ms per selected source frame; it cannot correct the fan’s rotor instability.
