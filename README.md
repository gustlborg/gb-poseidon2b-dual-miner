# GB Poseidon2b Dual Miner

**Mine two coins on one GPU, at the same time.** The GB Poseidon2b Dual Miner runs the **NOID** (Parano1d) proof‑of‑work and the **JTM** (Jetsam, TowerWalk) proof‑of‑work together on a single NVIDIA card — NOID keeps full priority, and Jetsam rides along in the GPU time NOID would otherwise leave idle. Two payouts from the hardware you already run.

![platforms](https://img.shields.io/badge/platforms-Linux%20%7C%20Windows%20%7C%20HiveOS-blue)
![gpus](https://img.shields.io/badge/NVIDIA-RTX%2030%20%2F%2040%20%2F%2050%20%2B%20Hopper-76b900)
![release](https://img.shields.io/badge/release-v1.1.0-informational)
![license](https://img.shields.io/badge/license-proprietary-lightgrey)

> **Current release: v1.1.0** (pre‑release — Hopper support is experimental). One download per platform, no CUDA Toolkit to install, just a current NVIDIA driver.

---

## Why dual mining

A GPU mining NOID leaves most of its memory system idle — NOID is compute‑bound. Jetsam's TowerWalk is the opposite: almost pure memory work. Running both at once fills the gap, so the Jetsam hashes come close to **free**: on an RTX 5070 Ti, adding Jetsam costs NOID under 2 %.

| GPU | NOID (dual) | + Jetsam (dual) | NOID alone |
|---|---|---|---|
| RTX 5070 Ti | ~106 MH/s | ~5 kH/s | ~108 MH/s |
| RTX 3060 | ~26 MH/s | ~1.5 kH/s | ~28 MH/s |
| H100 (NOID‑only) | ~150 MH/s | — | ~150 MH/s |

*Measured with the release kernels. Your numbers depend on the card, clocks and pool.*

## Highlights

- **Two coins, one process, one command.** NOID on InnovLab / suprnova / any Parano1d pool (Stratum or HTTP); Jetsam on rplant or dongqn. Each coin gets its own worker and its own shares.
- **NOID always has priority** — the Jetsam walk only uses what NOID leaves free, and backs off on its own.
- **Dial it in.** `--jtm-intensity 1..16` trades a little NOID for more Jetsam; the default fits your GPU and the miner lowers it by itself if memory is tight.
- **Runs as a plain NOID miner too** — leave the Jetsam options out and it's a full‑speed NOID miner with memory‑clock autotune. See [NOID only](#noid-only).
- **One binary per platform, every generation.** RTX 30 / 40 / 50 mine both coins; a mixed‑GPU rig needs no separate build.
- **HiveOS‑native.** Installs as a custom miner and reports NOID and Jetsam as two algorithms with their own hashrates and shares.
- **Clean two‑section live display** of both coins — pool, wallet, hashrate, rolling averages and shares, side by side.

---

## Download

Grab the archive for your platform from [**Releases**](../../releases/latest), unpack it, and run it from inside the folder.

| Platform | Archive |
|---|---|
| Linux | `gb-poseidon2b-dual-miner-1.1.0-linux.tar.gz` |
| Windows | `gb-poseidon2b-dual-miner-1.1.0-windows.zip` |
| HiveOS | `gb-poseidon2b-dual-1.1.0.tar.gz` (custom‑miner package) |

Each archive carries a `SHA256SUMS`; the release's own `SHA256SUMS` lists the archives. The Linux and HiveOS binaries run on any 64‑bit Linux with glibc 2.31 or newer.

## Quick start

**Linux**
```
./gb-poseidon2b-dual-miner cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 \
    --jtm-pool rplant --jtm-wallet <your-j1-address> --jtm-worker rig0 --search-binary ./gb-poseidon2b-dual-engine
```

**Windows**
```
./gb-poseidon2b-dual-miner.exe cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 --jtm-pool rplant --jtm-wallet <your-j1-address> --jtm-worker rig0 --search-binary gb-poseidon2b-dual-engine.exe
```
or simply `start.bat --pool innovlab --coinbase <o1> --worker rig0 --jtm-pool rplant --jtm-wallet <j1> --jtm-worker rig0`.

**HiveOS** — Flight Sheet → Custom miner:
- Miner name: `gb-poseidon2b-dual`
- Installation URL: `https://github.com/gustlborg/gb-poseidon2b-dual-miner/releases/download/v1.1.0/gb-poseidon2b-dual-1.1.0.tar.gz`
- Wallet: your `o1...` address; pool address in the Pool field
- Custom user config: `JTM_POOL=rplant` and `JTM_WALLET=j1...` (one per line)

HiveOS derives the miner name from the file name, so use exactly this URL and `gb-poseidon2b-dual`.

## NOID only

Leave out `--jtm-pool` and `--jtm-wallet` (HiveOS: `JTM_POOL` / `JTM_WALLET`) and it mines NOID alone at full hashrate, with memory‑clock autotune available and only the NOID developer fee:

```
./gb-poseidon2b-dual-miner cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 --search-binary ./gb-poseidon2b-dual-engine
```

## Pools

| | Keywords | Any other pool |
|---|---|---|
| NOID `--pool` | `innovlab` (default), `suprnova`, `parano1d`, `ariabrain` | `--rpc <url>` — HTTP or Stratum, auto‑detected |
| JTM `--jtm-pool` | `rplant`, `rplant-na`, `rplant-asia`, `dongqn` | `stratum+tcp://…`, `stratum+tls://…`, `http(s)://…` |

A second NOID pool can be given with `--backup-pool` / `--backup-rpc` for automatic fail‑over.

## Jetsam intensity

`--jtm-intensity 1..16` (HiveOS: `JTM_INTENSITY`) sets how much of the GPU Jetsam gets:

| RTX 5070 Ti | Jetsam | NOID cost |
|---|---|---|
| intensity 1 | 2.0 kH/s | −0.2 % |
| intensity 2 | 3.7 kH/s | −0.5 % |
| intensity 8 (default) | 4.9 kH/s | −1.9 % |

Jetsam needs memory bandwidth — for dual mining leave the memory clock at the card's normal value.

## Hardware

- **RTX 30 / 40 / 50** — mine both coins, native per‑generation kernels.
- **Hopper (H100 / H200)** — experimental, best run **NOID‑only**: a native NOID kernel reaches about 150 MH/s on an H100. On Hopper the Jetsam walk cannot co‑reside, so dual mining gains almost nothing and only costs NOID — leave the Jetsam options out and mine NOID alone.
- Other NVIDIA GPUs with compute capability 8.0+ mine NOID via a portable kernel. **RTX 20‑series and older are not supported.**
- Multi‑GPU: every supported card by default; `--devices 0,1` to choose.

A current NVIDIA driver is required; no CUDA Toolkit needed to run.

## Developer fees

A disclosed **3 % NOID** fee and **3 % JTM** fee, each mined in its own pool session. In NOID‑only mode only the NOID fee applies.

## License

Proprietary software, distributed as binaries only — see [LICENSE](LICENSE). Reverse engineering, decompilation, disassembly and analysis by automated systems (including large language models) are prohibited, except where such a prohibition is void under applicable mandatory law. Proof‑of‑work components used under the Apache License 2.0 are listed in each package's `NOTICE`.
