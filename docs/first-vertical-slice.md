# First vertical slice

## Proposed scope

Build a small synthetic learner journey: course list -> lesson -> listening question -> answer feedback -> saved progress -> resume. Add record-and-playback as a technical probe, without claiming speech assessment.

Use original sample sentences and explicitly reusable audio, or generated test tones. No real learner accounts, production APIs, private activity bundles, or licensed course media.

## Work sequence

1. Privately inventory the current LEN feature set and platform dependencies. Publish only an approved abstract capability list.
2. Confirm launch platforms and minimum OS/browser requirements. Select a stable Flutter SDK and pin it; choose an organization-approved license.
3. Create the app and a host-appropriate CI pipeline with format, analysis, unit tests, widget tests, and a web build. Add Android build coverage separately; obtain macOS CI for iOS validation. Never equate a web build with mobile validation.
4. Implement the synthetic learner flow and durable local progress with adapters that can later be replaced by reviewed API integrations.
5. Exercise media, accessibility, offline recovery, synchronization failure, and existing-content integration as separate technical probes.
6. Compare the same workflow with the RN baseline and record a go/no-go decision before committing to migration.

## Acceptance evidence required

- A fresh checkout can run the documented commands and execute the sample journey.
- Phone and wide-screen layouts are tested; keyboard and touch paths work.
- Progress survives application restart. Native offline resume works; browser offline launch is tested separately with an explicit caching implementation.
- Retried attempts do not create duplicates, and out-of-order responses cannot overwrite newer progress. Test multiple-device conflicts against a clearly stated server contract.
- Audio playback and recording are exercised on Android, iOS, and target browsers, including permission-denied and interruption cases. Record untested platforms honestly.
- Keyboard navigation, screen reader semantics, and large text are checked on representative devices.
- Record cold load, startup, media latency, memory, and release artifact size with device/build/network details. Agree acceptable budgets before the comparison; do not invent benchmark numbers.
- CI results refer to the exact candidate commit. Device smoke-test evidence is separate from static checks and builds.

## Decision gate

Adopt Flutter only if the slice demonstrates acceptable learner experience, reporting correctness, platform integration, accessibility, and sustainable team ownership. Retain RN if migration cost or compatibility outweighs the demonstrated benefits. A mixed approach is valid only with explicit maintenance boundaries.

## Current verification

Documentation-only starting point. No Flutter application, device test, benchmark, CI pipeline, or production integration exists yet. Shipping, store publication, and any change to the current LEN app need separate approval.
