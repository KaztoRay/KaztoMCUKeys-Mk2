# KaztoMCUKeys

FIDO2 security-key firmware for an ESP32-S3 NodeMCU with native USB.
The firmware enumerates as **KaztoRay FIDO2 Security Key** and requires a
physical press of the BOOT button for credential creation and authentication.

This repository is a device-specific derivative of
[polhenarejos/pico-fido](https://github.com/polhenarejos/pico-fido) at commit
`b8e5e488cf09fca85f64db7ae9238621e1efb401`. Its vendored
`pico-keys-sdk` source is based on commit
`7892b8ce3adf231815e327cd750702c1d59d8931`. The original documentation is
preserved in [UPSTREAM_README.md](UPSTREAM_README.md).

## Device configuration

- Target: ESP32-S3, revision 0.2
- Flash: 16 MB
- PSRAM: 8 MB
- User-presence button: GPIO0 / BOOT, active low
- USB VID/PID: `303A:0002`
- USB manufacturer: `KaztoRay`
- USB product: `KaztoRay FIDO2 Security Key`
- Interfaces: FIDO HID only; OATH, OTP, CCID, and WCID are disabled
- User-presence timeout: 30 seconds

The checked-in `sdkconfig` and `dependencies.lock` record the configuration
used for the tested firmware. ESP-IDF downloads managed components during the
first build.

## Build

Install and activate ESP-IDF 5.5.1, then run:

```sh
idf.py build
```

The firmware image is generated at `build/pico_fido.bin`.

To erase and flash the board, connect it in download mode by holding BOOT while
plugging in USB, substitute the detected serial device, and run:

```sh
idf.py -p /dev/cu.usbmodemXXXX erase-flash flash
```

Reconnect USB without holding BOOT after flashing. Each registration and login
request must then be approved by briefly pressing BOOT.

## Verified behavior

- CTAP MakeCredential is denied after approximately 30 seconds without a
  physical button press.
- CTAP MakeCredential succeeds after pressing BOOT.
- CTAP GetAssertion succeeds after pressing BOOT.
- The ES256 assertion signature is cryptographically verified.

## Security notes

Secure Boot and Flash Encryption are intentionally not enabled in this
configuration. Enabling either feature modifies one-time-programmable eFuses
and should only be done after validating recovery procedures and securely
backing up signing and encryption keys.

Keep another passkey, recovery codes, or a commercial security key as an
account-recovery method. Do not rely on a single development board as the only
credential for an important account.

The USB VID/PID in this repository is an Espressif development identifier.
Do not distribute compiled devices or firmware under a VID/PID that you do not
own or have permission to use.

## License

This derivative retains the upstream GNU Affero General Public License v3.
See [LICENSE](LICENSE) for the full terms and preserve upstream attribution
when redistributing modified source or firmware.
