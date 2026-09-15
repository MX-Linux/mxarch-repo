# mxarch-repo

Bootstrap package for the MX Linux Arch repository (`mxarch`), served from
<https://arch.mxrepo.com/>.

Existing MX-Arch installs have no way to reach the repository on their own - a
package that added it would have to come *from* it. This package closes that
gap through AUR, which every MX-Arch install can already use. New installs get
the repository preconfigured from the ISO and do not need it.

## Enabling the repository

    paru -S mxarch-repo

then add to `/etc/pacman.conf`:

    [mxarch]
    SigLevel = Required DatabaseRequired
    Include = /etc/pacman.d/mxarch-mirrorlist

and `sudo pacman -Sy`. The install script prints this reminder itself, and stays
quiet once `[mxarch]` is present.

## What it installs

    /usr/share/pacman/keyrings/mxarch.gpg       repository signing key
    /usr/share/pacman/keyrings/mxarch-trusted   its fingerprint and trust level
    /etc/pacman.d/mxarch-mirrorlist             Server = ... (backup=, so edits survive)

The install script runs `pacman-key --populate mxarch`, which imports the key and
applies the declared trust - rather than making users run `pacman-key --add` and
`pacman-key --lsign-key` by hand, where a mistyped key id fails confusingly.

`pacman.conf` uses `Include =` rather than a literal `Server =` so the hosting
can move later without every user editing `pacman.conf` again.

## The key expires 2027-05-30

`A220D6ABB427CF38A8C5E560442D90008FE173AE`, the `home:mx-packaging` OBS project
key. When OBS renews it the fingerprint is unchanged but the published key gains
an extended self-signature, so this package needs a refreshed `mxarch.gpg` and a
new `pkgver` around then - otherwise signature checks start failing on installed
systems. Because `mxarch-repo` is also built into the repository itself, that
renewal reaches users through a normal `pacman -Syu`.

## Keyring and mirror list in one package

Deliberate. The usual `-keyring` / `-mirrorlist` split serves repositories with
hundreds of keys and hundreds of mirrors changing on different cadences; `mxarch`
has one key on a four-year expiry and one mirror. If a second mirror ever
appears, splitting then is a normal migration - a new `mxarch-mirrorlist` with
`provides`/`replaces`.

## Removing it

Deletes the key, which breaks updates from `mxarch` while the repository is
still enabled in `pacman.conf`. Remove the `[mxarch]` section too.
