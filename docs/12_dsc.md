# 12 — Marine VHF DSC (channel 70): research and test plan

**Status: NOT DEPLOYED.** A DSC receiver is not included in the Compose stack yet. A working, validated ARM64 headless decoder + SDR input path must be selected and tested first. Do not invent or download unverified container images.

## Requirements

- Dedicated third SDR, planned serial `dsc`
- VHF channel 70: **156.525 MHz** receive-only
- Marine VHF antenna; suitable filtering if necessary
- Decoder implementing the appropriate VHF DSC waveform (1200-baud FFSK, not a generic AIS decoder)
- Structured local event log: received UTC time, raw payload, decoded fields, MMSI, classification, validation/error status, software/build version
- Replay of known-good legal synthetic signals or recordings for regression tests

## Candidate decoders

1. [SDRangel DSC demodulator](https://github.com/f4exb/sdrangel/blob/master/plugins/channelrx/demoddsc/readme.md): documented VHF DSC decoding; evaluate ARM64/headless/API operation.
2. [GopherTrunk DSC](https://github.com/MattCheramie/GopherTrunk): inspect implementation and test evidence, ARM64 build/dependencies and read-only use before adoption.
3. [COAA DSCdecoder](https://www.coaa.co.uk/dscdecoder.htm): another reference implementation, but GUI/host compatibility need scrutiny.

No candidate is declared production ready for this specific Pi 5.

## Acceptance tests (required before adding DSC to Compose)

- Verify kernel/Ubuntu/ARM64 compatibility and licensing.
- Confirm the decoder selects only the `dsc` SDR.
- Confirm tuned centre frequency and demodulator settings.
- Decode a documented known-good VHF DSC test recording with expected fields.
- Reject or mark corrupted frames; no fabricated MMSI/position from decode errors.
- Confirm UTC timestamps and structured message retention.
- Cold reboot/replug test; logs and resource use; coexistence with live ADS-B and AIS.
- Confirm observer is not presented as an approved maritime distress receiver.

## Important safety limitations

A hobby SDR station is **not GMDSS-approved distress equipment** and must not be used as the sole maritime emergency alerting channel. Do not transmit test/distress DSC calls over the air from this project. Data retention, disclosure and downstream notifications need a deliberate policy. A missed call is possible; lack of an alert is never evidence of no distress.
