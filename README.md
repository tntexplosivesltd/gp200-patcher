# GP-200 Patcher

A self-contained, offline browser tool for applying reverse-engineered custom
firmware mods to a [Valeton GP-200](https://valetonmusic.com/) multi-effects
pedal.

Read the writeup: [gp200-reversing.hashnode.dev](https://gp200-reversing.hashnode.dev)

## What this is

This repo holds only the built, self-contained patcher page released alongside
each blog post - not the reverse-engineering tooling or source used to build
the mods. See the [Releases](../../releases) page for downloads.

Each release is a single `.html` file. It runs entirely in your browser with
no server and no network calls: open it directly from your downloads folder,
point it at your own stock firmware image, and it verifies, patches, and lets
you download the modified image client-side.

## Releases

- **tuner** - the tightened tuner deadband + footswitch-LED tuner status +
  strobe display mod, covered in "The Front Panel, and a Tuner That Actually
  Locks" on the blog above
