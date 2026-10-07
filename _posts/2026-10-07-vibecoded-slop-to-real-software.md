---
layout: post
title: "From Vibecoded Slop to Real Software: The Engineering Stuff Nobody Told Me About"
date: 2026-10-07
categories: [Homelab, Networking, Infrastructure]
tags: [Mikrotik, MikroTikManager, AI, VibeCoding, OpenSource, SoftwareEngineering, Linting, Testing, CI, GitHubActions, Dependabot, Security, TypeScript, ClaudeCode]
description: "Six months ago I vibecoded MikroTik Manager and shipped it with zero tests. Here's everything I learned taking it from AI slop to a real open-source project: linting, tests, CI, AI push gates, dependency hygiene, security reporting, docs, PR review, and releases."
banner:
  image: https://img.youtube.com/vi/7kNS6horIyI/maxresdefault.jpg
  opacity: 0.618
---

![](//youtu.be/7kNS6horIyI)

I've got a running joke with my wife. She has a degree in software engineering and writes software for a living. Every time MikroTik Manager comes up, I tell her: "I don't know if you knew this, but I'm a software engineer. I'm on GitHub!"

She does not think it's funny. I can't imagine why.

Quick backstory. About six months ago I vibecoded a single-pane-of-glass management platform for MikroTik network gear, because MikroTik doesn't have one. I made a video about it, you all told me to "just release it," and I did. Then I walked downstairs, had lunch with my wife, and proudly told her all about it. She proceeded to school me, hard, on every way what I'd done was amateur, unsustainable, and poor software engineering.

She was right. I didn't know what linting was. Or unit tests. Or CI. I'd made a thing that ran in a Docker container on my machine and I'd shipped it out the door. And now strangers were about to point it at their routers.

So I learned. This post is what I learned, roughly in the order I'd do it if I were starting over, with enough detail that you can apply it to your own project.

## Where it started and where it is

| | First public release (v0.10.0-beta) | Today (v0.33.0-beta) |
| --- | --- | --- |
| Lines of code | 29,363 | about 102,800 |
| Automated tests | 0 | over 1,400 |
| Features | around 40 | over 140 |
| How you install it | Clone the repo and build it yourself | That, or prebuilt signed images for amd64 and arm64 |
| Issues opened | 0 | 127 (123 from people who aren't me) |
| Stars / forks | 0 / 0 | 178 / 36 |

My first release was vibecode slop. It isn't today. And here's the part I want you to take away: the way the code gets written didn't change. It's still AI-assisted. What changed is everything wrapped around the code.

One note before we get into it. My stack is TypeScript, Node, and React, hosted on GitHub, so that's what the examples use. Every single thing here has an equivalent in whatever you're building with, and I'll call out a few as we go.

## The mindset shift: "it runs" is not a test

When a vibecoded app only lives on your machine, a bug is an annoyance. The second somebody else installs it, a bug is their outage. In my case the software stores admin credentials for people's network gear, so a bug could be a lot worse than an outage.

The core problem with vibecoding is that "it runs" is the only test anything ever gets. And AI is extremely good at producing code that runs. Unused variables, risky patterns, sloppy error handling, all of it sails right through, because the app starts and the page loads.

Every practice below is the same idea wearing a different hat: get an answer to "is this actually correct?" from something other than the AI that wrote it.

## 1. Linting: let a machine nitpick the machine

A linter reads your code without running it and flags things that are legal but suspicious. This was the first thing I added, three days after the repo went public, and it's the cheapest win on this entire list.

For a TypeScript project:

```bash
npm install --save-dev eslint @eslint/js typescript-eslint eslint-plugin-security
```

And this is, more or less, my backend config:

```js
// eslint.config.mjs
import js from '@eslint/js';
import tseslint from 'typescript-eslint';
import security from 'eslint-plugin-security';

export default tseslint.config(
  { ignores: ['dist/**', 'node_modules/**'] },
  js.configs.recommended,
  ...tseslint.configs.recommended,
  security.configs.recommended,
  {
    files: ['src/**/*.ts'],
    rules: {
      '@typescript-eslint/no-explicit-any': 'warn',
      '@typescript-eslint/no-unused-vars': ['warn', { argsIgnorePattern: '^_' }],
    },
  }
);
```

That `eslint-plugin-security` line matters. It adds rules for things like injection risks, unsafe regular expressions, and insecure randomness, which are exactly the kind of thing AI will write without blinking.

Pair the linter with the type checker. `npx tsc --noEmit` checks every type in the project without building anything. In Python the equivalents are `ruff` for linting and `mypy` or `pyright` for types. In Go, it's `golangci-lint`.

Three things I'd tell you up front:

**The first run will be ugly.** It took me two cleanup commits the same day just to get everything passing. Don't try to fix it all at once. Start with the recommended rule sets, fix the errors, and leave the noisy stuff as warnings for now.

**Then start turning warnings into errors.** This one bit me. I had a function that was written, exported, and unit tested, but never actually wired into the component it was written for. The result was a broken port sort that shipped to users. The linter *did* warn about it. That warning was sitting in a pile of other warnings that nobody reads, and my checks only failed on errors. On the frontend, unused variables are now a hard error. A warning that doesn't stop anything is just decoration.

**Make the AI fix its own lint.** Once the linter exists, "run the linter and fix what it finds" becomes part of every change. The AI is genuinely good at this. It just won't do it unless something makes it.

## 2. Tests: start where being wrong hurts

A unit test is a tiny program that runs one piece of your code with known inputs and checks that the output is what it should be. That's it. The value isn't any one test. It's that you can run all of them in a few seconds after every change and find out you broke something *before* your users do.

That matters more with AI than without it. When you ask an AI to add a feature, it will cheerfully modify code three files away that you weren't thinking about. Tests are how you find out.

I went from zero tests to 47 the day after linting went in. Today it's over 1,400. But let me be honest about how that actually went, because the curve is the lesson. On September 1, almost five months in, the project had 30 test files. On October 1 it had 129. I half-assed testing for months and then had to do it in a sprint. Don't be me. Add them as you go.

Where to start when you have nothing:

- **Test the code where being wrong is expensive.** My first batch covered the crypto that protects stored credentials and the middleware that decides who's logged in and what they're allowed to do. If your app handles money, auth, or other people's data, start there.
- **Test your decision logic, not your plumbing.** The functions that take data in and return an answer are easy to test and where the real bugs hide. For me that's things like "will this config change cut off my own management access?" You don't need a router plugged in to test that. You need recorded example data and a function.
- **Every bug gets a test first.** When a bug comes in, have the AI write a test that reproduces it and *fails*. Then fix the bug and watch the test pass. Now that bug can never quietly come back.

And a few things to watch for when AI is writing your tests:

- **Tests that can't fail.** AI loves a test that mocks the thing it's testing and then confirms the mock returned what it was told to return. Ask yourself: if I broke the real code, would this test notice? Break it on purpose and see.
- **Tests edited to match the bug.** If a change makes a test fail, the AI has two ways to make it green: fix the code or change the test. It will sometimes pick the second one. A test file changing in the same commit as the code it covers deserves a look.
- **Chasing a coverage percentage.** I don't enforce one. A thousand tests on getters and setters will give you a great number and catch nothing.

## 3. CI: because "I ran it on my machine" doesn't scale

CI (continuous integration) is a clean computer that runs all of your checks every time code is pushed or someone opens a pull request. On GitHub it's free for public repos and it's just a YAML file. Here's the skeleton:

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

jobs:
  backend:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: backend
    steps:
      - uses: actions/checkout@v7
      - uses: actions/setup-node@v7
        with:
          node-version: 'lts/*'
          cache: 'npm'
          cache-dependency-path: backend/package-lock.json
      - run: npm ci
      - run: npm run lint
      - run: npx tsc --noEmit
      - run: npm test
      - run: npm run build
```

Lint, type-check, test, build. Same four things, every time, no exceptions, no "I'll run them later."

Two mistakes I made here that you can skip:

**A test that doesn't run automatically is a suggestion.** For months, my backend tests only ran on my own machine. CI ran lint, types, and the build, but not the tests for crypto, secrets, auth, and Change Guard. Which means `main` could have broken any of them and CI would have given me a green checkmark. If a check matters, it goes in CI.

**Don't publish what hasn't passed.** My image publishing used to kick off on every push to `main`, in parallel with CI. Think about what that means: a commit that failed its tests still got built and published as `:latest`, and anyone running the documented update command pulled it. Now publishing only starts after CI finishes successfully, on the exact commit CI tested.

Once CI exists, go to your repo settings, add a branch protection rule (or ruleset) for `main`, and require the CI checks to pass before anything merges. That's what turns CI from a report into a gate.

One more, and this one's specific: **check the things you never personally run.** I had a deployment file for the prebuilt images that was maintained separately from the one I use every day. It quietly drifted. It lost a port and a pile of settings, and I had no idea until somebody outside the project reported it, because nobody on my end ever used that path. That file is now generated from the main one, and CI fails if it's out of date.

## 4. Put a gate in front of the AI

This is the one that's specific to vibecoding, and it might be the highest-value thing on the list.

Telling an AI "always run the tests before you push" works right up until it doesn't. Instructions get forgotten in a long session. So don't make it an instruction. Make it a wall.

I have two small scripts in the repo. The first, `scripts/ci-preflight.sh`, runs every gate CI runs, locally, and reports everything that failed in one pass instead of failing on the first problem. The second is a hook for my AI coding tool that intercepts any attempt to run `git push`, runs the preflight, and refuses the push if anything fails. With Claude Code, that's a `PreToolUse` hook in `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "scripts/claude-push-gate.sh" }
        ]
      }
    ]
  }
}
```

When it blocks a push, it hands the failure output straight back to the AI with a message that says, basically: this would fail on GitHub too, fix these, run the preflight until it passes, then push. And the AI goes and does that, with no intervention from me.

If your tool doesn't support hooks, a plain git `pre-push` hook gets you most of the way there. Whatever your setup is, the principle is the same: the AI shouldn't be able to skip the checks, even by accident.

## 5. Dependencies: you shipped a lot of code you've never seen

Your vibecoded project is maybe 10% code your AI wrote and 90% open-source packages it pulled in. Every one of those is something you're now handing to your users. The last thing I'd ever want is to bring something nasty into someone else's network because of a package I didn't know I had.

Here's the wake-up call I got. The very first time I ran a dependency audit, three days after release, it flagged a critical issue in `axios` and four CVEs in `nodemailer`. That was in a brand new project. Nothing was old. That's just what was sitting in there.

What to set up:

**Dependabot.** Add a `.github/dependabot.yml` and GitHub will open pull requests when your dependencies have updates. Mine checks weekly:

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/backend"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 5

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

Don't forget the non-obvious ecosystems. I track npm for both frontend and backend, the Docker base images, the images in my compose file, and the GitHub Actions my workflows use. Your CI is a dependency too.

**Brace for the volume.** Of the 107 pull requests on my repo, the large majority were opened by Dependabot. That's normal. It's also the best argument for having tests: when a dependency bump comes in and CI goes green, you can merge it with some confidence instead of crossing your fingers.

**Don't auto-merge major versions blindly.** My Dependabot config ignores major version bumps for my databases, with the reason written right in the file. A newer major version of Postgres can't just open an older version's data directory. That's a planned upgrade with a migration, and merging that PR would have broken every existing install. Dependabot also has a `cooldown` option that waits a set number of days after a release before proposing it, which is cheap insurance against a freshly compromised package.

**Fail CI on real vulnerabilities.** I run `audit-ci` against production dependencies and fail the build on anything rated high or worse:

```yaml
- run: npx --yes audit-ci@^7 --high --skip-dev
```

Sometimes an advisory truly doesn't apply to how you use a package. You can allowlist it, but write down *why* in a comment right next to it. Future you will not remember.

**Commit your lockfile and use `npm ci`.** That guarantees CI and your users get the exact versions you tested, not whatever was newest that morning.

**Check that the packages the AI picked are real.** This one is unique to AI-written code. Models sometimes invent package names that sound right. One 2025 study found that 19.7% of the packages recommended by code-generating models didn't exist, and attackers have figured out they can register those made-up names and wait. It's called [slopsquatting](https://en.wikipedia.org/wiki/Slopsquatting). When the AI adds a dependency you've never heard of, spend thirty seconds looking at its registry page. Does it exist? Who publishes it? How old is it? Does anyone else use it?

## 6. Security: scan it, then make it easy for people to tell you you're wrong

**Turn on code scanning.** GitHub's CodeQL does static security analysis and is free for public repositories. Mine runs on every push, every pull request, and once a week on a schedule, using the `security-extended` query set. It finds real things. More than once, a new feature release got a same-day follow-up fix because CodeQL flagged something in the new code. That's the system working.

While you're in there, turn on secret scanning and push protection. AI will hardcode an API key into a file if that's the fastest way to make something work.

**Write a SECURITY.md.** This is a file in the root of your repo that tells people how to report a vulnerability privately instead of opening a public issue that announces it to the world. Mine covers:

- Which versions get security fixes (for me: the latest release only)
- How to report (GitHub's private vulnerability reporting, which you enable in your repo's security settings)
- What to include in a report
- What they can expect from me: an acknowledgement within 3 business days and a status update within 7

Then actually honor those numbers.

**When a report comes in, say thank you and fix it.** I've had two experiences that shaped how I think about this.

The first was a report of a role bypass, rated 9.6 out of 10 on the CVSS severity scale. That's about as bad as it gets, in software people were running on their networks. It got fixed, and then I did a full pass over that whole area of the code to make sure the same mistake wasn't hiding anywhere else.

The second was an independent code and security review by [Novus Insight](https://novusinsight.com), who run MikroTik throughout their environment. They came back with 12 serious findings, 36 bugs, and 54 hardening items. That's 102 things wrong with software I was pretty proud of.

Your gut reaction to a list like that is going to be defensive. Ignore your gut. Somebody just spent real time doing, for free, the thing companies pay a lot of money for. Every one of those findings is fixed, across 17 releases, and the project is dramatically better for it. That review is where session revocation, per-site access roles, certificate pinning, and signed images came from. They're credited in the SECURITY.md, because credit is the only currency an open-source project has.

**Be upfront that AI is involved.** My README and SECURITY.md both say plainly that this project is AI-assisted. People are going to have opinions about that. They're entitled to make an informed decision about what they run.

## 7. Docs: the README is not enough

At launch my documentation was one README. Today it's 23 pages covering everything from architecture to backups to SSO setup, published as a docs site that CI rebuilds automatically.

You don't need 23 pages on day one. You need four things:

1. **What it is and who it's for.** Two sentences at the top of the README.
2. **How to install it.** Copy-pasteable commands that work on a clean machine. Test them on a clean machine.
3. **How to configure and update it.** Every environment variable, what it does, and its default.
4. **What not to do with it.** Mine says, in bold, not to expose it directly to the internet.

Two things I didn't expect. First, docs cut down on issues, because half of "it's broken" is "I didn't know I had to set that." Second, docs make the AI better. An architecture doc that explains how the pieces fit together is context you can hand the AI at the start of a session so it stops reinventing things that already exist.

## 8. People: the part that actually made it good

Six days after I released the project, I got my first pull request. I'm a little ashamed to admit that I may not have technically understood what a PR was at the time.

So, for anyone in the same boat: a pull request is someone saying "I changed your code to fix or add something, here's exactly what I changed, do you want it?" You get to read it, test it, ask for changes, and decide.

Here's the routine I landed on for reviewing a PR from someone I don't know:

1. **Read the whole diff.** Every changed line. If it's too big to read, it's too big to merge, and it's fine to ask for it to be split up.
2. **Check what else changed.** New dependencies? Changes to workflow files, Dockerfiles, or install scripts? Those deserve extra scrutiny, because that's where a supply chain problem would hide.
3. **CI has to pass.** This is where everything above pays off. A stranger's code runs through the same lint, types, tests, and security checks as mine.
4. **Run it yourself.** Pull the branch and use the feature. For one early PR, I tested it against a clone of my real production database before merging.
5. **Ask for changes when you need them.** "Can you rework this part?" is a normal thing to say, and good contributors expect it.
6. **It's OK to say no.** Not every PR has been merged. Sometimes the idea is right and the approach doesn't fit, or it conflicts with something already in progress. Say thanks, explain why, and close it.

Feel free to use your AI as a second reviewer. It's good at "explain what this diff does and what could go wrong." Just don't let it be the only reviewer.

Issues and feature requests need the same care:

- **Respond quickly, even if the answer is "not yet."** Nothing kills a contributor's enthusiasm like silence.
- **Reference the issue number in the commit that fixes it** (`Fixes #123`). GitHub links them and closes the issue automatically, and anyone can trace a change back to why it was made.
- **Give ideas a place to live.** I opened a single "what features do you want?" discussion thread, and it pulled in more than fifty comments. A lot of what's in the product today started there.

