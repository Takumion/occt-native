# occt-native — the native OCCT builds Takumion downloads

[Open CASCADE Technology](https://github.com/Open-Cascade-SAS/OCCT) (OCCT),
built from its unmodified source as shared libraries, for the platforms where
Open Cascade publishes no binaries of its own. The Takumion desktop app offers
these as its full geometry kernel and downloads the one its release pins, on
the user's request. OCCT is never part of the Takumion installer, and never
linked into Takumion statically.

Nothing is committed here but this file: everything is a
[release](https://github.com/Takumion/occt-native/releases), published by
Takumion's release workflow from the build it ships.

## What the releases are

| Release | What it holds |
|---|---|
| `occt-<platform>-<arch>-<hash>` | `occt-<platform>-<arch>-<hash>.tar.gz`: the libraries, OCCT's licence and exception, the licences of the third-party code in them, and a notice naming the source. `<hash>` is the first 12 characters of the archive's SHA-256; the release notes give all of it. Beside it, `occt-recipe.json`: the recipe it was built with. |
| `occt-source-<version>` | OCCT's source for that version, unmodified: the commit the recipe pins, as Open Cascade publishes it. The source of every archive here built from that version. |

Old releases are kept: an installed Takumion downloads the archive its own
release pinned, and checks it against that SHA-256 before using it.

## How an archive is built

`occt-recipe.json` is the whole recipe: OCCT's version, tag and commit, the
cmake options, and the release of [jemalloc](https://github.com/jemalloc/jemalloc)
(pinned by SHA-256) that OCCT's own `USE_MMGR_TYPE=JEMALLOC` option links into
`TKernel` as its allocator.

1. Build jemalloc's release as a static library, with the recipe's
   `configure` options.
2. Configure OCCT's source with the recipe's `cmake` options, each name in
   `without` as `-DUSE_<name>=OFF`, `-D3RDPARTY_JEMALLOC_DIR` pointing at
   step 1, and an install rpath of `$ORIGIN` (`@loader_path` on macOS) so the
   toolkits find each other in their own folder.
3. Build and install. The archive holds the installed libraries Takumion
   loads, under the names its binaries ask for.

OCCT's source is not patched. These are separate shared libraries loaded at
run time: you may rebuild them from the same source with your own changes and
put them in Takumion's kernel folder in their place. The Takumion desktop app
can also run this same build on your own computer ("Build it yourself…").

Windows is not here: there Takumion downloads Open Cascade's own release.

## Licence

OCCT is Copyright (c) 1999-2026 OPEN CASCADE S.A.S., licensed under the GNU
Lesser General Public License 2.1 with the Open CASCADE exception; both texts
are in every archive (`LICENSE_LGPL_21.txt`, `OCCT_LGPL_EXCEPTION.txt`).
jemalloc is under a BSD 2-clause licence, in every archive as
`third-party-licenses/jemalloc-COPYING`.
