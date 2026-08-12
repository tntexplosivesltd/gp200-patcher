# GP-200 Patcher

A self-contained, offline browser tool for applying reverse-engineered custom
firmware mods to a [Valeton GP-200](https://valetonmusic.com/) multi-effects
pedal.

Read the writeup: [gp200-reversing.hashnode.dev](https://gp200-reversing.hashnode.dev/the-front-panel-and-a-tuner-that-actually-locks)

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

You select the patched firmware file, the editor shows your GP-200 as connected, a progress bar appears for a moment and then disappears. Or the device stays in update mode forever.

Before anything else, check that Valeton's own firmware still updates your unit. Load Valeton's unmodified V1.8.0 file in the updater and let it run. If that does not work either, the problem is with the updater or the connection rather than with the patched file, and that needs sorting out first. Step 1 below is the most common reason for that.

Then work through these in order.

**1. If you are on Windows 11, check this first.**

Valeton's editor has a known problem on Windows 11. It can fail to update firmware, and in the worst case it leaves the pedal stuck on the update screen. This affects Valeton's own firmware too, so it is not caused by these mods, and it will stop any firmware from loading.

Windows 10 and macOS are not affected.

Valeton's answer is to swap a driver in Device Manager. **Be careful: the GP-200 appears there as more than one device, and only one of them should be changed.** The community write-up is more complete than the official instructions:

<https://www.facebook.com/groups/1080579552678581/permalink/2192962678106924/>

If your pedal is already stuck on the update screen, the most reliable fix is to borrow a Mac or a Windows 10 PC and run the update from there.

**2. Check which model you have.**

Look at the label on your unit.

These mods work on the **GP-200**, and that includes the **GP-200R** and **GP-200X**, which are the same pedal in different colours.

They do **not** work on the **GP-200LT** or the **GP-200JR**. Those are separate products with their own firmware, and there is no way to make these mods fit them.

The editor reads the model name out of the firmware file and compares it against the unit you have plugged in. A file built for a GP-200 is refused by an LT or a JR, and that refusal looks exactly like the problem above.

**3. Check the file name.**

The patcher names the file it builds after a fingerprint of that file's own contents, so the name tells you what you ended up with. For the tuner release, it should be exactly:

```
GP-200_V1.8.0_f5afac84.bin
```

If yours ends in `7ea40d3e` instead, you did not tick the mod checkbox, and what you built is an unmodified copy of Valeton's firmware. Go back and tick it.

If the last eight characters are anything else at all, please tell us what they are. That would mean your computer built something we do not recognise, which is worth knowing about.

**Make sure you are flashing the `.bin` and not the `.zip`.** This is easy to get wrong. Windows hides file extensions by default, and the zip and the firmware inside it have the same name, so in Explorer they look almost identical. Even their sizes both round to 6.15 MB.

What you download is the `.zip`. You have to open it and take the `.bin` out first. In Properties, the one you want says **BIN File** next to Type, not Compressed (zipped) Folder, and its size in bytes is **6,451,048**, where the zip is 6,452,062.

Also compare the `.bin` and never the `.zip` if you are checking it against anyone else's. The zip records the time you built it, so no two people get the same zip even when the firmware inside is identical.

**4. Check the file is complete.**

Right-click the `.bin` file, choose **Properties**, and look at **Size**.

It must be exactly **6,451,048 bytes**. Windows shows this as `6.15 MB (6,451,048 bytes)`. Read the number in brackets, not the rounded one.

If it is any other number, the file was damaged while downloading or unzipping. Unzip it again from the `.zip` you downloaded, or build it again.

If you are comfortable with a command prompt, this prints the file's full fingerprint, which the patcher page also showed you when it built the file:

```
certutil -hashfile "patched_firmware_name_here.bin" SHA256
```

**5. Build the file yourself. Do not use one someone sent you.**

If somebody passed you a finished `.bin`, set it aside and make your own with the patcher page.

The patcher checks your firmware before it changes anything, so building it yourself catches a damaged file, or one meant for a different model or a different firmware version, before it ever reaches your unit. A file handed to you in a forum post or a chat message has had none of those checks run, and there is no way to tell by looking at it what it was built from.

Always start from a fresh, unmodified copy of GP-200 firmware V1.8.0. Never patch a file that has already been patched.

### Details we want to know
- which operating system you are on, and if Windows, whether 10 or 11
- the model name from the label on the unit
- the patched .bin filename and size in bytes from Properties
- the editor version, from its About screen
- whether you built the file yourself or someone sent it to you

### If the unit will not start up

Update mode lives in a part of the GP-200 that these mods never touch, so a failed or interrupted update can always be undone. Put the unit back into update mode with the boot-select button combination and flash Valeton's own firmware with the official editor.

**If Valeton's own firmware will not load either, and you are on Windows 11, the problem is your PC and not your pedal.** See step 1. Borrowing a Mac or a Windows 10 machine to finish the update is the most reliable way out, and it is what Valeton themselves recommend.
