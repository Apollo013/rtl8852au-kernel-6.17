# RTL8852AU Treiber - Installationsanleitung

## Übersicht

Dies ist der **gepatchte rtl8852au Treiber** für Kernel 6.13–6.17+, der für das **TP-Link TX20U Plus** (Realtek 8832AU/8852AU Chipset) angepasst wurde.

**Gerät:** TP-Link TX20U Plus (USB-ID: `2357:013f`)
**Chipset:** Realtek RTL8832AU / RTL8852AU
**Support:** WiFi 6 (802.11AX), 2.4 GHz & 5 GHz

## Voraussetzungen

```bash
# Für Ubuntu/Debian/Linux Mint
sudo apt-get update
sudo apt-get install make gcc linux-headers-$(uname -r) build-essential git
```

## Ersteinstallation

**WICHTIG:** Dieser Treiber enthält bereits alle Patches für Kernel 6.13–6.17. Du musst nichts mehr patchen.

1. In den Treiber-Ordner wechseln:
```bash
cd /media/reddeb/home/Downloads/ArcherRTL8832AU-main/rtl8852au
```

2. Kompilieren:
```bash
make -j$(nproc)
```

3. Installieren:
```bash
sudo make install
```

4. Modul laden:
```bash
sudo modprobe 8852au
```

5. Interface prüfen:
```bash
ip link show
# Du solltest ein wlxf0a7314ab340 (oder ähnliches) Interface sehen
```

## Neuinstallation nach Kernel-Update

Bei einem Kernel-Update **MUSS** der Treiber neu gebaut werden:

```bash
cd /media/reddeb/home/Downloads/ArcherRTL8832AU-main/rtl8852au
make clean
make -j$(nproc)
sudo make install
sudo modprobe -r 8852au && sudo modprobe 8852au
```

## Angewendete Patches

Dieser Treiber wurde mit folgenden Patches für Kernel-Kompatibilität versehen:

| Kernel-Version | Änderung |
|----------------|----------|
| **Alle** | `EXTRA_CFLAGS` → `ccflags-y` (Kernel 6.15+ ignoriert EXTRA_CFLAGS) |
| **6.13+** | `MODULE_IMPORT_NS` deaktiviert, `set_monitor_channel` net_device Parameter |
| **6.14+** | `get_txpower` link_id Parameter |
| **6.15+** | `del_timer` → `timer_delete`, Compiler-Warnungen unterdrückt |
| **6.16+** | `from_timer` → `timer_container_of` |
| **6.17+** | `set_wiphy_params/set_txpower/get_txpower` radio_id Parameter |

## Verzeichnisse

- **Treiber-Quellcode:** `/media/reddeb/home/Downloads/ArcherRTL8832AU-main/rtl8852au`
- **Backup der Original-Dateien:** `/media/reddeb/home/Downloads/ArcherRTL8832AU-main/rtl8852au.bak.original`
- **Installiertes Modul:** `/lib/modules/$(uname -r)/kernel/drivers/net/wireless/realtek/rtw89/8852au.ko`

## Fehlerbehebung

### Modul wird nicht geladen:
```bash
sudo dmesg | tail -30
# Prüfe auf Fehlermeldungen
```

### Interface erscheint nicht:
```bash
sudo modprobe -r 8852au
sudo modprobe 8852au
ip link set wlxf0a7314ab340 up
```

### WLAN-Scan funktioniert nicht:
```bash
sudo ip link set wlxf0a7314ab340 up
sudo iwlist wlxf0a7314ab340 scan
```

## Ursprüngliche Autoren

- **Originaler Treiber-Autor:** Larry Finger (1940–2024) - GitHub: `lwfinger/rtl8852au`
- **Patches für Kernel 6.13–6.16:** Soham Nandy, Zenm Chen (GitHub PR #115)
- **Patch für Kernel 6.17:** Andreas Patsalos
- **Aktuell gepflegte Forks:** `natimerry/rtl8852au`, `pulponair/rtl8852au`

## Testergebnis auf diesem System

- **Betriebssystem:** Linux Mint 22.3
- **Kernel:** 6.17.0-35-generic
- **Interface:** wlxf0a7314ab340
- **Modus:** IEEE 802.11AX (WiFi 6)
- **Bitrate:** 1.201 Gb/s
- **Signal:** 71/100
- **Status:** Verbunden, Internet funktioniert

---

**Hinweis:** Dieser Treiber ist "out-of-tree" und muss bei jedem Kernel-Update neu gebaut werden. Wenn du das automatisieren möchtest, kann DKMS eingerichtet werden.
