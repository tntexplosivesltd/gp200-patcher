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

## Troubleshooting

### The update never starts

You select the patched firmware file, the editor shows your GP-200 as connected with an Update button, but pressing Update just takes you back to the file-select screen. Or a progress bar appears for a moment and then disappears. Meanwhile Valeton's own firmware file updates the unit normally.

Work through these three in order.

**1. Check which model you have.**

Look at the label on the underside of your unit.

These mods work on the **GP-200**, and that includes the **GP-200R** and **GP-200X**, which are the same pedal in different colours.

They do **not** work on the **GP-200LT** or the **GP-200JR**. Those are separate products with their own firmware, and there is no way to make these mods fit them.

This is the most common cause. The editor reads the model name out of the firmware file and compares it against the unit you have plugged in. A file built for a GP-200 is refused by an LT or a JR, and that refusal looks exactly like the problem above.

**2. Check the file is complete.**

Right-click the `.bin` file, choose **Properties**, and look at **Size**.

It must be exactly **6,451,048 bytes**. Windows shows this as `6.15 MB (6,451,048 bytes)`. Read the number in brackets, not the rounded one.

If it is any other number, the file was damaged while downloading or unzipping. Unzip it again from the `.zip` you downloaded, or build it again.

**3. Build the file yourself. Do not use one someone sent you.**

If somebody passed you a finished `.bin`, set it aside and make your own with the patcher page.

The patcher checks your firmware before it changes anything, so building it yourself catches a damaged file, or one meant for a different model or a different firmware version, before it ever reaches your unit. A file handed to you in a forum post or a chat message has had none of those checks run, and there is no way to tell by looking at it what it was built from.

Always start from a fresh, unmodified copy of GP-200 firmware V1.8.0. Never patch a file that has already been patched.

### There are no logs to send

The editor does not keep a log file, so there is nothing to dig out when an update fails. If the three checks above do not explain it, the useful details are:

- the model name from the label on the underside of the unit
- the size in bytes from Properties
- the editor version, from its About screen
- whether you built the file yourself or someone sent it to you

If you are comfortable with a command prompt, this prints the file's full fingerprint, which the patcher page also showed you when it built the file:

```
certutil -hashfile "yourfile.bin" SHA256
```

### If the unit will not start up

Update mode lives in a part of the GP-200 that these mods never touch, so a failed or interrupted update can always be undone. Put the unit back into update mode with the boot-select button combination and flash Valeton's own firmware with the official editor.
