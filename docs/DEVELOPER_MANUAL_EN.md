# PneuMatrix Developer Manual

[한국어](DEVELOPER_MANUAL_KO.md) | [English](DEVELOPER_MANUAL_EN.md)

USB Serial / RS485 protocol · Document v1.3

| Item | Reference |
| --- | --- |
| Document version | 1.3 |
| Date | 2026-09-27 |
| Reference firmware | **v2.8.1** / HW v002 |
| Audience | Control software developers and PLC/PC integrators |
| Scope | Device identification, valve and pressure control, queries, host parsing |

## 1. Quick reference

Prefix every command with one address character and terminate it with LF (`\n`). Address `1` is used in the examples.

| Purpose | Request | Example response |
| --- | --- | --- |
| Device identity | `1?I` | `1?I,TKPM-D11,v002,v2.8.1,PM26J001,100,100,0,0` |
| Open valve 3 | `1o3` | `1O3,ON` |
| Close valve 3 | `1x3` | `1X3,OFF` |
| Close all valves | `1b00` | `1B00,OK` |
| Valve states | `1?V` | `1?V,0,0,0,0,0,0,0,0` |
| Valve bitmask | `1?V/` | `1?V/,00` |
| Set regulator 1 to 20.5 kPa | `1p1,20.5` | `1P1,20.5,OK` |
| Read regulator 1 setpoint | `1?P1` | **`1?P,20.5`** |
| Read regulator 1 measured pressure | `1?A1` | `1?A1,18.3` |
| Read all measured pressures | `1?A` | `1?A,18.3,0.0,DISABLED,DISABLED` |

Pressure values are illustrative. Actual ACK values depend on channel range, calibration, and DAC quantization. **Setpoint (`?P`) and measured pressure (`?A`) are different quantities.**

## 2. Connection and framing

### 2.1 Communication settings

| Item | Value |
| --- | --- |
| Interface | USB Serial |
| Baud rate | 115200 bps |
| Data / parity / stop bits | 8 / none / 1 (8N1) |
| Flow control | None |
| Command encoding | ASCII |
| Request terminator | LF (`\n`); CRLF is also accepted |
| Response terminator | CRLF (`\r\n`) |

- These settings apply to normal control communication. The firmware updater uses separate download-mode settings.
- The USB-connected device forwards commands over RS485 to the device at the requested address.

```text
[one-character address][command body]\n

Example: 1p1,20.5\n
```

- Received CR characters are ignored. LF triggers command processing. Empty lines are ignored.
- Leading/trailing line whitespace is trimmed. Do not insert spaces inside commands or numbers.
- The current buffer stores at most 127 characters, excluding CR/LF. Additional characters are discarded; there is no dedicated overlength error. Hosts must avoid oversized frames.
- There is no request ID or frame checksum. Send one request at a time and process its response or timeout before sending another.

### 2.2 Addressing and USB bridging

| Address | USB behavior | RS485 behavior |
| --- | --- | --- |
| `0` | Targets the directly connected device | Requests addressed to 0 are ignored |
| `1–9`, `A–F` | Executes locally if the address matches; otherwise forwards to RS485 | Executes only requests matching the node address |

Valid node addresses are `1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F`. Hexadecimal `A–F` represents decimal 10 through 15.

Both uppercase and lowercase hexadecimal address letters are accepted; responses use uppercase. Address `0` is **not a broadcast address**. Responses use the request address: `0?I` produces `0?I,...`.

Assign unique addresses to devices on the same RS485 bus. Address changes require direct USB and `0n<address>` (section 9).

### 2.3 Case sensitivity

| Command | Rule |
| --- | --- |
| `o`, `x`, `b`, `p` | Send in **lowercase** |
| `?I`, `?V`, `?A`, `?P` | Either case accepted |
| Set-command ACK | Uppercase `O`, `X`, `B`, `P` |
| Address command `n` | Both `n` and `N` accepted |

For example, `1P1,20` is not a set command. A local USB request produces `ERR-102`; on RS485, uppercase-plus-comma frames can be classified as responses and relayed instead. Preserve command case.

## 3. Identity and channel configuration

### 3.1 Device identity — `[ADDR]?I`

