# vitruvianports

Software packaged for V\OS. Each port is a Debian source package built with
[vitruvianporter](https://github.com/VitruvianOS/vitruvianporter) in a clean
V\OS build root; the resulting packages are published in the V\OS apt
repository.

## Layout

```
ports/
  apps/       native applications
  toolkits/   toolkit and library support (Qt platform plugins, SDL)
  x11/        X11 support
```

Upstream code is never copied into this tree. A port records where the
source comes from and carries only the packaging and patches.

## Building

Requires [vitruvianporter](https://github.com/VitruvianOS/vitruvianporter),
Python 3 and Docker.

```
vitruvianporter bootstrap                    # build root, testing-nightly/amd64
vitruvianporter list                         # ports in build order
vitruvianporter check --all                  # lint the packaging
vitruvianporter build takenotes xcalc        # .debs and logs in ./out
vitruvianporter build --all --out out/
```

Run from the root of this repository, or pass `-C <path>` or set
`$VITRUVIANPORTS`. `--suite` (`testing-nightly`, `testing`,
`trixie-nightly`, `trixie`) and `--arch` pick the build root.

## Adding a port

Create `ports/<category>/<name>/` with a `port.yaml` and the packaging,
then run `vitruvianporter check <name>` and `vitruvianporter build <name>`.

### Port from an upstream git repository

```
ports/apps/takenotes/
  port.yaml
  debian/
```

```yaml
git: https://github.com/HaikuArchives/TakeNotes
commit: 80906176bf0c85248252ca61ede4f449a076c953
subdir: source          # optional
```

| Key | Meaning |
|---|---|
| `git` | public `https://` URL of the upstream repository |
| `commit` | full 40-character sha; the orig tarball is `git archive` of it, so builds are reproducible |
| `subdir` | optional; package only this directory of the repository |

`debian/` is a normal Debian packaging directory: `control`, `changelog`,
`rules`, `copyright` (DEP-5), `source/format`, and `install`, `links`
and similar files as needed. Changes to upstream code are quilt patches
in `debian/patches/` with a `series` file.

### Patched Debian package

```
ports/x11/xorg-server/
  port.yaml
  debian/changelog
  patches/
  rules.diff
```

```yaml
debian: xorg-server
version: 2:21.1.16-1.3+deb13u4
```

| Key | Meaning |
|---|---|
| `debian` | Debian source package name |
| `version` | exact Debian version to fetch |

`debian/` holds only a `changelog` entry for the V\OS revision.
`patches/` (with a `series` file listing the patches in order) and an
optional `rules.diff` sit next to `port.yaml` and are applied on top of
Debian's packaging.

### Patches

Every patch carries a [DEP-3](https://dep-team.pages.debian.net/deps/dep3/)
header: `Description` (a short subject, then the reason), `Author`,
`Forwarded` (`not-needed` or the upstream URL), `Origin`, and
`Last-Update`, followed by `---`.

### Packaging rules

- Build with `dh $@ --with vos`. dh-vos provides the legacy Makefile-Engine
  buildsystem (`--buildsystem=vos_makefile_engine`), lists the files whose
  resources become attributes on install, keeps those resources through
  stripping, and handles the system libraries in `dh_shlibdeps`.
- No maintainer scripts for attributes, no wrapper scripts, no overrides
  of `dh_strip`, `dh_dwz` or `dh_shlibdeps`.
- Apps install to `/system/apps`; add a Deskbar entry with a `.links` file.
- `vitruvianporter check --all` must pass.

## Ports

### Applications

| Port | Description |
|---|---|
| 8dock | Desktop dock launcher for V\OS |
| armyknife | Audio tag editor and MusicBrainz client for V\OS |
| artpaint | Layered raster painting and image-manipulation app for VitruvianOS |
| becasso | Layer-based graphics editor for V\OS |
| bedc | Direct Connect client for V\OS |
| bemines | Minesweeper game for V\OS |
| benettris | Networked Tetris game for V\OS |
| beohms | Ohm's law calculator |
| bepdf | PDF viewer for V\OS |
| bespider | Spider solitaire for V\OS |
| bsnow | Falling-snow desktop effect for V\OS |
| burnitnow | CD/DVD burning frontend for V\OS |
| calendar | Month and day calendar application for V\OS |
| capitalbe | Double-entry personal finance manager for V\OS |
| clipup | Clipboard manager for V\OS |
| colors | Color picker |
| copynametoclipboard | Copy-name-to-clipboard Tracker add-on |
| deskbareyes | Watching-eyes deskbar widget |
| desknotes | Sticky notes on the desktop |
| dockbert | Dock panel for application icons and folders |
| ffmpeggui | GUI frontend for ffmpeg |
| filer | Rule-based file organiser |
| fontboy | Font browsing and preview application |
| ftppositive | FTP client |
| haiku2048 | 2048 puzzle game |
| hare | Audio ripper and encoder frontend |
| hexvexed | Hexagonal tile puzzle game |
| hustler | Musical deskbar companion |
| keycursor | Keyboard-as-mouse input add-on and settings preflet |
| maps | Map viewer with place search for V\OS |
| melt | CD burning frontend for V\OS |
| midisynth | MIDI synthesiser keyboard for V\OS |
| moe | Desktop mascot companion for V\OS |
| netpulse | Network activity tray monitor for V\OS |
| opentargetfolder | Open-target-folder Tracker add-on for V\OS |
| paladin | Project-based IDE for writing V\OS applications |
| pecorename | Batch file renamer for V\OS |
| ponpokodiff | Two-file visual diff viewer for V\OS |
| rename | Batch-rename Tracker add-on for V\OS |
| shredder | Secure-delete Tracker add-on for V\OS |
| slayer | Process and thread monitor for V\OS |
| streamradio | Internet radio player for V\OS |
| takenotes | Post-it style notes |
| textworker | Text transformation editor for V\OS |
| theawesomeresizer | Interactive image resizer for V\OS |
| timetracker | Per-task time tracking application for V\OS |
| tipster | On-screen usability tips for V\OS |
| trackergrep | Text-search Tracker add-on for V\OS |
| trax | File-finding utility for V\OS |
| usbdeskbar | USB device list tray item for V\OS |
| vision | IRC client with scripting and DCC for V\OS |
| wakeup | Countdown alarm clock for V\OS |
| weather | Weather forecast application for V\OS |
| workspacenumber | Workspace number tray indicator for V\OS |
| xcalc | XCalc calculator for V\OS |

### Toolkits

| Port | Description |
|---|---|
| qt5-vos-plugin | Qt 5 platform plugin for V\OS |
| qt6-vos-plugin | Qt 6 platform plugin for V\OS |
| sdl2-compat | Patched Debian package `sdl2-compat` |
| sdl3 | Patched Debian package `libsdl3` |

### X11

| Port | Description |
|---|---|
| libxcb | Patched Debian package `libxcb` |
| xorg-server | Patched Debian package `xorg-server` |
## License

This repository has no license of its own. Each port keeps the license of
the software it packages: see `debian/copyright` in the port (or Debian's
copyright for patched Debian packages). Packaging files and patches follow
the license of the package they belong to.
