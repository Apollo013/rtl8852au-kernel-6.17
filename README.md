# RTL8852AU Driver for Linux Kernel 6.13–6.17+

Compatibility patches for recent Linux kernels.

Tested on **TP-Link TX20U Plus** (`USB ID: 2357:013f`)

---

## Overview

This repository provides compatibility patches for the RTL8852AU driver to support Linux kernels **6.13 through 6.17+**.

The original driver is no longer maintained for newer kernels and fails to compile due to several networking and timer API changes introduced in recent Linux releases.

## Supported Device

| Device             | USB ID    | Status             |
| ------------------ | --------- | ------------------ |
| TP-Link TX20U Plus | 2357:013f | Tested and working |

Additional RTL8852AU-based devices may also work but have not been verified.

---

## Applied Patches

| Kernel Version | Changes                                                                                          |
| -------------- | ------------------------------------------------------------------------------------------------ |
| All            | `EXTRA_CFLAGS` replaced with `ccflags-y`                                                         |
| 6.13+          | Disabled `MODULE_IMPORT_NS`; updated `set_monitor_channel()`                                     |
| 6.14+          | Added `link_id` parameter support in `get_txpower()`                                             |
| 6.15+          | Replaced `del_timer()` with `timer_delete()`                                                     |
| 6.15+          | Suppressed compiler warnings introduced by newer GCC versions                                    |
| 6.16+          | Replaced `from_timer()` with `timer_container_of()`                                              |
| 6.17+          | Added `radio_id` parameter support in `set_wiphy_params()`, `set_txpower()`, and `get_txpower()` |

---

## Requirements

* Linux Kernel 6.13+
* GCC
* make
* Linux kernel headers

Ubuntu / Mint:

```bash
sudo apt install build-essential linux-headers-$(uname -r)
```

Debian:

```bash
sudo apt install build-essential linux-headers-amd64
```

---

## Build

```bash
make -j$(nproc)
```

---

## Install

```bash
sudo make install
```

Load the driver:

```bash
sudo modprobe 8852au
```

Verify:

```bash
lsmod | grep 8852au
```

---

## Rebuild After Kernel Update

```bash
make clean

make -j$(nproc)

sudo make install

sudo modprobe -r 8852au

sudo modprobe 8852au
```

---

## DKMS (optional)

For automatic rebuilds after kernel upgrades:

```bash
sudo dkms add .

sudo dkms build 8852au/1.0

sudo dkms install 8852au/1.0
```

---

## Tested System

| Component        | Value              |
| ---------------- | ------------------ |
| Device           | TP-Link TX20U Plus |
| USB ID           | 2357:013f          |
| Operating System | Linux Mint 22.3    |
| Kernel           | 6.17.0-35-generic  |
| Wi-Fi Standard   | Wi-Fi 6            |
| Band             | 5 GHz              |
| Link Speed       | 1.2 Gbit/s         |
| Status           | Working            |

---

## Notes

This repository only provides compatibility fixes for recent Linux kernels.

No additional functionality has been added compared to the original RTL8852AU driver.

---

## License

Same license as the original RTL8852AU driver source.
