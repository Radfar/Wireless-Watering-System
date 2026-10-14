# RF Protocol Notes

## Frequency and library

The system is specified as a 433 MHz RF link and uses the RCSwitch Arduino library. Confirm the exact transmitter/receiver module models and supported modulation before production deployment.

## Command encoding

For valve identifier `n`, the controller creates these decimal values:

| Action | Formula | Example for valve 1 |
|---|---|---:|
| Open / ON | `2560 + n` | 2561 |
| Close / OFF | `1280 + n` | 1281 |

The current controller sends each value as a 24-bit RCSwitch transmission twice, with approximately 200 ms delays around the repeated sends. The valve-node code compares received values against the formulas above.

## Known protocol limitations

- No acknowledgement or response path is implemented in the reviewed sketches.
- No cryptographic authentication, encryption, or replay protection is provided.
- Repeated transmission can improve the chance of reception but does not guarantee delivery.
- The current node has a compile-time valve ID (`VAL_NO = 1`).
- Physical valve state is not measured by the RF protocol.

## Commissioning checks

1. Confirm RF transmitter and receiver compatibility and intended 433 MHz operation.
2. Verify that controller and node valve IDs match.
3. Test open and close codes with the valve isolated from water.
4. Record reliable range and failure cases in the actual installation.
5. Do not infer valve movement solely from a transmitted code.
