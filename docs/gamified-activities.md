# Gamified next-generation activities

**Status: direction draft.** This records the current thinking from a design discussion. It is not an approved product plan, and the learner audience is still an open question (see below).

## Intent

Make learning English more fun so learners put in more practice, more often. Modernizing the framework is a means, not the goal. A new generation of game-like activities is the strongest reason found so far to consider Flutter, but it does not require Flutter: the cheapest way to try it is inside the existing lesson engine.

## What was considered

- **React Native is good enough for the current app.** It was recently upgraded, the team works in TypeScript, and lessons run as web content inside a WebView. Switching framework alone would not change the learning experience.
- **Rebuilding the existing lesson engine natively is expensive.** The current engine covers dozens of activity types, several study strategies, progress, recovery, and speech services, and it also powers the browser experience. Reaching parity in a new framework would mean months of work and two engines that must score identically.
- **AI does not decide the framework.** LLM calls, realtime voice, and scoring belong on the server; the client captures audio, streams, and renders. Flutter has first-party Google tooling (Gemini via Firebase, an experimental generative UI SDK); the JS/TS ecosystem is larger and keeps one language across the stack.
- **Gamification has two layers.**
  1. *Around lessons:* streaks, XP, levels, quests, rewards, celebrations. Mostly backend and app shell, works with existing activities, and either framework handles it.
  2. *Inside activities:* timed rounds, physics drag and drop, word games, animated characters. Custom-rendered, animation-heavy UI is where Flutter is strongest (everything is drawn by Flutter, with Flame for 2D game loops and Rive for interactive animation, from one codebase for mobile and web, though web rendering, input, and load time differ and need their own testing). React Native can do this with Skia and Reanimated, with more assembly and a weaker browser story. Web canvas and WebGL can do it inside the existing lesson engine with no framework change at all.

## Two separate decisions

1. **Should we build game-like activities?** A product and learning-design question. It can be answered without choosing a framework.
2. **What should they be built with?** A technology question. It only matters once the first answer is yes, and the spike must say which of the two it is testing.

## Proposed direction

- **Legacy activities** stay in the existing web-based engine, embedded in a WebView, unchanged.
- **New-generation activities** are designed from scratch as game-like experiences. The delivery technology is undecided; the candidates are below.
- **A shared progress and scoring contract** lets both report the same way. New activities do not need to replicate legacy behavior, only report results through the contract.
- **Gamification around lessons** (XP, streaks, quests) is built once in the shell and rewards both legacy and new activities.

## Candidate paths for new activities

Ordered from cheapest to most expensive.

| Path | What changes | Runs in | Cost and risk |
|------|--------------|---------|---------------|
| **A. Canvas or WebGL inside the existing lesson engine** | A new activity type rendered with HTML canvas or WebGL (for example PixiJS or Phaser), shipped through the same WebView and browser bundle as today. | Mobile WebView and browser, immediately. | Cheapest. Reuses the existing CMS, progress, and recovery. Risk: WebView performance and audio latency on older phones; game feel is bounded by the WebView. |
| **B. Native activities inside the current app** | New activities built in React Native with Skia and Reanimated, or as a Flutter module embedded with add-to-app. The app shell stays as it is. | Mobile only, unless rebuilt separately for the browser. | Moderate. No shell rewrite. Add-to-app adds a second toolchain and larger binaries; RN Skia keeps one language. |
| **C. New Flutter app shell plus WebView for legacy** | The whole app (navigation, auth, course list, settings, offline) is rewritten in Flutter; legacy lessons stay in a WebView; new activities are Flutter widgets. | Mobile and browser from one codebase. | Most expensive. Rewrites most of the app before any new activity exists. Only justified if a shared mobile and browser app is itself a goal. |

Path A should be tried first unless it is shown to fall short. It answers decision 1 without committing to decision 2, and anything learned about activity design carries over to B or C.

## Experience sketch (a guess, to react to)

