# Asterisk-CallerID-Spoofing-PoC
# extensions_custom.conf (proof-of-concept-custom)

## Purpose
This custom Asterisk dialplan implements a simple proof-of-concept call spoofing flow and call forwarding to a local extension.

## What the custom conf does (exactly)
1. Defines an included context `[from-internal-custom]` which includes the `[proof-of-concept-custom]` dialplan.
2. In `[proof-of-concept-custom]`, an extension `12345` answers the call.
3. Plays a short welcome message (`pbx-transfer`) and prompts the caller to enter a spoof number (`please-enter-your` + `number`).
4. Reads the spoof number into the variable `SPOOF` (up to 12 digits, 5 seconds timeout).
5. If a spoof number was provided:
   - Sets `CALLERID(num)` to `+${SPOOF}`.
   - Sets `CALLERID(name)` to `+${SPOOF}`.
   - Sets `CDR(outbound_cnum)` to `+${SPOOF}`.
   If no spoof number provided, no changes to caller ID are made.
6. Prompts the caller to enter a target extension and reads it into `TARGET` (up to 4 digits, 5 seconds timeout).
7. If `TARGET` is empty, plays `vm-goodbye` and hangs up.
8. Plays confirmation prompts (`to-call`, `if-this-is-correct`, `1-yes-2-no`) and reads a single-digit confirmation `CONFIRM`.
9. If `CONFIRM` equals `1`, proceeds to `do_call`; otherwise goes to `hangup`.
10. `do_call` logs a `NoOp` with the target and current `CALLERID(num)`, plays `auth-thankyou`, then dials `PJSIP/${TARGET}` for 30 seconds and hangs up.
11. `hangup` plays `vm-goodbye` and hangs up.

## Files
- `extensions_custom.conf` — contains the dialplan shown (context names: `from-internal-custom`, `proof-of-concept-custom`).

## Setup Guide
These guides were followed to build and configure this proof-of-concept:
- [How to Build Your Own Caller ID Spoofer – Part 1](https://www.rapid7.com/blog/post/2018/05/24/how-to-build-your-own-caller-id-spoofer-part-1/)
- [How to Build Your Own Caller ID Spoofer – Part 2](https://www.rapid7.com/blog/post/2018/07/12/how-to-build-your-own-caller-id-spoofer-part-2/)
