# X6h trailing output and disconnect timing

On a Raspberry Pi Zero 2 W running Debian GNU/Linux 13.7 (trixie), BlueZ 5.82,
an X6h intermittently omitted the final printed rows or trailing paper feed.
The X6 transport uses writes without response, waits after the feed command,
and then disconnects. There is no documented print-completion notification.

Increasing the post-feed wait from 500 ms to 5 seconds resolved the reported
missing trailing output in repeated user tests. The user subsequently confirmed
complete illustrated Markdown jobs, feed, and an end-to-end QR hunt on
2026-09-17. The accepted application settings were density 6 and feed 64, with
BlueZ `ControllerMode = le`. Exact printer firmware, battery level, trial counts,
and before/after HCI traces were not recorded.

This is a hardware-tested workaround, not proof of a universal timing requirement.
It changes no packets or inter-packet pacing. It applies to every device using
the X6 protocol, including browser consumers of the core state machine, and adds
approximately 4.5 seconds per job (also between copies). LX-D02 behavior is
unchanged. The test pins the wait before `Done` so callers cannot disconnect
before the allowance expires.

## Reproducing and evaluating the tradeoff

Use the same content and print options for sequential runs with the old and new
wait. Include a recognizable final line followed by a tear rule and feed. Check
both the final line and blank paper, and let each request finish before the next.
Record printer model, firmware if known, OS, BlueZ version, options, elapsed time,
and logs. No precise success-rate claim is made from the available observations.

Five seconds has not been established as the minimum necessary delay or verified
on all X6 variants and platforms. A configurable allowance or a measured drain
mechanism may be preferable long term; those need further hardware evidence.
If output still truncates, inspect flow control and feed delivery before simply
increasing this timeout again.
