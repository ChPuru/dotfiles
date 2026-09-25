# Hyprland rice research digest: one repo for Arch and NixOS (as of 2026-09-25)

This digest combines 24 reports on reference repos and 7 reports on the current state of the ecosystem. Facts come from those reports. Anything I recommend or propose myself is marked **Proposal**. Where reports disagree or a claim wasn't checked, I say so with **CONFLICT** or **UNVERIFIED**.

**Short names used for the reference repos**

| Tag | Repo | Target | Tag | Repo | Target |
|---|---|---|---|---|---|
| JaKooLit | JaKooLit/Arch-Hyprland + Hyprland-Dots | Arch | Panacea | EnsixD/Panacea | Arch-first |
| Bocchi | Spelljinxer/dotfiles | Arch | Sly-Harvey | Sly-Harvey/NixOS | NixOS |
| auspicious | LoneWolf4713/auspicious-dots | Arch | oddlama | oddlama/nix-config | NixOS (niri) |
| zenities | hayyaoe/zenities | Arch | datsfilipe | datsfilipe/dotfiles | NixOS (niri) |
| Kakikori | WasamiKirua/Kakikori-Hyprland-Rice | Arch | yunfachi | yunfachi/nix-config | NixOS |
| trihmah | trihmah/dotfiles | Arch | Sigmanificient | Sigmanificient/dotfiles | NixOS (Qtile/X11) |
| NeKoRoSHELL | NeKoRoSYS/NeKoRoSHELL | Arch-first | annmaro | annmaro/nixri | NixOS (niri) |
| Cenaure | Cenaure/dotfiles | Arch | librephoenix | librephoenix/nixos-config | NixOS |
| saurabh | saurabhcodes25/dotfiles | Arch (bspwm/X11) | workflow | workflow/dotfiles | NixOS (niri) |
| kamol | kamolmachine/dotfiles | Arch (i3/X11) | rumda | Nytril-ark/rumda | Fedora (Hyprland) |
| garuda | yurihikari/garuda-hyprdots | Arch | hakuspace | hakuimaku/hakuspace | Arch, Fedora, NixOS |
| impasto | andreumassanet/impasto | Arch | greed-d | greed-d/.dotfiles | Arch |

The seven ecosystem reports cover Hyprland, shells, theming, NixOS, Arch, NyArch and assets.

---

## 1. Which components the repos use, and what to pick

All counts are out of 24 repos and count what each repo actually uses, not just what it has installed.

