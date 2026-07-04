---
name: pedagogical-notes
description: Write rigorous, technically dense lecture notes or derivations as polished LaTeX (.tex) files for technically strong readers (researchers, graduate students). Use whenever the user asks to "derive X", "polish into notes", "make a writeup", "create lecture notes", or wants a math/ML/physics/CS derivation turned into a .tex document. Use proactively when a derivation is substantial enough to warrant a structured document rather than an inline reply, or when the user requests a follow-up document building on a prior one. Trigger even if the user does not explicitly say "lecture" or "skill" — phrases like "in a separate document", "write this up rigorously", or "make this into a tex file" all qualify.
---

# Pedagogical Notes

Produces lecture-style technical notes in LaTeX. The reader is technically strong — a researcher or graduate student, not a beginner. Output is a `.tex` file plus a verified PDF, written to `/mnt/user-data/outputs/` and presented via `present_files`.

The single most important principle: every introduced quantity gets both a definition AND a motivation. Most "unclear" technical writing fails on this point alone.

---

## Definition AND motivation, every time

Before writing down any new symbol, derived expression, or quantity, do two things in order: motivate (why is this the right thing to define?) then define (write the equation). A reader who sees a definition with no motivation has to take it on faith. A reader who sees motivation followed by definition can re-derive the formula if they forget it.

Bad:
> The TD residual is $\delta_t = r_t + \gamma V_\phi(s_{t+1}) - V_\phi(s_t)$.

Good:
> The Bellman equation gives $A^\pi(s_t, a_t) = \mathbb{E}_{s_{t+1}}[r_t + \gamma V^\pi(s_{t+1}) - V^\pi(s_t)]$. Replace $V^\pi$ by a learned $V_\phi$ and the expectation by one sample to get the TD residual $\delta_t \coloneqq r_t + \gamma V_\phi(s_{t+1}) - V_\phi(s_t)$.

When a formula has several terms, briefly say what each one does. For $\hat A_t^{(k)} = -V_\phi(s_t) + \sum_{l=0}^{k-1} \gamma^l r_{t+l} + \gamma^k V_\phi(s_{t+k})$, follow with: "Three terms with clean interpretations. $-V_\phi(s_t)$ is the baseline subtraction; $\sum_l \gamma^l r_{t+l}$ is $k$ real reward samples; $\gamma^k V_\phi(s_{t+k})$ is the bootstrap." Underbraces under the equation work too.

---

## Derive, don't drop

Quantities that look ad hoc must be derived from already-established results. The reader should never see a definition that "comes from nowhere."

