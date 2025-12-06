# Fedora-OBS-Codecs-VAAPI-Guide
Guide to install important codecs to use OBS and other rendering apps on Fedora.

![Fedora Logo](images/Logo_fedoralogo.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![OBS Logo](images/obs_studio_logo.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Fedora Logo](images/FFmpeg_logo.png)

A complete guide to enabling **codecs** and **hardware encoding (VAAPI/QuickSync/AMD)** for OBS Studio on **Fedora Linux**. This repo aims to help streamers and content creators configure OBS with the best settings for 720p and 1080p.

---

## Table of Contents

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

## Requirements

* Fedora Workstation (KDE Plasma, GNOME, etc.)
* OBS Studio (native, not Flatpak)
* GPU that supports VAAPI (Intel iGPU, AMD, or NVIDIA via NVENC if supported)
* Internet connection (to install extra packages)

---

## Install RPM Fusion & Codecs

1. Fedora ships without some codecs. You’ll need **RPM Fusion**:

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```
2. Install the **GStreamer Codecs**:
```bash
sudo dnf install gstreamer1-devel gstreamer1-plugins-base-tools gstreamer1-doc gstreamer1-plugins-base-devel gstreamer1-plugins-good gstreamer1-plugins-good-extras gstreamer1-plugins-ugly gstreamer1-plugins-bad-free gstreamer1-plugins-bad-free-devel gstreamer1-plugins-bad-free-extras
```

3. Install the **FFMPEG Codecs**:
```bash
sudo dnf install ffmpeg --allowerasing
```

4. Instal the **Libva Codecs**:
```bash
sudo dnf install libavcodec-freeworld libva-utils
```
---

## Enable Hardware Acceleration

If you’re using Intel install VAAPI drivers for hardware acceleration:

```bash
sudo dnf install intel-media-driver
```
If you're using AMD, VAAPI is already installed on kernel but you need to swap to use it:
```bash
sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld --allowerasing
```
```bash
sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld --allowerasing
```
---

## Verify VAAPI Support

Run:

```bash
vainfo | grep "H264"
```

You should see lines like:

```
#example result
      VAProfileH264Main               : VAEntrypointVLD
      VAProfileH264Main               : VAEntrypointEncSlice
      VAProfileH264Main               : VAEntrypointFEI
      VAProfileH264Main               : VAEntrypointEncSliceLP
      VAProfileH264High               : VAEntrypointVLD
      VAProfileH264High               : VAEntrypointEncSlice
      VAProfileH264High               : VAEntrypointFEI
      VAProfileH264High               : VAEntrypointEncSliceLP
      VAProfileH264ConstrainedBaseline: VAEntrypointVLD
      VAProfileH264ConstrainedBaseline: VAEntrypointEncSlice
      VAProfileH264ConstrainedBaseline: VAEntrypointFEI
      VAProfileH264ConstrainedBaseline: VAEntrypointEncSliceLP
```

This means hardware encoding/decoding is available and instaled properly.

---

## OBS Configuration

### General Settings

1. Open **OBS Studio** (native version).
2. Go to **Settings → Output**.

### Encoder Settings (Advanced VAAPI)
- **Audio Encoder:** `FFmpeg AAC`
- **Video Encoder:** `FFmpeg VAAPI H.264`
- **VAAPI Device:** Select your GPU (e.g., Intel HD Graphics, AMD Radeon)
- **Profile:** `High`
- **Level:** `Auto`
- **Rate Control:** `CBR` (streaming) or `CRF` (recording)
- **Bitrate (streaming 720p):** `2500–4000 Kbps`
- **Keyframe Interval:** `2s`
- **Max B-frames:** `0`

### Recommended Presets

#### 🔹 720p (Streaming)
- Resolution: 1280x720
- FPS: 30 or 60
- Bitrate: 2500–4000 Kbps
- Profile: High
- Level: Auto

#### 🔹 1080p (Streaming)
- Resolution: 1920x1080
- FPS: 30/60
- Bitrate: 4500–6000 Kbps
- Profile: High
- Level: Auto

#### 🔹 Recording (Better Quality)
- Rate Control: `CRF`
- CRF: 21–23
- Preset: Balanced or Quality

---

## Troubleshooting

* **OBS Flatpak can’t see VAAPI:** Use the native DNF package instead.
* **No VAAPI in dropdown:** Install missing drivers (`mesa-va-drivers`, `libva-intel-driver`).
* **Streaming lags:** Lower bitrate or FPS.
* **Recording stutters:** Use `CRF` instead of `CBR`.

---

## Extra Tips

* Use **BTRFS with Zstd** (default in Fedora) for efficient disk usage when recording.
* Store recordings on SSD if possible.
* For editing, convert recordings to `mkv` → `mp4` with:

  ```bash
  ffmpeg -i input.mkv -codec copy output.mp4
  ```
* Share your OBS profile with friends by exporting `~/.config/obs-studio`.

---

📌 Maintainer: \[Dzaky]

Contributions welcome! Fork this repo, improve the guide, and submit PRs.