| Component | What the 24 repos use (count) | Best pick for Sept 2026 | Deprecated, renamed, or watch out |
|---|---|---|---|
| **Compositor** | Hyprland 17. niri 4 (oddlama, datsfilipe, annmaro, workflow). X11 3 (saurabh bspwm, kamol i3, Sigmanificient Qtile). niri also appears as a secondary in greed-d, Panacea (beta), hakuspace and trihmah's sidebar. | **Hyprland 0.56.2** (Arch extra and nixos-unstable), started with `start-hyprland`. UWSM is optional. | nixos-26.05 ships 0.55.4 and nixos-25.11 ships 0.52.2. Starting it with `Hyprland` directly has been superseded since 0.53 and now triggers a watchdog warning. |
| **Hyprland config language** | Lua 6 (zenities, Cenaure, hakuspace, impasto, Panacea, Sly-Harvey). hyprlang with 0.53+ syntax 3 (JaKooLit, NeKoRoSHELL, garuda). Older hyprlang 8 (Bocchi, auspicious, Kakikori, trihmah, rumda, greed-d, yunfachi, librephoenix, the last pinned at 0.52.2). | **`hyprland.lua` split with `require()`**, plus `pcall(require, "host")` for per-machine overrides. | hyprlang has been deprecated since 0.55. It gets a deprecation notice in 0.56.1, is already removed on main (#15539), and is expected to be gone in 0.57. `windowrulev2`, the `gestures{workspace_swipe}` block, flat `drop_shadow`/blur keys and the `togglesplit` dispatcher are all gone. |
| **Shell / bar** | Waybar 11 (JaKooLit, Bocchi, auspicious, Kakikori, trihmah, NeKoRoSHELL, garuda, hakuspace, greed-d, Sly-Harvey, workflow). Custom Quickshell 5 (Cenaure, rumda, impasto, Panacea, datsfilipe). DMS 1 (annmaro). noctalia 1 (oddlama; also optional in Sly-Harvey and annmaro). eww 1 (zenities, which half-migrated to qs). ashell 1 (librephoenix). polybar 2. Qtile bar 1. none 1 (yunfachi). | **Custom Quickshell 0.3.1** (Arch extra and nixpkgs-unstable). Ready-made alternative: **DankMaterialShell 1.6.2**, which has the best packaging on both distros. Fallback: Waybar 0.15.0. | HyprPanel is archived (nixpkgs throws). AGS v1 is deprecated (JaKooLit, garuda). eww's last release was 2024-04. noctalia-shell v4, the Quickshell version, is unmaintained; v5 is native C++. Ignis is being rewritten in Rust. Waybar workspace clicks fail when Hyprland runs a Lua config (#5008/#5035, closed, but no fix confirmed in a tagged release). |
| **Launcher** | rofi 15 (JaKooLit, Bocchi, auspicious, zenities, Kakikori, trihmah, NeKoRoSHELL, garuda, hakuspace, rumda, yunfachi, saurabh, kamol, Sly-Harvey, greed-d). fuzzel 3 (oddlama, librephoenix, workflow). Built into Quickshell 5 (Cenaure, impasto, Panacea, datsfilipe, annmaro). Qtile prompt 1. | **Launcher built into the Quickshell shell** (the impasto prefix-sigil and Cenaure morph patterns). Fallback: **rofi 2.0.0**. Secondary dmenu: fuzzel 1.15. | `rofi-wayland` was merged into rofi 2.0. Arch rofi provides/replaces it, and nixpkgs `rofi-wayland` now throws. |
| **Notifications** | dunst 7. swaync 6. Quickshell NotificationServer 4. mako 2. DMS/noctalia 2. fnott 1. homemade eww daemon 1 (not spec-compliant). none 1. | **Quickshell `NotificationServer`**. Fallback: swaync 0.12.6. | Only one process can own `org.freedesktop.Notifications`. archinstall's Hyprland profile installs dunst, so mask it. Quickshell drops notifications unless you set `n.tracked = true`. |
| **Lock** | hyprlock 10. swaylock/effects 4. Quickshell WlSessionLock 3 (impasto, Panacea, datsfilipe). X11 lockers 3. gtklock 1. noctalia 1. broken or missing 2 (Cenaure, zenities). | **hyprlock 0.9.6** (still a hyprlang `.conf`) or **Quickshell WlSessionLock + PAM**. | If Quickshell exits while locked, the session stays locked (docs warning). Lockers built by Nix can't use PAM on Arch (HM #7027). |
| **Idle** | hypridle 7. swayidle 3. Quickshell IdleMonitor 1. none 13. | **hypridle 0.1.8**. Its dpms examples now use Lua dispatch, and it supports `condition_cmd`. | `hyprctl dispatch dpms off` is the old syntax and fails under a Lua config. Ordering bugs found: annmaro turns DPMS off at 420 s but locks at 600 s; hakuspace dims and suspends both at 420 s. |
| **Logout / power menu** | rofi power menus 7. Quickshell session panel 5 (Cenaure, impasto, Panacea, datsfilipe, rumda). wlogout 3 (+1 via hyprdots). DMS/noctalia 2. wleave 1. The rest have none. | **Quickshell session panel** with a confirm step. Exit through `hyprshutdown` (or `uwsm stop`). | wlogout hasn't released since 2024-04. `kill -9 -1` for logout (auspicious) is dangerous. Unconfirmed `shutdown now` binds exist in greed-d and zenities. |
| **Wallpaper daemon** | swww/awww 14 (6 already call it `awww`). hyprpaper 3. swaybg 3. noctalia built-in 1. X11 3. mpvpaper for video in 5. | **awww 0.12.1** (Arch extra, nixpkgs, HM `services.awww`), plus **mpvpaper 1.9** for video. | **swww was renamed to awww** in 0.12.0. Arch's awww ships no `swww` binary, and nixpkgs' `swww` is a warnAlias. hyprpaper 0.8 dropped `preload =` in favour of `wallpaper{}` blocks. |
| **Colour pipeline** | Generated from the wallpaper at runtime 7: wallust 2 (JaKooLit, NeKoRoSHELL), pywal/wallbash 2 (Bocchi, Kakikori), matugen 1 (zenities), colorthief 1 (hakuspace), ImageMagick+Python 1 (impasto). Custom named themes switched at runtime 3 (Cenaure JSON, Panacea, rumda). Nix build-time 7: Stylix 3 (annmaro, librephoenix, workflow), catppuccin/nix 1 (Sigmanificient), custom Nix attrsets 3 (yunfachi base24, oddlama, datsfilipe). Static hex hand-copied into each app 7. | **matugen 4.2.0** with InioX/matugen-themes templates, run non-interactively (`--source-color-index 0`). Add WCAG contrast lifting (impasto). Keep a curated fallback palette. On NixOS use Stylix or catppuccin/nix **only for surfaces that are set at build time** (`autoEnable = false`). | matugen 4.x can stop at an interactive colour picker. nixos-26.05 ships matugen 4.0.0. wallust master is 4.x alpha and breaks palette names. nixpkgs `pywal` is now an alias for pywal16. Stylix has no runtime theming (issue #2031 is open). |
| **Terminal** | kitty 14. alacritty 5. foot 3. ghostty 1. wezterm 1. | **kitty 0.48.2**: `cursor_trail`, reload on SIGUSR1 or `kitten themes --reload-in=all`, and a remote-control socket for live recolouring. Alternative: Ghostty 1.3.1 (reloads on SIGUSR2). | foot does not reload its config at runtime; SIGUSR1/2 only switches between its dark and light colour sections. Old foot `[colors]` sections were removed in 1.28. |
| **Shell** | zsh 13, fish 8, bash 1, unconfigured 2. | **fish or zsh**. This is taste; nothing in the reports decides it. | — |
| **Prompt** | starship 8. p10k 5. oh-my-zsh themes 3. hand-written prompt 3. oh-my-posh 1. hydro 1. none 3. | **starship 1.26** using ANSI colour names so it follows the terminal palette (impasto). | powerlevel10k is on "very limited support" (flagged by the Bocchi and Kakikori reports). |
| **Editor** | Neovim 13. VS Code/VSCodium 2. Emacs 1. none 8. | **Neovim** with a matugen colours template and a SIGUSR1 `Signal` autocmd. lush.nvim reading a JSON palette (zenities) also works. | NvChad v2 layouts copied by hand go stale (Kakikori). |
| **File manager** | Thunar 9. Nautilus 5. Dolphin 2. yazi as the main one 2 (yazi appears in about 9 repos in total). Built into Quickshell 1 (Panacea). pcmanfm 1. ranger 1. none 3. | **Thunar + yazi**. Use yazi as the GTK/portal file chooser via `xdg-desktop-portal-termfilechooser` (librephoenix). | yazi 25.x renamed the `manager` section to `mgr` (annmaro report, **UNVERIFIED**). |
| **Fetch** | fastfetch 9. neofetch 5. catnip 1. microfetch 1. none 8. | **fastfetch 2.68.1** with a `kitty-direct` PNG logo (Kitty/Ghostty) and a `command-raw` fallback (needs 2.45 or later). | **neofetch is archived** (used by Kakikori, trihmah, saurabh, rumda, greed-d). |
| **Login manager** | SDDM 8 (JaKooLit, Bocchi, Kakikori, impasto, Panacea, Sly-Harvey, annmaro, librephoenix). ly 2. GDM 1. greetd 1 (datsfilipe, with a Quickshell greeter). tty or startx 3. none 9. | **Most cohesive:** greetd plus a Quickshell greeter that shares its UI with the lock screen (datsfilipe). **Simpler:** SDDM + SilentSDDM (Catppuccin presets) or sddm-astronaut (the nixpkgs build is current). | The sddm-astronaut AUR PKGBUILD is stuck at 2025-01 (Chaotic-AUR has a newer build). Re-theming SDDM at runtime needs a sudoers rule, because it runs as the sddm user. |
| **Boot theme** | Custom GRUB theme 2 (Panacea custom pf2 + 9-slice, Sly-Harvey distro-grub-themes). Plymouth 1 (yunfachi, breeze). catppuccin/nix system-level 1 (Sigmanificient; whether it themes GRUB is **UNVERIFIED**). rEFInd text-only 1. The other ~19 have none. | **Limine** (Arch extra 12.9.0, NixOS `boot.loader.limine.style.*`, catppuccin/limine palettes, Secure Boot support), plus **Plymouth** (catppuccin-plymouth or a custom paw-print theme). Fallback: catppuccin-grub. | archinstall's Limine install writes `EFI/arch-limine/limine.conf`, which takes priority over the `$ESP/limine.conf` that limine-snapper-sync maintains. adi1090x plymouth themes are dormant and about 524 MB unless you set `selected_themes`. |
| **GTK theme** | Catppuccin GTK 6. adw-gtk3 (+generated CSS) 4. Stylix 3. Adwaita 2. Andromeda, Fluent, WhiteSur, Midnight-Gray 1 each. none 5. | **adw-gtk3 plus a matugen `colors.css`** (libadwaita `@define-color`). Static option: **Magnetic Catppuccin** or **Colloid** with `tweaks=[catppuccin]`. | **catppuccin/gtk was archived on 2024-06-02.** nixpkgs removed gtk-engine-murrine and about 50 themes that depend on it on 2026-07-22 (Graphite, Vimix, Lavanda, Sweet and more). |
| **Qt** | Kvantum or templated qt5ct: JaKooLit, garuda, Sly-Harvey, workflow, librephoenix. qt6ct env or generated palette: NeKoRoSHELL, Cenaure, impasto. Stylix qt: annmaro, librephoenix. adwaita: oddlama, yunfachi. The rest: none. | **qt6ct with a generated colour scheme** (matugen qtct template or KDE `.colors` + Darkly). New option: hyprqt6engine (AUR only). | Rebuild qt6ct-kde/darkly after Qt updates. `QT_QPA_PLATFORMTHEME` settings conflict in Sly-Harvey and JaKooLit (set twice). |
| **Icons** | Papirus 10. Tela-circle 3. Adwaita 2. Flat-Remix, Reversal, WhiteSur, We10X 1 each. unset 5. | **Papirus-Dark with recoloured folders.** Either catppuccin-papirus-folders, or impasto's approach: a user icon theme that inherits Papirus-Dark and symlinks folder icons to the swatch nearest the accent, no root needed. | nixpkgs tela-circle only takes fixed colour names. Hex colours need the NyArch fork's `install.sh`. |
| **Cursor** | Bibata 11 (Modern-Ice 7, Classic 4). catppuccin-cursors 2. Quintom 2. BreezeX-RosePine 1. WhiteSur 1. Notwaita hyprcursor 1. elementary 1. none 5. | **Bibata Modern** (GPL-3.0; a hyprcursor fork exists) or **catppuccin-cursors** with `home.pointerCursor.hyprcursor.enable`. | Posy ports are CC-BY-NC. Anime cursor sets are NC or have no licence. |
| **Fonts** | Main monospace is JetBrainsMono NF in about 16 repos. Iosevka 3, Fira Code 3, Terminess 1. Maple Mono is installed in 2 (Sly-Harvey, annmaro). **5 repos use proprietary UI fonts:** SF Pro (auspicious), Product Sans (Kakikori), Segoe UI (oddlama), Google Sans + PP Neue Machina (greed-d). | Monospace: **JetBrainsMono NF** (Arch extra) or **Maple Mono NF CN** (nixpkgs `maple-mono.NF-CN-unhinted`). UI: **Nunito** (Arch extra, nixpkgs), or Google Sans Flex with its ROND axis (OFL, but not yet in nixpkgs). Icons: **Material Symbols Rounded**. Plus **Noto Color Emoji** and **Noto CJK**. | nixpkgs `noto-fonts-emoji` now throws; use `noto-fonts-color-emoji`. `pkgs.nerdfonts` is gone; use `nerd-fonts.<name>`. Nerd Fonts v2 codepoints were removed in v3 (Kakikori, saurabh). |

---

## 2. Top 25 features, ranked by wow × feasibility × cuteness

I scored each 1–5 on **W**ow, **F**easibility on both Arch and NixOS, and **C**uteness. The scores are my own judgement; the features themselves come from the reports.

1. **The whole desktop recolours live from the wallpaper** — JaKooLit (the ThemeChanger reload routine), zenities (`kitty @ set-colors`, `nvim --server … --remote-send`), impasto (WCAG 4.5:1 and 7:1 contrast lifting), NeKoRoSHELL (runs the colour generator on a 200 px thumbnail for speed). W5 F5 C4 = 100.
   *How:* matugen 4.2 templates, each with a post_hook: kitty `kitten themes --reload-in=all`, ghostty SIGUSR2, nvim SIGUSR1 autocmd, waybar SIGUSR2, Quickshell `FileView{watchChanges:true}` JSON, Hyprland `colors.lua` applied live with `hyprctl eval "hl.config(...)"` (Cenaure).
2. **A springy workspace capsule with a colour per workspace** — Cenaure (the highlight slides between slots in 220 ms OutCubic; up to 3 app glyphs plus "+N"; prefers `wayland.appId`), Bocchi (each persistent workspace in its own character colour via `nth-child`), rumda (chamfered 8 px pills, 34 px tall when active vs 19 px, OutQuart 200 ms), Sly-Harvey (elastic pill). W4 F5 C5 = 100.
   *How:* QML `Repeater { model: Hyprland.workspaces }` with a `Behavior on implicitWidth` using `Easing.OutBack`, an array of pastel colours indexed by workspace, and `HyprlandWorkspace.activate()`, which knows about Lua configs.
3. **A "now playing" polaroid on the lock screen** — trihmah. hyprlock `image{}` with `reload_cmd = art.sh`, `reload_time = 2`, 256 px, rounding 6, 3 px border. A second copy with `rotate = -6` behind it makes a polaroid stack. W4 F5 C5 = 100.
   *How:* rewrite `art.sh` to use `$XDG_CACHE_HOME`, `mkdir -p`, proper quoting, and handle `file://` art URLs. hyprlock stays hyprlang.
4. **A launcher with "leaf" corners and an illustrated banner** — auspicious. Elements use `border-radius: 15px 5px 15px 5px`, the entry uses the mirror image, the inputbar has `background-image: url(…, width)` with 100px 60px padding, colours are misty-rose #FFE4E1, lavender #645074 and sky #73C3EB. W4 F5 C5 = 100.
   *How:* rofi 2.0 `.rasi` or the same shape in QML. Use your own or CC0 banner art.
5. **Popups that grow out of the bar like an island** — Cenaure `FloatingPanel` (width animates OutQuint, height takes 1.1× as long, radius 0.8×, contents fade in after 45% of the duration), Panacea (the whole shell is one capsule; `NotchShape.qml` draws concave corners with `PathArc`), impasto (panel sizes declared up front, notch fillets via `PathAngleArc`). W5 F4 C4 = 80.
   *How:* one fullscreen `PanelWindow` per screen with a `Region` mask and HyprlandFocusGrab (the caelestia pattern from the shells report).
6. **A choreographed theme switch** — rumda: the bar slides off-screen over 600 ms InOutCubic, a Loader swaps the bar after 950 ms, awww runs a `grow` transition from the top-right over 1 s with bezier `0.4,0.0,0.2,1.0`, and every app's theme swaps before one `hyprctl reload`. JaKooLit: dark/light switches use a `grow` transition from the waybar button position `0.925,0.977`. W5 F4 C4 = 80.
   *How:* one script runs `awww img -t grow --transition-pos <button>`, `matugen -m light|dark …`, then `qs ipc call theme set …`.
7. **A Plymouth splash with OwO faces and "Pawsword? :3c"** — NyArch `nyarch-logo`. Faces: `>w<` idle, `OwO` typing, `^w^` success, `>-<` fail. Password bullets are paw prints cycling pink, hotpink, lightblue, darkblue. It picks 720p/1080p/1440p/4k assets by screen height. W4 F4 C5 = 80.
   *How:* the script is GPL-2.0-or-later. Redraw the mascot art yourself, because the licence of the logo images is unclear. On NixOS, substitute the hard-coded `/usr/share` path in `.plymouth`.
8. **A big stacked two-tone clock on the lock screen** — hakuspace (hour in the accent colour over minutes in white at 80%, 130 pt, on a 420×640 card with rounding 64, "Hi there, $USER"), greed-d (stacked HH/MM at 150 pt, greetings by time of day such as "GO TO SLEEP!", pill-shaped status chips). W4 F5 C4 = 80.
   *How:* hyprlock `label{}` plus a `shape{}` card. Swap greed-d's commercial PP Neue Machina for an OFL font.
9. **A pixel-art cat mascot** — rumda. A sprite on its own layer-shell surface with a 9-frame jump (each frame keyframes margin, size and rotation) and a 16-frame walk. It leaves the bar when the theme switches, and the wifi indicator is a cat face that frowns when offline. W5 F3 C5 = 75.
   *How:* a QML frame timer or AnimatedSprite. Use your own sprite art.
10. **Arch-chan as a Live2D desktop pet** — NyArch DesktopPuppet (GPL-3.0, gtk4-layer-shell + WebKit 6, "only Hyprland is fully supported"). W5 F3 C5 = 75.
    *How:* it only reads `hyprctl cursorpos` and `hyprctl monitors -j`. Convert its README binds to `hl.bind`. The model's licence isn't stated by NyArch; the Speykious archive says CC0, so check the exact file you use.
11. **A lock-screen title in three palette colours** — Bocchi. Three labels in `$color6`, `$color3` and `$color4` spell ぼっち・ざ・ろっく！ above a 64 pt clock and "Hi there, $USER". W3 F5 C5 = 75.
    *How:* use your own phrase, and install Noto CJK.
12. **A cute fetch logo** — impasto (an animated pixel-art GIF drawn in the current palette via kitty-icat, pixels scaled ×5 first), NyArch `nyaofetch` (shows a kitty PNG if the terminal supports it, otherwise ANSI art sized to the terminal width). W3 F5 C5 = 75.
    *How:* fastfetch `"type":"kitty-direct"` with `command-raw` as the fallback.
13. **A theme carousel** — Cenaure (SUPER+T). Thumbnails are 152×86 with radius 10; inactive ones scale to 0.84 at 50% opacity; 18 px swatches. Any `themes/*.json` is picked up automatically. `apply-theme.bash` runs one script per app, checks the generated Lua with `luac -p`, and writes files atomically. JaKooLit does the same with `wallust theme list | rofi`. W4 F4 C4 = 64.
14. **Widgets that appear only on an empty desktop** — Cenaure. A media card and a CPU/RAM/per-core card sit under the bar only when the focused workspace has no windows. They reflow in 320 ms, and the media card reveals its controls on hover. W4 F4 C4 = 64.
15. **A media dock with a cava visualizer** — datsfilipe. The bottom dock animates its `exclusiveZone` so windows move up smoothly. 96 cava bars at 60 fps sit behind a 62% scrim, with a seek strip. cava only runs while the dock is visible and music is playing. W4 F4 C4 = 64.
16. **Rounded screen corners and a cava strip under the bar** — hakuspace. A Cairo even-odd fill draws 20 px corners that clicks pass through. Spacer windows with an exclusive zone go on the **BOTTOM** layer to push the bar inward. A VTE terminal running cava sits under the bar. W4 F4 C4 = 64.
    *How:* reimplement inside one Quickshell process. The report puts hakuspace's separate Python helpers at 60–100 MB each.
17. **kitty cursor trail and small polish** — `cursor_trail 3` with `cursor_trail_decay 0.1 0.4` (hakuspace, yunfachi), `background_opacity` 0.8–0.9, and a `listen_on unix:${XDG_RUNTIME_DIR}/kitty.sock` socket for live recolouring (impasto). W3 F5 C4 = 60.
18. **Candy-coloured and fused bar pills** — auspicious (each module gets its own pastel background with a tinted foreground), trihmah (two modules share one capsule using `8px 0 0 8px` / `0 8px 8px 0` radii and ±5 px margins), garuda (floating `rgba(36,39,58,0.6)` pills, radius 10, margin 8). W3 F5 C4 = 60.
    *How:* generate the hues from one palette and check contrast. auspicious has #c8c8c8 text on #F5F5F5, which is nearly unreadable.
19. **Bar pets, sticky notes and mini-games** — impasto. Five pets that hatch from eggs and never die, in four art styles. Sticky notes in the Grape Nuts handwriting font (OFL) on a `#c4ffffff` paper wash. 13 mini-games. W5 F2 C5 = 50.
20. **A pastel calendar tooltip** — garuda, workflow and hakuspace all use months #ffead3, days #ecc6d9, weeks #99ffdd, weekdays #ffcc66, today #ff6699. W2 F5 C5 = 50.
21. **Cute wording** — Sigmanificient's lock text ("OwO what's this?", "beep boop...", "Oh no!"), NyArch ("Pawsword? :3c", the `nyay`/`pacnyan` wrappers, `ILoveCandy`), Cenaure's empty notification centre ("Nothing to catch up on"), rumda's kaomoji installer. W2 F5 C5 = 50.
    *How:* hyprlock `placeholder_text`, `fail_text`, notification copy.
22. **Windows in a matte picture frame** — rumda, using borders-plus-plus: an 11–13 px inner border in the surface colour inside a 3 px accent border. A "pixel" variant uses rounding 1, stacked 2/0/3/8 px borders, and a hard shadow with offset 9 9, range 1, render_power 12. W4 F3 C4 = 48.
    *How:* borders-plus-plus is still one of the four official plugins, pinned for 0.56.x. Guard its config with `if hl.plugin.borders_plus_plus ~= nil`. Note that the upstream README's Lua example is missing commas.
23. **Icons that follow the palette** — impasto (Papirus folders re-pointed to the nearest swatch, no root), NyArch (Tela-circle fork: `install.sh -n Tela-circle-MaterialYou <hex>`). W3 F4 C4 = 48.
24. **A wallpaper picker grid with thumbnails** — JaKooLit (icon size computed from monitor height and scale; GIF and video thumbnails; a random entry), NeKoRoSHELL (4×3 grid at 120 px, thumbnails generated in the background, paste a URL to download it with a progress notification), Sly-Harvey (320×180 thumbnails built at Nix build time, shown 6×1 at 160 px). W3 F5 C3 = 45.
    *How:* rofi dmenu rows in the form `name\0icon\x1f/thumb`, or a Quickshell grid.
25. **Animation presets you can switch at runtime** — impasto (Glide, Brisk, Calm, Bounce, None, applied atomically with one `hyprctl eval` Lua chunk; `fadeIn` is disabled on purpose so the wallpaper doesn't flash while windows re-tile), JaKooLit (17 presets picked from rofi). W3 F5 C3 = 45.

**Honourable mentions** (useful, not cute): launcher prefixes `=` calculator, `@` windows, `!` timer, `'` clipboard with images, `>` shell panels (impasto; datsfilipe has `>`/`?`/`=`); a keyboard-layout OSD that follows the cursor (Cenaure); face unlock as a separate PAM service (impasto); a screenshot notification with OCR, edit and copy actions, plus QR scanning (oddlama); special-workspace scratchpads with `slidefadevert -50%` (librephoenix); `hyprgamemode`, which restores the exact values from the Nix config afterwards (librephoenix); a VM test that generates the README screenshot in CI (Sigmanificient); a "reboot into another OS" entry in the launcher (Panacea); a GUI keybind editor (Panacea); an SDDM theme that matches the lock screen (impasto, Panacea).

---

## 3. What the prettiest repos have in common

### 3.1 The best palettes (hex taken straight from the reports)

| Palette (source) | Hex |
|---|---|
| **Minimal Bocchi**, dark muted pastel (Bocchi) | bg #1b1e25, surface #242830, fg #ebddbc, **pink #EAB2C2** (main accent), gold #DEC386, blue #83A6BF, rose #D5A5AA, mauve #967786, steel #7293A9, muted #a49a83. Rofi selection #F0AAC180 on #522936E6. |
| **Rumda Light**, milk tea (rumda) | bar #f0c997, surface #F5DCC6, modules #f0bb90 / #f2cdac, border #f2bc88, indicator #dba380, accent #a1694d, accent2 #c47c4f, gradient #a87358, error #9A4235, shadow #AA784d37. Terminal: fg #4E2E1F, cursor #6D4A39, selection #E6C0A1. Heatmap ramp #db986e → #ba7245 → #9e5b2f → #8f4f24 → #7d431f. |
| **Rumda Dark**, espresso (rumda) | bar #1c1612, dashboard #2b221a, modules #3d2e21, border #3A2D22, accent #c47c4f, gradient #d68e60, text #f0bb90. Pixel-flavour border #784D37. |
| **Misty-rose launcher** (auspicious) | bg #FFE4E1, alt #ffffff, fg #645074, selected #73C3EB, active #2f88ff, urgent #a55377. Cava gradient #65BEAA #9A80CE #DA8BAA #C388B2 #CC94B0 #DD88A5 #CC88AA #DD99A7. |
| **Shorekeeper**, lavender (Cenaure) | surface #2b243d, fg #ebd7f5, active #bf7ee0, secondary #5d578c, disabled #606275. Kitty: surface #202033, fg #e9e6f7, active #867ee0. |
| **Nightshade Drift**, kitty base (Cenaure) | bg #232330, fg #E0D7E5, cursor #8D6B94. ANSI normal/bright pairs: #D05A8E/#D96FA0, #7A8F6F/#96AA89, #B89A6A/#D4B484, #5A6FA8/#7088C4, #8D6B94/#A67FAE, #5E8A94/#75A8B4, #A394A8/#E0D7E5. |
| **Girls' Last Tour**, sepia and gold (Cenaure) | surface #2a2723, foreground on surface #e0d7c6, active #d9a441, secondary #9a896f, disabled #6b6459. |
| **Fairy Floss** (librephoenix) | 00 #343145, 01 #464258, 02 #6A6483, 03 #422013 (**bug:** nearly invisible on the background), 04 #9673D3, 05 #A0A0C0, 06 #B5B2Bd, 07 #F8F8F0, 08 #CC6666, 09 #FFEA00, 0A #C2FFDF, 0B #55b3cc, 0C #8295D6, 0D #FFB8D1, 0E #C5A3FF, 0F #96CBFE. |
| **UwUnicorn** (librephoenix) | 00 #241b26, 01 #2f2a3f, 02 #46354a, 03 #6c3c62, 04 #7e5f83, 05 #eed5d9, 07 #e4ccd0, 08 #de5b44, 09 #e39755, 0A #a84a73, 0B #c965bf, 0C #9c5fce, 0D #6a9eb5, 0E #6ac38f, 0F #a3ab5a. |
| **Pastel sidebar**, light (saurabh) | bg #f9f9f9, fg #fa74b2, accent #cf8ef4. Extras: lightpink #ffb6c1, darkpink #e75480, lightpurple #b19cd9, mediumpurple #9370DB. |
| **Deep navy periwinkle** (Sigmanificient) | BG #0F0F1C / #1A1C31 / #22263F, blue #8B8AF1 / #A7A5FB, purple #D78AF1 / #E5A5FB, cyan #8ADEF1 / #A5EBFB, red #D22942 / #DE4259, green #17B67C / #3FD7A0, yellow #F2A174 / #EED49F, text #CAD3F5. |
| **Catppuccin Macchiato + Archcraft border** (garuda) | Active border 0xFFB4A1DB → 0xFFD04E9D at 45deg. Mauve #c6a0f6, pink #f5bde6, lavender #b7bdf8, base #24273a. |
| **Catppuccin Mocha + Frappé border** (Sly-Harvey) | Border rgba(ca9ee6ff) → rgba(f2d5cfff) at 45°. Cava #94e2d5 #89dceb #74c7ec #89b4fa #cba6f7 #f5c2e7 #eba0ac #f38ba8. |
| **Pastel accent presets on black glass** (hakuspace) | Pink #F9B2D7, Peach #FFB399, Slate Blue #7288AE, Green #A2CB8B, Yellow #EFBF04, Grey #BFC9D1. |
| **Warm accents on Nord at dusk** (trihmah) | salmon #fa9f8e, mint #b9fac2, cream #d4d2a9, blue #85b4ea, on #242933 / #3B4252. |
| **Pastel ANSI** (Kakikori kitty) | bg #14151e, #dd6777, #90ceaa, #ecd3a0, #86aaec, #c296eb, #93cee9. |

### 3.2 Font pairings seen, and a recommended one

- JaKooLit: JetBrainsMono NF for the bar and rofi, FantasqueSansM NF in kitty, a **173 pt JBM ExtraBold clock with the date in Victor Mono Bold Italic** on the lock screen.
- Cenaure: AnnotationM NF for the UI, Material Symbols Outlined and Rounded for icons, Adwaita Sans on the media card, **Noto Sans CJK JP chosen per string** for track titles, JBM NF Mono in the terminal.
- impasto: Inter and Inter Display, JBM NF Mono, and **Grape Nuts handwriting (OFL) for notes**. Type scale 10/11/13/14/16/30/68.
- rumda: Terminess NF, **Dogica Pixel 7 in the launcher**, Noto Sans Bold for the clock. The licences of Dogica and the bundled Suls.ttf weren't checked.
- saurabh: **Comfortaa 12 with feather 18 icons** in the rofi menus.
- datsfilipe: Inter and JBM NF, spaced lower-case labels, a 96 px mono clock on the lock screen.
- oddlama: puts `symbol_map` to *Symbols Nerd Font Mono* in kitty so it doesn't need patched fonts, which is a useful trick. Its Segoe UI is proprietary.
- Avoid the proprietary fonts: SF Pro, Product Sans, Segoe UI, Google Sans (the non-Flex version is fine; it's OFL in google/fonts), PP Neue Machina.
- **Proposal, from the assets report:** **Maple Mono NF CN** for monospace (rounded, cursive italics), **Nunito or Quicksand** for UI, or **Google Sans Flex** with its roundness axis once it's packaged, **Material Symbols Rounded** for icons, **Noto Color Emoji**, and **Noto CJK / LXGW WenKai** for CJK.

### 3.3 Geometry numbers

| Repo | Rounding (power) | Gaps in/out | Border | Blur | Shadow | Opacity (active/inactive) |
|---|---|---|---|---|---|---|
| JaKooLit | 10 | 2/4 | 2, solid | 6×3, xray | range 3, power 1, coloured | 1.0/0.9, dim 0.1 |
| Bocchi | 5 | 6/15 | 0 | 4×3 | 11/3 (old keys) | per-app rules 0.8 |
| auspicious | 8 | 5/8 | 3 | 8×3 | 4/3 (old keys) | 0.93/0.80 |
| zenities | 14 | 4/8 | 0 | off | off | 1.0/0.9 |
| NeKoRoSHELL | 10 (2) | 5/20 | 2 | 5×2, vibrancy 0.1696 | 4/3 | 0.825/**0.3** (too see-through) |
| Cenaure | 10 (5) | 6/10 | 1, gradient 135° | 3×1 | 4/3 | 1.0/0.9 |
| garuda | 10 | 5/10 | 2, gradient 45° | size 16 | defined but unused | 1.0/0.8 |
| rumda (light) | 7 | 9 / 45,40,45,40 | 3 + 11 px matte | 1×2 | range 7, power 9, offset 5 4, 0xAA784d37 | 1.0/0.99 |
| hakuspace | 12 (10) | 10/16 | 2 | 10×3, vibrancy 2, contrast 1.4, noise 0.05 | 20/6, offset 2,2 | 1.0/0.9 |
| impasto | 22 (2) | 7/18 | 0 | 6×2, xray | off (14/3 optional) | 1.0/1.0 |
| Panacea | 12 (4) | 5/5 | 1, white→grey | off | off | 1.0/0.90 |
| Sly-Harvey | 10 | 4/9 | 2, gradient 45° | 6×2 | off | tiered: 1.0 / 0.9 / 0.8 / 0.7 |
| yunfachi "marui" | 6 | 6/12 | 2 | default | 10/4 | — |
| librephoenix | 0 | 14/14 | 0 | 10×3, contrast 1.17, brightness 0.65 | on | dim 0.15, terminals 0.80 |
| NyArch-Hyprland | 15 | 5/20 | 2 (`$primary`) | 12×4 | 30/4 | — |
| niri repos | datsfilipe: gaps 8, radius 6, ring 4, shadow softness 30 spread 5. annmaro: gaps 8, radius 12, border 1, blur passes 3. workflow: gaps 4, border 2. | | | | | |

Hyprlock settings worth copying:
- JaKooLit: blur 3×2, noise 0.0117, contrast 1.3, brightness 0.8, vibrancy 0.21.
- garuda: screenshot background, blur 4×3, noise 0.0117, vibrancy 0.21, `fail_transition = 300`.
- librephoenix: blur 4×5, contrast 0.8916, brightness 0.8172, vibrancy 0.1696, pill-shaped input (`rounding = -1`).
- hakuspace: a card of 420×640 with rounding 64 over a screenshot at brightness 0.5.

Floating-pill bars cluster at height 28–40, radius 8–15, margins 6–10: Bocchi 28/10, garuda 30/8, Sly-Harvey 36/10 with radius 11, NeKoRoSHELL radius 15, Cenaure's Quickshell bar 40 with radius height/2.

**Proposal, a cute baseline:** rounding 12–16 with `rounding_power` 2–4, gaps 6/12, a 2 px pastel gradient border at 45°, blur size 6–8 with 2–3 passes and vibrancy 0.1696, a soft shadow (range around 12–20, render_power 3), and inactive opacity 0.9. Never go as low as 0.3.

### 3.4 The best-looking animation sets (quoted exactly from the reports)

These hyprlang blocks have to be translated for 0.55 and later: use `hl.curve(name,{type="bezier",points={{x0,y0},{x1,y1}}})` plus `hl.animation({leaf=…, speed=…, bezier=…, style=…})`. Use **`bezier =` or `spring =`, never `curve =`**; the wiki has a bug on this. For springs use `dampening`, which works on 0.56.2 and on main.

**JaKooLit default (springy):**
```
animations {
  enabled = yes
  bezier = wind, 0.05, 0.9, 0.1, 1.05
  bezier = winIn, 0.1, 1.1, 0.1, 1.1
  bezier = winOut, 0.3, -0.3, 0, 1
  bezier = liner, 1, 1, 1, 1
  bezier = overshot, 0.05, 0.9, 0.1, 1.05
  bezier = smoothOut, 0.5, 0, 0.99, 0.99
  bezier = smoothIn, 0.5, -0.5, 0.68, 1.5
  animation = windows, 1, 6, wind, slide
  animation = windowsIn, 1, 5, winIn, slide
  animation = windowsOut, 1, 3, smoothOut, slide
  animation = windowsMove, 1, 5, wind, slide
  animation = border, 1, 1, liner
  animation = borderangle, 1, 180, liner, loop #used by rainbow borders and rotating colors
  animation = fade, 1, 3, smoothOut
  animation = workspaces, 1, 5, overshot
  animation = workspacesIn, 1, 5, winIn, slide
  animation = workspacesOut, 1, 5, winOut, slide
}
```

**The hyprdots family, with layer pop-in** (NeKoRoSHELL legacy skin; Bocchi and auspicious use the same beziers):
```
    bezier = wind, 0.05, 0.9, 0.1, 1.05
    bezier = winIn, 0.1, 1.1, 0.1, 1.1
    bezier = winOut, 0.3, -0.3, 0, 1
    bezier = liner, 1, 1, 1, 1

    animation = windows, 1, 6, wind, slide
    animation = windowsIn, 1, 6, winIn, slide
    animation = windowsOut, 1, 5, winOut, slide
    animation = windowsMove, 1, 5, wind, slide
    animation = border, 1, 1, liner
    animation = borderangle, 1, 30, liner, loop
    animation = layers, 1, 6, wind, popin 90%
    animation = layersIn, 1, 6, winIn, popin 90%
    animation = layersOut, 1, 5, winOut, popin 90%
    animation = workspaces, 1, 5, wind

    bezier = almostLinear, 0.5, 0.5, 0.75, 1.0
    animation = fadeIn, 1, 1.73, almostLinear
    animation = fadeOut, 1, 1.46, almostLinear
    animation = fade, 1, 3.03, almostLinear
```

**Material 3 / end-4 style** (greed-d):
```
    bezier = md3_standard, 0.2, 0, 0, 1
    bezier = md3_decel, 0.05, 0.5, 0.1, 1
    bezier = md3_accel, 0.3, 0, 0.6, 0.15
    bezier = overshot, 0.05, 0.9, 0.1, 1.1
    bezier = menu_decel, 0.1, 1, 0, 1
    bezier = menu_accel, 0.38, 0.04, 1, 0.07
    animation = windows, 1, 3, md3_decel, popin 60%
    animation = windowsIn, 1, 3, md3_decel, popin 60%
    animation = windowsOut, 1, 3, md3_accel, popin 60%
    animation = fade, 1, 5, md3_decel
    animation = border, 1, 0.5, liner
    animation = layersIn, 1, 4, menu_decel, slide
    animation = layersOut, 1, 1.6, menu_accel
    animation = fadeLayersIn, 1, 1, menu_decel
    animation = fadeLayersOut, 1, 2.5, menu_accel
    animation = workspaces, 1, 5, menu_decel, slide
```
**CONFLICT:** the md3 values differ between repos. greed-d uses `md3_decel 0.05,0.5,0.1,1` and `md3_accel 0.3,0,0.6,0.15`. JaKooLit's END-4 preset uses `md3_decel 0.05,0.7,0.1,1` and `md3_accel 0.3,0,0.8,0.15`. Sly-Harvey's Lua uses `md3_decel {0.05,0.7},{0.1,1}`.

**Lua with named curves** (Cenaure):
```
-- easeOutQuint
hl.curve("ivory", {
    type = "bezier",
    points = {
        { 0.23, 1.00 },
        { 0.32, 1.00 },
    },
})

-- Warmer, slightly softer ease-out
hl.curve("felt", {
    type = "bezier",
    points = {
        { 0.16, 0.80 },
        { 0.25, 1.00 },
    },
})

-- Ease-in
hl.curve("damper", {
    type = "bezier",
    points = {
        { 0.40, 0.00 },
        { 1.00, 1.00 },
    },
})

hl.animation({
    leaf = "windowsIn",
    enabled = true,
    speed = 5.00,
    bezier = "ivory",
    style = "slide",
})
```
The report also lists: `sustain (0.45,0.05),(0.55,0.95)` for fades and borders, `resonance (0.25,0.46),(0.45,0.94)` for zoomFactor, and specialWorkspace using `slidevert`.

**User-selectable presets** (impasto `Motion.qml`):
```
{ id: "macos", label: "Glide",
  curve: [0.32, 0.72, 0, 1],
  slow: 2.6, medium: 1.8, fast: 1.4,
  windows: "slide", workspaces: "slide" },
{ id: "snappy", label: "Brisk",
  curve: [0.22, 1, 0.36, 1],
  slow: 1.3, medium: 1.0, fast: 0.8,
  windows: "popin 94%", workspaces: "slide" },
{ id: "smooth", label: "Calm",
  curve: [0.25, 0.1, 0.25, 1],
  slow: 4.2, medium: 3.0, fast: 2.2,
  windows: "popin 85%", workspaces: "fade" },
{ id: "springy", label: "Bounce",
  curve: [0.05, 0.9, 0.1, 1.15],
  slow: 3.2, medium: 2.0, fast: 1.4,
  windows: "popin 80%", workspaces: "slide" },
```

**Soft "liquid" motion** (Panacea, Lua):
```
    hl.curve("myBezier",     { type = "bezier", points = { {0.05, 0.9}, {0.1, 1.05} } })
    hl.curve("easeOutQuint", { type = "bezier", points = { {0.23, 1.0}, {0.32, 1.0} } })
    hl.curve("liquid",       { type = "bezier", points = { {0.16, 1.0}, {0.3,  1.0} } })
    hl.animation({ leaf = "windows",    enabled = true, speed = 7, bezier = "myBezier", style = "popin 80%" })
    hl.animation({ leaf = "windowsOut", enabled = true, speed = 6, bezier = "liquid",   style = "popin 80%" })
    hl.animation({ leaf = "layers",    enabled = true, speed = 6, bezier = "liquid", style = "fade" })
    hl.animation({ leaf = "workspaces",           enabled = true, speed = 7, bezier = "easeOutQuint", style = "slide" })
```

**rumda** (hyprlang, dark config):
```
    bezier = easeOutQuint,0.23,1,0.32,1
    bezier = overshoot, 0.05, 0.9, 0.1, 1.1
    bezier = fakeElastic, 0.68, -0.1, 0.265, 1 
    bezier = xfcBezier, 0.1, 0.9, 0.1, 1.03
    animation = windows, 1, 4.79, easeOutQuint
    animation = windowsIn, 1, 4, default, slide bottom
    animation = windowsOut, 1, 8, default, slide top   
    animation = windowsMove, 1, 3, fakeElastic, slide
```
The report says workspaces use `xfcBezier` at speed 4.

**Shell motion tokens** (datsfilipe QML):
```
      readonly property int duration: 200
      readonly property list<real> curve: [0.34, 0.80, 0.34, 1.00, 1, 1]      // fast
      readonly property int duration: 350
      readonly property list<real> curve: [0.42, 1.67, 0.21, 0.90, 1, 1]      // std (overshoot)
      readonly property int duration: 500
      readonly property list<real> curve: [0.38, 1.21, 0.22, 1.00, 1, 1]      // slow
      readonly property int duration: 400
      readonly property list<real> curve: [0.05, 0.7, 0.1, 1, 1, 1]           // enter
      readonly property int duration: 200
      readonly property list<real> curve: [0.3, 0, 0.8, 0.15, 1, 1]           // exit
```
(The `// …` labels are mine; the report names the tokens fast, std, slow, enter and exit.)

**Springy CSS hover** used in Bocchi, JaKooLit, Sly-Harvey and greed-d: `transition: all 0.3s cubic-bezier(.55,-0.68,.48,1.682);`

**Wallpaper transition that grows from the cursor** (Bocchi; now `awww img`):
```
swww img "$(readlink "${wallSet}")" \
--transition-bezier .43,1.19,1,.4 \
--transition-type "$xtrans" \
--transition-duration 0.7 \
--transition-fps 60 \
--invert-y \
--transition-pos "$( hyprctl cursorpos )"
```

Rules that apply to all of these:
- Only use `borderangle … loop` when the border is a gradient. It forces a redraw every frame (auspicious, NeKoRoSHELL and librephoenix waste it on solid borders or `border_size = 0`).
- `fadeIn` can be turned off to avoid a wallpaper flash while tiling (impasto).

---

## 4. The best NixOS structural patterns

**How to lay out the flake**
- Plain flake with a `mkHost` helper (ecosystem report; Sly-Harvey). Sigmanificient's version returns `(lib.nixosSystem conf) // { inherit conf; }`, so the VM screenshot test can reuse the exact same modules.
- flake-parts with **import-tree** in the "dendritic" style (workflow, annmaro, oddlama). Each feature file defines both `flake.modules.nixos.<x>` and `flake.modules.homeManager.<x>`. Folders starting with `_` are skipped by auto-import.
- "Vertical slice" layout: `modules/<cat>/<feature>/{os.nix,user.nix}`, all auto-imported by a recursive `scanPaths` and turned on with enable options (datsfilipe).
- Denix `delib.rice` with **inheritance** (yunfachi). A geometry-only base rice (`marui`: rounding 6, border 2) is inherited by palette rices, and each host just names a rice.
- `hosts/*` discovered automatically with `readDir` (librephoenix).

**Options and switches**
- Typed enums beat untyped variable files. annmaro has `systemSettings.bar = "DMS"|"noctalia"` and more. workflow has `dendrix.{isLaptop,hasNvidia,isImpermanent}`. yunfachi derives host features (cli, gui, gaming) from the host type. Sly-Harvey's untyped `variables.nix`, edited by `sed` in its installer, is the fragile counter-example.
- **Import every profile and gate each with `mkIf`** (annmaro), so which modules exist never depends on option values.
- **A shell selector that turns off redundant daemons**: pick noctalia and hyprlock, hypridle and wlogout get `mkForce`d off (Sly-Harvey).
- A global `performanceOptimizations` flag that turns off blur, shadows, animations and opacity for every app at once (librephoenix).

**Home Manager**
- Run HM as a NixOS module with `useGlobalPkgs`, `useUserPackages`, `backupFileExtension` and `sharedModules` (Sly-Harvey, librephoenix, workflow). Read `osConfig` from HM modules (workflow checks `osConfig.specialisation == {}` to know if light mode is on).
- **Deploy the raw `hyprland.lua` files with `xdg.configFile`, and generate only `variables.lua`** with script store paths and per-host values (Sly-Harvey). This is the pattern that makes Lua configs shareable with Arch.
- Output both `nixosConfigurations` and standalone `homeConfigurations` from one tree (yunfachi, workflow).
- Package Quickshell as a derivation and create **one wrapper binary per IPC action** with `makeWrapper` (`wlauncher`, `wlock`, `wvolume-osd` and so on), so compositor binds just call names (datsfilipe).
- Build scripts with `writeShellApplication` and `runtimeInputs`, keeping script bodies in separate `.sh` files so shellcheck runs at build time (workflow). Generate assets at build time, such as wallpaper thumbnails via `runCommand` and ImageMagick (Sly-Harvey).

**Theming**
- Stylix with `autoEnable = false` and targets turned on one by one, plus **mustache templates** for apps Stylix doesn't cover (qt5ct, kdeglobals, a Doom Emacs theme) (librephoenix). annmaro turns off Stylix targets for apps it themes by hand.
- **Themes as data.** librephoenix: a folder per theme with base16 hex, polarity, and a wallpaper URL with its sha256, and an option enum generated from `readDir`. The downside is that hotlinked wallpapers rot and break builds.
- A typed base24 option with fallbacks defined by the spec, and a colour object exposing `.hex`, `.rgb` and `.value` (yunfachi).
- **A bridge from Stylix colours to Material 3 tokens** (`theme.json` for DMS) (annmaro).
- libadwaita `@define-color` overrides written to both `gtk-3.0` and `gtk-4.0` `gtk.css` (oddlama, yunfachi). Watch for oddlama's missing `#` bug.
- Light mode as a NixOS **specialisation** that writes an `/etc/specialisation` marker file (workflow).
- Colours substituted into QML at build time (datsfilipe) are reproducible, but changing the theme needs a rebuild.

**Testing and CI**
- `pkgs.testers.runNixOSTest` boots the real modules and screenshots the desktop (Sigmanificient). Running Hyprland under QEMU in the test driver is **UNVERIFIED**.
- CI builds every host with `--override-input secrets nixpkgs` and pushes to Cachix (workflow). git-hooks.nix for pre-commit (Sigmanificient). A justfile `fmt` target that includes `qmlformat` and `kdlfmt` (datsfilipe).

**Ecosystem rules for NixOS**
- Track **nixos-unstable with HM master** for exact version parity with Arch: Hyprland 0.56.2, Quickshell 0.3.1, matugen 4.2.0.
- HM's `configType` defaults to `"lua"` for `stateVersion >= 26.05`. HM **writes `hypr/hyprland.lua` whenever `systemd.enable = true`**, even with empty settings, so don't enable HM's Hyprland module if you link the hypr folder yourself.
- With `programs.hyprland.withUWSM = true`, set HM `systemd.enable = false`.
- `hardware.nvidia.open` must be set explicitly for drivers ≥ 560.
- hyprpm is unsupported on Nix. Use `hyprlandPlugins.*`, or `hl.plugin.load` with a path.
- `mkOutOfStoreSymlink` must be given an **absolute string path**.

---

## 5. The best Arch install and deploy patterns

- **Install in two stages** (Arch report). Stage 1 is `archinstall --config … --creds … --silent` (archinstall 4.4): GPT, an ESP of 4 GiB or more at /boot, btrfs with `compress=zstd` and subvolumes @, @home, @log, @pkg plus your own @snapshots, Limine, zram, PipeWire, NetworkManager, the Hyprland profile and SDDM. Stage 2 is a script run **as your normal user** for AUR packages, snapshots and dotfiles.
  - `custom_commands` run as root inside the chroot before genfstab, so makepkg can't run there.
  - Get valid password hashes by running `archinstall --dry-run` once.
  - Compute the root partition size per disk; the parser rejects misaligned partitions.
- **Installer ergonomics to copy:**
  - JaKooLit: whiptail checklist, `--preset`, one re-runnable script per component, `Install-Logs/`, `uninstall.sh`, `copy.sh` with backups that **keeps UserConfigs across upgrades**, and hardware detection (NVIDIA, VM, chassis type).
  - NeKoRoSHELL: `--dry-run`, tar backups, adds touchpad `gesture =` lines on laptops, fills monitor placeholders, a user sandbox with post-install/post-update/on-theme-change hooks, and a per-file keep/diff/merge updater.
  - zenities: timestamped backups, detects existing symlinks into the repo, `stow --restow`, retries, a log file, and **CI that runs shellcheck and then a real install in an `archlinux:latest` container**.
  - impasto: copies files with a **sha256 manifest**, writes `.new` next to files you've edited, `--dry-run`, `sync --watch`, a `check` verb that CI also runs, and optional package groups that disable a feature instead of failing.
  - Cenaure: dry-run, confirmation, `ln -sfn`, bootstraps yay-bin, masks dunst, mako and swaync.
  - hakuspace: install, update, rollback and doctor commands, a choice of deep-symlink or copy mode, tagged stable releases, "ONCE_CONFIGS" for files a GUI edits, and a user override folder `~/hakucfg`.
  - Panacea: a `binary|package|purpose` DEPS table, an `OBSOLETE_PKGS` list, a self-updater that carries user settings over, and `--no-*` flags.
- **Package handling rules** (Arch report):
  - One `sudo pacman -Syu --needed` transaction; never `-Sy <pkg>` (JaKooLit's `pacman.sh` does a partial upgrade).
  - Check package names with `pacman -Si` and the AUR RPC first so renames fail early.
  - `set -Eeuo pipefail` with an ERR trap.
  - Install yay from **yay-bin** (packaged by upstream). Avoid **paru-bin**, which is orphaned.
  - Keep a **short, fixed AUR list and review PKGBUILDs**. The AUR had malware waves in 2026 and adoptions are disabled.
  - Chaotic-AUR only as an opt-in. **Do not** add the CachyOS repos to plain Arch; use linux-zen or linux-cachyos from Chaotic instead.
  - Use gum with a plain `read` fallback. The ISO has no git, gum, whiptail or jq.
- **Snapshots:** snapper with the flat @snapshots layout, snap-pac, limine-mkinitcpio-hook and limine-snapper-sync, and `sd-btrfs-overlayfs` in HOOKS (archinstall uses a systemd initramfs). Delete archinstall's `EFI/arch-limine/limine.conf`. The zero-effort alternative is GRUB with grub-btrfs, which archinstall sets up automatically but with snapper's nested layout.
- pacman.conf: `Color`, `ILoveCandy`, `ParallelDownloads` (NyArch, saurabh).
- mkinitcpio 42 needs TPM2 LUKS re-enrolment (Arch news, 2026-09-22).
- NVIDIA: `nvidia-open-dkms`. Pascal and older cards need `nvidia-580xx-dkms` from the AUR.

---

## 6. Sharing one set of dotfiles between Arch and NixOS

**Options seen**

| Option | Seen in | Pros | Cons |
|---|---|---|---|
| a. Stow, one package per app | kamol (per role), greed-d (per app), zenities | Plain files. Edits apply instantly. | Needs a matching NixOS side. |
| b. HM `mkOutOfStoreSymlink` to the same tree | ecosystem (NixOS and Arch reports) | Same files as Stow. Hot reload works. | Not pure. The path must be an absolute string. Collides with HM modules for the same app. |
| c. Nix generates everything | Sly-Harvey, librephoenix, yunfachi, datsfilipe, oddlama, annmaro, workflow | Reproducible and type-checked. | Can't be used on Arch without extracting the files. Scripts contain store paths. |
| d. Hybrid: raw files through `xdg.configFile`/`home.file` plus a small generated file | Sly-Harvey (raw Lua plus `variables.lua`), Sigmanificient (raw Qtile Python) | Most of the config is portable. | Still read-only store links, so no live editing. |
| e. Nix installs packages, an imperative installer deploys dotfiles | hakuspace, JaKooLit's archived NixOS-Hyprland | Simple. | Not declarative. Scripts that `sed`-edit configs break on read-only files. |
| f. HM standalone on Arch | homeConfigurations in yunfachi and workflow | One tool on both. | PAM for Nix-built lockers breaks (HM #7027). GPU needs `non-nixos-gpu-setup`. NVIDIA libraries must match the host driver on every update. |
| g. chezmoi / yadm / dotbot | ecosystem | Templates and secrets. | chezmoi's `dot_` source naming can't be read by HM, so two tools would fight over the same files. |

**Recommended** (Proposal, following the NixOS and Arch reports):
1. One repo with `config/<app>/…` as the **plain source of truth**. On Arch: `stow --no-folding -d ~/dotfiles -t ~/.config config` (or one Stow package per app). On NixOS: `xdg.configFile.<app>.source = config.lib.file.mkOutOfStoreSymlink "${config.home.homeDirectory}/dotfiles/config/<app>"`.
2. Nix and HM manage **only** packages, services, system glue, fonts, cursor, and boot/greeter theming. Do **not** enable HM modules for linked apps (`wayland.windowManager.hyprland`, `programs.kitty.settings`, `programs.quickshell.configs`).
3. **Keep generated palette files out of the repo and out of HM.** matugen and apply scripts write to `$XDG_STATE_HOME/...` (like hakuspace's `~/.local/state/hakuspace/theme/`, impasto's `~/.local/state/quickshell`, NeKoRoSHELL's `~/.cache/wallust`). The tracked configs only `require`, `@import`, `include` or `source` those files. This is the "porting lesson" in the JaKooLit report.
4. Split each config into a managed core and a writable user layer: zenities' `core/*.lua` plus `user/*.lua`, Panacea's `pcall(require,'custom.*')`, JaKooLit's `configs/` plus `UserConfigs/`. Per-host differences go in `pcall(require,"host")`, which is gitignored.
5. Pin NixOS to **nixos-unstable** so versions match Arch. Add `//@ pragma ShellId <name>` to Quickshell, because since 0.3.0 the shell id comes from the symlink path. No FHS paths anywhere: call binaries by name and run helpers as systemd user units.
6. On Arch, install the whole graphical stack with pacman. Nix isn't needed there.

---

## 7. Anti-patterns and pitfalls

**Deprecated syntax and tools** (from the ecosystem reports)
- **Hyprland:**
  - hyprlang (`hyprland.conf`) is deprecated since 0.55 and removed on main; expect it gone in 0.57.
  - `windowrulev2` was merged in 0.48 and the rule syntax was overhauled in 0.53. It is now `hl.window_rule`.
  - `gestures{workspace_swipe}` was removed in 0.51; use `hl.gesture`.
  - Flat `drop_shadow`/`shadow_range`/`col.shadow` moved into `shadow{}` in 0.45.
  - Flat `blur_*` keys and `blurls` are gone. So are `new_is_master`, `no_gaps_when_only`, and the `togglesplit` dispatcher (0.54).
  - `new_window_takes_over_fullscreen` became `on_focus_under_fullscreen`. `dwindle:pseudotile` was removed in 0.55.
  - Under a Lua config, `hyprctl dispatch <legacy>` and `hyprctl keyword` scripts break. This affects gamemode scripts in librephoenix, Sly-Harvey and Bocchi, NeKoRoSHELL's `decoration:drop_shadow`, hypridle `dpms` lines, and zenities' eww scripts.
  - Wiki bugs: `curve =` should be `bezier =`; springs use `dampening`, while git uses `damping`.
- **Plugins:** official hyprexpo and hyprtrails were dropped. Use the sandwichfarm/hyprexpo fork; hyprtrails has no maintained fork. hyprpm is unsupported on Nix.
- **Ecosystem renames and deaths:**
  - swww became awww. rofi-wayland became rofi 2.0. hyprland-qtutils became hyprland-guiutils.
  - HyprPanel is archived. AGS v1 is deprecated, and nixpkgs `ags` is stuck at 2.3.0. noctalia-shell v4 is unmaintained. eww and wlogout are stale. Ignis is being rewritten.
  - hyprpaper `preload` is gone. neofetch is archived. `exa` became `eza`. p10k is in limited maintenance.
  - Waybar `wlr/workspaces` became `hyprland/workspaces`, and `today-format` is deprecated. Nerd Fonts v2 codepoints are gone.
  - picom `experimental-backends` was removed. Alacritty 0.14 moved its keys (**UNVERIFIED**, kamol report).
  - catppuccin/gtk is archived. murrine-based themes were removed from nixpkgs. `noto-fonts-emoji` and `nerdfonts` aliases now throw.
  - Arch `nvidia-dkms` was removed. `hardware.pulseaudio` was renamed to `services.pulseaudio`.

**Hard-coded paths and machine assumptions**
- Committed absolute symlinks: JaKooLit (`/home/ja/...`), Bocchi (`/home/kyokino/...`).
- `/home/<user>` everywhere: auspicious (prtyksh), Kakikori (irako), trihmah (tdm), NeKoRoSHELL (nekorosys), greed-d (greed), oddlama (malte).
- Usernames in flakes: dwilliams, farlion ("because it's hardcoded"), yunfachi, annmaro, dtsf.
- Relative CSS imports like `'../../.config/waybar/...'` (JaKooLit). Repos that must live at a fixed path (`~/.config/rumda`).
- FHS paths: `/usr/lib/polkit-*`, `/usr/share/...`, `/bin/fish`.
- Hardware and locale: `hwmon7`, `intel_backlight`, `BAT0`, `asus::kbd_backlight`, monitor `desc:` strings, kb layouts, 1920×1080 pixel offsets (auspicious, rumda, Cenaure). Intel-only `LIBVA_DRIVER_NAME=iHD` in shared env (hakuspace). NVIDIA VA-API env set on every host (oddlama).

**Licensing**
- Unattributed fan art and copyrighted art in repos:
  - Bocchi: Bocchi the Rock! art.
  - NeKoRoSHELL: MHA, Chainsaw Man, Bleach, Totoro, Windows 11 stock images, bad-apple.mp4.
  - Cenaure: Girls' Last Tour and Shorekeeper.
  - garuda: March 7th from pixiv, Blue Archive.
  - impasto: 40 unattributed JPEGs.
  - zenities: 28 JPEGs.
  - Panacea: 300+ Unsplash photographer names plus Ultrakill art.
  - yunfachi: 137 pixiv images with no stated licence.
  - JaKooLit: Wallpaper-Bank, "AI generated or enhanced".
  - librephoenix: hotlinked from wallhaven and alphacoders.
  - Kakikori: the "Anime" SDDM theme is a renamed CC-BY-SA "Elegant".
  - saurabh: Spotify's proprietary `.spa` files.
- **Wallpaper repos' licence files don't cover the art inside them.** rose-pine/wallpapers contradicts itself (CC0 file vs "CC BY-SA 4.0" in the README).
- NC-only assets: Posy cursors, anime-cursors. No licence at all: chihuahua sound theme, ctrlcat0xx cursors. The NyArch logo's origin (a Reddit post) has no licence. Pokémon sprites are Nintendo IP.
- Proprietary fonts: SF Pro, Product Sans, Segoe UI, PP Neue Machina, Google Sans (non-Flex patched builds).
- **Rule:** ship only your own, CC0, or credited CC-BY art. Fetch everything else at install time with a source and licence manifest.

**Fragile scripts and runtime habits**
- Scripts that `sed` their own configs at runtime and re-symlink files: JaKooLit's DarkLight and WallpaperSelect, NeKoRoSHELL's `customize`, rumda's `hyprtheme`. These break on read-only Nix configs.
- Refresh races based on `sleep`, and restarting apps with `killall` instead of reloading them.
- Polling: `hyprctl cursorpos` every 50 ms (NeKoRoSHELL navbar-hover); 1 s polls in Waybar where signals would do (workflow).
- `exec_always` spawning a duplicate loop on every reload (kamol). Starting two wallpaper daemons (Kakikori runs swaybg and swww). Two notification daemons.
- A FIFO for IPC instead of Quickshell's `IpcHandler` (rumda). A 6543-line `shell.qml` (Panacea). The whole QML tree copied six times (rumda).
- `inactive_opacity 0.3` (NeKoRoSHELL). Font Awesome used as a text font (Kakikori). Low-contrast pill colours (auspicious).
- Palettes copied by hand drift apart (Sigmanificient, Sly-Harvey mixing Catppuccin flavours, garuda's leftover Archcraft colours). A base16 → ANSI mapping that turns terminal colours grey (zenities). matugen hanging on its interactive picker.
- Committed runtime state: `mpd.db`, `nohup.out`, `state/*.json`, `.pyc`, `.bak` files.
- Idle chains in the wrong order (annmaro; hakuspace dims and suspends at the same 420 s) and very aggressive ones (librephoenix: lock at 165 s, suspend at 180 s).
- Lock screens that don't exist (Cenaure's Lock button does nothing; zenities gitignores its hyprlock config).
- Installers: `curl | bash`, `git stash && git pull`, `source`-ing preset files, `rm -rf ~/.config/<dir>`, forced reboots, disabling systemd-resolved (zenities).

**CONFLICTS and UNVERIFIED items to test before relying on them**
- Cenaure hard-codes Lua mode because it says "Quickshell's `Hyprland.usingLua` wrongly reports false". The shells report says `usingLua` exists and `activate()` understands Lua. **Test `usingLua` on 0.56.2.**
- Whether `hyprctl keyword` is fully removed in 0.57 comes only from downstream commit messages.
- Waybar's Lua-mode click issues are closed, but no tagged release with the fix is confirmed.
- Screenshots: the Arch report prefers **hyprshot** (in Arch extra, unchanged since 2024, untested on 0.56). The Hyprland report notes **grimblast** was updated for Lua in 2026-08 (AUR `-git` only; nixpkgs has `grimblast`).
- noctalia: oddlama, annmaro and Sly-Harvey still pin noctalia-shell v4, while the ecosystem report says the v5 rewrite is not Quickshell.
- `hl.animation` "wobble" and blur variants are git-only, not in 0.56.2.
- Running Hyprland in the NixOS VM screenshot test is unverified.
- The licences of Dogica Pixel, Victor Mono and hyprglass weren't covered by the reports.

---

## 8. Three aesthetic directions (Proposal)

### A. "Milk Tea Stationery": warm, light, cozy paper and pixel art
- **Palette:**
  - Rumda Light base: surface #F5DCC6, bar #f0c997, modules #f2cdac / #f0bb90, border #f2bc88, accent #c47c4f, deep accent #a1694d, text #4E2E1F, error #9A4235, shadow 0xAA784d37.
  - Cute accents borrowed from Bocchi: pink #EAB2C2, gold #DEC386, sky #83A6BF.
  - Dark companion is Rumda espresso: #1c1612 / #2b221a / #3d2e21, text #f0bb90.
  - The colours are **curated and fixed** (theme JSON files dispatched to each app, Cenaure style) instead of taken from the wallpaper, because earthy palettes need hand-tuning.
- **Fonts:** Nunito for UI, Maple Mono NF CN for code, a pixel font for the launcher only (Dogica, as in rumda; check its licence first), Grape Nuts for sticky notes.
- **Shell:** custom Quickshell with a 48 px vertical left bar and chamfered workspace pills (rumda), a dashboard that slides up with a heatmap in the accent ramp, a sticky-notes deck (impasto), and a launcher card with square corners and a 6 px hard offset shadow (rumda).
- **Signature touches:**
  - A pixel cat mascot that jumps when the theme switches, and a wifi status face that frowns when offline (rumda).
  - Matte picture-frame windows through borders-plus-plus, with a "pixel" flavour: rounding 1, stacked borders, shadow offset 9 9.
  - A light↔dark switch where the bar slides out, awww grows in from a corner, and everything reloads once.
  - A compact-mode key that shrinks gaps from 45,40 to 5.
  - A Plymouth paw-print splash in the "Pawsword" style.
  - Limine in latte colours.
- **Motion:** rumda's set: `xfcBezier 0.1,0.9,0.1,1.03` for workspaces, easeOutQuint for windows, windows slide in from the bottom and out to the top, `fakeElastic` for moves. QML pills use OutQuart at 200 ms.
- **Why it's cute:** it feels like a kissaten notebook, with warm milk tea tones, handwriting, and a pixel pet that reacts to what you do.

### B. "Lavender Moon Island": dark glass, colours from the wallpaper, a breathing notch
- **Palette:**
  - Default (used when no wallpaper colour is available) is Shorekeeper: surface #2b243d, fg #ebd7f5, active #bf7ee0, secondary #5d578c, disabled #606275.
  - Terminal uses Nightshade Drift (#232330 / #E0D7E5 with #D96FA0, #96AA89, #D4B484, #7088C4, #A67FAE, #75A8B4).
  - Border: a lavender→pink gradient at 45° in the style of garuda and Sly-Harvey (B4A1DB→D04E9D or ca9ee6→f2d5cf).
  - Otherwise colours come from **matugen** (`scheme-tonal-spot` or `expressive`, `--source-color-index 0`), with impasto's WCAG contrast lifting. hakuspace falls back to white when the accent is too dark; this direction would instead lighten the hue to keep it pastel.
- **Fonts:** Google Sans Flex (OFL, ROND axis; it has to be packaged for Nix) with Inter as fallback, JetBrainsMono NF, Material Symbols Rounded, and Noto Sans CJK JP chosen per string for anime titles (Cenaure).
- **Shell:** custom Quickshell with a top island.
  - Panacea's concave `NotchShape` corners combined with Cenaure's `FloatingPanel` grow-from-widget animation.
  - A launcher with prefixes (impasto), a notification centre, and a theme carousel.
  - Media and stats cards shown only on an empty desktop (Cenaure), a media dock with cava (datsfilipe), rounded screen corners (hakuspace), and a keyboard-layout OSD at the cursor.
- **Lock and login:** trihmah's now-playing polaroid plus hakuspace's stacked two-tone clock card, or one Quickshell `AuthFace` shared by `WlSessionLock` and a greetd greeter (datsfilipe).
- **Motion:** Cenaure's `ivory`, `felt` and `damper` curves as the base, impasto's "Bounce" preset `[0.05,0.9,0.1,1.15]` as the cute option, and datsfilipe's QML `std` overshoot `[0.42,1.67,0.21,0.90]` for popups.
- **Geometry:** rounding 14–22, gaps matched to the shell margins (impasto's 7/18), layer blur on the Quickshell namespaces. Wallpaper changes grow from the cursor position.
- **Why it's cute:** a soft lavender glow on night-time anime wallpapers, a notch that bounces open, and a Japanese-aware now-playing card. It's polished and a little dreamy rather than loud.

### C. "Cotton Candy Toybox": bright, many colours, sticker shapes, the classic stack
- **Palette:**
  - Light base from saurabh's pastel sidebar: #f9f9f9, with pink #fa74b2 and lilac #cf8ef4.
  - Surfaces are misty rose #FFE4E1 with dusty-lavender text #645074 and sky #73C3EB for selection (auspicious).
  - Each module pill gets its own tint from the pastel calendar colours (#ffead3, #ecc6d9, #99ffdd, #ffcc66, #ff6699). Cava uses the mint → lavender → pink gradient (#65BEAA … #DD99A7).
  - Dark variant is Fairy Floss (#343145 / #464258 / #6A6483 with #FFB8D1, #C5A3FF, #C2FFDF, #96CBFE), with base03 fixed.
  - Border: a **visible** 2–3 px rainbow gradient across base08 → 0F that rotates with `borderangle … loop` (librephoenix's idea, which was wasted there at `border_size = 0`). The loop costs GPU time.
- **Fonts:** Comfortaa for display text and the launcher (reserved font name; don't ship modified copies) with feather or Material Symbols Rounded icons (saurabh), Quicksand for UI, Maple Mono NF with cursive italics in the terminal.
- **Shell:** the **classic stack**: Waybar 0.15 + swaync + rofi 2.0.
  - Each Waybar module is its own candy pill (auspicious), related modules are fused into one capsule (trihmah), workspaces each have a "band" colour via `nth-child` (Bocchi), hover uses the bouncy `cubic-bezier(.55,-0.68,.48,1.682)`, drawer groups collapse modules, and the calendar tooltip is pastel.
  - **Caveat:** Waybar workspace clicks fail in Lua mode, so either confirm the fix or use a custom module that calls `hyprctl dispatch 'hl.dsp.focus(...)'`.
- **Signature touches:**
  - **Leaf corners** `15px 5px 15px 5px` everywhere, plus a rofi launcher with an illustrated banner and a power menu of round buttons with a confirm step (auspicious).
  - The **Live2D Arch-chan desktop pet** (NyArch DesktopPuppet).
  - The **OwO / "Pawsword? :3c" Plymouth** with your own art.
  - Kaomoji and "OwO what's this?" lock text, `nyay`/`pacnyan` aliases, `ILoveCandy`.
  - A fastfetch kitty-direct logo.
- **Motion:** the hyprdots set (`winIn 0.1,1.1,0.1,1.1` popin, `winOut 0.3,-0.3,0,1`, `wind 0.05,0.9,0.1,1.05`) plus `overshot 0.13,0.99,0.29,1.1` for workspaces.
- **Why it's cute:** it's a toy box. Every element has its own candy colour, shapes are sticker-like and asymmetric, things bounce, and there's a mascot on the desktop. It's also the cheapest and most portable of the three: HM has `programs.waybar`, and Arch has every piece in extra.

**How they differ:** A is warm, light, colours fixed, pixel and paper, vertical bar, low blur. B is dark and glassy, one accent taken from the wallpaper, an island notch with morphing popups, custom Quickshell. C is bright with many colours, sticker shapes, the Waybar/swaync/rofi stack, and the most kawaii touches.