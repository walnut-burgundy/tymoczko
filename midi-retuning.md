# Programmatic MIDI retuning beyond 12-EDO

This note records the design idea prompted by Georg Vogel's 31-tone performance
practice and the existing written-note spelling work in this repository.

## Distinguish the tuning targets

"Quarter tone" can mean different things and should not be used as a single
implementation label.

### 24-EDO

Literal quarter-tone equal temperament divides the octave into 24 equal steps:

    step = 1200 / 24 = 50 cents

A converter can map ordinary MIDI pitches into this space mechanically, although
ordinary 12-note MIDI does not by itself tell us when an intermediate quarter tone
was intended.

### Quarter-comma meantone

Quarter-comma meantone is not 24-EDO. It narrows the fifth so that four tempered
fifths produce a pure 5:4 major third.

Its fifth is

    5^(1/4)

or about 696.578 cents.

### 31-EDO

31-EDO is a close circulating approximation to quarter-comma meantone:

    fifth:       18 × 1200/31 ≈ 696.774 cents
    major third: 10 × 1200/31 ≈ 387.097 cents

This is the most direct equal-tempered target for experiments inspired by
Vogel's 31-tone keyboard.

## Why ordinary MIDI is insufficient by itself

An ordinary MIDI note number has already collapsed enharmonic spelling.

For example, MIDI note 61 does not say whether the musical note is C♯ or D♭.

In 12-EDO that distinction does not change the physical pitch. In 31-EDO or a
meantone system, it can.

Therefore a musically serious retuner should not treat

    MIDI note number -> destination pitch

as the whole semantic mapping.

The more useful pipeline is

    MIDI events
    -> infer or preserve written-note spelling and harmonic role
    -> choose tuning-system pitch
    -> emit retuned MIDI events

This connects directly to issue #1, which treats written note spelling as musical
information rather than a display preference:

https://github.com/walnut-burgundy/tymoczko/issues/1

## Three useful converter modes

### 1. 24-EDO mechanical

Map source notes into a 24-step octave with an explicit fixed policy.

This is useful for quarter-tone experiments but does not recover harmonic
spelling lost by the source MIDI.

### 2. 31-EDO mechanical

Use a fixed table from the twelve source pitch classes into a chosen subset of
31-EDO.

This is deterministic and easy to test, but enharmonic distinctions remain lost.
For example, source pitch class 1 must be mapped globally to one chosen destination
even when one occurrence is functionally C♯ and another is D♭.

### 3. 31-EDO harmonic

Infer or preserve written-note identity first, then map that written note into the
31-tone system.

Example:

- in A major, source pitch class 1 is naturally C♯;
- in a context where the same equal-tempered pitch functions as D♭, choose D♭;
- C♯ and D♭ may then become physically different pitches.

This is the mode that most directly exercises Tymoczko's written-note semantics.

## MIDI output mechanisms

### Pitch bend

MIDI 1.0 pitch bend can retune a note away from its nominal semitone.

The important limitation is that pitch bend applies to a whole channel. Two
simultaneous notes that require different bends cannot share that channel.

A polyphonic retuner therefore needs dynamic channel allocation or an MPE-like
layout where independently tuned notes receive independent channels.

### MIDI Tuning Standard

MIDI Tuning Standard can describe alternate tuning tables directly. It is useful
when the synthesizer supports it and when a stable note-number-to-frequency table
is sufficient.

### MPE

MPE is useful for MIDI 1.0-style per-note pitch control because each note can
receive its own channel and pitch bend.

### MIDI 2.0

MIDI 2.0 offers cleaner per-note pitch control and should eventually be a natural
target, but it should not be required for the first implementation.

## First implementation boundary

The smallest useful executable experiment should not attempt full automatic
harmonic analysis immediately.

Start with:

1. parse a Standard MIDI File;
2. preserve timing, velocity, program changes, and non-pitched events;
3. ignore or pass percussion through unchanged;
4. accept an explicit tuning mode;
5. for mechanical modes, map pitches deterministically;
6. for harmonic mode, accept explicit spelling/harmonic annotations before trying
   to infer them;
7. allocate channels so simultaneous notes can carry independent bends;
8. emit a new MIDI file;
9. record the exact tuning policy and pitch-bend range in a receipt or sidecar
   text file.

Do not let the first MIDI encoding define the permanent music-theory interface.

## Later harmonic inference

Automatic harmonic retuning can be added as a separate layer:

    sounding MIDI
    -> local key/chord/voice-leading evidence
    -> candidate written spellings
    -> tuning-system pitches

The inference should be allowed to remain ambiguous. A global
"prefer sharps / prefer flats" switch is not an adequate substitute for harmonic
meaning.

Potential evidence includes:

- key or tonal center;
- chord root and chord quality;
- scale membership;
- voice leading from neighboring notes;
- bass function and inversions;
- preserved note spelling from MusicXML or another notation source when
  available.

MusicXML or another notation-bearing source may therefore be a better input than
MIDI when the goal is exact written-note identity.

## Acceptance examples

A useful first regression set should include:

- A-major triad: A C♯ E, not A D♭ E;
- A/C♯ slash chord: preserve C♯ as the bass spelling;
- a contrasting context where the same 12-EDO pitch class is functionally D♭;
- one sustained note whose required bend differs from another simultaneous note,
  proving independent channel allocation;
- a 31-step chromatic walk matching `examples/georg-vogel-youre-everything-31edo/31edo.scl`;
- a round-trip check that timing and non-pitch MIDI events remain intact.

## Open questions

- Which written-note representation should become the stable Idriç-facing
  contract?
- Should 31-EDO note placement be derived from an explicit chain-of-fifths
  spelling model, a lookup table generated from that model, or both?
- How should ambiguity be represented when MIDI alone cannot distinguish C♯ from
  D♭?
- Should a future converter accept MusicXML, MIDI, and chord-symbol sidecars as
  separate evidence sources?
- Which synthesizers on the maintained Android targets support pitch bend, MPE,
  or MIDI Tuning Standard well enough for physical acceptance?

The core semantic rule is:

    tuning follows musical identity;
    MIDI note number is only a transport representation.
