# GP-200 Patcher

A self-contained, offline browser tool for applying reverse-engineered custom firmware mods to a [Valeton GP-200 or GP-200LT](https://valetonmusic.com/) multi-effects pedal.

Read the writeup: [gp200-reversing.hashnode.dev](https://gp200-reversing.hashnode.dev/)

## What this is

This repo holds only the built, self-contained patcher page, not the reverse-engineering tooling or source used to build the mods. See the [Releases](../../releases) page for downloads.

Each release is a single `.html` file. It runs entirely in your browser with no server and no network calls: open it directly from your downloads folder, point it at your own stock firmware image, and it verifies, patches, and lets you download the modified image client-side.

## What is in the current release

One page covers both pedals. The patcher works out which one you have from the firmware file you load, by its SHA-256, and then offers only the mods that exist for that pedal. It does not ask you, and it does not go by what the file is called.

Eight mods, every one of them optional, on both the **GP-200** and the **GP-200LT**:

- **Strobe tuner and footswitch LED indicators** - adds a strobe display mode on a footswitch, shows tuner state on the footswitch LEDs, and tightens the in-tune lock from +/-5 cents to +/-1.5 cents.
- **Carry-over tap tempo** - a Global Settings toggle. In Carry-over mode a tapped tempo follows you across patch changes instead of resetting. Per-patch stays the default and your patches are never rewritten.
- **Global output balance** - a cut-only left/right balance control on the main outputs, applied in the output path so USB recording is untouched.
- **Footswitch labels** - A/B/C/D show the name of the patch they switch to, and CTRL 1-8 show the effect blocks they toggle rather than "CTRL n".
- **Fast patch change** - shortens the fade-in after a patch change from 113 ms to 9 ms, so switching stops feeling like it lands late. It stays a ramp, so it still suppresses switching clicks.
- **Unlock all display languages** - restores the full eight-language list on units whose factory configuration restricts it. China-market units are the reported case. On an unrestricted unit it changes nothing you can see.
- **Portuguese (Brazil) display language** - replaces the pedal's built-in Spanish translation with reviewed Brazilian Portuguese, in place. No other language is affected.
- **Factory-reset cleanup for mod settings** - clears mod settings on a Global Settings or All Data reset, so a reset gives a clean slate. Included automatically with any mod that saves a setting.

**New for the GP-200LT.** Every release before this one was GP-200 only.

I do not own a GP-200LT. The LT builds were traced against Valeton's own LT firmware image and checked byte by byte before release, and they have been running on beta testers' LT units without a problem, but they have not had the hands-on testing on my own bench that the GP-200 builds have had. If you are an LT owner, keep the stock file you load into the patcher. Reflashing it puts the pedal back.

**GP-200 firmware V1.8.2 is supported.** Valeton's V1.8.2 update relinked the firmware, so a patch built for V1.8.0 would land on the wrong bytes. The page recognises both and builds the right one from the file you load. V1.8.2 is a GP-200 release; the GP-200LT has V1.8.0.

**Always use the newest release.** Releases before "Tuner mod v2" build a file that units with newer boot software refuse to flash, and they refuse it without showing any error. See step 1 under Troubleshooting.

## Troubleshooting

### The update never starts

You select the patched firmware file, the editor shows your GP-200 as connected, a progress bar appears for a moment and then disappears. Or the device stays in update mode forever.

Before anything else, check that Valeton's own firmware still updates your unit. Load Valeton's own unmodified firmware file in the updater and let it run. If that does not work either, the problem is with the updater or the connection rather than with the patched file, and that needs sorting out first. Step 2 below is the most common reason for that.

If Valeton's own firmware **does** load and only the patched file fails, start at step 1 and work through these in order.

**1. Check your Boot version. This is the most common cause.**

Press and hold **BACK** and **SAVE** together while powering up the unit. Part way through the boot splash screen it will enter the factory QC screen. One of the lines on it reads `Boot:`.

This screen is totally safe. To leave it, either press BACK and SAVE together again, or power cycle the unit.

If it says **V0.0.5 or newer**, use the newest patcher release. Anything from "Tuner mod v2" onwards is fine. Earlier releases changed a version label inside the firmware file, and units with newer boot software refuse an update when that label has been changed. They refuse it silently, so the editor shows no error and the pedal is left sitting on the update screen.

This is confirmed: several users who could not flash v1 have all flashed v2 successfully, on units reporting Boot V0.0.5 and V0.0.6. If you are on V0.0.5 or newer and you used an older release, this is almost certainly what happened, and v2 fixes it.

If it says **V0.0.3**, this is not your problem. Go on to the next step.

**2. If you are on Windows 11, check this first.**

Valeton's editor has a known problem on Windows 11. It can fail to update firmware, and in the worst case it leaves the pedal stuck on the update screen. This affects Valeton's own firmware too, so it is not caused by these mods, and it will stop any firmware from loading.

This particular problem is a Windows 11 one. If you are on Windows 10 or macOS, go on to the next step.

Valeton's answer is to swap a driver in Device Manager. **Be careful: the GP-200 appears there as more than one device, and only one of them should be changed.** The community write-up is more complete than the official instructions:

<https://www.facebook.com/groups/1080579552678581/permalink/2192962678106924/>

If your pedal is already stuck on the update screen, the most reliable fix is to borrow a Mac or a Windows 10 PC and run the update from there.

**3. Check which model you have.**

Look at the label on your unit.

These mods work on the **GP-200**, and that includes the **GP-200R** and **GP-200X**, which are the same pedal in different colours. They also work on the **GP-200LT**, which is new in this release.

They do **not** work on the **GP-200JR**. It is a separate product with its own firmware, and nothing here has ever been built for it or tested on one. The patcher will refuse a JR firmware image rather than guess.

You do not have to tell the patcher which pedal you have. It reads the file you load, matches it against the images it knows, and offers you that pedal's mods, so it cannot build you a GP-200 file out of an LT image or the other way round.

What the patcher cannot check is the pedal at the far end. The editor compares the model name in the firmware file against the unit plugged in, and a GP-200 file is refused by an LT exactly as an LT file is refused by a GP-200. That refusal looks the same as the problem at the top of this page. So make sure the stock file you started from is the one for the pedal you are actually flashing.

**4. Check the file name.**

The patcher names the file it builds after a fingerprint of that file's own contents. The last eight characters of the name are the first eight characters of the file's SHA-256, so the name is not decoration. It is a short checksum you can read at a glance, and step 5 is how you check the file against it.

Every mod you tick changes the contents, so every mod you tick changes those eight characters. **There is no one correct file name any more.** If your name does not match somebody else's, that only means the two of you picked a different set of mods.

Three names do mean something specific, and all three mean you should go back a step:

`GP-200_V1.8.0_7ea40d3e.bin` and `GP-200_V1.8.2_c3686187.bin` are unmodified GP-200 firmware, and `GP-200LT_V1.8.0_8f43322d.bin` is unmodified GP-200LT firmware. If you got any of those, you did not tick any mod, and what you built is an untouched copy of Valeton's file. Go back and tick at least one.

`GP-200_V1.8.0_f5afac84.bin` is the old v1 tuner build. If you got that, you used an old patcher release. That is the file described in step 1. Build it again with the newest release.

**Make sure you are flashing the `.bin` and not the `.zip`.** This is easy to get wrong. Windows hides file extensions by default, and the zip and the firmware inside it have the same name, so in Explorer they look almost identical, and their sizes round to the same figure.

What the patcher gives you is the `.zip`. You have to open it and take the `.bin` out first. In Properties, the one you want says **BIN File** next to Type, not Compressed (zipped) Folder.

The zip holds one other thing: a `manifest.json` recording which mods went in, the fingerprint of the stock file you started from, and the fingerprint of the file that came out. You do not need it to flash anything, but it is worth keeping, and it is the single most useful thing to attach if you ever report a problem.

Also compare the `.bin` and never the `.zip` if you are checking it against anyone else's. The zip records the time you built it, so no two people get the same zip even when the firmware inside is identical.

**5. Check the file is complete.**

There are two things to check, the size and the fingerprint, and the patcher gives you both.

**The size.** Right-click the `.bin` file, choose **Properties**, and look at **Size**.

A GP-200 file must be exactly **6,451,048 bytes** on V1.8.0 or **6,452,728 bytes** on V1.8.2, and a GP-200LT file must be exactly **6,560,436 bytes**. Windows shows the V1.8.0 GP-200 figure as `6.15 MB (6,451,048 bytes)`. Read the number in brackets, not the rounded one. The mods are patched into the firmware in place, so the size is the same whichever ones you tick.

**The fingerprint.** The size on its own is not enough. A file can be exactly the right length and still have damaged contents, and the updater may reject it without showing you any error at all.

When the patcher finishes a build it prints the full SHA-256 of the file it made, on the page, under the download button. That is the number to compare against, and the `.zip` records it too, in `manifest.json`. To work the fingerprint out from the file on your disk, run the command below for your system, putting in your own file name.

**On Windows**, open a command prompt and run:

```
certutil -hashfile "GP-200_V1.8.0_xxxxxxxx.bin" SHA256
```

**On macOS**, open Terminal, which is in Applications then Utilities, and run:

```
shasum -a 256 "GP-200_V1.8.0_xxxxxxxx.bin"
```

Rather than typing the file name, you can type `shasum -a 256 ` with a space after it and then drag the `.bin` file from Finder into the Terminal window. That fills in the full path for you and avoids typing mistakes.

The two commands lay their output out differently. `certutil` prints a line of its own above and below the fingerprint, and `shasum` prints the fingerprint followed by the file name on the same line. Neither matters. The only thing to compare is the long string of letters and numbers.

**If you no longer have the patcher's screen in front of you, you can still check the file against itself.** The last eight characters of the file's name are the first eight characters of its fingerprint, so a file that was damaged on its way to your disk will no longer match its own name. That catches a bad download on its own, without anything to compare against.

If the size or the fingerprint is different, the file was damaged on its way to your disk. Unzip it again from the `.zip` you downloaded, or build it again. If it keeps coming out wrong, an antivirus tool or a browser extension may be altering it.

**6. Build the file yourself. Do not use one someone sent you.**

If somebody passed you a finished `.bin`, set it aside and make your own with the patcher page.

The patcher checks your firmware before it changes anything, so building it yourself catches a damaged file, or one meant for a different model or a different firmware version, before it ever reaches your unit. A file handed to you in a forum post or a chat message has had none of those checks run, and there is no way to tell by looking at it what it was built from.

Always start from a fresh, unmodified copy of the stock firmware for your own pedal: V1.8.0 or V1.8.2 on the GP-200, V1.8.0 on the GP-200LT. Never patch a file that has already been patched.

### Details we want to know
- **a photo of the factory QC screen**, which is the most useful thing you can send us
- which operating system you are on, and if Windows, whether 10 or 11
- the model name from the label on the unit
- the patched .bin filename and size in bytes from Properties
- the fingerprint from the `certutil` command in step 5
- the editor version, from its About screen
- whether you built the file yourself or someone sent it to you

**A picture of the factory QC screen.** Press and hold **BACK** and **SAVE** together while powering up the unit. Part way through the boot splash screen it will enter the factory QC screen.

A photo of the whole screen is much more useful than typing the `Boot:` line out on its own. The screen reports several other version numbers alongside it, so it is better to have all of them than to find out later that we asked for the wrong one.

This screen is totally safe. To leave it, either press BACK and SAVE together again, or power cycle the unit.

### If the unit will not start up

Update mode lives in a part of the GP-200 that these mods never touch, so a failed or interrupted update can always be undone. Put the unit back into update mode with the boot-select button combination and flash Valeton's own firmware with the official editor.

**If Valeton's own firmware will not load either, and you are on Windows 11, the problem is your PC and not your pedal.** See step 2. Borrowing a Mac or a Windows 10 machine to finish the update is the most reliable way out, and it is what Valeton themselves recommend.

### Helping us investigate

The version-label problem described in step 1 is solved, so if that was your symptom, the fix is simply to use the newest release. What follows is for anyone whose unit still will not update after working through the steps above.

**Start with a photo of the factory QC screen.** Press and hold **BACK** and **SAVE** together while powering up the unit, and it will enter the QC screen part way through the boot splash. It is safe, and you leave it by pressing the same two buttons again or power cycling. That one picture takes a minute, needs nothing installed, and tells us more per minute of your time than anything else on this page.

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