A learner opens the app and a short daily quest is waiting: three or four bite-size activities, about five minutes in total.

- A timed listening or reading round where the learner catches the right word.
- A sentence puzzle where word tiles snap together.
- A speaking challenge where a character reacts to what was said (later; needs a server-side speech service).
- Instant feedback with sound, animation, and combo streaks for consecutive correct answers.
- Visible progress: XP, levels, streak calendar. Later, a topic map or a character that grows.
- Possibly a light social layer such as team leaderboards or friendly challenges.
- Later, AI-driven review of words the learner keeps missing.

## Design principles

- **Reward learning, not tapping.** XP and combos follow correct retrieval and real speaking attempts. Fast guessing must not pay better than thinking.
- **Short and finishable.** A session should be completable in minutes and end on a clear win.
- **Motivating, not punishing.** No guilt mechanics, shame notifications, or streaks that wipe out progress after one missed day; prefer streak freezes and gentle recovery.
- **Accessible by default.** Timers can be relaxed or switched off, motion respects reduced-motion settings, everything works with keyboard and screen reader, and nothing relies on color alone.
- **Tone fits the audience.** Characters and playful rewards suit some learners and feel childish to others. Decide after the audience question is answered.

## Anti-goals

- A slot machine wrapper around unchanged drills.
- Engagement metrics going up while learning outcomes stay flat.
- Features that only work on the fastest phones.

## Spike: gamified activity prototype

A throwaway prototype under `spikes/gamified_activities/`, using synthetic content only. Which path it tests (A, B, or C above) is decided before it starts, and the same activities are specified so a later comparison is fair.

- **Quest flow:** start screen, a run of activities, and a results screen with XP, accuracy, and best combo.
- **Recall Rush:** a vocabulary retrieval round. A prompt (definition, picture, or audio) is shown and the learner picks or types the word. Consecutive correct answers build a combo; a wrong answer breaks it and queues the item to come back later in the round. A timer is optional and off by default.
- **Sentence Builder:** shuffled word tiles are dragged or tapped into order; correct placement snaps with feedback, and the finished sentence celebrates.

Both activities keep the learning act (retrieval, ordering) at the center and add game feel around it. A pure reaction-time game, such as catching floating words, was considered and rejected because it rewards reflexes rather than English and is the hardest kind of activity to make accessible.

What the spike should tell us:

- Does it feel fun on a phone and in a browser? Mobile builds cannot be produced on the current development host, so phone testing uses a web build opened on a real device, and that limit is recorded with the results.
- How much effort does a polished game-like activity take on the chosen path?
- Do the attempt events below carry everything a progress and scoring service would need?
- What accessibility costs come with timed, animated play?

### Minimal attempt event

Every answer in the spike emits one event so results can be analyzed later and a real contract can be designed from evidence. This is a sketch, not an API.

```
activity_id      which activity (for example recall_rush)
item_id          which prompt or sentence
attempt_number   1 for the first try on this item in this session, then 2, 3, ...
correct          true or false
response         what the learner chose, typed, or arranged
latency_ms       time from prompt shown to answer
combo_before     combo length before this answer
timestamp        client time, ISO 8601
```

It is out of scope for this spike: real accounts, real APIs, licensed course content, speech scoring, backend persistence, and production quality.

## Open questions

1. Who are the learners for this, and what makes them drop off today?
2. Which skills matter most to make fun first: vocabulary, listening, speaking, or grammar?
3. Must new activities also run in a browser, or is mobile first acceptable?
4. Is a social layer (teams, leaderboards) wanted, and for which audiences?
5. Which outcome proves success: more sessions, longer streaks, better test results, or course completion?
6. Where does content for new activities come from? If the CMS cannot emit the new format, authoring tooling is a bigger constraint than the framework.
7. Is the spike testing decision 1 (do game-like activities help learning) or decision 2 (which technology)? The answer picks the path.
