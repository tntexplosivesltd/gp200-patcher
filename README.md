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

**Always use the newest release.** Releases before "Tuner mod v2" build a file
that units with newer boot software refuse to flash, and they refuse it without
showing any error. See step 1 under Troubleshooting.

## Troubleshooting

### The update never starts

You select the patched firmware file, the editor shows your GP-200 as connected, a progress bar appears for a moment and then disappears. Or the device stays in update mode forever.

Before anything else, check that Valeton's own firmware still updates your unit. Load Valeton's unmodified V1.8.0 file in the updater and let it run. If that does not work either, the problem is with the updater or the connection rather than with the patched file, and that needs sorting out first. Step 2 below is the most common reason for that.

If Valeton's own firmware **does** load and only the patched file fails, start at step 1 and work through these in order.

**1. Check your Boot version. This is the most common cause.**

Press and hold **BACK** and **SAVE** together while powering up the unit. Part way through the boot splash screen it will enter the factory QC screen. One of the lines on it reads `Boot:`.

If it says **V0.0.5 or newer**, use the newest patcher release, "Tuner mod v2" or later. Earlier releases changed a version label inside the firmware file, and units with newer boot software appear to refuse an update when that label has been changed. They refuse it silently, so the editor shows no error and the pedal is left sitting on the update screen.

If it says **V0.0.3**, this is not your problem. Go on to the next step.

This screen is totally safe. To leave it, either press BACK and SAVE together again, or power cycle the unit.

**2. If you are on Windows 11, check this first.**

Valeton's editor has a known problem on Windows 11. It can fail to update firmware, and in the worst case it leaves the pedal stuck on the update screen. This affects Valeton's own firmware too, so it is not caused by these mods, and it will stop any firmware from loading.

This particular problem is a Windows 11 one. If you are on Windows 10 or macOS, go on to the next step.

Valeton's answer is to swap a driver in Device Manager. **Be careful: the GP-200 appears there as more than one device, and only one of them should be changed.** The community write-up is more complete than the official instructions:

<https://www.facebook.com/groups/1080579552678581/permalink/2192962678106924/>

If your pedal is already stuck on the update screen, the most reliable fix is to borrow a Mac or a Windows 10 PC and run the update from there.

**3. Check which model you have.**

Look at the label on your unit.

These mods work on the **GP-200**, and that includes the **GP-200R** and **GP-200X**, which are the same pedal in different colours.

They do **not** work on the **GP-200LT** or the **GP-200JR**. Those are separate products with their own firmware, and there is no way to make these mods fit them.

The editor reads the model name out of the firmware file and compares it against the unit you have plugged in. A file built for a GP-200 is refused by an LT or a JR, and that refusal looks exactly like the problem above.

**4. Check the file name.**

The patcher names the file it builds after a fingerprint of that file's own contents, so the name tells you what you ended up with. For the tuner release, it should be exactly:

```
GP-200_V1.8.0_414aa9a2.bin
```

If yours ends in `f5afac84`, you built it with an older patcher release. That is the file described in step 1. Build it again with the newest release.

If yours ends in `7ea40d3e` instead, you did not tick the mod checkbox, and what you built is an unmodified copy of Valeton's firmware. Go back and tick it.

If the last eight characters are anything else at all, please tell us what they are. That would mean your computer built something we do not recognise, which is worth knowing about.

**Make sure you are flashing the `.bin` and not the `.zip`.** This is easy to get wrong. Windows hides file extensions by default, and the zip and the firmware inside it have the same name, so in Explorer they look almost identical. Even their sizes both round to 6.15 MB.

What you download is the `.zip`. You have to open it and take the `.bin` out first. In Properties, the one you want says **BIN File** next to Type, not Compressed (zipped) Folder, and its size in bytes is **6,451,048**, where the zip is 6,452,062.

Also compare the `.bin` and never the `.zip` if you are checking it against anyone else's. The zip records the time you built it, so no two people get the same zip even when the firmware inside is identical.

**5. Check the file is complete.**

Right-click the `.bin` file, choose **Properties**, and look at **Size**.

It must be exactly **6,451,048 bytes**. Windows shows this as `6.15 MB (6,451,048 bytes)`. Read the number in brackets, not the rounded one.

**The size on its own is not enough.** A file can be exactly the right length and still have damaged contents, and the updater may reject it without showing you any error at all. To check every byte, open a command prompt and run:

```
certutil -hashfile "GP-200_V1.8.0_414aa9a2.bin" SHA256
```

For the tuner release it must print exactly:

```
414aa9a2d29599138a2c74a3d1851332071a046c81a0477908f7b820f23b43b2
```

