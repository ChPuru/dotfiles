# "Mochi" — pastel Hyprland rice for Arch + NixOS (one repo)

## Context
You want a top-tier, cute, pastel Hyprland rice that installs on both Arch Linux and NixOS from one new repo, `nixandarch/`. The build runs in a **cloud session**, so this plan is self-contained.

The research is done:
- 24 reference repos (JaKooLit, NeKoRoSHELL, Cenaure, impasto, rumda, Panacea, hakuspace, trihmah, Sly-Harvey, librephoenix, datsfilipe, yunfachi, annmaro, workflow and others), NyArch, and the ecosystem as of 2026-09-25.
- The full digest and the verified syntax examples go into `docs/research/` in the repo (`digest.md`, `eco.json`).
- **Builders must read `docs/research/digest.md` §1, §3.4, §4, §6 and §7 before writing any config.**

**Your choices:**
- **Hardware:** both OSes on one **portable USB SSD**.
  - It boots on your AMD/Intel laptop (the main target), and sometimes on a Lenovo LOQ 15IRX9 (i7-13650HX + RTX 4060 hybrid with a MUX switch, 1080p 144 Hz, Windows on its internal disk).
  - The installers can also put one OS on a whole internal disk.
  - Secure Boot is off.
- **Look:** a pastel kawaii default, plus a one-click **Material You mode** that takes its colors from the wallpaper (matugen). "Cute but tasteful" anime level: kaomoji, cute icons, optional anime wallpapers downloaded at install (never bundled).
- **Custom Quickshell shell.**
- **Tools:** zsh + starship, kitty + Neovim.
- **Scope:** full install automation from the ISO, including the boot menu, Plymouth and the login screen.

## Pinned versions (Arch and nixos-unstable ship the same ones)
- Hyprland **0.56.2** with the **Lua config**. hyprlang is deprecated and gets removed in 0.57.
- Quickshell **0.3.1**
- matugen **4.2.0**
- awww **0.12.1** (swww was renamed to awww)
- hyprlock 0.9.6 (still a hyprlang `.conf`)
- hypridle 0.1.8
- kitty 0.48
- fastfetch 2.68
- rofi 2.0 (fallback only)
- Limine 12.x

NixOS tracks **nixos-unstable + home-manager master** so its versions match Arch's.

