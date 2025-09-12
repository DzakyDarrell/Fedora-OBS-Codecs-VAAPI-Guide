# Fedora-OBS-Codecs-VAAPI-Guide
Guide to install important codecs to use OBS and other rendering apps on Fedora.

![Fedora Logo](images/Logo_fedoralogo.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![OBS Logo](images/obs_studio_logo.png)

A complete guide to enabling **codecs** and **hardware encoding (VAAPI/QuickSync/AMD)** for OBS Studio on **Fedora Linux**. This repo aims to help streamers and content creators configure OBS with the best settings for 720p and 1080p.

---

## 📑 Table of Contents

1. [Requirements](#requirements)
2. [Install RPM Fusion & Codecs](#install-rpm-fusion--codecs)
3. [Enable Hardware Acceleration](#enable-hardware-acceleration)
4. [Verify VAAPI Support](#verify-vaapi-support)
5. [OBS Configuration](#obs-configuration)

   * [General Settings](#general-settings)
   * [Encoder Settings](#encoder-settings)
   * [Recommended Presets](#recommended-presets)
6. [Screenshots](#screenshots)
7. [Troubleshooting](#troubleshooting)
8. [Extra Tips](#extra-tips)

---

## ✅ Requirements

* Fedora Workstation (KDE Plasma, GNOME, etc.)
* OBS Studio (native, not Flatpak)
* GPU that supports VAAPI (Intel iGPU, AMD, or NVIDIA via NVENC if supported)
* Internet connection (to install extra packages)

---

## 📦 Install RPM Fusion & Codecs

Fedora ships without some codecs. You’ll need **RPM Fusion**:

```bash
# Enable RPM Fusion repos
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
                 https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Update and install multimedia codecs
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf groupupdate sound-and-video

# Extra ffmpeg tools
sudo dnf install ffmpeg ffmpeg-libs
```

If you’re using Intel/AMD, install VAAPI drivers:

```bash
# Intel VAAPI
sudo dnf install libva-intel-driver libva-utils

# AMD VAAPI
sudo dnf install mesa-va-drivers mesa-vdpau-drivers
```

---

## ⚡ Enable Hardware Acceleration

For Intel/AMD GPUs:

```bash
sudo dnf install libva-utils
vainfo
```

If `vainfo` shows supported codecs (H.264, HEVC), VAAPI is working.

For NVIDIA (optional):

```bash
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda
```

---

## 🔍 Verify VAAPI Support

Run:

```bash
vainfo | grep -i h264
```

You should see lines like:

```
VAProfileH264High : VAEntrypointVLD
VAProfileH264High : VAEntrypointEncSlice
```

This means hardware encoding/decoding is available.

---

## 🎥 OBS Configuration

### General Settings

1. Open **OBS Studio** (native version).
2. Go to **Settings → Output**.

### Encoder Settings

* **Recording Encoder:** `FFmpeg VAAPI (h264_vaapi)`
* **Streaming Encoder:** `FFmpeg VAAPI (h264_vaapi)`
* **Rate Control:** CBR (for streaming) / CRF (for recording)
* **Keyframe Interval:** 2s

### Recommended Presets

#### 🔹 720p (Recording/Streaming)

* Resolution: 1280x720
* FPS: 30 or 60 (depending on performance)
* Bitrate: 2500–4000 Kbps (streaming)
* CRF: 23 (recording)
* Preset: Balanced

#### 🔹 1080p (Recording/Streaming)

* Resolution: 1920x1080
* FPS: 30/60
* Bitrate: 4500–6000 Kbps (streaming)
* CRF: 21 (recording)
* Preset: Balanced/Quality

---

## 🖼 Screenshots

Add your own screenshots here:

* ![OBS Output Settings](images/obs-output-settings.png)
* ![OBS Encoder Dropdown](images/obs-encoder.png)
* ![VAAPI Working](images/vainfo.png)

---

## 🛠 Troubleshooting

* **OBS Flatpak can’t see VAAPI:** Use the native DNF package instead.
* **No VAAPI in dropdown:** Install missing drivers (`mesa-va-drivers`, `libva-intel-driver`).
* **Streaming lags:** Lower bitrate or FPS.
* **Recording stutters:** Use `CRF` instead of `CBR`.

---

## 💡 Extra Tips

* Use **BTRFS with Zstd** (default in Fedora) for efficient disk usage when recording.
* Store recordings on SSD if possible.
* For editing, convert recordings to `mkv` → `mp4` with:

  ```bash
  ffmpeg -i input.mkv -codec copy output.mp4
  ```
* Share your OBS profile with friends by exporting `~/.config/obs-studio`.

---

📌 Maintainer: \[Dzaky / GitHub Handle]

Contributions welcome! Fork this repo, improve the guide, and submit PRs.
