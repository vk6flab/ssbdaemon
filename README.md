README for ssbdaemon
===

`ssbdaemon` is a tiny wrapper script that takes `cwdaemon` output and turns this
into `play` commands allowing you to use a cwdaemon capable logger to generate
voice-keyer audio.

It does this by launching cwdaemon in debug mode, without connecting to a radio,
sound card. The debug information from cwdaemon is captured by the wrapper and
converted into play commands.

The ESC interrupt transmit is also captured and used to kill the currently
running play command.


Installation
---

This wrapper is a simple bash script. Launch it on a terminal and it will stay
in the foreground, mostly silent - except for spurious output from the kill
command.

It requires access to `cwdaemon`, which on a Debian system is in /sbin/

It requires access to `play`, to actually play an audio file, but it's simple to
use any audio play command.

The script will fail if cwdaemon is already running as a daemon in the
background. It uses the default cwdaemon port, but you can update the parameters
on the last line of the script.

In my shack the audio follows the same path as is used for WSJT-X. The radio is
set to Digital VOX and sending audio will automatically set the radio to
transmit. You should be able to use an alternative TX mode if needed, either by
setting up your logger, or by enabling radio control in cwdaemon, again on the
last line of the script.


Audio
---

This voice-keyer requires a directory full of audio files. The files need to be
named in the same way that your logging tool sends commands to cwdaemon. Using
TLF, output of callsigns is in UPPER-case, but this might not be universal.

The script doesn't care which audio format you use, as long as you can play it
from a command-line. The default extension is "aiff", but it's trivial to
change.

For every message that your logger sends to cwdaemon, the script checks to see
if there is a file with that name. If your logger sends out "VK6FLAB", the script
will attempt to play "VK6FLAB.aiff"

If that file doesn't exist, the script splits the message into individual
characters, if VK6FLAB.aiff doesn't exist, the script looks for: V.aiff, K.aiff,
6.aiff, F.aiff, L.aiff, A.aiff and B.aiff and plays those in order.

The file "A.aiff" needs to contain the audio for the phonetic word "Alpha",
"B.aiff" needs to contain "Bravo", etc.

The following characters are translated:
* `.` = decimal
* `/` = stroke
* `?` = query

You can record the audio yourself and process it using a tool like audacity. For
my station You can use the MacOS `say` command and output the text to aiff like 
this:

    say "Hello World" -o "Hello World.aiff"

**Tip**: Name the files for their phonetic content and make a symbolic link to
play them. For example:

    ln -s "Alpha.aiff" A.aiff
    ln -s "CQ Contest, Victor Kilo 6 Foxtrot Lima Alpha Bravo.aiff" "CQ.aiff"


Bugs
---

The following known issues exist:

* Only two of the features of cwdaemon are handled, request and abort.
* The kill only stops the currently playing audio file, but not a sequence.
* The kill is noisy and echos to stdout.
* You cannot pass any arguments to cwdaemon.
* WPM has no effect on the audio output.
* There is no volume control.
* Each output line from cwdaemon is processed twice.


Credits
---

ssbdaemon was written by Onno Benschop <cq@vk6flab.com> after discovering a
distinct lack of Linux based voice-keyers that integrate with logging software.

This script is a companion to Episode 333 of the "Foundations of Amateur Radio"
podcast titled: "How to run an SSB contest without using your voice...".

See https://podcasts.vk6flab.com/ for details.
