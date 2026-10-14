# GB Poseidon2b Dual Miner

**Mine two coins on one GPU, at the same time.** The GB Poseidon2b Dual Miner runs the **NOID** (Parano1d) proof‑of‑work and the **JTM** (Jetsam, TowerWalk) proof‑of‑work together on a single NVIDIA card — NOID keeps full priority, and Jetsam rides along in the GPU time NOID would otherwise leave idle. Two payouts from the hardware you already run.

![platforms](https://img.shields.io/badge/platforms-Linux%20%7C%20Windows%20%7C%20HiveOS-blue)
![gpus](https://img.shields.io/badge/NVIDIA-RTX%2030%20%2F%2040%20%2F%2050%20%2B%20Hopper-76b900)
![release](https://img.shields.io/badge/release-v1.3.0-informational)
![license](https://img.shields.io/badge/license-proprietary-lightgrey)

> **Current release: v1.3.0.** One download per platform, no CUDA Toolkit to install, just a current NVIDIA driver.

---

## Why dual mining

A GPU mining NOID leaves most of its memory system idle — NOID is compute‑bound. Jetsam's TowerWalk is the opposite: almost pure memory work. Running both at once fills the gap, so the Jetsam hashes come cheap: on an RTX 5070 Ti, adding Jetsam costs NOID about 5 %.

| GPU | NOID (dual) | + Jetsam (dual) | NOID alone |
|---|---|---|---|
| RTX 5070 Ti | ~116 MH/s | ~5.2 kH/s | ~122 MH/s |
| RTX 4080 SUPER | ~113 MH/s | ~4.0 kH/s | ~123 MH/s |
| RTX 3060 | ~28 MH/s | ~1.8 kH/s | ~31 MH/s |
| H100 NVL (default level 2) | ~275 MH/s | ~19.2 kH/s | ~382 MH/s |

*Measured with the release kernels (H100 with a 400 W power limit; the card's default is 310 W). Your numbers depend on the card, clocks, power limit and pool.*

## Highlights

- **Two coins, one process, one command.** NOID on InnovLab / suprnova / any Parano1d pool (Stratum or HTTP); Jetsam on rplant or dongqn. Each coin gets its own worker and its own shares.
- **NOID always has priority** — the Jetsam walk only uses what NOID leaves free, and backs off on its own.
- **Dial it in.** `--jtm-intensity 1..16` sets Jetsam's share of GPU memory; the default fits your GPU and the miner lowers it by itself if memory is tight.
- **New in 1.3.0: faster NOID kernels** — about +11–13 % NOID on RTX 30 / 40 / 50 and about 2.5× on the H100, from the NOID miner 2.18.4.
- **New in 1.3.0: live dashboard.** Both coins side by side in your terminal — pools, hashrates with rolling averages, shares, power and temperatures per GPU, and the log underneath. Press `n` or `j` to pause a coin, `q` to quit; it redraws to fit whenever you resize the window.
- **Jetsam balance for Hopper.** An H100 has far more room for Jetsam than an RTX card — `--jtm-balance 0..3` sets the split between NOID and Jetsam (level 2 by default). See [Jetsam balance](#jetsam-balance-hopper).
- **Start without typing a command.** Put your wallets into the settings block of `start.bat` (Windows, then double-click) or `start.sh` (Linux, then `sh start.sh`).
- **Runs as a plain NOID miner too** — leave the Jetsam options out and it's a full‑speed NOID miner with memory‑clock autotune. See [NOID only](#noid-only).
- **One binary per platform, every generation.** RTX 30 / 40 / 50 and Hopper; a mixed‑GPU rig needs no separate build.
- **HiveOS‑native.** Installs as a custom miner and reports NOID and Jetsam as two algorithms with their own hashrates and shares.

---

## Download

Grab the archive for your platform from [**Releases**](../../releases), unpack it, and run it from inside the folder.

| Platform | Archive |
|---|---|
| Linux | `gb-poseidon2b-dual-miner-1.3.0-linux.tar.gz` |
| Windows | `gb-poseidon2b-dual-miner-1.3.0-windows.zip` |
| HiveOS | `gb-poseidon2b-dual-1.3.0.tar.gz` (custom‑miner package) |

Each archive carries a `SHA256SUMS`; the release's own `SHA256SUMS` lists the archives. The Linux and HiveOS binaries run on any 64‑bit Linux with glibc 2.31 or newer.

## Quick start

**Linux** — open `start.sh` in a text editor, fill in `WALLET="o1..."` and `JTM_WALLET="j1..."` (optionally `WORKER`), then:
```
sh start.sh
```
or pass everything yourself:
```
./gb-poseidon2b-dual-miner cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 \
    --jtm-pool rplant --jtm-wallet <your-j1-address> --jtm-worker rig0 --search-binary ./gb-poseidon2b-dual-engine
```

**Windows** — right-click `start.bat` → Edit, fill in `WALLET` and `JTM_WALLET` (optionally `WORKER`) at the top, save, then double-click `start.bat`. Or from a console:
```
start.bat --pool innovlab --coinbase <your-o1-address> --worker rig0 --jtm-pool rplant --jtm-wallet <your-j1-address> --jtm-worker rig0
```

**HiveOS** — Flight Sheet → Custom miner:
- Miner name: `gb-poseidon2b-dual`
- Installation URL: `https://github.com/gustlborg/gb-poseidon2b-dual-miner/releases/download/v1.3.0/gb-poseidon2b-dual-1.3.0.tar.gz`
- Wallet: your `o1...` address; pool address in the Pool field
- Custom user config: `JTM_POOL=rplant` and `JTM_WALLET=j1...` (one per line; on Hopper optionally `JTM_BALANCE=0..3`, default 2)

HiveOS derives the miner name from the file name, so use exactly this URL and `gb-poseidon2b-dual`.

## NOID only

Leave out `--jtm-pool` and `--jtm-wallet` (HiveOS: `JTM_POOL` / `JTM_WALLET`; `start.sh`: leave `JTM_WALLET` empty) and it mines NOID alone at full hashrate, with memory‑clock autotune available and only the NOID developer fee:

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

`--jtm-intensity 1..16` (HiveOS: `JTM_INTENSITY`) sets how much GPU memory Jetsam gets:

| RTX 5070 Ti | Jetsam | NOID cost |
|---|---|---|
| intensity 1 | 4.7 kH/s | −4.4 % |
| intensity 2 | 4.8 kH/s | −4.7 % |
| intensity 8 (default) | 5.2 kH/s | −5.4 % |

Jetsam needs memory bandwidth — for dual mining leave the memory clock at the card's normal value.

## Jetsam balance (Hopper)

RTX cards already run Jetsam at their memory limit next to NOID. A Hopper GPU has far more room, and `--jtm-balance 0..3` (HiveOS: `JTM_BALANCE`) chooses how it is split between NOID and Jetsam — without the option the miner runs level 2 on Hopper:

| H100 NVL (400 W) | NOID | Jetsam |
|---|---|---|
| `--jtm-balance 0` | 362 MH/s | 0.2 kH/s |
| `--jtm-balance 1` | 307 MH/s | 13.8 kH/s |
| `--jtm-balance 2` (default on Hopper) | 275 MH/s | 19.2 kH/s |
| `--jtm-balance 3` | 256 MH/s | 22.5 kH/s |

On RTX 30 / 40 / 50 the option changes nothing and the miner says so.

## Hardware

- **RTX 30 / 40 / 50** — mine both coins, native per‑generation kernels.
- **Hopper** — native kernels, validated on the H100 NVL (the H200 / GH200 use the same architecture but have not been tested). NOID alone reaches about 380 MH/s on an H100 NVL; in dual mode it runs `--jtm-balance` level 2 unless you choose another.
- Other NVIDIA GPUs with compute capability 8.0+ mine NOID via a portable kernel. **RTX 20‑series and older are not supported.**
- Multi‑GPU: every supported card by default; `--devices 0,1` to choose.

A current NVIDIA driver is required; no CUDA Toolkit needed to run.

## Developer fees

A disclosed **3 % NOID** fee and **3 % JTM** fee, each mined in its own pool session. In NOID‑only mode only the NOID fee applies.

## License

Proprietary software, distributed as binaries only — see [LICENSE](LICENSE). Reverse engineering, decompilation, disassembly and analysis by automated systems (including large language models) are prohibited, except where such a prohibition is void under applicable mandatory law. Third‑party components and their licenses are listed at the end of the same file.
