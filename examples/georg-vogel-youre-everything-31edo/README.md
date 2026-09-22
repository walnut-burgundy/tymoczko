# Georg Vogel / Chick Corea — “You’re Everything” in 31-EDO

This directory records the microtonal performance/transcription reference that
motivated adding a concrete 31-tone example to Tymoczko.

## Attribution and sources

- **Composition:** Chick Corea, “You’re Everything” (lyrics in the original song
  by Neville Potter).
- **Performance:** Georg Vogel, playing “You’re Everything” on the Basler
  Clavemusicum Omnitonum:
  https://www.youtube.com/watch?v=upbwQPeGm0Q
- **31-EDO transcription/reference:** Stephen Weigel, “You´re Everything -
  Georg Vogel / Clavemusicum Omnitonum (microtonal transcription)”:
  https://www.youtube.com/watch?v=kxYYD8QAnC4
- **Weigel's description of the tuning:** his 2025-01-22 post describes the
  source as Vogel playing the piece on a “31-tone equal tempered keyboard”:
  https://bsky.app/profile/stephenweigel.bsky.social/post/3lgdlkky5fc2i
- **Instrument/project context:** Georg Vogel / Projekt Studio 31:
  https://www.georgvogel.net/
  https://www.projektstudio31.com/

## Files

### `31edo.scl`

A Scala tuning generated here from the mathematical definition of 31 equal
divisions of the 2:1 octave. Each step is 1200/31 cents.

This file is not copied from Vogel or Weigel. It encodes literal 31-EDO. The
source above is evidence that Weigel analyzed/transcribed the performance as
31-tone equal temperament; it is not a claim that every tuning of the physical
Clavemusicum Omnitonum is fixed to 31-EDO.

### `31edo-reference.mid`

An original diagnostic MIDI generated for this repository. It plays all 31
steps from C4 to the octave C5 using ordinary MIDI notes plus pitch bend.
Pitch-bend sensitivity is explicitly set to ±2 semitones before playback.

This MIDI is **not** a transcription of Chick Corea's composition or Georg
Vogel's performance. It exists to make the tuning executable/audible with
ordinary MIDI machinery.

## Full song MIDI

No full MIDI transcription of “You’re Everything” is vendored here. A public
redistributable MIDI corresponding to Vogel's performance or Weigel's
transcription was not verified. Attribution alone would not establish
permission to redistribute a full transcription.

If an author-supplied or otherwise licensed MIDI becomes available, preserve it
verbatim and record its source, author, license/permission, and exact relation
to the Vogel performance before adding it.
