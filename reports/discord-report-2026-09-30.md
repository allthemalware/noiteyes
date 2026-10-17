<!-- Discord-ready: each section between the ===== lines is one message, under 2,000 characters. -->

**Noita Eye Messages: independent analysis, status report (2026-09-30)** [1/7]

**Short version:** still unsolved. Here's what we've established:
• Our transcription, re-derived from the pixels, matches the eye data embedded in `noita.exe` (Jan 2025 build) byte for byte.
• The **"period-4" signal is a duplication artifact.** It goes away when each repeated event is counted once.
• **Large-state models (S83 GAK, a per-letter deck shuffle) don't fit the resyncs.**
• The best-fitting model so far is a **group autokey over C83:C41 (affine maps) with small per-letter steps**. It reproduces most of the fingerprint.
• The key can't be recovered from the ciphertext alone, so we built a **crib tester**. **We need crib candidates** (see the last message).

Rule we worked by: nothing gets used until it has been reproduced from primary data. Every tool also has to pass synthetic "known-answer" gates before we read its verdict on the real data.

=====

**1. Data and provenance** [2/7]

• Eyes found by exact template match: 3,108 eyes, 1,036 letters, lengths 99/118/137/119/114 (east) and 103/102/124/120 (west).
• Of the 36 possible digit-significance orders, **exactly one** gives a contiguous 0–82 alphabet (83 letters). This is the standard reading.
• Pulled the eye data straight out of the current `noita.exe` (u64 mov-immediates, base-7 packed). That gives 3,194 characters, identical to our pixel transcription. There are **no other encoded runs**: no hidden tenth message, no west-5.
• A full sweep of `data.wak` (all 14,745 files: keyword sweep in English and Finnish, plus a pixel scan of 9,046 images for eye grids) found **no decoder, plaintext table or extra mural**. The game ships the ciphertext and nothing else.

**Confirmed structure**
• **0 adjacent doubles** where ~12 are expected. This is built into the mechanism.
• Position 0 differs in all 9 messages. Positions 1–2 are the same in all 9 (`66,5`).
• {east-1, east-2, west-1} share positions 1–24. {east-4, east-5, west-4} share positions 1–20.
• 4 **resyncs**: two messages diverge for 1–4 letters, then become identical again (e.g. east-1/west-1 realign for 13 letters at position 37).

=====

**2. What we've ruled out (each with a validated instrument)** [3/7]

• **Every additive / cyclic-group walk ("cut deck", incrementing wheel).** The isomorph equations can't be satisfied without merging letters. This holds for every prime modulus from 59 to 127, not just 83. It agrees with the community's C83 chaining result.
• **Collision-skip walks** ("step again if it would double"), at any skip density.
• **Re-keying locked to rows.** The L=33 isomorph east-5[33:66] ~ west-4[34:67] runs straight across a row-pair boundary in both copies.
• **Period-4 as an additive key index**, and a pure period-4 keystream.
• **A single progressive rotor** (σ = T^δ): fit 0/4, against 42/42 on the control.
• **Large-state autokey (S83 / A83 GAK).** A state that size should essentially never re-collide into a 13-letter identical run after a differing patch. The corpus does it 4 times.
• **K-state machines** (K = 8–30) and **hybrid registers**: their isomorphs come out as plain repeats, and walk dimension and resync are wrong.
• **Pupil geometry:** the σ maps don't preserve msd/mid/lsd digits any better than a shuffle does.

=====

**3. Period-4 is an artifact, and there are new isomorphs** [4/7]

The distance-4 excess (26 seen vs ~12 expected) was the community's main periodicity signal, and ours too. Most of those 26 hits are **copies of the same event**: family members share prefixes, and isomorph partners repeat each other. We merged identical positions and aligned isomorph positions with union-find, then counted each recurrence once. After that, d4 = 11 against 7.8 expected (p = 0.16), level with d5, d9 and d13. **There is no period.** What does survive is **near-repeat depletion**: d1 = 0, with d2 and d3 low.

