# Video Player for Rigee

A [Rigee](https://github.com/Qyuan-Tech/rigee) plugin: MP4, MOV, WebM, MKV and Ogg video opens in
Chromium's own player instead of the text editor — streamed from disk, so a long film starts at once
and seeking reads only what it plays — with speed, full screen, and the position kept when the
editor is split.

## Installing

In Rigee, open the **Plugins** view, search for `video` and install **Qyuan-Tech/rigee-plugin-video**.
Rigee downloads the latest release, checks it against the checksum GitHub reports for it, and asks
before anything runs. It needs Rigee 0.0.1-alpha.6 or later.

What it declares: **read files on this computer** — the video being played. As with every Rigee
plugin, that is a statement of what it is for, not a limit Rigee enforces.

## What it opens, and what it does not

Only containers Chromium plays are claimed, and each was played before it went on the list. AVI,
WMV, FLV and MPEG-2 are not: a player that opened only to say it cannot would be worse than leaving
them to the text editor's honest refusal.

## How it is built

Two halves, but nothing is read in the main process: a film is far too big to hand over whole. The
main half decides a file may be played and asks Rigee for a URL that streams it
(`ctx.shell.serveFile`), and the `<video>` element reads from that what it is about to play. The URL
names a token rather than the path, and stops working when the player closes or the plugin stops.

```
src/protocol.ts           the two commands the halves share, typed for both
src/main/index.ts         main half: checks the file, asks for a URL, lets it go
src/renderer/index.tsx    renderer half: binds the editor
```

```bash
pnpm install
pnpm run typecheck
pnpm run build          # dist/main.js and dist/renderer.js
npm pack                # rigee-plugin-video-<version>.tgz, the release asset
```

To try a build in Rigee without publishing it, unpack the tarball's `package/` into
`<userData>/plugins/video/` and approve it in the Plugins view.

`@rigee/plugin-api` is not on npm yet; `vendor/` holds the copy this plugin is built against.

## Releasing

Set the same version in `version` and `rigee.version`, commit, and push a tag `v<version>`. The
release workflow checks the three agree, builds, packs, and publishes a release carrying
`rigee-plugin-video-<version>.tgz` — the one asset Rigee installs from.

## Licence

MIT
