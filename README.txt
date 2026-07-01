openscad-wasm — Corresponding Source mirror
==================================================

This repository publishes the Corresponding Source for the WebAssembly
build of openscad (license: GPL-2.0-or-later) used in edgetools.io.

Contents
  build/      our build recipe: Dockerfile + helper scripts/config/patches.
              Rebuild with:  docker build build/
  upstream/   the exact upstream source archive(s) the build fetched,
              byte-identical and sha256-verified (see below).

Upstream sources:
  openscad
    https://github.com/openscad/openscad.git
    commit 0a66508c67374febcfc814a73b5b948dd84a1ca3 (+submodules)