```text
TX 1?I
RX 1?I,TKPM-D11,v002,v2.8.1,PMABCD001,100,100,0,0
```

| Field after `1?I` | Meaning |
| --- | --- |
| 1 | Model name |
| 2 | Hardware version |
| 3 | Running firmware version |
| 4 | Device serial number |
| 5–8 | Regulator 1–4 maximum pressure, integer kPa |

- Model, hardware version, serial number, and channel ranges use the values stored on the device. The firmware version identifies the running image.
- The model and serial number above are examples. Using these example values in your application can cause errors.
- A maximum pressure of `0` means **not installed/disabled by configuration**.
- Send `?I` immediately after connecting. If no valid profile exists, identity fields may contain `UNPROVISIONED`. Do not begin normal operation; contact the manufacturer for support.

### 3.2 Channel availability

```text
Active channels = available pressure regulation options (depending on the purchased configuration or subsequent device upgrades).
```

- Setting a disabled channel with `p` returns `ERR-301`.
- Individual `?A`, `?A/`, `?P`, and `?P/` queries return `DISABLED` for disabled channels.
- All-channel `?A` returns `DISABLED` in the corresponding channel position.
- `?I` reports stored maximum pressures.

## 4. Valve control

The device controls eight valves. Valve queries report output command states, not physical valve-position feedback.

### 4.1 Individual valves — `[ADDR]o[N]`, `[ADDR]x[N]`

`N` is 1 through 8.

```text
1o3  -> 1O3,ON
1x3  -> 1X3,OFF
```

### 4.2 Bitmask — `[ADDR]b[HEX]`

Send an 8-bit hexadecimal mask. Bit 0 controls valve 1.

```text
1b00 -> 1B00,OK       All OFF (0b00000000 = 0x00)
1b05 -> 1B05,OK       Valves 1 and 3 ON; others OFF (0b00000101 = 0x05)
1bFF -> 1BFF,OK       All ON (0b11111111 = 0xFF)
```

Do not include a `0x` prefix. The ACK mask uses two uppercase hexadecimal digits.

### 4.3 State queries — `[ADDR]?V`, `[ADDR]?V/`

```text
1?V  -> 1?V,1,0,1,0,0,0,1,0
1?V/ -> 1?V/,45
```

The eight fields are valves 1 through 8; 1 means ON, 0 means OFF. Both examples describe the same state.

Without a provisioned profile, relay outputs are forced OFF. Set-command ACKs may still echo the request, so check `?V` rather than assuming the ACK proves the output was applied.

## 5. Pressure commands and setpoint queries

### 5.1 Set pressure — `[ADDR]p[REG],[VALUE]`

| Item | Rule |
| --- | --- |
| `REG` | 1 through 4 |
| `VALUE` | Nonnegative decimal pressure in kPa |
| Resolution | 0.1 kPa |
| Extra decimal places | Round at the second decimal digit |
| Normal-control minimum | Rounded values below 1.0 kPa produce DAC raw 0 |
| Maximum | Limited by the channel range and output calibration |

```text
1p1,20      -> 1P1,20.0,OK
1p2,20.55   -> 1P2,20.6,OK
1p1,0.5     -> 1P1,0.0,OK
1p1,0       -> 1P1,0.0,OK
```

- The normal response format is `[ADDR]P[REG],[applied setpoint],OK`.
- The shorthand `1p20.5` sets regulator 1 to 20.5 kPa. `1p,20.5` is not valid shorthand. New implementations should always specify the channel.
- An integer part is required. `-1`, `+1`, `.5`, `1.`, `1e2`, and `10xyz` are invalid. Do not use inputs outside the device's supported range.
- Operation below 1 kPa is not supported to prevent device malfunction.

### 5.3 Range and calibration

- Read each channel's maximum pressure from `?I`.
- Output conversion uses a stored per-channel calibration table or the applicable default calibration. Pressure control is calibrated before shipment.

### 5.4 Setpoint query — `[ADDR]?P[REG]`

**An enabled regulator 1 omits its channel digit in the response.**

