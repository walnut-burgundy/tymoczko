# Written notes for ChordReader

In E major, the third scale degree is G♯. A♭ can sound at the same equal-tempered
pitch and still say something different about the music. A♭ major similarly
retains A♭ as its tonic, and A/C♯ retains the written identity of its bass.
An explicit A/D♭ may be intentional. Reading a chart must preserve that choice.

This is the bounded contract requested by [issue1](https://github.com/walnut-burgundy/tymoczko/issues/1)
and [ChordReader issue #2](https://github.com/dilapidated-shed/tablature/issues/2).
The existing OPTIC module states which distinctions a quotient identifies; it
does not reconstruct written identity after that information has been forgotten.
No general harmonic-analysis or OPTIC completion is required to apply this contract.

## Distinct information

| Object | Information retained |
| --- | --- |
| Original chart notation | Source text, including spelling, whitespace, line endings and Unicode; save/export this text unless the user edits it |
| Written note | One letter A–G and a signed integral accidental; octave register is a separate extension when needed |
| Sounding pitch class | Equal-tempered semitone position modulo twelve; it can be derived from a written note, but does not recover that note |
| Written interval | Signed letter displacement and signed semitone displacement together |
| Declared key | A written tonic and an explicit mode; major and natural minor remain distinct |
| Harmonic function | An explicitly known scale degree or other justified role; a key declaration alone does not establish a function for every chromatic note |
| Display spelling | A written note derived from justified function/interval information, or the preserved source spelling when function is unknown |

The accidental is not bounded to single sharps/flats. Double accidentals and
further integral displacements have meanings without switching to a pitch-class
naming table. Chord roots, degree alterations and slash basses remain separate
parts of the chord object.

## Written transposition

Move the letter by the declared letter displacement. Choose the accidental
that gives the declared semitone displacement from the original written note.
Keep octave displacement while doing that arithmetic, even when the final
written-note value omits octave register. Transpose root and slash bass with
the same interval. Compose intervals by adding both displacements; negating
both gives the inverse.

For example, a written minor second moves A/C♯ to B♭/D. The letter motion
determines B and D; the pitch motion then determines their accidentals. It
does not first reduce C♯ to pitch class1 and choose a name from a global table.
The same interval moves an explicit E♭ to F♭, preserving its chromatic
relationship rather than silently turning it into an unrelated functional claim.

A semitone-only request does not uniquely determine a written interval.
ChordReader's incremental chromatic control has an explicit, reversible letter
motion policy. A user-declared source/destination key supplies a different,
fully specified written interval. Neither operation licenses rewriting the
saved chart's source notation.

## Keys and known scale degrees

A known degree determines a letter relative to the written tonic. The mode
determines its semitone distance; together these determine the accidental.
The bounded application contract covers major and natural minor:

| Context | Required written result |
| --- | --- |
| E major, degree3 | G♯ |
| A♭ major, degree1 | A♭ |
| A major, degrees1/3/5 | A C♯ E |
| A minor, degrees1/3/5 | A C E |
| F♯ minor, degree3 | A |
| Preserved source A/C♯ | A/C♯ |
| Preserved explicit source A/D♭ | A/D♭ |

Transposition between two declared keys derives both displacements from their
written tonics. The current uniform operation requires the modes to agree;
changing major to minor would require changing intervals within the harmony,
so the application refuses it rather than pretending it is uniform transposition.
Harmonic and melodic minor, modulations inferred from audio, and automatic
functional analysis are outside this bounded contract.

When only sounding pitch is supplied, spelling remains undetermined until
additional evidence is supplied. When source spelling exists and function
cannot be established, preserve it. A declared major key must not erase every
chromatic accidental in the source.

## Consumer implementation and evidence

The one executable chord-chart implementation remains in
[dilapidated-shed/tablature PR #7 — Run ChordReader music, charts and reader state in checked managed Idriç](https://github.com/dilapidated-shed/tablature/pull/7).
Its Music.idric defines written notes/intervals and parser objects; Key.idric
implements declared major/minor keys and degree spelling; Chart.idric and
Reader.idric preserve source and persistence. The Android UI consumes these
checked operations instead of implementing a second music-theory model.

At producer2051c4f8abcd29125dec236e66a328b9a70c2ad2,
[run41336558642](https://github.com/dilapidated-shed/tablature/actions/runs/41336558642)
passes 387 checked host cases, 1,049 retained musical comparisons and 2,142
declared-key comparisons. At producer50046f7336123392790c6f0082cbc066b23312e2,
[run41338214188](https://github.com/dilapidated-shed/tablature/actions/runs/41338214188)
executes 40 assertions against the actual managed DEX on ART and rejects the
compiled written-interval mutation. Its installed UI subsequently fails an
accessibility-idle observation during auto-scroll; the later repaired candidate
must earn its own UI result. These are consumer results, not tests of a new
Tymoczko spelling engine. Physical TAB_P10 acceptance remains blocked by access.