(We also found and retracted a transcription error in our own notes. There was only ever one period-4 observable, not two.)

An exhaustive diagonal scan (every pair of messages at every offset) found 28 real isomorph windows. Shuffled and reversed controls give 0. **16 of the 28 are new**, including:
• east-4[68:102] ~ west-4[71:105] (L=34)
• east-5[66:99] ~ west-4[68:101] (L=33)
• **east-3 is now linked**: it ties to the family-B tail and to west-1 (east-3[86:109] ~ west-1[55:78]).

Alphabet count: the 9 web occurrences need **at least 8 distinct substitution alphabets**, with exactly one proven reuse, inside the east-1/west-1 resync region.

=====

**4. Leading model: gak41 with short steps** [5/7]

Of the six transitive groups in the community's GAK classification, **C83:C41** was the only one nobody had ruled out. It has 41 multipliers, so the state is small:
• state (b, a), affine update x → b·x + a mod 83, with b in the order-41 subgroup
• output c = D[a]; a ≠ 0 gives zero doubles structurally
• **per-letter additive steps are small (1..27)**, and **2–3 "twist" letters** change b

We scored it against the real fingerprint across five tournament rounds. The fingerprint covers long exact isomorphs, walk dimension 1, non-commuting σ, 0 doubles, d2/d3 depletion, resync rate and IoC. This model averages **~5.5/7** and is the first candidate to reproduce walk dimension 1 at all. The short steps are what produce the soft d2/d3 depletion.

What we learned about resync: with short 1–4-letter plaintext edits, like the real mismatch islands, even this lossless autokey realigns by coincidence often enough. So the 4 resyncs **don't prove** that the state update loses information. Resets and state-driven twists were tried, and both break the isomorphs.

It's still a model, not a solution. Its remaining misses are noise in the walk-dimension test and a resync rate that depends on the unknown plaintext.

=====

**5. Key recovery: where it stands** [6/7]

• **Affine fit on the isomorph web: inconclusive.** Once the two short excursions (east-1/west-1 at 25–28 and 33–36) are cut out, the web fits gak41, but with a null space of dimension 8. The web neither refutes the model nor pins the key.
• **Blind annealing for the hidden order π: fails its own gate.** The true key scores 0 and every reachable optimum scores ≥305. The basin around the truth is only ~5 transpositions wide, so the search ends in decoys.
• **Crib tester (new).** A guessed plaintext for a shared opening becomes exact GF(83) equations, which are solved together with the isomorph equations. On synthetic gak41 corpora:
  – **Sound**: 0 of 72 wrong cribs accepted, 0 of 12 true cribs rejected.
  – **When a true crib is accepted, the key comes out**: π recovered on 67–73 symbols. This is the first method in the project that reaches the key.
  – **Low power**: most runs hit the search cap. Longer cribs (~20 letters) work best. It costs ~72 min per crib on a 16-core machine.

On real data, an **accept** would be decisive: decrypt everything and read it. A **reject** rules the crib out. **Capped** means nothing.

=====

**6. Call for cribs** [7/7]

The tester needs guesses for the two shared openings. Position 0 is a per-message header outside the chain, so it isn't included:
• **A**: positions 1–24 shared by **east-1, east-2, west-1** (24 letters)
• **B**: positions 1–20 shared by **east-4, east-5, west-4** (20 letters)

**Format:** one per line, `A<TAB>phrase` or `B<TAB>phrase`. Letters only. Each phrase is tried both with and without spaces as symbols, and it's cut to 24 or 20 letters. **Finnish is welcome and maybe more likely.** Nolla Games is Finnish, and ä/ö are handled. Good sources: in-game text, lore, already-cracked Noita puzzles, and repeated boilerplate that fits the family structure. Opening A repeats 4 symbols internally, so a guess needs matching repeats in the same places. Those checks reject some guesses instantly.

Caveats: the tester assumes the gak41 short-step model, allows at most one twist letter inside the crib, and has low power. A capped run tells us nothing either way.

Happy to share tools and data. Every result above comes with its script and a gate that has to pass before the real-data verdict counts.