If the size or the fingerprint is different, the file was damaged on its way to your disk. Unzip it again from the `.zip` you downloaded, or build it again. If it keeps coming out wrong, an antivirus tool or a browser extension may be altering it.

**6. Build the file yourself. Do not use one someone sent you.**

If somebody passed you a finished `.bin`, set it aside and make your own with the patcher page.

The patcher checks your firmware before it changes anything, so building it yourself catches a damaged file, or one meant for a different model or a different firmware version, before it ever reaches your unit. A file handed to you in a forum post or a chat message has had none of those checks run, and there is no way to tell by looking at it what it was built from.

Always start from a fresh, unmodified copy of GP-200 firmware V1.8.0. Never patch a file that has already been patched.

### Details we want to know
- **a photo of the factory QC screen**, which is the most useful thing you can send us
- which operating system you are on, and if Windows, whether 10 or 11
- the model name from the label on the unit
- the patched .bin filename and size in bytes from Properties
- the fingerprint from the `certutil` command in step 5
- the editor version, from its About screen
- whether you built the file yourself or someone sent it to you

**A picture of the factory QC screen.** Press and hold **BACK** and **SAVE** together while powering up the unit. Part way through the boot splash screen it will enter the factory QC screen.

A photo of the whole screen is much more useful than typing the `Boot:` line out on its own. The screen reports several other version numbers alongside it, and we are still working out which of them matter, so it is better to have all of them than to find out later that we asked for the wrong one.

This screen is totally safe. To leave it, either press BACK and SAVE together again, or power cycle the unit.

**This is worth sending even if the mod worked perfectly for you.** So far every QC screen we have has come from someone it failed for, which makes it hard to tell whether the pattern we think we are seeing is real. A screen from a unit that updated with no trouble at all is genuinely one of the most useful things anyone could send us right now.

### If the unit will not start up

Update mode lives in a part of the GP-200 that these mods never touch, so a failed or interrupted update can always be undone. Put the unit back into update mode with the boot-select button combination and flash Valeton's own firmware with the official editor.

**If Valeton's own firmware will not load either, and you are on Windows 11, the problem is your PC and not your pedal.** See step 2. Borrowing a Mac or a Windows 10 machine to finish the update is the most reliable way out, and it is what Valeton themselves recommend.

### Helping us investigate

**Start with a photo of the factory QC screen.** Press and hold **BACK** and **SAVE** together while powering up the unit, and it will enter the QC screen part way through the boot splash. It is safe, and you leave it by pressing the same two buttons again or power cycling. That one picture takes a minute, needs nothing installed, and tells us more per minute of your time than anything else on this page. Please send one even if your update worked perfectly, because so far almost every screen we have has come from a unit that failed, and that makes the pattern much harder to read.

Open an issue at <https://github.com/tntexplosivesltd/gp200-patcher/issues> and attach it there. Unlike the captures below, the QC screen is fine to post publicly. It reports version numbers and live readings from the controls, and nothing that identifies you or your unit.

The lines we are most interested in are `HW`, `HW INFO`, `FW` and `BOOT`, on the left hand side. A photo of the whole screen is still better than those four typed out, because it keeps everything else in view.

If you are willing to go further, a recording of the USB traffic between the editor and the pedal is the most detailed thing you could send us. It takes a bit of setup, so it is worth doing only if your unit still will not update after the steps above.

What is most useful is **two recordings, made one after the other in the same sitting**: first the failed attempt with the patched file, then a normal update with Valeton's own file. The pair is the point. On its own, the failed recording shows where things stop, but not whether the working one would have behaved any differently at that moment.

1. Install Wireshark from <https://www.wireshark.org>, making sure the **USBPcap** component is ticked. It will ask to reboot.
2. Open Wireshark. Before you start recording, open the settings for the USBPcap interface and **tick only the GP-200**. Left alone, USBPcap records every device on the same USB hub, which could include your keyboard. Only the pedal is wanted here.
3. Record the patched-file attempt, starting before you press Update and continuing until it is clear that nothing is going to happen. Save it as `modded.pcapng`.
4. Then record a normal update with Valeton's own firmware the same way. Once the progress bar is clearly moving, you can stop recording after about twenty seconds. Stopping the recording does not interrupt the update, so **let the update itself finish**. Save it as `stock.pcapng`.
5. Zip both files. They compress to roughly a tenth of their size. If the zip is still large, ten seconds of the working update is plenty.
6. Open an issue at <https://github.com/tntexplosivesltd/gp200-patcher/issues> and say you have captures. **Please do not attach them to the issue.** We will reply there with somewhere private to send them.

Never power off or unplug the pedal during an update.

Captures are raw data and hard to check over before it is out in the open, which is why they are not posted publicly. They will be deleted once the question is answered.
