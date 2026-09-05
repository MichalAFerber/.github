# Contributing to the TGWAB estate

This file exists because most of the rules that keep this estate working are
currently written somewhere only Claude Code sessions read. If you are a human,
a different AI agent, or a future version of any of us, this is the short list —
and every rule below was earned by something breaking.

Read it before your first change. It is deliberately short.

---

## 1. Find out who is already working on it

**This is the rule most likely to waste your afternoon.** On 2026-09-02 three
tickets were each picked up by two or three contributors at once, and the
duplicate PRs reached *ready for review* before anyone noticed. On 2026-09-05
five PRs were invisible to two separate scoping passes on the same night.

Before you start:

```sh
gh pr list  -R <owner>/<repo> --state all --search "<term>"
gh issue list -R <owner>/<repo> --state all --search "<term>"
```

**`--state all` is not optional.** `gh pr list` defaults to open PRs and most
repos here sit at zero open, so the bare search returns nothing and reads as
*clear*. Measured on one ticket: the search alone returned **0**; the same
search with `--state all` returned **3**. The duplicate is usually already
merged — which is exactly why nobody noticed it.

**Put a searchable identifier in your PR body** (`Closes #N`), or the check has
nothing to find for the next person.

## 2. Resolve the owner; never assume it

The estate spans **two** GitHub accounts — `MichalAFerber/` and `TGWAB/` — and
repos migrate between them. GitHub redirects a transferred repo, so the old
path still resolves and **nothing fails loudly to tell you it moved**.

```sh
gh api repos/<owner>/<name> --jq .full_name    # prints the current truth
```

At least one repo exists only under `TGWAB/` and will never appear in a listing
of the other.

## 3. Open a draft. Do not merge your own work

Three hands, in order, and nobody skips a step:

| who | does |
| --- | --- |
| contributor | opens the PR **as a draft** |
| maintainer | reviews, then marks it ready for review |
| Michal | merges |

Draft is a *state*, not a convention — which is the point. A warning can be
forgotten; a draft cannot be merged.

## 4. Claims are part of the change

An inaccurate claim in a code comment, commit message, PR body, review, or
release note is a **defect in its own right**, not a style issue. This is
DEV-STANDARDS §15 "trap 9", and it is enforced in review.

That means: **if you write a number, say how you counted it.** On one night a
single question — "how many repos carry this rule?" — produced **11, 13, 14, 15,
16 and 17** from six sincere instruments, every one of them believed at the time.
A count without a method is not a weak finding; it is not a finding.

The same applies to citations. Open every `#N`, file path, and section reference
you cite and confirm it says what you claim. A migration header once cited an
issue about job-scheduling as the origin of a rule about delete cascades; it
survived a clearance because the reviewer verified the arithmetic and never
opened the citation.

## 5. An empty result is a claim about your instrument, not the world

Before believing any negative — *not found*, *clean*, *zero*, *nothing matches* —
run the same instrument against something you **know** matches, and say so.
Before believing a positive, prove it can produce a negative.

Real failures from this estate, all of which looked like clean answers:

- `gh api .../logs` returns a **ZIP**, so a log full of your search string greps
  to zero. Use `gh run view --log`.
- Firing many `gh` calls quickly hits a **secondary** rate limit; failed calls
  return empty and read as *"no results"*. Go serial; count failures separately
  from negatives.
- `path=` as a shell variable in **zsh** silently destroys `PATH`; every command
  afterwards returns "command not found" and every `grep -c` returns 0.
- A file that 404s because *your PR adds it* returns zero matches on the default
  branch. That is a 404, not a measurement.
- GitHub code search **does not index forks**, so any estate-wide count built on
  it silently omits them.

## 6. A file existing is not evidence it works

A workflow file is not a workflow: check it has ever run, and what its
conclusion was. A `Makefile` target is not a working build: run it. A green
check is not a deploy.

> Two premises in one task brief were false on exactly this — a CI config that
> "solved the dependencies" whose only run had failed, and packaging targets
> that "encoded real knowledge" and had never once succeeded.

**And merging is not deploying.** Several repos here reach production only via a
manual step. Say what you verified and what you did not.

## 7. If you are working on a local clone

Everything under `~/GitHub` is a **single checkout shared by several concurrent
sessions**. Moving `HEAD` silently redirects someone else's work — a
`git checkout` performed *to read a file* once landed another contributor's
commit on the wrong branch.

Read without touching the working tree:

```sh
git -C <repo> fetch -q origin
git -C <repo> show origin/main:path/to/file
```

To write, use a worktree, and **bind the `cd` to the `add`** so a failure cannot
leave you writing in the shared clone:

```sh
git worktree add "$WT" <branch> && cd "$WT" || exit 1
```

## 8. Things that cost real data

- **Confirm destructive operations twice**, stating the literal target with its
  object count — *and what cascades from it*. An approved one-row delete removed
  four; the three children were unread and therefore unrestorable.
- **Never commit secrets, and check history before making a repo public.**
  Publishing exposes every commit, not the current tree.
- **Never mint long-lived credentials.** Scope tokens to the task and delete
  them as the task's final step.
- **Monitor-only means monitor-only.** Several audits deliberately report
  without remediating. Do not convert a reporting job into an acting one because
  it seemed helpful.

## 9. Repo conventions

- Public + MIT is **Class A**; the class **MUST** appear in the README's first
  paragraph and match the `LICENSE`. A repo shipping MIT while its README calls
  itself internal is a contradiction that ships.
- Every Class A/B repo needs a row in [`REGISTRY.md`](./REGISTRY.md) — that file,
  not any local list, is the authority on what exists.
- `main` is PR-protected in most repos. Branch, push, open a PR.
- Match the repo you are in. Its conventions beat your defaults.

---

## The one-line version

**Say what you measured, how you measured it, and what you did not check.**
Almost every rule above is a specific instance of that, and the estate's most
common defect is not broken code — it is a check that reports success while
doing nothing.

Full standards: [`DEV-STANDARDS.md`](./DEV-STANDARDS.md). Product map:
[`REGISTRY.md`](./REGISTRY.md).
