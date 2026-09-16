# X6h trailing content and feed test

An X6h user reported that the same Markdown curl job sometimes printed all text without feed and sometimes also omitted the tear line. The X6 transport sends writes without response, waits a fixed interval after the feed command, and then disconnects. It has no known print-completion notification.

This diagnostic build increases that final wait from 500 ms to 5 seconds. It leaves raster bytes, feed command, pacing, and Markdown unchanged, so a repeated hardware test can isolate the disconnect delay. Each HTTP request now takes approximately 4.5 seconds longer. This is not a confirmed fix or a guarantee for arbitrary job lengths.

Install the ARM64 artifact from this commit's GitHub Actions push build using the game's installation guide (substitute the new run ID and commit). Keep the prior binary as a rollback copy. Restart printable.service, then repeat the same curl job with feed 120 at least three times sequentially, letting each request finish. Check the END OF PRINT text, tear line, and blank feed every time. Record the JSON response and service log if any job is incomplete.

If the ending remains incomplete, examine flow control and feed delivery rather than further increasing an unverified timeout. If repeatable output returns, the result supports early disconnect as the cause; the five-second allowance can then be tuned on hardware.