If a quantity comes from a known identity in the field (Bellman in RL, the path integral in physics, Itô's lemma in stochastic calculus, the chain rule, etc.), name the identity explicitly: "starting from [identity], substitute [thing], obtain [result]." The reader should be able to see which load-bearing fact is doing the work.

Spend the detail budget on the hard step, not the trivial ones. A derivation's job is to make the *non-obvious* transition checkable, and the common failure is inverted effort: spelling out trivial connective steps (distributing a product, relabeling a summation index, substituting a definition) while the one step that actually carries the result — a change of measure, an integration by parts, a non-obvious inequality, an exchange of limit and integral, the application of a specific identity — gets compressed into "after some algebra." For each derivation, identify the load-bearing step (the one a competent reader could not reproduce without thought) and show *that* step in full: name the identity or maneuver, write the intermediate line, state the condition that licenses it. Trivial steps can be combined or stated in words ("expanding and collecting terms gives"); the reader does not need every index relabel spelled out. The test is not *how many steps* but *can the reader reconstruct the one move that matters*. This complements the rule above: that governs where a *quantity* comes from; this governs how each *transition* between lines is justified.

Phrases that paper over the load-bearing step — "it is easy to see", "clearly", "after some algebra", "it follows that", "a straightforward calculation shows" — are banned when they stand in for the hard move. They are fine only for genuinely trivial transitions; the sin is using them to skip the step the reader actually needed.

If a quantity is a heuristic with no derivation, say THAT explicitly. Don't hide heuristics behind notation.

---

## Notation hygiene

Open with a Preliminaries section that defines the setup, the basic objects, and any prior results being built on. Even when "everyone knows" a basic object like $V^\pi$, define it. If the document continues an earlier one, restate inherited equations rather than only citing.

Two corollaries that are worth their own line:
- Every symbol introduced should be used; every symbol used should be defined. If you write $\theta \in \mathbb{R}^d$ and never reference $d$, drop it.
- Don't add qualifiers that aren't load-bearing ("bounded", "deterministic", "compactly supported"). If you don't use the bound, don't claim one.

---

## No forward references in remarks

If a remark refers to a concept that hasn't been introduced yet, it confuses the reader. Either move it to where the second concept is defined, or delete it. Symptoms: phrases like "as we'll see in PPO", "this contrasts with the off-policy bias below". Audit remarks for this pattern before finalizing.

---

## Respect existing structure when revising

When asked to revise an existing document (rather than write a new one from scratch), preserve its section structure unless the user asked to restructure it or there is a clear improvement worth flagging. Surgical fixes within sections are almost always preferable to silent reorganization. If you do restructure, say so explicitly so the user can audit.

---

## Honest about heuristics vs. principled methods

When a method has theoretical justification, state it. When it doesn't, state THAT. Do not hide weak justifications behind hand-waving phrases. Examples of common claims that need hedging:

- "The variance is reduced" — usually requires unstated covariance assumptions; "weakly reduces" or "removes zero-mean noise" is more honest.
- "The estimator is unbiased" — often holds only in a limit (large sample, exact model, on-policy data).
- "By [standard identity]" — many such identities hold only at a fixed point or under regularity that may not apply during training or away from the relevant limit.

The specific traps depend on the field. The principle is to flag, not paper over, conditions under which the claim fails.

When the derivation hides nontrivial conditions or failure modes, end the document with a Caveats section that flags them — this is where honest writing distinguishes itself from textbook propaganda, and without it such a document overclaims. The section is optional, not mandatory: if every limitation has already been flagged inline at the point it arises, or the result genuinely carries no hidden conditions, do not manufacture a Caveats section to fill the slot. When you do omit it, make sure the honesty lives somewhere — inline hedges, not silence.

---

## Connect new concepts to old ones

When introducing a generalization, state what specializes to what. "GAE with $\lambda = 0$ recovers the actor-critic advantage from the previous section" is more useful than presenting GAE as a separate construct. When introducing an alternative formulation, state the equivalence and prove or cite it.

---

## Figures

A figure earns its place when it shows something prose handles badly:

- **Spatial layout**: indices lining up across tensors, token alignment in causal LMs, memory layout, time-step indexing.
- **Graph structure**: autograd graphs, computational dependencies, data flow, control flow.
- **Function shape**: clipped surrogates, loss landscapes, decision boundaries, scaling regimes.
- **Symbol↔code correspondence**: which math variable lives where in the code.

A figure does not earn its place by restating something the prose already covers, or by being decorative.

Use TikZ (with `pgfplots` for plots) and keep the figure source in the same `.tex` file as the prose so the two cannot drift out of sync. Annotate aggressively: arrow labels, colored regions for the load-bearing parts, in-figure callouts naming array shapes or indices. The caption should let the figure stand alone — the body text should not have to recapitulate what the figure shows.

When revising a note after a discussion, ask whether the discussion produced a structural insight that a figure would carry better than added prose. Common cases where the answer is yes: graph topology, before/after tensor shapes, the geometry of a clipped objective, alignment between two indexed arrays.

---

## LaTeX conventions

Standard preamble:

```latex
\documentclass[11pt]{article}
\usepackage[margin=1.1in]{geometry}
\usepackage{amsmath, amssymb, amsthm}
\usepackage{mathtools}
\usepackage{microtype}
\usepackage{parskip}

\newtheorem{theorem}{Theorem}
\newtheorem{lemma}[theorem]{Lemma}
\newtheorem{proposition}[theorem]{Proposition}
\theoremstyle{remark}
\newtheorem*{remark}{Remark}
```

Add as needed: `algorithm` + `algpseudocode` for procedures. (Bold math uses `\boldsymbol{}`, already provided by `amsmath` — no `bm` needed.)

Math: inline in `$...$`, display in `equation` (numbered) or `\[...\]` (unnumbered). LaTeX commands for all symbols, never Unicode. `\boldsymbol{x}` for vectors. `\coloneqq` for definitional equality. Number any equation that gets referenced; use `\eqref{label}`.

**No custom macros — define none, consume none.** Write every symbol and operator in full, standard LaTeX (or loaded-package) commands at each use: `\operatorname{Tr}`, `\boldsymbol{x}`, `\mathrm{d}`, `\bar{\psi}`, `\langle f, g\rangle`, and so on. Do not introduce notation shortcuts via `\newcommand`, `\renewcommand`, `\def`, or `\DeclareMathOperator`, and do not rely on any such macro inherited from a prior document or a source you are transcribing — expand it to its standard form. (The `\newtheorem` declarations in the preamble are standard amsthm *environment* structure, not notation macros, and stay.) Rationale: these notes are routinely read or rendered as fragments — an equation pasted into Markdown, a chat, an email, or another file, *without* the preamble that defined the macro — where the macro is undefined and the math fails to render. Written-out commands keep every equation self-contained and portable.

Environments: `theorem` for named results (give them a name: `[REINFORCE]`); `lemma` for structural facts in service of theorems; `proposition` for significant-but-not-headline; `remark` sparingly and never for forward references.

Prose: no markdown italics; use `\emph{}` sparingly. Default to flowing prose; bullets only for genuinely list-like content. If the document has a Caveats section, put it at the end rather than strewn through the body.

User-stated preferences (math formatting, tone) take precedence over these defaults; flag and ask if there's a conflict.

---

## Workflow

1. Identify the results to derive and the chain of identities each rests on.
2. For each derivation, mark its load-bearing step(s) *before drafting*: walk the chain of transitions and tag each as trivial or load-bearing (the distinction is drawn in §"Derive, don't drop"). Keep this tagged list as working notes — it does not go in the document. This is a separate, deliberate pass: do not assume the load-bearing step will be obvious in hindsight during the final read — by then the draft already reflects whatever (possibly wrong) judgment was made implicitly. The tagging drives where the detail goes in the draft, and becomes the checklist the final pass verifies against.
3. Plan the structure: Preliminaries → main derivation → refinements → caveats (the last only if the derivation hides conditions not already flagged inline).
4. Draft the `.tex` file, showing each tagged load-bearing step in full (identity named, intermediate line written, licensing condition stated) and compressing the trivial ones.
5. Compile twice with `pdflatex -interaction=nonstopmode` (second pass resolves cross-references). Surface errors and unresolved references with `grep -E "^!|Warning|undefined|Rerun" <log>` — hard errors begin with `!`, but undefined `\eqref`s and "Rerun to get cross-references right" appear only as warnings.
6. Final pass. Read end to end:
    - Every step tagged load-bearing in step 2 is shown in full; no hard move is hidden behind "it follows that" / "after some algebra" while trivial steps are spelled out. (If the read surfaces a load-bearing step the tagging missed, that is a tagging error — fix the prose and flag the miss in the closing summary, step 8.)
    - Every symbol used has been defined; every defined symbol gets used.
    - No awkward or ungrammatical sentences. Read aloud where unsure.
    - No section was silently restructured during a revision.
    - No paragraph could be deleted without loss.
    - No custom macros are defined or consumed (`\newcommand`/`\renewcommand`/`\def`/`\DeclareMathOperator`); every symbol is written in standard LaTeX so the math renders without the preamble.
    - Every hedge ("in practice", "typically", "approximately") earns its place.
7. Copy `.tex` and `.pdf` to `/mnt/user-data/outputs/` and present via `present_files`.
8. In the chat response, briefly flag the substantive choices so the user can audit. If you restructured, say so.

---

## Revisions and follow-up documents

**Don't re-enact the discussion that produced the revision.** When a follow-up conversation surfaces a confusion or sharpens a definition, do not insert a "what X really means" paragraph or Q&A clarification at the spot where the confusion lived. That leaves the discussion's structure visible in the document and makes the prose reactive instead of declarative. Rewrite the original passage so the confusion is unlikely in the first place. The reader should encounter one author's coherent voice, not a transcript with a clarifying coda.

Marker phrases that reveal a re-enactment (treat as smells; rewrite or delete):

- "What X really means is..."
- "It might seem that..., but actually..."
- "You might wonder why..."
- "Why X at all? Because..."
- "To be precise..." / "More carefully..." prefacing a sentence that simply replaces an earlier imprecise one
- "Note that this is not the same as..."
- A new section or paragraph titled with a question the reader was about to ask

These are occasionally appropriate (genuine, well-documented pitfalls many readers actually hit), but the default is to state the right thing once, in the natural place. If the original prose contradicts what the discussion clarified, replace the prose; do not append corrections.

Workflow when revising after a discussion:

1. Distill the *content* the discussion produced (a precise definition, a missed nuance, a design tradeoff). Discard the conversational structure.
2. Find where that content belongs in the natural flow of the document — usually the same paragraph that was imprecise; sometimes the insight reshapes a section.
3. Edit in place as if you'd known from the start. Avoid append-only edits that leave the old framing intact.
4. Ask whether a figure carries the insight better (see the Figures section above). Structural insights — graph topology, alignment, geometry — often do.
5. Read the section cold. Does it read as one author's coherent exposition, or as a transcript with a clarifying coda? If the latter, revise again.

Common revision requests after the first draft:
- "X isn't motivated" → Add a derivation or motivation for X. Usually one or two sentences, not a half page.
- "You skipped a step" / "how did you get from here to here" → Identify the load-bearing move between the two lines and show it explicitly (name the identity, write the intermediate line); compress or cut the trivial padding around it rather than adding more.
- "Make it pedagogical" / "this is unclear" → Audit every introduced quantity for definition + motivation. Do a full pass.
- "This remark is confusing" → Usually a forward reference. Delete or move.
- "More concise" → Strip examples, not motivation.

Never respond to a clarity request with bloat. Clearer almost always means more precise, not longer.

When the user requests a follow-up document building on the first, reference the prior doc by name, restate inherited equations (don't assume the reader has the prior open), and keep notation and preamble consistent across the series.