| Request | Enabled response example | Disabled response |
| --- | --- | --- |
| `1?P`, `1?P0`, `1?P1` | `1?P,20.5` | `1?P1,DISABLED` |
| `1?P2` | `1?P2,20.5` | `1?P2,DISABLED` |
| `1?P3` | `1?P3,20.5` | `1?P3,DISABLED` |
| `1?P4` | `1?P4,20.5` | `1?P4,DISABLED` |

Pressure values always have one decimal place.

## 6. Sensor queries

### 6.1 All measured pressures — `[ADDR]?A`

```text
1?A -> 1?A,18.3,0.4,DISABLED,DISABLED
```

Four fields correspond to regulators 1 through 4. Enabled channels return calibrated pressure in kPa with one decimal place. Uninstalled or disabled channels return `DISABLED`.

### 6.2 One measured pressure — `[ADDR]?A[REG]`

```text
1?A1 -> 1?A1,18.3
1?A3 -> 1?A3,DISABLED
```

`REG` is 1 through 4. Unlike `?P`, even an enabled regulator 1 includes its digit. `?A0` is invalid.


## 7. Responses and errors

### 7.1 Basic error responses

```text
ERR-301
1?P3,DISABLED
```

- Error lines have no device address prefix.
- Parse the required leading fields of normal responses and allow additional error descriptions.

### 7.2 Error codes

| Code | Meaning |
| --- | --- |
| `ERR-101` | Invalid address/body framing; replied on USB, silently dropped on RS485 |
| `ERR-102` | Unsupported command |
| `ERR-201` | Invalid valve number (valid: 1 through 8) |
| `ERR-202` | Invalid regulator number or malformed `?A`/`?P` query |
| `ERR-203` | Invalid node address (valid: hexadecimal 1 through F) |
| `ERR-204` | Invalid valve mask (valid: hexadecimal 00 through FF) |
| `ERR-205` | Invalid pressure number format |
| `ERR-301` | Attempt to control a disabled regulator |
| `ERR-302` | Query target disabled; primarily an extra field in verbose `DISABLED` responses |
| `ERR-303` | Command requires direct USB and address 0 |

Trailing garbage such as `?P1abc` or `p1,10xyz` is rejected. Oversized frames still follow the buffer limitation in section 2.1.

### 7.3 Boot and RS485 forwarding

- At application initialization, one `?I`-formatted banner using the current node address is sent to USB only. No boot banner is sent on RS485.
- Whether opening USB resets the board depends on hardware, drivers, and DTR/RTS settings. Request `?I` explicitly instead of relying on the banner.
- The USB bridge forwards remote query replies, set-command ACKs, address ACKs, and `ERR-...` lines to USB.
- `ERR-...` lines have no address. When several devices are daisy-chained, connect directly to the device over USB to identify the source of an error.

## 8. Persistence and restart

- The device stores model/hardware/serial information, address, installed channels and maximum pressures, calibration tables, response mode, and output states.
- Valve and pressure control states are saved to internal memory approximately 1500 ms after the last change, provided no further changes occur. This supports automatic recovery after an unexpected power interruption.
- Saved relay and pressure outputs may be restored when power returns.
- Outputs are restricted if valid device information cannot be read. Do not begin normal operation; contact the manufacturer.

## 9. Host implementation sequence

1. Open at 115200/8N1 and assemble received bytes into LF-terminated lines.
2. Request `?I` from the target address and inspect model, hardware, firmware, and channel ranges.
3. Use `?A` or individual queries to determine effective channel availability. Exclude channels with a zero maximum pressure from control.
4. Send output commands individually and handle ACKs, errors, and timeouts.
5. Poll `?V`, `?P[REG]` for enabled channels, and `?A` to update state.
6. Display applied setpoints separately from measurements. Distinguish `DISABLED` from numeric zero.

Example with node 1 and channels 1/2 enabled:

```text
TX 1?I
RX 1?I,TKPM-D11,v002,v2.8.1,PMABC001,100,100,0,0
TX 1p1,20.5
RX 1P1,20.5,OK
TX 1?P1
RX 1?P,20.5
TX 1?A
RX 1?A,18.3,0.4,DISABLED,DISABLED
TX 1?V/
RX 1?V/,00
TX 1p1,0.5
RX 1P1,0.0,OK
TX 1p3,10
RX ERR-301
```
