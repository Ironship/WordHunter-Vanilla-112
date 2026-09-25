# The 1.12 port, copied off the client it was living on

Three addons built for World of Warcraft **1.12.1** (`## Interface: 11200`),
running on the Project Legacy client at
`C:\Users\Oleg\Games\Project Legacy\Interface\AddOns`:

| addon | version |
|---|---|
| WordHunterWoW | 1.16.0-vanilla |
| WordHunterWoW-Dictionary-DE | 1.4.6-vanilla |
| WordHunterWoW-ENPanel | 1.5.3-vanilla |

## Why this folder exists

Because until it did, the port existed in exactly one place. An audit on
2026-09-14 went looking for it and found:

- no repository for it anywhere in this workspace
- no branch and no tag in `WordHunterWoW` mentioning vanilla, 1.12 or legacy
- `Vanilla.lua` — a file the port has and mainline does not — in **no commit of
  any branch**
- `Tools/build_release.ps1` accepts only `retail` and `classic`, so nothing
  builds it
- no mention in any of the four handoff documents

Deleting that game folder would have deleted the work. This is a second copy,
made so that is no longer true. It decides nothing about where the port should
finally live.

## What is actually different, and why it matters

`Compat.lua` is forked, and the fork is the interesting part. Mainline answers
`RETAIL` when `WOW_PROJECT_ID` is not a number:

```lua
-- WordHunterWoW/Compat.lua
if type(WOW_PROJECT_ID) ~= "number" then return RETAIL end
```

1.12 defines neither `WOW_PROJECT_ID` nor `WOW_PROJECT_MAINLINE`, so on that
client mainline calls itself Retail — which is the wrong answer for every branch
that then reads it. The port inverts it:

```lua
-- the port
if type(WOW_PROJECT_ID) ~= "number" then return CLASSIC end
```

So the fix for a real defect in the shipped compatibility layer is sitting here,
untracked, and has been for as long as this port has existed.

The port also carries `Vanilla.lua`, which mainline has no counterpart for, and
its manifest is **unsuffixed** — `WordHunterWoW.toc` rather than
`WordHunterWoW_Mainline.toc` — so the client loads it on any flavour rather than
on one.

## What has not been decided

Whether this becomes a repository, a branch of the three it forks, or stays a
copy. Whether the `Compat.lua` inversion should be carried back into mainline
behind a guard, which would fix the same fault there without a fork. And whether
`build_release.ps1` should learn a third flavour, which is the change that would
let this be built rather than hand-maintained.

Nothing here has been modified. It is a copy, taken 2026-09-14.

## Licence

This README and the `WordHunterWoW` folder are MIT, see `LICENSE`; the
folder's own `LICENSE` followed the source repository to MIT on 2026-09-25.
The dictionary and the English panel stay GPL v3: they carry data derived from
Questie, see each folder's `NOTICE`.
