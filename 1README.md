# Niri & Noctalia Desktop Environment Dotfiles

[![OS: Fedora](https://img.shields.io/badge/OS-Fedora%20Linux-blue?logo=fedora&logoColor=white)](https://fedoraproject.org/)
[![Compositor: Niri](https://img.shields.io/badge/WM-Niri-darkgreen)](https://github.com/YaLTeR/niri)
[![Shell: Noctalia](https://img.shields.io/badge/Shell-Noctalia-purple)](https://github.com/noctalia-shell)
[![Terminal: Ghostty](https://img.shields.io/badge/Terminal-Ghostty-black)](https://ghostty.org/)
[![DNS: Quad9](https://img.shields.io/badge/DNS-Quad9%20DoT-red)](https://quad9.net/)

A professional, keyboard-driven Wayland workspace configuration for Fedora Linux. This repository aggregates declarative configurations for the Niri scrollable-tile window manager, Noctalia shell, Ghostty terminal, custom typography, and hardened DNS-over-TLS via systemd-resolved.

---

## Table of Contents

- [Environment Architecture](#environment-architecture)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Install Niri (Wayland Compositor)](#2-install-niri-wayland-compositor)
  - [3. Install Noctalia Shell](#3-install-noctalia-shell)
  - [4. Install Ghostty Terminal](#4-install-ghostty-terminal)
  - [5. Install Fonts](#5-install-fonts)
  - [6. Install Cursor Theme](#6-install-cursor-theme)
  - [7. Configure Quad9 DNS-over-TLS](#7-configure-quad9-dns-over-tls)
- [Configuration](#configuration)
  - [Niri](#niri)
  - [Noctalia](#noctalia)
  - [Ghostty](#ghostty)
  - [Noctalia Plugins](#noctalia-plugins)
- [Post-Installation](#post-installation)
- [Troubleshooting](#troubleshooting)

---

## Environment Architecture

| Component | Implementation | Configuration |
|:---|:---|:---|
| **Operating System** | Fedora Workstation | System-wide package dependencies |
| **Display Compositor** | Niri (Wayland) | Infinite horizontal layout (`config.kdl`) |
| **User Interface Shell** | Noctalia Shell | Top-bar panel, QML modules, plugins |
| **Terminal Emulator** | Ghostty | Native Wayland rendering, custom styling |
| **Hardware Cursors** | Bibata Modern Ice | Consistent vector-scale cursor set |
| **Upstream DNS** | systemd-resolved | Strict DNS-over-TLS via Quad9 nodes |

---

## Repository Structure

```text
.
├── config/
│   ├── ghostty/                  # Terminal config + Noctalia colorscheme
│   │   ├── config                # Main Ghostty configuration
│   │   ├── config.ghostty        # Secondary config entrypoint
│   │   └── themes/
│   │       └── noctalia          # Noctalia colorscheme for Ghostty
│   ├── niri/
│   │   └── config.kdl            # Window manager keybindings & workspace rules
│   └── noctalia/
│       ├── colors.json           # Global color token definitions
│       ├── plugins.json          # Active plugin registry
│       ├── settings.json         # Shell-wide settings
│       ├── user-templates.toml   # Custom bar/widget templates
│       ├── colorschemes/         # Additional colorscheme slots
│       └── plugins/
│           ├── fancy-audiovisualizer/   # GLSL shader audio visualizer widget
│           └── mpris-lyric/             # MPRIS-based real-time lyric display
├── fonts/
│   ├── JetBrainsMonoNL-Regular.ttf
│   ├── JetBrainsMonoNL-SemiBold.ttf
│   ├── MonaspaceRadon-Bold.otf
│   ├── MonaspaceRadon-ExtraBold.otf
│   ├── MonaspaceRadon-Light.otf
│   ├── MonaspaceRadon-Medium.otf
│   ├── OpenSans-Regular.ttf
│   └── OpenSans-SemiBold.ttf
├── quad9_dns/
│   ├── resolved.conf             # Hardened systemd-resolved DoT profile
│   └── readme.txt                # DNS setup notes
├── themes/
│   └── icons/
│       └── Bibata-Modern-Ice/    # Full X11 + Wayland cursor theme
│           ├── cursor.theme
│           ├── index.theme
│           └── cursors/          # Binary cursor files + symlink tree
└── assets/
    └── media/                    # Screenshots and demo media
```

---

## Prerequisites

Ensure the following are available on your Fedora system before proceeding.

```bash
# Update system packages
sudo dnf update -y

# Core build and runtime dependencies
sudo dnf install -y \
  git \
  curl \
  cava \
  wget \
  cargo \
  systemd \
  fontconfig \
  xdg-user-dirs
```

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/forbidden402/niri-noctalia-dotfiles.git ~/Documents/dotfiles
cd ~/Documents/dotfiles
```

### 2. Install Niri (Wayland Compositor)

Niri is a scrollable-tiling Wayland compositor. Install it via the COPR repository or build from source.

**Option A — COPR (recommended):**

```bash
sudo dnf copr enable yalter/niri -y
sudo dnf install -y niri
```

**Option B — Build from source:**

```bash
# Install Rust toolchain if not present
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

# Install build dependencies
sudo dnf install -y \
  gcc \
  libxkbcommon-devel \
  wayland-devel \
  mesa-libEGL-devel \
  libinput-devel \
  pipewire-devel \
  pango-devel \
  cairo-devel

# Build and install
cargo install --git https://github.com/YaLTeR/niri.git niri
```

**Deploy the Niri configuration:**

```bash
mkdir -p ~/.config/niri
cp config/niri/config.kdl ~/.config/niri/config.kdl
```

**Validate the configuration:**

```bash
niri validate --config ~/.config/niri/config.kdl
```

### 3. Install Noctalia Shell

```bash
# Install Noctalia via its installation script or package
# Refer to https://github.com/noctalia-shell for the latest method

# Once installed, deploy the configuration
mkdir -p ~/.config/noctalia
cp config/noctalia/settings.json   ~/.config/noctalia/settings.json
cp config/noctalia/colors.json     ~/.config/noctalia/colors.json
cp config/noctalia/plugins.json    ~/.config/noctalia/plugins.json
cp config/noctalia/user-templates.toml ~/.config/noctalia/user-templates.toml

# Deploy plugins
cp -r config/noctalia/plugins/fancy-audiovisualizer \
      ~/.config/noctalia/plugins/fancy-audiovisualizer

cp -r config/noctalia/plugins/mpris-lyric \
      ~/.config/noctalia/plugins/mpris-lyric
```

### 4. Install Ghostty Terminal

```bash
# Fedora — install via the official Ghostty repository or Flatpak
# Check https://ghostty.org for the current Fedora package

# Flatpak (universal fallback):
flatpak install flathub com.mitchellh.ghostty -y

# Deploy configuration
mkdir -p ~/.config/ghostty/themes
cp config/ghostty/config        ~/.config/ghostty/config
cp config/ghostty/config.ghostty ~/.config/ghostty/config.ghostty
cp config/ghostty/themes/noctalia ~/.config/ghostty/themes/noctalia
```

### 5. Install Fonts

```bash
# Create user font directory
mkdir -p ~/.local/share/fonts

# Copy all bundled fonts
cp fonts/*.ttf ~/.local/share/fonts/
cp fonts/*.otf ~/.local/share/fonts/

# Rebuild the font cache
fc-cache -fv

# Verify fonts are detected
fc-list | grep -E "JetBrains|Monaspace|OpenSans"
```

### 6. Install Cursor Theme

```bash
# Install to user icons directory
mkdir -p ~/.local/share/icons
cp -r themes/icons/Bibata-Modern-Ice ~/.local/share/icons/

# Apply the cursor theme via gsettings (GNOME/GTK stack)
gsettings set org.gnome.desktop.interface cursor-theme 'Bibata-Modern-Ice'

# For Niri / pure Wayland — set in ~/.config/gtk-3.0/settings.ini
mkdir -p ~/.config/gtk-3.0
cat >> ~/.config/gtk-3.0/settings.ini <<'EOF'
[Settings]
gtk-cursor-theme-name=Bibata-Modern-Ice
gtk-cursor-theme-size=24
EOF

# For GTK4
mkdir -p ~/.config/gtk-4.0
cat >> ~/.config/gtk-4.0/settings.ini <<'EOF'
[Settings]
gtk-cursor-theme-name=Bibata-Modern-Ice
gtk-cursor-theme-size=24
EOF

# Set system-wide default (optional)
sudo mkdir -p /usr/share/icons/default
sudo tee /usr/share/icons/default/index.theme > /dev/null <<'EOF'
[Icon Theme]
Inherits=Bibata-Modern-Ice
EOF
```

### 7. Configure Quad9 DNS-over-TLS

> **Note:** These files are owned by `root`. All commands in this section require `sudo`.

```bash
# Back up the existing resolved configuration
sudo cp /etc/systemd/resolved.conf /etc/systemd/resolved.conf.bak

# Deploy the hardened Quad9 DoT profile
sudo cp quad9_dns/resolved.conf /etc/systemd/resolved.conf

# Restart systemd-resolved to apply changes
sudo systemctl restart systemd-resolved

# Verify DNS resolution is working
resolvectl status

# Confirm DoT is active (look for DNSOverTLS: yes)
resolvectl show-server-state
```

The deployed `resolved.conf` configures:
- **DNS servers:** `9.9.9.9` and `149.112.112.112` (Quad9)
- **DNS-over-TLS:** strict mode (connections refused if DoT handshake fails)
- **DNSSEC:** enabled
- **Fallback DNS:** disabled for strict routing

---

## Configuration

### Niri

The main configuration lives at `~/.config/niri/config.kdl`. Key areas to customise:

```kdl
// Modify your preferred terminal
spawn-at-startup "ghostty"

// Adjust natural scroll direction
input {
    touchpad {
        natural-scroll
    }
}

// Set your display output name
output "eDP-1" {
    scale 1.0
}
```

After editing, reload without restarting:

```bash
niri msg action reload-config
```

### Noctalia

Shell-wide settings are in `~/.config/noctalia/settings.json`. Color tokens are separated into `colors.json` for easier theming without touching layout settings.

To switch or create a colorscheme:

```bash
# Colorscheme slots live here — drop a new JSON file in
ls ~/.config/noctalia/colorschemes/
```

To reload Noctalia after changes:

```bash
noctalia reload
# or kill and restart the bar process
pkill noctalia && noctalia &
```

### Ghostty

`~/.config/ghostty/config` controls font, theme, padding, and keybindings. The `noctalia` theme file in `themes/` is referenced by name:

```ini
theme = noctalia
font-family = JetBrains Mono NL
font-size = 13
```

To apply changes, simply open a new Ghostty window — it re-reads config on launch.

### Noctalia Plugins

#### Fancy Audiovisualizer

A GLSL shader-based audio visualizer rendered as a desktop widget.

```bash
# Plugin files are at:
~/.config/noctalia/plugins/fancy-audiovisualizer/

# Key files:
#   DesktopWidget.qml         — main widget layout
#   DesktopWidgetSettings.qml — settings panel
#   shaders/visualizer.frag   — GLSL fragment shader
```

Enable it by adding the plugin entry to `~/.config/noctalia/plugins.json` and reloading Noctalia.

#### MPRIS Lyric

Displays real-time lyrics for the currently playing track via MPRIS D-Bus.

```bash
# Plugin files are at:
~/.config/noctalia/plugins/mpris-lyric/

# Key files:
#   Main.qml        — core widget logic
#   BarWidget.qml   — compact bar-mode display
#   LyricParser.js  — LRC/timed lyric parser
#   Settings.qml    — configuration panel
```

Requires a running MPRIS-compatible media player (e.g. Rhythmbox, Spotify, mpv with mpris plugin).

---

## Post-Installation

Run the following after all steps are complete to launch into the new environment.

```bash
# Start Niri from a TTY (not from an existing Wayland/X session)
niri-session

# Or, if a session entry is installed, select "Niri" from your display manager

# Verify all services are running
systemctl --user status noctalia   # shell/bar
resolvectl status                  # DNS-over-TLS
```

### Recommended additional packages

```bash
sudo dnf install -y \
  waybar \          # fallback status bar (optional, Noctalia preferred)
  wofi \            # application launcher
  mako \            # Wayland notification daemon
  swaylock \        # screen locker
  grim \            # screenshot tool
  slurp \           # region selector for screenshots
  wl-clipboard \    # clipboard utilities (wl-copy / wl-paste)
  playerctl         # MPRIS media controls (required for mpris-lyric plugin)
```

---

## Troubleshooting

**Niri fails to start**
Run `niri validate --config ~/.config/niri/config.kdl` and check for KDL syntax errors. Ensure your GPU drivers support Wayland (`mesa` or proprietary).

**Cursor theme not applying in some apps**
Set `XCURSOR_THEME=Bibata-Modern-Ice` and `XCURSOR_SIZE=24` in your environment file (`~/.config/environment.d/cursor.conf` for systemd-based sessions).

**DNS-over-TLS not active**
Confirm `DNSOverTLS=yes` in `/etc/systemd/resolved.conf` and that port 853 is not blocked by a firewall rule. Check with `resolvectl show-server-state`.

**Noctalia plugins not loading**
Verify the plugin `id` field in `manifest.json` matches the entry in `plugins.json`. Check Noctalia logs with `journalctl --user -u noctalia -f`.

**Fonts not rendering in Ghostty**
Run `fc-cache -fv` and confirm the font name with `fc-list | grep "JetBrains"`. Ensure the exact family name in `~/.config/ghostty/config` matches the output.

---

> Configuration files in `quad9_dns/` are owned by `root` and require `sudo` to modify. All other dotfiles are owned by the user and can be edited freely.
