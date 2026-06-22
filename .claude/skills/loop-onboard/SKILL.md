---
name: loop-onboard
description: One-time setup interview for The Loop. Run this first, right after cloning the repo. Interviews you about your business, your meeting types, and what you want out of each call, then writes your loop.config.yaml and scaffolds your data folders. Use when the user says "/loop-onboard", "set up the loop", "onboard me", "configure the loop", or runs the project for the first time and no loop.config.yaml exists.
---

# The Loop — Onboarding

This is the first-run setup. It interviews the user and generates their personal
`loop.config.yaml` so the ingest engine knows how to process *their* conversations.

## Procedure
1. Check whether `loop.config.yaml` already exists at the repo root.
   - If it exists, ask the user whether they want to **re-onboard (overwrite)** or **edit specific sections**.
2. Read `engine/onboarding-workflow.md` and follow it exactly.
3. The workflow ends by writing `loop.config.yaml`, scaffolding the `loop/` data folders, seeding a
   starter voice file, and printing a "you're ready" message.

## Repo root
The directory containing this repo (where `loop.config.example.yaml` lives).