## Repo layout
```
nixandarch/
  PLAN.md  README.md  docs/research/{digest.md,eco.json}
  home/                           # SINGLE SOURCE of raw dotfiles (XDG tree) for both distros
    .config/hypr/hyprland.lua     # require()s: env, gpu, monitors, input, looks, animations, rules, binds, autostart
    .config/hypr/lua/*.lua        #   + pcall(require,"host") (gitignored per-machine) + require("mochi-colors") from state dir
    .config/hypr/{hyprlock.conf,hypridle.conf}
    .config/quickshell/mochi/     # the shell (`//@ pragma ShellId mochi`)
    .config/{kitty,nvim,starship.toml,fastfetch,btop,cava,yazi,gtk-3.0,gtk-4.0,qt6ct,zsh,matugen}
    .local/bin/mochi-*            # theme, wallpaper, screenshot, power, gpu-run helpers (call binaries by name, no FHS paths)
  theme/palettes/mochi.json       # curated pastel palette (source of truth for static mode)
  theme/templates/*               # matugen-format templates for every app
  arch/   install.sh · stage2.sh · packages/{base,desktop,aur}.txt
  nixos/  flake.nix · lib/mkHost.nix · hosts/{portable,laptop}/ · modules/{system,desktop,home}/ · disko/ · install.sh
  assets/ plymouth/mochi/ (own art) · limine/ · sddm/ · fastfetch/ · fetch-wallpapers.sh (+ licence manifest)
  tests/  check.sh
```

## Key design

### Sharing dotfiles
- The raw files in `home/` are the only copy.
- **Arch:** `stow --no-folding -t ~ home` symlinks them into place.
- **NixOS:** home-manager links them with `xdg.configFile.<app>.source = config.lib.file.mkOutOfStoreSymlink "${config.home.homeDirectory}/nixandarch/home/.config/<app>"`. It must be an absolute string path.
- Home-manager handles **only** packages, services, fonts, cursor and system glue.
- **Never enable HM's `wayland.windowManager.hyprland`, `programs.kitty.settings` or `programs.quickshell.configs`.** HM writes `hyprland.lua` whenever its Hyprland module is enabled with `systemd.enable = true`, which would clobber our link.
- Hyprland starts with `start-hyprland`, and UWSM is not used.

### Theme engine (one pipeline, two palette sources)
- All generated files go to `$XDG_STATE_HOME/mochi/`, which is never tracked. Configs only `require`, `@import`, `include` or `source` from there.
- **Static mode** feeds `theme/palettes/mochi.json` through the same templates. **Dynamic mode** runs `matugen image <wall> --source-color-index 0 -m dark`, which is non-interactive.
- Contrast lifting (impasto): accents are kept at ≥ 4.5:1 contrast against the surface and lightened toward pastel instead of darkened.
- The Mochi palette:

  | Role | Color |
  |---|---|
  | base | `#1e1b2e` |
  | mantle | `#18152a` |
  | surface | `#2a2540` |
  | overlay | `#3a3354` |
  | text | `#f4e9ff` |
  | subtext | `#bfb3d9` |
  | pink | `#f5b8d8` |
  | lavender | `#c9b6ff` |
  | mauve | `#e3a6ff` |
  | peach | `#ffc9a8` |
  | mint | `#b5ead7` |
  | sky | `#a8d8ff` |
  | butter | `#fff1b3` |
  | rose | `#ff9fb2` |

- Live reload with no restarts:
  - Hyprland: `hyprctl eval` of the generated `mochi-colors.lua`.
  - Quickshell: `FileView{watchChanges:true}` on `colors.json`.
  - kitty: `kitten themes --reload-in=all` through its remote-control socket (`listen_on`).
  - Neovim: a SIGUSR1 `Signal` autocmd.
  - GTK: adw-gtk3 plus a generated `colors.css` (`@define-color`) for GTK 3 and 4.
  - Qt: a generated qt6ct color scheme.
  - Also generated: btop, cava, fzf, starship (ANSI names), hyprlock, SDDM and Limine colors.
- Icons: Papirus-Dark, with a user icon theme whose folder icons are symlinked to the swatch nearest the accent (impasto; no root needed).
- Cursor: catppuccin-mocha-pink, with hyprcursor.

### Look
- Rounding 16 (`rounding_power` 3), gaps 6/12, a 2 px pink→lavender gradient border at 45° (`borderangle loop` is allowed only because the border is a gradient).
- Blur 7×3 with vibrancy 0.1696, a soft shadow (range 16, `render_power` 3), inactive opacity 0.92.
- Motion: impasto's "Bounce" curve `[0.05,0.9,0.1,1.15]` as the default. Windows `popin 80%`, workspaces `slide`, layers `popin 90%`, and `fadeIn` is off so the wallpaper doesn't flash.
- Presets switchable at runtime through one `hyprctl eval`: Glide, Brisk, Calm, Bounce, None.
- Use `hl.curve(name,{type="bezier",points=…})` and `hl.animation{leaf=…,bezier=…}`, **never `curve =`**; the wiki has a bug on this.
- Fonts: **Maple Mono NF CN** for code, **Nunito** for the UI, **Material Symbols Rounded** for icons, plus Noto Color Emoji and Noto CJK. No proprietary fonts.
- Layer blur applies to the `mochi-*` Quickshell namespaces.

### Quickshell shell `mochi` (with top features taken from the research)
- **Floating pill bar**: a springy workspace capsule with its own pastel color per workspace. It uses `Behavior on implicitWidth` with OutBack easing and `HyprlandWorkspace.activate()`, and shows up to 3 app glyphs plus "+N" (Cenaure, Bocchi, rumda).
  - Also: clock with a pastel calendar, a media chip, tray, and network/bluetooth/battery/volume/brightness indicators.
- **Popups that grow out of the bar like an island** (Cenaure `FloatingPanel` + Panacea concave notch corners): one fullscreen `PanelWindow` per screen with a `Region` mask and `HyprlandFocusGrab`.
- **Control center**: toggles, sliders, power profile, night light (hyprsunset), animation preset, and a Mochi/Material You switch.
- **Notifications**: Quickshell `NotificationServer` (set `tracked = true`), popups plus a center with cute empty states ("nothing to catch up on (˶ᵔ ᵕ ᵔ˶)"). Mask dunst, mako and swaync.
- **Launcher with prefixes** (impasto/datsfilipe): `=` calculator, `'` clipboard with images (cliphist), `@` windows, `>` commands, and "leaf" corners `15/5/15/5` (auspicious).
- **Wallpaper picker grid** with thumbnails. Applying one runs an awww `grow` transition from the click position and a **choreographed theme switch**: the bar slides out, apps re-theme, the bar slides back in (rumda).
- **Widgets that appear only on an empty desktop**: a media card with cava and a stats card (Cenaure, datsfilipe).
- **Rounded screen corners** (hakuspace), and volume/brightness/keyboard-layout OSDs.
- **Session panel** with a confirm step.
- `IpcHandler` for every action. Hyprland binds call `qs -c mochi ipc call <target> <fn>`.
- One tokens singleton for colors, motion (fast/std-overshoot/slow/enter/exit, as in datsfilipe) and font sizes.
- **Optional:** a pixel cat mascot (your own sprite) that hops when the theme changes.

### Lock, idle and login
- **hyprlock**:
  - Screenshot background with blur 4×3.
  - A card with rounding 48 and a **stacked two-tone clock**: hour in the accent, minutes at 80% white (hakuspace, greed-d).
  - Greetings by time of day, and a **now-playing polaroid** (trihmah `image{}` + `reload_cmd`, rotated −6°).
  - Placeholder "Pawsword? :3c" and failure text "OwO what's this?".
- **hypridle** (by default, in this order): dim at 240 s, lock at 300 s, screen off at 330 s (via Lua dispatch), suspend on battery at 900 s.
- **Login**: SDDM with SilentSDDM in a Mochi preset, with sddm-astronaut as a fallback. Builders check which one is packaged on both distros.

### Apps
- **kitty**: `cursor_trail 3`, background opacity 0.9, a remote-control socket, and `symbol_map` to Symbols Nerd Font.
- **Neovim**: lazy.nvim, a colors module generated by matugen, and a cute dashboard.
- **zsh**: antidote plugins (autosuggestions, syntax highlighting, fzf-tab), starship, zoxide, eza, bat.
- **fastfetch**: a `kitty-direct` PNG logo with a `command-raw` fallback.
- Also: yazi (with the termfilechooser portal), Thunar, btop, cava, Firefox, hyprpolkitagent, cliphist, grimblast (nixpkgs) / hyprshot (Arch extra) for screenshots with satty for editing, and brightnessctl.

### Disks
- **Shared mode (default, for the portable SSD):**
  - GPT with a 2 GiB ESP (label `MOCHI_ESP`) and one btrfs filesystem (label `mochi`, zstd:1, noatime, commit=120).
  - Subvolumes: `@arch @arch-home @arch-log @nixos @nixos-home @nix @shared @snapshots`.
  - Each OS has its own home; `@shared` is mounted in both at `~/Shared`, with uid 1000 on both. Swap is zram.
- **Single mode (one OS on a whole disk):** the same layout with only that OS's subvolumes.
- Installer safety:
  - Either OS can be installed first. The first creates the layout, and only after you type the disk name. The second **joins** it, creating only its own subvolumes and never formatting.
  - `--dry-run` shows what would happen, everything is logged, and USB/removable disks are clearly marked in the disk list.
- Portability:
  - Limine is installed at the fallback path `/EFI/BOOT/BOOTX64.EFI`, and firmware boot entries are never written (`canTouchEfiVariables = false`).
  - The early-boot image includes xhci_pci, usb_storage, uas, nvme, i915, amdgpu and both microcodes.
  - Mounts use labels, and journald keeps logs in RAM (`Storage=volatile`).

### Boot
- **Limine** with pastel colors and a Mochi wallpaper.
- In shared mode NixOS owns `limine.conf` (`boot.loader.limine.style.*`, `extraEntries` with fixed Arch entries). Arch keeps its kernel and initramfs at fixed ESP paths under `/arch/`.
- If only Arch is installed, it writes `limine.conf` with a marker comment, and NixOS takes the file over later.
- **The first build task:** read NixOS's `limine-install.py` and confirm it leaves `/arch/` alone. If it doesn't, fall back to systemd-boot with standard boot-loader entries from both OSes.
- Don't use the `EFI/arch-limine/` path, and don't use limine-snapper-sync in shared mode.
- Menu entries: Arch ♡, Arch (safe graphics), NixOS ✿, NixOS · NVIDIA (a specialisation), NixOS (safe graphics).
- **Plymouth "mochi"**: our own GPL script theme in NyArch style, with our own art (no NyArch images). OwO faces (`>w<` idle, `OwO` typing, `^w^` success, `>-<` fail) and paw-print password bullets. The hard-coded `/usr/share` path is substituted on NixOS. Early KMS keeps it flicker-free.

### GPU (the same SSD on the AMD/Intel laptop and the NVIDIA LOQ)
- `nvidia-open` ships on both OSes: `nvidia-open-dkms` on Arch, a specialisation on NixOS with `hardware.nvidia.open = true`. The driver loads only if an NVIDIA device exists.
- `hypr/lua/gpu.lua` checks for `/sys/module/nvidia`. If it's there, it sets the NVIDIA environment variables (`LIBVA_DRIVER_NAME`, `__GLX_VENDOR_LIBRARY_NAME`, `NVD_BACKEND=direct`), and it orders `AQ_DRM_DEVICES` so the iGPU comes first.
- `mochi-gpu-run` launches apps on the dGPU (PRIME offload).
- The monitor setting uses `highrr` for 144 Hz.
- No `desc:`, `hwmon` or `BAT0` names are hard-coded.

### NixOS structure (patterns from Sly-Harvey, annmaro, workflow and librephoenix)
- A plain flake with `lib/mkHost.nix`. Home-manager runs as a NixOS module with `useGlobalPkgs`, `useUserPackages` and `backupFileExtension`.
- **Typed `mochi.*` options** (enable toggles, `isLaptop`, `sharedDisk`, `user`). Every module is imported and gated with `mkIf`; no untyped variables file edited with sed.
- `programs.hyprland.enable` uses the nixpkgs package. hyprpm is not used; plugins would come from `hyprlandPlugins.*`.
- Hosts: `portable` (the default, hardware-neutral) and `laptop` (single mode).
- The installer uses disko to create the layout on an empty disk, or a join script for an existing one. It writes `hosts/<h>/settings.nix`, runs `nixos-generate-config --no-filesystems`, then `nixos-install --flake .#<h>`.

### Arch installer
- **Why a pacstrap script and not archinstall:** "join an existing disk" needs that control.
- **`install.sh`** runs as root from the ISO. It installs git and gum on the live system (falling back to plain `read`), partitions or joins the disk, runs `pacstrap -K`, then does the chroot steps: locale, user, `mkinitcpio` with the plymouth hook and early KMS, Limine, and services. Services: NetworkManager with iwd, pipewire, bluetooth, power-profiles-daemon, SDDM, thermald on Intel.
- **`stage2.sh`** runs as your user:
  - Package installs: one `pacman -Syu --needed` transaction, **yay-bin** for AUR packages (paru-bin is orphaned), and a short, reviewed AUR list.
  - Setup: `stow`, snapper + snap-pac for `@arch` only, and a first theme render.
  - Style: `set -Eeuo pipefail` with an ERR trap, backups of existing files, and `pacman.conf` with Color, ILoveCandy and ParallelDownloads.

## How the build is split
Four builder agents work on separate folders at the same time, then one reviewer checks everything:
1. `theme/` + `home/.config` (except quickshell) + `home/.local/bin`
2. `home/.config/quickshell/mochi`
3. `nixos/`
4. `arch/` + `assets/` + `tests/`

All of them follow `docs/research/digest.md`, especially the §7 list of deprecated syntax.

## Verification (in the cloud session: Linux + Docker)
- `shellcheck` on every script. `luac -p` on every Hyprland `.lua` file and on the generated `mochi-colors.lua`.
- `nixos/nix` container:
  - `nix flake check`
  - `nix build .#nixosConfigurations.{portable,laptop}.config.system.build.toplevel --dry-run`: evaluates both configs fully without building them.
- `archlinux` container:
  - Check every package in `packages/*.txt` exists, via `pacman -Si` and the AUR's RPC API.
  - Install hyprland and quickshell.
  - Run `qmllint` on the QML and `Hyprland --verify-config` if that flag is available.
  - Render the templates from both palette sources (static and matugen with a sample wallpaper) and check contrast.
- **Fake-disk test**: a privileged container with a loop-device disk runs both installers in both orders, Arch→NixOS and NixOS→Arch. The second must join without formatting. It checks the labels, subvolumes, fstab and `limine.conf` entries, plus the `--dry-run` output.
- **Not possible in the cloud:** seeing the rice on real hardware. The first boot on your laptop is the final test. The README includes a checklist, troubleshooting steps, and the LOQ notes: turn Secure Boot off, set `RealTimeIsUniversal` in Windows, use MUX Hybrid mode for battery life.