This is the part I'm proudest of. People have sent in bug reports, feature ideas, code, security findings, and full independent reviews. The project is better than anything I could have made by myself, AI or no AI. It's also the reason I couldn't walk away from it. Once other people were putting in effort, I was hooked.

## 9. Releases: what "stable" actually promises

Up to now, every commit to `main` has been a release. In the last 31 days alone I pushed over 70 tagged versions. That's fine for a beta where everyone knows what they signed up for. It's not what "stable" means.

Here's the standard I'm moving to. Semantic versioning (semver) is `MAJOR.MINOR.PATCH`:

- **PATCH** (1.0.0 to 1.0.1): bug fixes only. Always safe to take.
- **MINOR** (1.0.1 to 1.1.0): new features, nothing existing breaks.
- **MAJOR** (1.1.0 to 2.0.0): something breaks, and you need to read the notes before upgrading.

The number is a promise to the people running your software. Which means before you call anything 1.0, you have to decide what you're promising not to break: your config and environment variables, your database upgrade path, your API. Write that list down.

Practical things worth doing before your own 1.0:

- **Tag every release** and write notes a human can follow: what changed, and whether they need to do anything.
- **Mark betas as pre-releases** so nobody lands on one by accident.
- **Stop doing all your work on `main`.** Build on a branch, merge when it's ready, and let `main` always be something you'd be comfortable having people run.
- **Test the upgrade, not just the install.** A fresh install working tells you nothing about what happens to someone's existing data.
- **Publish something people can verify.** My images are built by CI for both amd64 and arm64, cryptographically signed, and ship with a software bill of materials. If you're asking people to run your container on their network, they should be able to confirm it actually came from your repo.

