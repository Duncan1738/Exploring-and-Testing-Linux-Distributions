# Exploring and Testing Linux Distributions

A hands-on project for trying out Linux distributions **without replacing your current operating system**, and comparing them on usability, performance, package management, hardware support, and fit for different use cases.

## Contents

- [Choosing a Testing Method](#choosing-a-testing-method)
- [1. Virtual Machines](#1-virtual-machines)
- [2. Online (Browser-Based) Testing](#2-online-browser-based-testing)
- [3. Bootable USB (Live Session)](#3-bootable-usb-live-session)
- [4. Docker Containers](#4-docker-containers)
- [Distributions to Explore](#distributions-to-explore)
- [Evaluation Criteria](#evaluation-criteria)
- [Recording Results](#recording-results)
- [Project Goals](#project-goals)
- [Contributing](#contributing)
- [License](#license)

---

## Choosing a Testing Method

| Method | Setup effort | Real hardware test | Full desktop | Kernel tested | Best for |
|---|---|---|---|---|---|
| Virtual machine | Medium | ❌ | ✅ | ✅ (guest) | Installing and living with a distro for a while |
| Browser-based | None | ❌ | ✅ (limited) | ❌ | A quick first look |
| Bootable USB | Low–Medium | ✅ | ✅ | ✅ | Checking Wi-Fi, GPU, audio, sleep, peripherals |
| Docker container | Low | ❌ | ❌ (CLI only) | ❌ (shares host) | Comparing package managers and CLI tooling |

**Rule of thumb:** start in the browser or a container, move to a VM once a distro looks interesting, and finish with a live USB before installing on real hardware.

---

## 1. Virtual Machines

Run a full Linux system inside your current OS.

**Tools**

- [VirtualBox](https://www.virtualbox.org/) — cross-platform, free
- [VMware Workstation / Fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion) — Windows, Linux, macOS
- [GNOME Boxes](https://apps.gnome.org/Boxes/) or [virt-manager](https://virt-manager.org/) (QEMU/KVM) — if your host is already Linux
- [UTM](https://mac.getutm.app/) — macOS, including Apple Silicon (use ARM64 ISOs)

**Advantages**

- Run several distributions on one machine.
- Snapshots let you break things and roll back.
- Full install experience, including the installer and partitioning.

**Tips**

- Give each VM at least 2 CPU cores, 4 GB RAM, and 25 GB disk for a desktop distro.
- Enable hardware virtualization (Intel VT-x / AMD-V) in your BIOS/UEFI.
- Take a snapshot right after installation so every test starts from a clean state.
- VM performance is not representative of bare metal — use it for relative comparisons only.

---

## 2. Online (Browser-Based) Testing

Try a distribution's desktop directly in the browser, with nothing to install.

**Platform**

- [DistroSea](https://distrosea.com/)

**Advantages**

- Zero setup — useful for a first impression of a desktop environment.
- Fast way to shortlist distributions before downloading anything.

**Limitations**

- Sessions are time-limited and can feel slow.
- Says nothing about performance or hardware compatibility.

---

## 3. Bootable USB (Live Session)

Boot Linux directly on your computer from a USB drive, usually without touching your disk.

**Tools**

- [Ventoy](https://www.ventoy.net/) — copy multiple ISOs onto one USB and pick at boot
- [Rufus](https://rufus.ie/) — Windows, writes a single ISO
- [balenaEtcher](https://etcher.balena.io/) — Windows, macOS, Linux

**Advantages**

- The only method that tests your actual hardware.
- Lets you check Wi-Fi, graphics, audio, touchpad, webcam, suspend/resume, and external displays.

**Before you boot**

1. **Verify the ISO** against the checksum published on the distro's website:
   ```bash
   sha256sum ubuntu-24.04-desktop-amd64.iso      # Linux
   shasum -a 256 ubuntu-24.04-desktop-amd64.iso  # macOS
   certutil -hashfile ubuntu.iso SHA256          # Windows
   ```
2. **Back up important files.**
3. If Secure Boot blocks the USB, check the distro's documentation before disabling it.

> ⚠️ A live session does not change your disk — but the installer can. Don't click "Install" unless you mean to, and never modify existing partitions you aren't sure about.

---

## 4. Docker Containers

Lightweight environments for comparing userlands, package managers, and command-line tools.

**Tools**

- [Docker](https://www.docker.com/) or [Podman](https://podman.io/)
- [Docker Hub](https://hub.docker.com/) for official images

**Quick start** — each command drops you into a throwaway shell that is deleted on exit:

```bash
docker run -it --rm ubuntu:24.04 bash
docker run -it --rm debian:stable bash
docker run -it --rm fedora:latest bash
docker run -it --rm archlinux:latest bash
docker run -it --rm alpine:latest sh
docker run -it --rm opensuse/tumbleweed bash
```

**Advantages**

- Starts in seconds, easy to script and reproduce.
- Ideal for comparing package availability, versions, and install speed.

**Limitations**

- Containers share the host kernel, so you are not testing the distro's kernel, init system, or desktop.
- Images are minimal and differ from a normal installation.
- Linux Mint has no official container image; use Ubuntu as a close stand-in.

---

## Distributions to Explore

| Distribution | Primary use | Package manager | Release model | Website |
|---|---|---|---|---|
| Ubuntu | Desktop and development | `apt` (+ Snap) | Fixed (LTS every 2 years) | [ubuntu.com](https://ubuntu.com/) |
| Debian | Stability and servers | `apt` | Fixed (stable) | [debian.org](https://www.debian.org/) |
| Fedora | Modern Linux technologies | `dnf` (+ Flatpak) | Fixed (~6 months) | [fedoraproject.org](https://fedoraproject.org/) |
| Linux Mint | Beginner-friendly desktop | `apt` (+ Flatpak) | Fixed (Ubuntu LTS base) | [linuxmint.com](https://linuxmint.com/) |
| Arch Linux | Customization and learning | `pacman` (+ AUR) | Rolling | [archlinux.org](https://archlinux.org/) |
| Alpine Linux | Containers and minimal systems | `apk` | Fixed (+ edge) | [alpinelinux.org](https://alpinelinux.org/) |
| openSUSE | Administration and development | `zypper` | Leap: fixed · Tumbleweed: rolling | [opensuse.org](https://www.opensuse.org/) |

---

## Evaluation Criteria

| Criterion | What to look at |
|---|---|
| Installation | Installer clarity, time to a working desktop, partitioning options |
| Ease of use | Defaults, settings app, first-run experience |
| Resource usage | Idle RAM, CPU at idle, boot time, disk footprint |
| Package management | Speed, dependency handling, rollback support |
| Software availability | Official repos, Flatpak/Snap, third-party sources |
| Security and updates | Update cadence, support lifetime, default firewall/SELinux/AppArmor |
| Hardware compatibility | Wi-Fi, GPU drivers, audio, suspend, external displays |
| Development tools | Compiler and language versions, container tooling, IDE availability |

---

## Recording Results

Use the same commands on every distribution so results are comparable:

```bash
free -h                      # memory usage
systemd-analyze              # boot time (systemd distros)
df -h /                      # disk usage after install
uname -r                     # kernel version
lsblk                        # disk layout
inxi -Fxz                    # hardware summary (install inxi first)
```

Record each test in a file such as `results/<distro>.md`:

```markdown
## Fedora Workstation 41 — VirtualBox, 4 GB RAM, 2 vCPU

- Date tested:
- Install time:
- Idle RAM after boot:
- Boot time:
- Disk used after install:
- Hardware issues:
- Notes:
- Rating (1–5):
```

---

## Project Goals

1. Explore a range of Linux distributions using safe, non-destructive methods.
2. Document installation and testing procedures step by step.
3. Compare features, system requirements, and resource usage.
4. Record benchmarks and observations in a consistent format.
5. Recommend suitable distributions for specific use cases (beginners, developers, servers, older hardware).

---

## Contributing

Suggestions, corrections, and new test results are welcome.

- Open an issue to propose a distribution or report a mistake.
- Submit a pull request with results using the template above.
- Please include your test environment (VM or hardware, RAM, CPU) so results can be compared fairly.

---

## License

Released under the [MIT License](LICENSE) — free to use, modify, and distribute.
