# Fedora-OBS-Codecs-VAAPI-Guide
Guide to install important codecs to use OBS and other rendering apps on Fedora.

![Fedora Logo](images/Logo_fedoralogo.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![OBS Logo](images/obs_studio_logo.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Fedora Logo](images/FFmpeg_logo.png)

A complete guide to enabling **codecs** and **hardware encoding (VAAPI/QuickSync/AMD)** for OBS Studio on **Fedora Linux**. This repo aims to help streamers and content creators configure OBS with the best settings for 720p and 1080p.

---

## Table of Contents

1. [Requirements](#requirements)
2. [Install RPM Fusion & Needed Codecs](#install-rpm-fusion--codecs)
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
2. Enable Fedora **Cisco OpenH264**:
```bash
sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1
```

3. Install the **FFMPEG Codecs**:
```bash
sudo dnf install ffmpeg --allowerasing
```

4. Instal the **Libva Codecs**:
```bash
sudo dnf install libavcodec-freeworld libva-utils
```

5. Install the complete **GStreamer Codecs** (Optional):
```bash
sudo dnf install gstreamer1-devel gstreamer1-plugins-base-tools gstreamer1-doc gstreamer1-plugins-base-devel gstreamer1-plugins-good gstreamer1-plugins-good-extras gstreamer1-plugins-ugly gstreamer1-plugins-bad-free gstreamer1-plugins-bad-free-devel gstreamer1-plugins-bad-free-extras
```
---

## Enable Hardware Acceleration

For Intel user install needed drivers for iGPU hardware acceleration:

```bash
sudo dnf -y install libva-intel-driver intel-media-driver intel-mediasdk-devel
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

## OBS Configuration For 720p Recording

### General Settings

1. Open **OBS Studio**.
2. Go to **Settings → Output**.

### Encoder Settings (Advanced VAAPI)
- **Audio Encoder:** `FFmpeg AAC`
- **Video Encoder:** `FFmpeg VAAPI H.264` you can also use Intel `QuickSync H264`
- **VAAPI Device:** Select your GPU (e.g., Intel HD Graphics, AMD Radeon)
- **Profile:** `High`
- **Level:** `3.1`
- **Rate Control:** `CBR` (streaming) or `CQP` (recording)
- **Bitrate (streaming 720p):** `3000–4000 Kbps`
- **Keyframe Interval:** `2s`
- **Max B-frames:** `2`

### Recommended Presets

#### 🔹 720p (Streaming)
- Resolution: 1280x720
- FPS: 30 or 60
- Bitrate: 3000–4000 Kbps
- Profile: High
- Level: 3.1

#### 🔹 1080p (Streaming)
- Resolution: 1920x1080
- FPS: 30 or 60
- Bitrate: 4500–6000 Kbps
- Profile: High
- Level: 4.0

#### 🔹 Recording (Better Quality)
- Rate Control: `CQP`
- QP: 21–23
- Profile: High
- Level: 3.1

---

## Troubleshooting

* **OBS Flatpak can’t see VAAPI:** Use the native DNF package instead.
* **No VAAPI in dropdown:** Install missing drivers (`mesa-va-drivers`, `libva-intel-driver`).
* **Streaming lags:** Lower bitrate or FPS.
* **Recording stutters:** Use `CQP` instead of `CBR`.

---

📌 Maintainer: \[Dzaky]

Contributions welcome! Fork this repo, improve the guide, and submit PRs.
