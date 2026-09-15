# mxarch-repo

Bootstrap package for the MX Linux Arch repository (`mxarch`), served from
<https://arch.mxrepo.com/>.

Existing MX-Arch installs have no way to reach the repository on their own - a
package that added it would have to come *from* it. This package closes that
gap through AUR, which every MX-Arch install can already use. New installs get
the repository preconfigured from the ISO and do not need it.

## Enabling the repository

    paru -S mxarch-repo
    sudo mxarch-enable
    sudo pacman -Syu

`mxarch-enable` appends the `[mxarch]` section to `/etc/pacman.conf`, keeping a
timestamped backup of the previous file. It is idempotent - run twice and it
says the repository is already enabled rather than adding a second section - and
it refuses to touch a `pacman.conf` that mentions `mxarch` without having an
`[mxarch]` section, since that means someone has edited it in a way the script
should not guess at. The section goes last, so the official repositories keep
precedence.

The install script prints this reminder itself, and stays quiet once `[mxarch]`
is present.

## What it installs

    /usr/share/pacman/keyrings/mxarch.gpg       repository signing key
    /usr/share/pacman/keyrings/mxarch-trusted   its fingerprint and trust level
    /etc/pacman.d/mxarch-mirrorlist             Server = ... (backup=, so edits survive)
    /usr/bin/mxarch-enable                      adds the [mxarch] section to pacman.conf

The install script runs `pacman-key --populate mxarch`, which imports the key and
applies the declared trust - rather than making users run `pacman-key --add` and
`pacman-key --lsign-key` by hand, where a mistyped key id fails confusingly.

`pacman.conf` uses `Include =` rather than a literal `Server =` so the hosting
can move later without every user editing `pacman.conf` again.

## Why enabling is a separate command

`/etc/pacman.conf` belongs to the `pacman` package, and a package should not
edit another package's config as a side effect of being installed. The removal
path is the genuinely risky half: un-appending a section from a file the user
may have edited means `sed` against unknown content, and a botched edit there
breaks all package management, not just this repository.

Run deliberately - by hand, or from a GUI where the user clicked something
labelled "Enable MX Arch repo" - that objection does not apply, because the
config change is the thing being asked for rather than a hidden consequence of
something else. Hence a command the user runs, rather than a `post_install`
that rewrites `pacman.conf` behind them.

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
