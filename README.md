# gtkhx-flatpak

The Flatpak repository for [GtkHx](https://github.com/mishan/gtkhx), served
at <https://dl.gtkhx.org>.

```sh
flatpak install --user https://dl.gtkhx.org/gtkhx.flatpakref
```

This repository holds no packages. The site is built by a workflow and
deployed to GitHub Pages as a whole; nothing it serves is committed here.

## Publishing

`.github/workflows/publish.yml` publishes one release of GtkHx to one
channel. mishan/gtkhx starts it when a release is published (its
`flatpak-dispatch.yml`); it can also be run from the Actions tab:

- **tag**: the release, e.g. `v1.4.1`.
- **channel**: `stable` or `beta`. A pre-release tag can only go to beta.
  A stable release newer than the beta goes to beta as well.
- **source**: `release` takes the release's `.flatpak` assets, which must
  cover both x86_64 and aarch64; `rebuild` builds the tag from source
  first, for recovery when those are missing or unusable.
- **bootstrap**: start a new, empty repository. Refused when the site is
  already up. The dispatch never sets it: the first dispatched run fails,
  saying so, and is then run again by hand with it ticked.

Each run mirrors the live site, adds the new commits, signs them and
redeploys the whole site. Runs queue behind each other, but GitHub keeps
only one waiting and cancels an older waiting run, which then has to be
started again. If the live site can't be read, the run stops
rather than deploy a repository missing the other channel. How the
repository is laid out, and how to recover it, is in GtkHx's
[docs/flatpak-repo.md](https://github.com/mishan/gtkhx/blob/main/docs/flatpak-repo.md).

The signing key is the `FLATPAK_GPG_KEY` secret (the armored signing
subkey only) with `FLATPAK_GPG_KEY_ID`, in the `github-pages`
environment. Its public half is `packaging/flatpak/gtkhx.gpg` in
mishan/gtkhx.
