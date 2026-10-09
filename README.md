# GB Poseidon2b Dual Miner

**GB Poseidon2b Dual Miner** mines **NOID** (Parano1d) and **JTM** (Jetsam) on the same NVIDIA GPU at the same time — RTX 30/40/50, Linux, Windows and HiveOS.

NOID keeps full priority on the GPU; the Jetsam memory walk runs alongside it in the time NOID leaves free. On an RTX 5070 Ti that is NOID 106 MH/s plus JTM about 5 kH/s (NOID alone: 108 MH/s). It also runs as a plain NOID miner — see [NOID only](#noid-only).

**Current release: v1.0.0**

---

## Download

Grab the archive for your platform from [Releases](../../releases/latest), unpack it, and run it from inside the folder.

| Platform | Archive |
|---|---|
| Linux | `gb-poseidon2b-dual-miner-1.0.0-linux.tar.gz` |
| Windows | `gb-poseidon2b-dual-miner-1.0.0-windows.zip` |
| HiveOS | `gb-poseidon2b-dual-1.0.0.tar.gz` (custom-miner package) |

Every archive carries a `SHA256SUMS` for the files inside it, and the `SHA256SUMS` on the release lists the archives. No CUDA Toolkit needed — only a current NVIDIA driver. The Linux and HiveOS binaries run on any 64-bit Linux with glibc 2.31 or newer.

## Quick start

**Linux**
```
./gb-poseidon2b-dual-miner cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 \
    --jtm-pool rplant --jtm-wallet <your-j1-address> --search-binary ./gb-poseidon2b-dual-engine
```

**Windows**
```
./gb-poseidon2b-dual-miner.exe cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 --jtm-pool rplant --jtm-wallet <your-j1-address> --search-binary gb-poseidon2b-dual-engine.exe
```

**HiveOS** — Flight Sheet → Custom miner:
- Miner name: `gb-poseidon2b-dual`
- Installation URL: `https://github.com/gustlborg/gb-poseidon2b-dual-miner/releases/download/v1.0.0/gb-poseidon2b-dual-1.0.0.tar.gz`
- Wallet: your `o1...` address, pool address in the pool field
- Custom user config: `JTM_POOL=rplant` and `JTM_WALLET=j1...` (one per line)

HiveOS derives the miner name from the archive's file name, so use exactly this URL and `gb-poseidon2b-dual`. HiveOS shows NOID and Jetsam as first and second algorithm.

## NOID only

Leave out `--jtm-pool` and `--jtm-wallet` (HiveOS: `JTM_POOL` and `JTM_WALLET`) and the miner mines NOID alone, at the full NOID hashrate, with memory-clock autotune available and only the NOID developer fee:

```
./gb-poseidon2b-dual-miner cuda-mine --pool innovlab --coinbase <your-o1-address> --worker rig0 --search-binary ./gb-poseidon2b-dual-engine
```

## Pools

| | Keywords | Other pools |
|---|---|---|
| NOID `--pool` | `innovlab` (default), `suprnova`, `parano1d`, `ariabrain` | `--rpc <url>` (HTTP or Stratum) |
| JTM `--jtm-pool` | `rplant`, `rplant-na`, `rplant-asia`, `dongqn` | `stratum+tcp://`, `stratum+tls://`, `http(s)://` address |

`--jtm-worker` sets the Jetsam worker name (default: `--worker`).

## Jetsam intensity

`--jtm-intensity 1..16` (HiveOS: `JTM_INTENSITY`) sets how much of the GPU Jetsam gets. Higher mines more JTM, costs NOID a little more and needs more GPU memory; the default fits the GPU generation, and the miner lowers it on its own when the card lacks free memory.

| RTX 5070 Ti | JTM | NOID |
|---|---|---|
| intensity 1 | 2.0 kH/s | −0.2 % |
| intensity 2 | 3.7 kH/s | −0.5 % |
| intensity 8 (default) | 4.9 kH/s | −1.9 % |

Jetsam needs memory bandwidth: for dual mining leave the memory clock at the card's normal value.

## Hardware

RTX 30/40/50 mine both coins. Other NVIDIA GPUs with compute capability 8.0 or newer mine NOID only. RTX 20-series and older are not supported. One binary covers all generations; a multi-GPU rig mines every supported card by default (`--devices 0,1` to choose).

## Developer fees

3 % NOID and 3 % JTM, each mined in its own pool session; in NOID-only mode only the NOID fee. Details are in the README of each package.

## License

Proprietary software, distributed as binaries only — see [LICENSE](LICENSE). Reverse engineering, decompilation, disassembly and analysis by automated systems (including large language models) are prohibited, except where such a prohibition is void under applicable mandatory law. Third-party components under the Apache License 2.0 are listed in the `NOTICE` file of each package.
