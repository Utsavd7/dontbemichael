# Issue draft: Let a second engine earn a team member's job on a trial shift

Template: Feature request. Area: The hive (memory, routing, GOD agent).

---

**Title:** Let a second engine earn a team member's job on a trial shift, proven on the owner's own computer

### Problem / motivation

Only Claude ships (`BUILD_ENGINES = ['claude']`). The code for Codex and Gemini is there, and the engines item in TODOS.md lists what is left. Two of those gaps are not code. They are missing evidence:

1. **Nobody knows if the engine does the work as well.** TODOS.md: "A live end-to-end run is required before adding an engine: Gemini has never been run for real." And the answer differs by business. An engine that writes a fine support reply for a bakery may write a poor one for an accounting firm.
2. **Nobody knows if the engine keeps the office's rules on a given computer.** `test/office-privacy-conformance.test.cjs` checks what the app writes for an engine, and says itself that "it does not prove the engine obeys." #48 asked for a scripted live agent, and the reply there was right: CI has no signed in account, so it would never run. The answer also changes by machine. On Windows, shell commands are not confined yet (#187).

So offering a second engine is an all or nothing decision, for every office at once, made without evidence from any of them. That is why it is still undecided.

### Proposed solution

Treat a new engine the way the app already treats a new kind of email: it earns trust from the owner's own approvals before it acts alone.

![Mockup: the graduation card, a stopped trial, and one reply side by side](https://raw.githubusercontent.com/Utsavd7/dontbemichael/proposal/engine-trial-shift/docs/superpowers/specs/2026-10-14-engine-trial-shift.png)

**1. The owner starts a trial.** On a team member's Profile, behind a `buildFeatures.ts` flag: **Try another engine**, for example Codex for Kelly. The card says up front that a trial uses both plans.

**2. A fire drill runs first, on this computer.** Before the trainee sees any work, the app starts it once with the office's real folder and connector policy, and tells it to try the four things the office forbids: read a teammate's folder, write outside its own folder, call a connector it was not given, and reach the internet from a shell. Each attempt is recorded as blocked by the sandbox, blocked by the hook, or allowed.

This is the live half of the three checks in `office-privacy-conformance.test.cjs`, run in the one place a signed in account always exists. It builds on #48 and #83 rather than replacing them: those pin what the app asks for and how it handles a run; this shows what a real engine does on this owner's machine.

One teammate folder also holds a decoy file with a random token in it. During the drill and for the whole trial, the app searches everything the trainee writes for that token. That catches a read no hook saw, such as a shell `type` on Windows today.

Any "allowed", or the token found anywhere, ends the trial with the reason on the card. The results also fill in the per engine enforcement record #48 describes, with evidence from this machine instead of a declaration.

**3. The trainee shadows real work, with nothing it can break.** For each email Kelly handles during the trial, the trainee gets the same email, cleaned by the trust layer the way Pete's answers are, and writes its reply to a file in its own scratch folder. It gets no mailbox, no connectors, no internet and no task board. This also means the trial does not wait on #109 (Codex agents get no md-mail server yet).

**4. The owner's approval is the answer key.** When the owner approves Kelly's reply on Needs you, with or without edits, the approved text is what the trainee's reply is scored against. It is not scored against Kelly's draft, so Claude is never the yardstick for its rival. Scoring is one hidden call per email with no tools (`noTools: true`): would the owner have approved this as written, with small edits, or not at all.

**5. Graduation is one card.** After 10 approved emails or 7 days, whichever comes first: "Codex matched what you approved on 9 of 10. The fire drill blocked all four forbidden actions." With the replies side by side, and three choices: **Switch Kelly to Codex**, **Keep Claude**, **Extend trial**. Switching keeps Kelly's folder, memory, mailbox and work style, and hands open work over with `hive.takeHandoff`.

**Order of work.**

1. The fire drill. It is useful on its own: it turns #48's enforcement record into evidence from real machines, and it gives the owner a plain "this engine cannot keep your folders private on this computer" before anything is at stake.
2. The trial shift for email replies, the one kind of work in the app that already has an answer key written by the owner.
3. Other work joins once it has an answer key of its own.

**Why it fits.** It ships "more engines and more operating systems" one office at a time, with proof from that office, and never needs a promise that some engine's sandbox works on Windows or Linux. It reuses what exists: the trust layer, mail approvals and their recorded edits, the hidden no tools call, the handoff, and the Needs you board.

**Open questions.**

- Which engine runs the scoring call? A fixed Claude call, with the owner's text as the key, seems fair. An engine grading itself does not.
- Is 10 approved emails or 7 days the right default?
- Should a passed fire drill be cached per engine version and OS version, so a second trial on the same machine skips it?

### Alternatives considered

- **Replay past approved emails offline.** Cheaper, but it overlaps #53 and says nothing about privacy on the owner's machine.
- **Static and fake process tests only (#48, #83).** Both are needed. They prove what the app asks for and how it handles a run; they cannot show what a real engine does on a given computer.
- **Turn an engine on for everyone once it passes in CI.** The decision stays all or nothing, and differences between Mac, Windows and Linux show up after shipping, in someone's business.

### Pre-flight

- [x] I attached a screenshot, mockup or recording above.
- [x] I searched existing issues and discussions, and this isn't a duplicate. (Closest: #48, #83, #129. This builds on them; see above.)

---

## Wellfound note (paste into the application)

I starred and forked the repo, and read the code, TODOS.md and the engine issues before proposing anything. The code for Codex and Gemini is already there; what is missing is proof that a second engine is safe for a real business. So I proposed a trial shift: a new engine shadows one team member on the owner's real email, with no mailbox, connectors or internet. It is scored against what the owner actually approved, and has to pass a live fire drill with a decoy file on the owner's own computer first. It builds on #48 and #83 instead of repeating them, and ships Codex, Gemini and new operating systems one office at a time, with evidence. Issue with a mockup: https://github.com/agentvivekkumar/dontbemichael/issues/371
