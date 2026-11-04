# vitruvianports

Software packaged for V\OS. Each port is a Debian source package built with
[vitruvianporter](https://github.com/VitruvianOS/vitruvianporter) in a clean
V\OS build root; the resulting packages are published in the V\OS apt
repository.

```
ports/
  apps/       native applications
  toolkits/   toolkit and library support (Qt platform plugin, SDL, ...)
  x11/        X11 support
```

## A port

```
ports/apps/takenotes/
  port.yaml   where the source comes from
  debian/     the packaging
```

`port.yaml` pins the upstream source:

```yaml
git: https://github.com/HaikuArchives/TakeNotes
commit: 80906176bf0c85248252ca61ede4f449a076c953
```

The orig tarball is `git archive` of that commit, so builds are
reproducible. Upstream code is never copied into this tree; changes to it
are quilt patches in `debian/patches/`.

A patched Debian package names the Debian source instead:

```yaml
debian: xorg-server
version: 2:21.1.16-1.3
```

Its `debian/` holds only a `changelog` entry for the V\OS revision
(`<version>vos1`); `patches/` (with a `series` file) and an optional
`rules.diff` sit next to `port.yaml` and are applied on top of Debian's
packaging.

## Packaging rules

- Build with `dh $@ --with vos`. dh-vos provides the legacy Makefile-Engine
  buildsystem (`--buildsystem=vos_makefile_engine`), lists the files whose
  resources become attributes on install, keeps those resources through
  stripping, and handles the system libraries in `dh_shlibdeps`.
- No maintainer scripts for attributes, no wrapper scripts, no overrides
  of `dh_strip`, `dh_dwz` or `dh_shlibdeps`.
- Apps install to `/system/apps`; add a Deskbar entry with a `.links` file.
- `vitruvianporter check` must pass.

## Building

```
vitruvianporter bootstrap
vitruvianporter build takenotes
vitruvianporter build --all --out out/
```
