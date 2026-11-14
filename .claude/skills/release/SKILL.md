---
name: release
description: Use when the user wants to release, publish or ship a new version of Berlinan for the FM-1 ("udělej release", "vydej novou verzi", "make a release"), or to build the release firmware package (.fwsc).
---

# Releasing Berlinan for the FM-1

A release is: version → tests → release package `berlinan-X.Y.Z.fwsc` →
commit + tag → GitHub Release → website (`gh-pages`) → live checks → project
catalog. Every step below has a check; a step is not done until its check
passed.

**Nothing is ever written to a connected FM-1** (global rule): no
`flash-fw.sh`, no `fm1_install.py`, no rescue script, not even to test the
package. Trying the release on the device is the user's step; ask them.

**Pushing needs the user's explicit OK** (global rule). Before step 5 ask one
bold question that names everything this release publishes: the push of
`main`, the tag `vX.Y.Z`, the GitHub Release, the website (a force push of
`gh-pages` by `tools/publish.sh`) and the catalog update of step 7 (a push to
`jiripisa/jiripisa.github.io` or a rebuild of it), unless the user has
already said yes to exactly that in the conversation.

## 1. Prepare

1. On `main`, `git status` clean, up to date with `origin/main`;
   `git submodule status` shows no line starting with `-`.
2. The build's prerequisites (`BUILDING.md`): Docker (or podman) running, the
   JieLi toolchain and the AC79 SDK in place, a Python with Pillow, `emcc`
   for the emulator.
3. Pick the version, counted from the last tag (`git describe --tags --abbrev=0`):
   patch for fixes, minor for new features, major only when the user says so.
   Each part must stay below 100: the package identity is `FM-1_23XXYYZZ`.
   Say which and why.
4. Replace the old version where the repo names it:
   `git grep -n "<old version>"` — `tools/site.sh` (the default `V` and its
   comment) and the identity example in `README.md`
   (`X.Y.Z answers FM-1_23XXYYZZ`).

## 2. Build and test

```bash
./build.sh --release X.Y.Z        # app/build/berlinan-X.Y.Z.fwsc
./build.sh                        # app/build/berlinan.fwsc, for the update-path test
sh tests/run_tests.sh > <scratch>/tests.txt 2>&1
sh base/tests/run_tests.sh > <scratch>/base-tests.txt 2>&1
```

Check: both builds end without an error (the build checks image, RAM and pool
sizes itself) and both test logs end green; read their summaries.

## 3. Release notes

Write them from `git log --oneline <last tag>..HEAD`, in the form of the
earlier releases (`gh release view <last tag>`): one paragraph on what changed
for the player, then the three links (install from the browser, the manual,
the emulator), each with its Czech page. Save them to `<scratch>/notes.md`
and show them to the user.

## 4. Commit and tag

1. `git status`, then commit the version change in the repo's style
   (`Area: what changed`, e.g. `Release: 0.5.2, <one line on what is new>`),
   never with AI attribution trailers.
2. `git tag vX.Y.Z` (lightweight, as the earlier tags).

## 5. Publish (order matters)

1. `git push origin main`, then `git push origin vX.Y.Z`.
2. Copy `app/build/berlinan-X.Y.Z.fwsc` to `<scratch>` and run
   `gh release create vX.Y.Z <scratch>/berlinan-X.Y.Z.fwsc --title "Berlinan for the FM-1 X.Y.Z" --notes-file <scratch>/notes.md`.
3. Check: `curl -sIL -o /dev/null -w "%{http_code}" https://github.com/jiripisa/fm1-berlinan/releases/download/vX.Y.Z/berlinan-X.Y.Z.fwsc` → `200`.
4. `sh tools/publish.sh X.Y.Z` — builds the site from the tagged commit and
   pushes it to `gh-pages`, then asks Pages to build.

## 6. Live checks

1. `gh api repos/jiripisa/fm1-berlinan/pages/builds/latest --jq .status`
   says `built` (retry for a minute while it says `building`).
2. `curl -s "https://jiripisa.github.io/fm1-berlinan/?x=$RANDOM" | grep -o "X.Y.Z"`
   and the same for `install/` and `cs/` show the new version.

## 7. Project catalog

Only after the live checks passed: run the global `update-catalog` skill for
vX.Y.Z. It puts Berlinan for the FM-1 into the right section of the catalog
at https://jiripisa.github.io/, updates its summary when this release changed
what the firmware does, and publishes the catalog so the new download shows
there.

## Report

Tell the user: the release URL, what was pushed, the results of every check,
the version choice and why, and ask them to install the release on their
FM-1 and confirm it starts and shows up as `FM-Berlinan` on USB.
