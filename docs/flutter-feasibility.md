# Flutter feasibility for next-generation LEN

## Recommendation

Proceed with a bounded Flutter proof of concept, not a full rewrite commitment. Flutter is a credible candidate when shared mobile, browser, and desktop UI is a product goal. Validate the hardest learning workflows before expanding the app shell.

This is a framework-level assessment based on official documentation. The current LEN implementation, native dependencies, backend contracts, accessibility baseline, and commercial content rights have not been audited. No cost saving, performance advantage, or feature parity is established.

## Platform fit

Flutter officially supports Android, iOS, web, Windows, macOS, and Linux. OS-version and architecture support depend on the pinned SDK. Framework support does not establish that every required plugin supports every platform.

Proposed order: Android and iOS plus responsive web first; desktop only after a demonstrated requirement. iOS builds require Apple tooling on macOS; Windows and macOS desktop builds need their corresponding build hosts. Plan a host-appropriate CI matrix rather than promising all builds from one Linux machine.

## Why consider Flutter

- Shared Dart UI and domain logic across the supported platform family.
- Explicit responsive layouts for phone, tablet, and desktop learning experiences.
- A rendering architecture suited to custom interactive learning UI.
- Platform channels and native integrations where shared code is insufficient.

These are capabilities, not proof that development will be faster or cheaper for this team.

## Fair comparison with React Native

React Native is not mobile-only. Its official documentation lists partner and community implementations for macOS, Windows, web, and other platforms. The decision is therefore not “Flutter supports multiple platforms, RN cannot.”

Retaining RN may preserve React/TypeScript expertise and existing implementation investment. Moving to Flutter entails Dart adoption, UI redevelopment, platform-integration validation, and dual-maintenance during migration. Conversely, Flutter's supported platform family may simplify a genuinely broad shared UI strategy. Measure the same representative learner flow before deciding.

Existing JavaScript learning activities cannot simply be imported as Dart widgets. Investigate native recreation versus a separately maintained web-content adapter. Embedded browser capabilities and messaging are platform-specific; a mobile WebView approach must not be assumed to work unchanged on desktop and web.

## Critical feasibility risks

1. **Learning activity compatibility:** audit actual activity types and interaction contracts privately. Test synthetic equivalents publicly. Keep paid learning content out of this repository.
2. **Audio and speaking:** prove playback, recording, permission denial, interruption, microphone availability, and device switching on each priority platform. Speech-service credentials must remain server-side. Speech scoring is separate from microphone capture.
3. **Offline and progress correctness:** implement durable local attempts, resume, an outbox, idempotency, ordering, and conflict handling. A retry must not duplicate completion or overwrite newer progress. Browser storage can be evicted; define recovery and retention policy explicitly.
4. **Web limitations:** measure cold load on representative devices and networks. Test Safari and browser audio policies, keyboard use, screen readers, deep links, CORS, and cache updates. Flutter's current web FAQ says a service worker is not generated or managed by default; offline web is explicit engineering work, not an automatic benefit.
5. **Web suitability:** use Flutter for the interactive learner application; assess a conventional HTML site separately for SEO-heavy public marketing and document content.
6. **Accessibility and internationalization:** verify focus order, semantics, text scaling, contrast, captions/transcripts, IME input, and representative translated layouts. Shared rendering is not proof of accessible behavior.
7. **Plugin coverage:** check platform support, maintenance, licenses, and fallbacks for audio, storage, authentication, notifications, purchases, and embedded content before adoption.
8. **Migration and operations:** preserve stable user identity, course entitlement, and reporting semantics. Do not assume shared APIs or migration compatibility without a contract review. Store distribution, signing, privacy disclosures, and support remain platform-specific work.

## Suggested architecture for the experiment

Start with one app, not a large package framework. Separate learner UI, lesson/attempt models, repository interfaces, and platform adapters. Use synthetic lesson fixtures and a mock progress service. Introduce a real API adapter only after a private contract and security review.

Distinguish immutable learner attempt events from replaceable progress snapshots. Document ordering and freshness rules before implementing retries. Keep secrets and speech-service calls behind an authenticated backend boundary.

Do not pick state-management or media packages solely by popularity. Choose the smallest maintained option that passes the platform acceptance tests.

## Sources

- Flutter supported deployment platforms: https://docs.flutter.dev/reference/supported-platforms
- Flutter architectural overview: https://docs.flutter.dev/resources/architectural-overview
- Flutter web support: https://docs.flutter.dev/platform-integration/web
- Flutter web FAQ, including offline/service-worker guidance: https://docs.flutter.dev/platform-integration/web/faq
- React Native out-of-tree platforms: https://reactnative.dev/docs/out-of-tree-platforms

Documentation is mutable. Recheck platform constraints when choosing and pinning the SDK. This assessment is not a benchmark result.
