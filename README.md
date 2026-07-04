# Pedagogical Notes — Claude Skill

A [Claude Code](https://claude.com/claude-code) skill that turns a derivation or
technical explanation into rigorous, lecture-style notes as a polished LaTeX
(`.tex`) file plus a verified PDF.

The intended reader is technically strong — a researcher or graduate student, not
a beginner. Its guiding principle: **every introduced quantity gets both a
definition and a motivation** (why is this the right thing to define?), because
that is where most "unclear" technical writing fails.

## When it triggers

Ask Claude to "derive X", "polish this into notes", "make a writeup", "create
lecture notes", or "write this up rigorously in a separate document" — anything
that turns a math / ML / physics / CS derivation into a structured `.tex`
document.

## Install

Drop the `pedagogical-notes/` folder into your Claude skills directory:

```bash
cd ~/.claude/skills
git clone https://github.com/yuxuanwang2009/pedagogical-notes-skill tmp-pn
mv tmp-pn/pedagogical-notes . && rm -rf tmp-pn
```

Claude Code discovers skills as plain folders under `~/.claude/skills/`, so once
`pedagogical-notes/SKILL.md` is in place it becomes available as
`/pedagogical-notes`.

## Contents

```
pedagogical-notes/
└── SKILL.md   # the skill definition (instructions + LaTeX conventions)
```
