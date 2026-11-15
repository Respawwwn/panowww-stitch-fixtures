# Panowww Stitch fixtures

Real Sessions captured with [Panowww](https://github.com/Respawwwn/panowww)
and the Panorama its Stitch made of each one, approved by eye. The main
repository's regression test (`crates/store/tests/real_sessions.rs`,
`just stitch-regress`) stitches every Session again and holds the result
to its reference, so a change to the Stitch cannot quietly degrade a
Session that once worked.

They live here rather than in the main repository because they weigh
half a gigabyte: every clone and every CI run of the main repository
would carry them otherwise.

## Layout

```
sessions/<slug>/      the Session folder as Panowww wrote it: the Shots
                      r<row>_c<col>.png and session.json (its Panorama
                      block removed)
expected/<platform>/<slug>.jpg
                      the approved Panorama per platform (macos-aarch64,
                      windows-x86_64, linux-x86_64), written by the test
                      itself with `just stitch-regress-accept`
failing/<slug>/       Sessions the Stitch must refuse, for the manual
                      tests only (not in the regression test)
```

The report of each Session (size, field of view, Steps, unaligned Shots,
dropped Steps) is snapshotted in the main repository next to the test,
one snapshot per platform, not here.

## Why one reference per platform

The floating point of a Stitch differs per CPU and maths library: the
same Session moves by a hundredth of a degree between an Apple M3 Max
and an Intel i7, and a textured Panorama such as the World of Warcraft
one then differs by 4 levels on average, 21 % of its pixels by more than
8. No tolerance holds two platforms to one reference and still catches a
regression, so each platform is held to its own, byte for byte. The
Linux references come from CI: when the workflow fails it uploads the
`real-sessions-linux` artifact with the Panoramas and the new snapshots
to look at.

## Sessions

| Slug | Game | Grid | Why it is here |
|---|---|---|---|
| `noclip-2026-11-14_12-58-50` | NoClip | 5 × 6 | A clean browser capture; one sky Shot placed from the grid. |
| `noclip-2026-11-14_13-50-29` | NoClip | 5 × 6 | Same scene, another run. |
| `noclip-2026-11-14_13-52-35` | NoClip | 5 × 6 | Same scene, another run. |
| `noclip-wow-2026-11-14_13-11-28` | NoClip over a World of Warcraft map | 5 × 7 | Matches PTGui's result. |
| `noclip-generic-macbook-2026-11-01_13-42-30` | Mario Kart in NoClip, MacBook screen | 7 × 14 | Sky Shots in Rows 6 and 7 that only the grid can place: fourteen at first, four since the matcher chose its centres by texture. |
| `world-of-warcraft-2026-11-07_23-14-46` | World of Warcraft | 7 × 14 | Two Shots dropped where the camera did not move; matches PTGui. |
| `r6-2026-11-15_14-31-29` | Rainbow Six Siege, Windows | 5 × 7 | A parking garage of white walls and grey floor: the Rows' closing pairs had one match each and the front seam broke by 20 px until the matcher chose its centres by texture. |

## As a Sessions folder

Panowww lists `sessions/*` and `failing/*` as Sessions when its Sessions
folder points at this clone (Settings → Sessions folder), which is how
the Windows pass gets the same Sessions as the Mac. The Panoramas it
writes there stay out of git (`.gitignore`). `failing/sky-2x3-2026-11-15`
is six plain-sky Shots of the Mario Kart Session as a 2 × 3 Session: the
Stitch must fail on it with *Too few Shots could be linked*.

## Adding a Session

From the main repository, with this one checked out next to it:

```sh
just stitch-fixture-add "~/Pictures/Panowww/<Game>/<timestamp>" <slug>
```

Then add a `real_session!` line to the test, run
`just stitch-regress-accept <slug>` on each machine, look at
`expected/<platform>/<slug>.jpg` with your own eyes, and commit here and
there.

## Re-approving a reference

When the Stitch changes on purpose and a Session's Panorama legitimately
changes with it, `just stitch-regress-accept` rewrites every reference
and snapshot of the platform it runs on: run it on the Mac and on
Windows, and take Linux's from the CI artifact. Look at each Panorama
before committing: the test only knows what it was told to expect.

## Licence

The Shots are captures of the games named above and belong to their
publishers; they are shared here only to test Panowww. Everything else
is MIT, like Panowww.
