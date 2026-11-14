# Berlinan for the M-VAVE FM-1

Berlinan is a generative sequencer in the Berlin School tradition: a small ensemble of voices that repeat, phase
against each other on loops of unequal length, and earn their mutations over time. This is its firmware for the
M-VAVE FM-1, so the sequencer runs on its own box and needs no laptop. The FM-1 makes no sound: plug it by USB
into a MIDI router, and each voice goes out on its own MIDI channel to your synthesizers. You play it from the
panel: the keys, the buttons, the encoders and the screen.

It plays what Berlinan for macOS plays: the same template and seed, with the
same actions at the same bars, give the same notes and the same CC, event for event. Tests check this against the
unchanged Swift engine. It starts from Berlinan's built-in templates and a seed; projects do not move between the
Mac and the FM-1. The FM-1 is the MIDI clock master (clock, Start and Stop over USB), or follows an incoming
clock from USB or the TRS jack. SAVE keeps the state across power-off.

Status: complete for its first version: the engine, the panel and the seven pages, MIDI out over USB, the clock
(INT and EXT), SAVE and the six slots. What it leaves out (harmony modes, melody, the MIDI echo, Link, capture) is
listed in the manual.

**Website: <https://jiripisa.github.io/fm1-berlinan/>** (in English and [Czech](https://jiripisa.github.io/fm1-berlinan/cs/)).
Install it from the browser: open the [install page](https://jiripisa.github.io/fm1-berlinan/install/) in Chrome or
Edge, connect the FM-1 by USB and press Install; the same page puts M-VAVE's firmware back. You can also
[try it in the browser](https://jiripisa.github.io/fm1-berlinan/emu/), its MIDI out to your synths over Web MIDI.

Its identity is `FM-1_23XXYYZZ` (0.5.1 answers `FM-1_23000501`). It reads no other firmware's projects or samples: a
saved record it does not recognise, another firmware's, is ignored, and it starts on the Demo trio with a fresh seed.

## Where to read

- [docs/MANUAL.md](docs/MANUAL.md): installing it, the controls, the pages, saving, sync, and how it differs from
  Berlinan on the Mac. In Czech: [docs/MANUAL.cs.md](docs/MANUAL.cs.md).
- The site: `tools/site.sh [VERSION]` builds it in `app/build/site` (the release package, the emulator, fm1-base's
  installer and Berlinan's own pages, `app/site/`, through `tools/site_pages.py`); `tools/publish.sh VERSION` pushes
  it to the repository's gh-pages branch. The drawing of the panel is `tools/gen_panel.py`'s.
- [The design](docs/superpowers/specs/2026-10-26-berlinan-fm1-design.md) (the spec) and
  [the plan](docs/superpowers/plans/2026-10-26-berlinan-fm1.md).
- [tools/reference](tools/reference): RefTool, a small Swift program over the unchanged Berlinan engine (the pinned
  commit, fetched with `git archive`), which writes the reference renders. `tools/reference/make_refs.sh` regenerates
  them; nothing is written into the Berlinan checkout.
- [tests/equiv/README.md](tests/equiv/README.md): how equivalence is tested. A script (a template, a seed, actions at
  ticks) is played by the C engine and compared, line by line, with what the Swift engine rendered for it
  (`tests/equiv/*.ref`, committed, so the tests need no Swift). The runtime's MIDI is compared the same way.
- [BUILDING.md](BUILDING.md): prerequisites, the build, the tests.

## Building and testing

[BUILDING.md](BUILDING.md) has the prerequisites. In short:

```
git submodule update --init --recursive
./build.sh                    # app/build/berlinan.fwsc
sh tests/run_tests.sh         # the host tests and the simulator scenarios
```

## Licence

GPL-2.0-or-later (see [LICENSE](LICENSE)). It is built on [fm1-base](https://github.com/charlesvestal/fm1-base)
(`base/`, MPL-2.0) and felucca-bsp, from Felucca by Leo Kuroshita (Hügelton Instruments); they keep their own
licences.

You can also try Berlinan in the browser, no flashing needed, in the free community [FM-1 Emulator](https://fm1-emulator.atst.tools/).