## What I still haven't done

In the spirit of not pretending, there's a list:

- No issue templates, no pull request template, no code of conduct, and my contributing guidance is just a section of the README.
- No enforced test coverage threshold.
- My backend lint config still treats unused variables as a warning. Yes, after everything I said in section 1. I see it too.
- No proper 1.0 yet. It's close.

A real project isn't one with nothing left to fix. It's one where you know what the list is.

## If you only have a weekend

In order, and each one is useful even if you stop there:

1. Add a linter and a type checker. Fix the errors.
2. Write tests for the three scariest things your app does.
3. Add a CI workflow that runs lint, types, tests, and build on every push and PR.
4. Require CI to pass before merging to `main`.
5. Add a local preflight script and block your AI from pushing until it passes.
6. Turn on Dependabot, code scanning, secret scanning, and a dependency audit in CI.
7. Write a SECURITY.md and enable private vulnerability reporting.
8. Write install, configure, and update docs. Test them on a clean machine.
9. Tag your releases and write down what changed.

## So is it slop or not?

I know plenty of people will write this off as vibecoded slop no matter what I say, or consider it less legitimate than something written entirely by hand. I'm not sure I can change that, and for actual AI slop, I feel the same revulsion they do.

But here's where I've landed. The difference between vibecoded slop and AI-assisted software engineering isn't who typed the code. It's the effort that goes into testing, validation, repeatability, and sustainability. Slop is code nobody checked. Engineering is the checking.

To be clear, I'm still not a software engineer. My wife will happily confirm that. But you absolutely can take something from slop to a real, sustainable open-source project. It takes intention, some standard practices that real engineers have been using for decades, and effort.

If you're running MikroTik gear and want to try it, [MikroTik Manager](https://github.com/2GT-Media-Group-LLC/mikrotik-manager) is open source under AGPL-3.0 and completely free. The [documentation is here](https://2gt-media-group-llc.github.io/mikrotik-manager/). And if you find a bug or something it's missing, open an issue or send a PR. I know what those are now.
