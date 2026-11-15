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
expected/<slug>.jpg   the approved Panorama, written by the test itself
                      with `just stitch-regress-accept`
```

The report of each Session (size, field of view, Steps, unaligned Shots,
dropped Steps) is snapshotted in the main repository next to the test,
not here.

## Sessions

| Slug | Game | Grid | Why it is here |
|---|---|---|---|
| `noclip-2026-11-14_12-58-50` | NoClip | 5 × 6 | A clean browser capture; one sky Shot placed from the grid. |
| `noclip-2026-11-14_13-50-29` | NoClip | 5 × 6 | Same scene, another run. |
| `noclip-2026-11-14_13-52-35` | NoClip | 5 × 6 | Same scene, another run. |
| `noclip-wow-2026-11-14_13-11-28` | NoClip over a World of Warcraft map | 5 × 7 | Matches PTGui's result. |
| `noclip-generic-macbook-2026-11-01_13-42-30` | Mario Kart in NoClip, MacBook screen | 7 × 14 | Fourteen sky Shots in Rows 6 and 7 that only the grid can place. |
| `world-of-warcraft-2026-11-07_23-14-46` | World of Warcraft | 7 × 14 | Two Shots dropped where the camera did not move; matches PTGui. |

## Adding a Session

From the main repository, with this one checked out next to it:

```sh
just stitch-fixture-add "~/Pictures/Panowww/<Game>/<timestamp>" <slug>
```

Then add a `real_session!` line to the test, run
`just stitch-regress-accept <slug>`, look at `expected/<slug>.jpg` with
your own eyes, and commit here and there.

## Re-approving a reference

When the Stitch changes on purpose and a Session's Panorama legitimately
changes with it, `just stitch-regress-accept` rewrites every reference
and snapshot. Look at each Panorama before committing: the test only
knows what it was told to expect.

## Licence

The Shots are captures of the games named above and belong to their
publishers; they are shared here only to test Panowww. Everything else
is MIT, like Panowww.
