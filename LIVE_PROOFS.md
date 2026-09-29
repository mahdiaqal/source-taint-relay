# StudioNet proofs

Contract: [0x0d97a61293727Cc9d438C6213e08139641D94F04](https://explorer-studio.genlayer.com/address/0x0d97a61293727Cc9d438C6213e08139641D94F04)

All transactions below were checked through StudioNet RPC: `status=FINALIZED` and leader `execution_result=SUCCESS`. `genlayer code` returned source equal to `contracts/SourceTaintRelay.py` after newline normalization. Both fixtures were independently fetched from pinned commit `5854053c4a1c1518b489fe66281394ced2fed9d5` and returned HTTP 200.

| Step | Transaction | Observed state |
| --- | --- | --- |
| Deploy matching source | [0xe14aa59a…](https://explorer-studio.genlayer.com/tx/0xe14aa59a019a3d464b43c3f17d3177ee9d31e514bb1ed8aef2dddc5da624093b) | Contract created |
| Register clean pinned source | [0x5c4933e4…](https://explorer-studio.genlayer.com/tx/0x5c4933e44accd5e4449dac3cb24265b5777d05d3c08f0eeeed1be4f2d1ee67be) | `clean-feed` registered |
| Register tainted pinned source | [0x13900ceb…](https://explorer-studio.genlayer.com/tx/0x13900cebe1898cc5c7e6a79badd58760263a7d7b705404eb881cf770120d8865) | `tainted-feed` registered |
| Ingest clean source | [0x1a3a10ee…](https://explorer-studio.genlayer.com/tx/0x1a3a10ee14aa0f4952cc6d4ec50c34b4180f0e600ccdc5e86f223f19094da5a8) | `[DATA, DATA]`, `CLEAN`; both lines in safe view |
| Ingest tainted source | [0xc892e7e9…](https://explorer-studio.genlayer.com/tx/0xc892e7e977cfd4e2c8b9627efab1d6757e0cce9123ecbcb484c3770cf79c0962) | `[DATA, INSTRUCTION]`, `FILTERED`; only first line in safe view |
| Close tainted source | [0xcb34ba6f…](https://explorer-studio.genlayer.com/tx/0xcb34ba6f8b54312e428e66c393e06e9a9e04e48d2159c62a1cd48ebfeb08102e) | `CLOSED`; current safe view empty, immutable earlier attempt retained |

The tainted attempt's onchain report bound content SHA-256 `81525f9f62a0f615f744117bd0e8a075ad7368d47f9704bf6ee9d690a63c6770`, exact label vector, safe-text SHA-256 `915ce1679e87e0b7f4b4c5b7497845f0a6187a03024b524fa5086c548c095abf`, and root `18a8b61417e338c489e045d1730afa4f79104fcf8a9bd0be925e231b9a219516`. The removed line was not returned by `get_safe_text`. This is a synthetic prompt-injection test, not proof that every possible injection can be detected.

Checks: GenVM lint and SDK validation passed; five direct tests passed. Direct mode does not exercise validator consensus, so the finalized ingest transactions are the consensus evidence.
