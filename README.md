README for ssbdaemon
===

`ssbdaemon` is a tiny wrapper script that takes `cwdaemon` output and turns this
into `play` commands allowing you to use a cwdaemon capable logger to generate
voice-keyer audio.

It does this by launching cwdaemon in debug mode, without connecting to a radio
or sound card. The debug information from cwdaemon is captured by the wrapper
and converted into play commands.

The ESC interrupt transmit is also captured and used to kill the currently
running play command.


Installation
---

This wrapper is a simple bash script. Launch it on a terminal and it will stay
in the foreground until you terminate it with Ctrl-C.

It requires access to `cwdaemon`, which on a Debian system is in /sbin/

It requires access to `play`, to actually play an audio file, but it's simple to
use any audio play command.

The script will fail if cwdaemon is already running.

It uses the default cwdaemon port. The default arguments sent to cwdaemon are:
`--cwdevice=null --nofork -i --system=n`

If you supply any parameters, you must also supply `--nofork` and `-i`.

In my shack the audio follows the same path as is used for WSJT-X. The radio is
set to Digital VOX and sending audio will automatically set the radio to
transmit. You should be able to use an alternative TX mode if needed, either by
setting up your logger, or by enabling radio control in cwdaemon.


Audio
---

This voice-keyer requires a directory full of audio files. The files need to be
named in the same way that your logging tool sends commands to cwdaemon. Using
TLF, output of callsigns is in UPPER-case, but this might not be universal and
both UPPER-case and lower-case versions of the individual word files are usable.

The script doesn't care which audio format you use, as long as you can play it
from a command-line. The default extension is "aiff", but it's trivial to
change.

For every message that your logger sends to cwdaemon, the script checks to see
if there is a file with that name. If your logger sends out "VK6FLAB", the
script will attempt to play "VK6FLAB.aiff" - note that the file name must match
the case for a message file to play.

If that file doesn't exist, the script splits the message into individual
characters, if VK6FLAB.aiff doesn't exist, the script looks for: V.aiff, K.aiff,
6.aiff, F.aiff, L.aiff, A.aiff and B.aiff and plays those in order.

The file "A.aiff" needs to contain the audio for the phonetic word "Alpha",
"B.aiff" needs to contain "Bravo", etc.

The script checks for a file name match in the following order:
* "VK6FLAB.aiff"
* "V.aiff"
* "v.aiff"
* "K.aiff"
* "k.aiff"
* ...

The following characters are translated:
* `.` = decimal
* `/` = stroke
* `?` = query

You can record the audio yourself and process it using a tool like audacity. You
can use the MacOS `say` command and output the text to aiff like this:

    say "Hello World" -o "Hello World.aiff"

To create a file capable of tuning your radio, name it "tune.aiff". You can use 
the following command to generate a 10 second file with 1 kHz tone:

    sox -V -r 22050 -n -b 16 -c 2 tune-test.aiff synth 10 sin 1000 vol -1dB

**Tip**: Name the files for their phonetic content and make a symbolic link to
play them. For example:

    ln -s "Alpha.aiff" A.aiff
    ln -s "CQ Contest, Victor Kilo 6 Foxtrot Lima Alpha Bravo.aiff" "CQ.aiff"


Bugs
---

The following known issues exist:

* Only three of the features of cwdaemon are handled, request, tune and abort.
* WPM has no effect on the audio output.
* There is no volume control.


Credits
---

ssbdaemon was written by Onno (VK6FLAB) Benschop <cq@vk6flab.com> after 
discovering a distinct lack of Linux based voice-keyers that integrate with
logging software.

This script is a companion to Episode 333 of the "Foundations of Amateur Radio"
podcast titled: "How to run an SSB contest without using your voice...".

See https://podcasts.vk6flab.com/ for details.
