# YATIME Master Project Specification

**Product, UI, UX, architecture, quality, and delivery blueprint**  
**Document version:** 1.0 · **Date:** 2 October 2026  
**Publication:** Public GitHub documentation  
**Status:** Implementation proposal grounded in the requirements and bounded evidence below

YATIME helps people turn documents and speech into usable, traceable knowledge. A person can import a scanned document, review its extracted text, listen to it, find a passage, and create a grounded summary without losing the connection to the original page. The primary product is a Windows application with an Android companion, serving Arabic, French, and English users.

This specification defines a coherent next product baseline from end to end. It is a buildable design and engineering brief, not a claim that every described feature already exists. Preserve working behavior, recover the existing client architecture before changing it, and implement the smallest complete workflows first. The desired result is serious, premium software whose reliability is visible in the interface.

## Contents

1. [How to use this specification](#1-how-to-use-this-specification)
2. [Evidence and current baseline](#2-evidence-and-current-baseline)
3. [Product purpose and success](#3-product-purpose-and-success)
4. [Scope and priorities](#4-scope-and-priorities)
5. [Product and interaction principles](#5-product-and-interaction-principles)
6. [Visual identity and design system](#6-visual-identity-and-design-system)
7. [Information architecture and navigation](#7-information-architecture-and-navigation)
8. [Critical end-to-end journeys](#8-critical-end-to-end-journeys)
9. [Screen specifications](#9-screen-specifications)
10. [Accessibility and localization](#10-accessibility-and-localization)
11. [Document extraction and review](#11-document-extraction-and-review)
12. [Audio playback and voice control](#12-audio-playback-and-voice-control)
13. [Search and grounded assistance](#13-search-and-grounded-assistance)
14. [Application architecture](#14-application-architecture)
15. [Data model and processing contracts](#15-data-model-and-processing-contracts)
16. [Privacy and security](#16-privacy-and-security)
17. [Device pairing and text transfer](#17-device-pairing-and-text-transfer)
18. [Reliability performance and recovery](#18-reliability-performance-and-recovery)
19. [Testing and acceptance](#19-testing-and-acceptance)
20. [Delivery plan and release gates](#20-delivery-plan-and-release-gates)
21. [Decisions requiring confirmation](#21-decisions-requiring-confirmation)
22. [Bounded implementation brief](#22-bounded-implementation-brief)
23. [Reusable definition of done](#23-reusable-definition-of-done)
24. [References and document maintenance](#24-references-and-document-maintenance)

## 1 How to use this specification

The intended readers are the product owner, designer, client and gateway engineers, QA engineers, and an implementation assistant. Read sections 2–5 before estimating work; sections 6–13 define the experience; sections 14–19 define implementation boundaries and verification; sections 20–23 define delivery.

**MUST** means an acceptance requirement for an enabled feature. **SHOULD** means the recommended default, with an explicit recorded reason for deviation. **MAY** means optional work. A requirement does not imply a shipped capability. Items in later phases are not prerequisites for releasing an earlier, honestly scoped phase.

Use these evidence labels consistently:

- **[REQ]** Explicit product requirement or direction supplied by the owner.
- **[OBS]** Bounded finding observed in the source or documentation available for this specification. It is not a complete application audit.
- **[REP]** Historical capability or result reported by the owner or in supplied project reports, without fresh independent verification here.
- **[PROP]** Proposed design, architecture, threshold, or behavior introduced by this document.
- **[OPEN]** Decision or evidence gap that must be resolved before the affected feature or public claim ships.

Unless explicitly labeled otherwise, the normative design and engineering requirements from section 5 onward are **[PROP]**, informed by the **[REQ]** baseline. Acceptance criteria describe what must be demonstrated, not what has already passed. No approval of a new palette, provider, protocol, or roadmap is implied by publication of this document.

## 2 Evidence and current baseline

### Evidence boundary

The full current Windows and Android client source was not available for this document. A bounded review of four project documents established a documented baseline: `DEVICE_LINK_PROTOCOL.md`, `ANDROID_CLIENT_MVP_DEVICE_ACCEPTANCE.md`, `0006-storage-architecture.md`, and `0007-auth-device-authorization.md`. These are evidence names, not promises that the files are published beside this specification. Their content does not establish current deployment, production readiness, or complete runtime behavior.

**[OBS] Documented architecture:** Flutter/Dart client references, a Rust native core, a TypeScript gateway, and a local vault as the source of record. Storage ADRs describe a SQLCipher/Drift index, chunked encrypted blobs, per-file keys, and encrypted search. Treat these as architectural documentation until the implementation is inspected. Do not assert Tauri or rewrite the client into an unrelated framework.

**[OBS] Documented device link:** selected document **title and text only**; matching 12-character verification code confirmed on both devices; both devices online and unlocked; recipient receipt verification. This is a transfer of a copy. It is not full-vault synchronization, original-PDF transfer, audio transfer, an offline mailbox, or automatic cross-device playback continuation.

**[REP] Historical release report:** pairing, Windows-to-Android transfer, encrypted local open, and Windows restart/reconnect were reported PASS for release 0.1.0-4. **[OBS] Separate acceptance documentation** identifies a debug acceptance build with version 0.1.0/code 4 and says physical-device confirmation was not completed in that document. These are different evidence records whose relationship remains unresolved. Do not erase the later reported PASS, combine the records into a fresh test, or infer production readiness. Link-service deployment status also remains unverified.

| Area | Evidence | Safe baseline | Required next evidence |
|---|---|---|---|
| Primary platforms | [REQ] [OBS] [REP] | Windows and Android | Reproducible builds and supported OS matrix |
| Documents and speech | [REQ] [OBS] [REP] | Document/scanned-PDF OCR to text/audio; recorded/live speech to text; source-linked summaries | Current fixture-based tests per enabled feature |
| Languages | [REQ] [OBS] | Arabic, French, English; Arabic RTL | Human review and mixed-script tests on both platforms |
| Scan workflow | [OBS] | Multi-page capture, crop/order, OCR correction/save described | Current camera/import and preservation acceptance |
| OCR quality | [REQ] [REP] | Geometry/layout awareness, OCR gating, mixed-page merging, RTL fixes, withheld-page review | Page-accounting and reading-order regression suite |
| Playback and parts | [REQ] [REP] | Ordered parts, remembered position, per-part or whole MP3 export | Current restart, navigation, and platform-specific export evidence |
| Voice control | [REQ] [OBS] [REP] | Wake phrase YATIME is Windows-only in the documented baseline; Android uses explicit initiation | Device-specific command and audio-focus acceptance |
| Android limitations | [OBS] | PDF export unavailable; background live capture beyond MVP | Honest feature availability in the actual build |
| Pairing and transfer | [OBS] [REP] | Authenticated selected-title/text copy flow | Current protocol conformance and physical-device evidence |
| Storage and recovery | [OBS] | Local encrypted vault documented; encrypted backup is a V2 proposal | Implementation review, recovery UX, and tested restore before claiming recovery |
| Biometrics | [OPEN] | Earlier ADR and later acceptance expectations differ | Actual platform/build behavior; no guaranteed biometric-unlock claim |
| Brand | [REQ] | Premium navy and warm gold; distinctive original symbol | Authoritative assets and exact tokens |
| New UI and API examples | [PROP] | Defined below | Approval, implementation, and acceptance |
| iOS and browser client | [PROP] | Future evaluation only | Separate scope and release decisions |

**Protocol precedence:** the later device-link document supersedes older ADR details where they differ. The documented link uses certificate-DER pinning, length-prefixed possession proofs, persistent SQLite nonce tracking, and a 120-second skew allowance. Do not merge those details with an older JWS/DPoP, Redis, or different-timing design. Recover the exact implemented version and its test vectors before changing or documenting a wire contract.

**Public claim rule:** “tested on” requires evidence identifying build, platform, fixture, and result. An architectural ADR or historical PASS is not a fresh security audit, compliance certification, or all-platform guarantee. The rest of this specification is a proposed target unless labeled otherwise.

## 3 Product purpose and success

### Main jobs to be done

1. **Read a difficult document.** Import a scan, recover reading order, inspect uncertain pages, and read clear text alongside the source.
2. **Listen without losing the source.** Convert accepted text to speech, follow the current sentence, and resume later on the same device. Cross-device audio and position synchronization are separate future work.
3. **Capture spoken information.** Transcribe an audio file or a deliberate recording, with visible uncertainty and timestamps.
4. **Find and understand a passage.** Search across permitted local sources, open the exact location, and ask questions with inspectable evidence.
5. **Carry text between devices.** Pair Windows and Android, send selected document title/text securely, and recover from interruption without unexplained duplicate or missing copies.

The initial audience is individuals working with multilingual documents and audio. Research, study, and professional reading are use cases; they do not justify inventing regulated-industry certifications or promising specialist accuracy.

### Product outcomes

The strongest success signal is a completed, trustworthy workflow: source imported, page accounting clear, useful text produced, listening resumed correctly, and output exported with its limitations visible. Track funnel completion using content-free events only when the user permits telemetry.

Proposed release measures:

- Every imported page has an explicit terminal or review state; no unexplained missing page is acceptable.
- The user can identify what is processing, what needs review, and what can already be used.
- Reading position, selected part, and listening position survive ordinary restart.
- Search and assistant citations reopen the correct source revision and location.
- Essential workflows work with keyboard and assistive technology as well as touch.
- A failed provider, lost connection, or revoked device leaves the original source intact and offers a useful recovery path.

Do not use raw import volume, decorative AI outputs, or an unsupported “accuracy percentage” as substitutes for these outcomes.

## 4 Scope and priorities

### Baseline priorities

| Priority | Included outcome | Boundary |
|---|---|---|
| P0 | Source preservation, import, page accounting, OCR review, library, reader, search, player, remembered position, text and MP3 export | Windows first; Android supports the agreed companion workflow |
| P0 | Arabic/French/English, RTL, accessibility, local storage, explicit cloud boundary, safe job recovery | Applies to every enabled baseline feature |
| P0 | Authenticated pairing, selected title/text transfer, local open, restart/reconnect, visible transfer state | Preserve reported companion capabilities rather than replacing them blindly |
| P1 | Audio-file transcription, recording, live captions, grounded summaries, bounded assistant, wake-word navigation | Deliver complete vertical slices; unready features remain unavailable or clearly labeled preview |
| P2 | Encrypted backup/restore, any audio/PDF transfer or position sync, advanced editing, additional export formats | Separate design and test acceptance |
| Future | iOS, full browser client, organization workspaces, collaboration, billing, public sharing | Not shipped or required by this baseline |

Priority is a sequencing proposal, not permission to remove a working feature. If a P1 capability already exists, retain it and assess its actual quality before changing it. Reclassify priorities with the owner if necessary.

### Explicit non-goals

The baseline does not include a marketplace, social feed, surveillance mode, automatic meeting joining, unrestricted web agent, team administration suite, payment system, or replacement for a professional translator or clinician. It does not require a new account service merely to read local files. Avoid adding a new platform, database service, or model provider unless a measured gap requires it.

## 5 Product and interaction principles

1. **The source stays visible.** Every derived item links to its document page or audio timestamp. Editing text never overwrites the original file.
2. **Quality has an honest state.** “Needs review” is useful information. Unknown text must not be silently invented or hidden.
3. **One document, one continuous journey.** Import, review, read, listen, search, summarize, and export belong to the same document model.
4. **Arabic is a first-class product language.** Design RTL and mixed-script behavior alongside English and French, not after layout completion.
5. **Calm surfaces, clear actions.** Use strong typography and spacing, one obvious primary action, and restrained gold accents. Avoid dashboard clutter and animation that competes with reading.
6. **Work survives interruptions.** Preserve accepted progress, make retries idempotent, and restore location without unexpectedly starting audio.
7. **Processing location is understandable.** The user should know when content stays on a device, is sent as a text copy to a named recipient, or leaves for a processing provider.
8. **The interface respects uncertainty.** Show source coverage and extraction limitations; do not present model confidence as truth.
9. **Power features remain discoverable.** Offer keyboard shortcuts, contextual menus, and voice commands without hiding the equivalent visible control.
10. **Improve the existing product incrementally.** The preferred unit of delivery is a tested end-to-end workflow, not an architectural rewrite.

## 6 Visual identity and design system

### Brand direction

**[REQ]** YATIME should feel premium, serious, and recognizable through deep navy/near-black surfaces and warm gold/champagne accents. Preserve an existing approved logo if its authoritative asset is recovered.

**[PROP]** If a new symbol is required, explore a simple original Y construction that can suggest a document fold, text lines, and a voice signal through negative space. It must remain readable at app-icon size and work in one color. This is a design brief, not a delivered or approved logo. Do not use a generic microphone pasted onto a letter or imitate another product's identity.

Use the name **YATIME** consistently. Keep the wordmark unmirrored in RTL. Define clear space and small-size variants only after reviewing the real vector asset. License-check all fonts and icons before bundling them.

### Proposed tokens

This is one coherent **[PROP]** token system, pending alignment with the original official palette. It is not a recovered or approved brand standard. Dark is the primary brand presentation; the light reader/application variant uses the same navy/gold identity.

| Semantic token | Dark primary theme | Light variant | Usage |
|---|---|---|---|
| `canvas` | `#0B1929` | `#F8F5EF` | Application background |
| `surface` | `#122A42` | `#FFFFFF` | Panels, sheets, reader controls |
| `text.primary` | `#F2F5F8` | `#172F46` | Main text |
| `text.secondary` | `#B6C3D1` | `#526579` | Supporting information |
| `brand.navy` | `#102A43` | `#102A43` | Brand anchor and gold-button labels |
| `brand.gold` | `#D8B36A` | `#D8B36A` | Restrained fills and accents |
| `text.accent` | `#D8B36A` | `#76531D` | Accessible accent text |
| `control.border` | `#78899A` | `#78899A` | Essential input boundaries |
| `focus` | `#D8B36A` | `#102A43` | Visible keyboard focus |

### Named palette and surface aliases

To preserve the requested brand vocabulary, use these **proposed aliases** for the same palette. They do not establish the original official values. `ChampagneGold` intentionally shares the main gold value for now; do not introduce a subtly different accent in each screen.

| Brand name | Proposed value | Maps to |
|---|---|---|
| `MidnightNavy` | `#0B1929` | Dark canvas |
| `DeepNavy` | `#102A43` | Brand navy and secondary dark surface |
| `SurfaceNavy` | `#122A42` | Dark cards and raised surfaces |
| `Gold` | `#D8B36A` | Primary accent |
| `ChampagneGold` | `#D8B36A` | Shared warm-gold accent alias |
| `LightGold` | `#F8F5EF` | Very light warm canvas tint |

| Surface role | Dark primary theme | Light variant |
|---|---|---|
| Main background | `MidnightNavy` | `LightGold` |
| Secondary background | `DeepNavy` | `#FFFFFF` |
| Card background | `SurfaceNavy` | `#FFFFFF` |
| Elevated surface | `SurfaceNavy` plus boundary/elevation treatment | `#FFFFFF` plus boundary/elevation treatment |
| Sidebar background | `MidnightNavy` | `LightGold` |
| Header background | `DeepNavy` | `#FFFFFF` |
| Modal background | `SurfaceNavy` | `#FFFFFF` |
| Default essential border | `#78899A` | `#78899A` |
| Soft decorative border | `SurfaceNavy` | `LightGold` |

Soft borders may be low contrast and must never be the only boundary of an input, focus indicator, or meaningful state. Use the default essential border for controls: it is 4.07:1 against the dark panel and 3.59:1 against white. Overlay dimming can use the dark canvas with opacity, but the modal itself remains opaque; validate text and focus against the resulting rendered surfaces. Elevation and grouping should not depend on barely distinguishable navy fills alone.

Light-surface semantic text uses error `#B42318`, success `#176744`, and warning `#805500`, each accompanied by a word/icon. In dark mode, use primary light text and an explicit status icon/label; do not reuse dark semantic foreground colors on dark surfaces without validating a new pair. This keeps semantic meaning accessible without adding untested dark tokens.

**Calculated flat-color contrast:** white/navy 14.64:1; navy/gold 7.37:1; light-theme primary text/cream canvas 12.60:1; secondary text/white 6.01:1; secondary text/cream 5.52:1; accent brown/white 6.94:1; dark primary text/dark canvas 16.19:1; dark primary text/panel 13.37:1; dark secondary text/panel 8.16:1; gold/dark panel 7.37:1. Essential control border/white is 3.59:1 and border/cream is 3.30:1.

These ratios were computed from opaque sRGB token pairs using the [W3C contrast method](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html). They validate these pairs only. Gold/white is only 1.99:1: never use gold alone as light-theme body text, essential icon, field edge, or focus ring. Gold buttons use navy labels. Transparency, gradients, pressed/disabled states, and the final rendered application need separate testing; this is not a compliance claim.

### Type spacing and shape

- Proposed Latin UI face: Inter, with platform fallbacks. Proposed Arabic face: Noto Sans Arabic, with tested platform fallbacks. The final font choice depends on license, packaging, rendering, and readable Arabic shaping on Windows and Android.
- UI text uses a 16 logical-pixel baseline. Secondary metadata may use 14; avoid critical instructions below this. Main screen titles use 28/36 and section titles 20/28. Reader text defaults to 18 with a user-adjustable size and approximately 1.65 line height; Arabic must be evaluated by native readers, not squeezed into Latin metrics.
- Spacing scale: 4, 8, 12, 16, 24, 32, 48 logical pixels. Use 24 for desktop panel padding and 16 on compact screens.
- Corners: 8 for controls, 12 for cards, 16 for major sheets. Avoid turning every surface into a floating card.
- Standard interactive height: 44 on desktop and 48 on touch interfaces. Icon glyphs remain visually modest inside these targets.
- Reading width: approximately 60–80 Latin characters where possible, with native-language review for Arabic line length. The page preview can occupy more width without stretching the text column.
- Icons use one coherent family, consistent stroke weight, and text labels where meaning is not obvious. Meaningful icons need accessible names.
- Motion: brief 120–180 ms transitions for state changes; no looping decorative waves. Honor reduced motion and avoid making audio status depend on animation.

### Reusable components

Build token-backed components before duplicating individual screens: app shell, navigation item, document row, document cover, source badge, status chip, language selector, search field, page thumbnail, quality notice, player bar, source citation, job row, consent panel, empty state, error panel, confirmation dialog, and toast.

Every component needs default, hover where available, keyboard focus, pressed, selected, disabled, loading, and error behavior where applicable. Disabled controls explain prerequisites. Loading never erases a confirmed selection. Destructive actions use explicit words and keep a safe default focus. A toast may confirm a completed action, but durable errors belong beside the affected object.

## 7 Information architecture and navigation

### Desktop shell

A persistent sidebar contains **Library**, **Search**, **Capture**, and **Devices**. Settings and Help sit below. Library is the primary home, with saved document state and a clear Import action. Avoid a separate vanity dashboard before useful content exists.

The top content bar shows page title, contextual action, search when relevant, and a compact activity indicator. A persistent bottom player appears only when a playback context exists; it does not reset when the user navigates to Search or Settings. Processing is accessible from the activity indicator and document rows.

At generous width, the Reader has a page/part rail, a main reading area, and an optional source or assistant pane. These panes are resizable within readable limits. Do not open both auxiliary panes so widely that the reading surface becomes unusable.

### Mobile shell

Use bottom navigation for **Library**, **Search**, **Capture**, and **More**. More contains Devices, Settings, and Help. The mini-player sits above navigation, respects the safe area, and expands to the full player. The Reader uses **Text**, **Source**, and **Details** tabs; assistant answers open a separate sheet or route with a reliable return path.

Proposed adaptive widths: compact below 600 logical pixels, medium 600–1023, expanded at 1024 and above. Test actual content and text scaling; these thresholds are starting points, not device-model assumptions. At 200% text size, collapse secondary panes and keep actions reachable rather than shrinking text.

### Location and identity

Use stable internal document, revision, page, part, and segment IDs. Labels such as “Part 2 of 8” are presentation, never primary keys. Opening a source citation restores the correct revision and highlight. Back navigation restores query, filters, scroll position, and focus. If the referenced revision is unavailable, explain that and offer the current revision without pretending they are identical.

## 8 Critical end-to-end journeys

### A Import a scanned document and listen

1. The user selects Import and chooses a PDF or supported image. Drag-and-drop is available on desktop with an equivalent button.
2. YATIME validates the container, file signature, supported limits, encryption state, and available space. It preserves the original before processing.
3. The import preview shows title, page count, detected languages, destination, processing location, and any provider disclosure. Password-protected files request the password only for that file; never retain it without explicit choice.
4. The job processes pages and reports real stage progress. The user may leave the screen.
5. A completion panel accounts for all pages: accepted, needs review, intentionally excluded, or failed. Unreviewed and failed pages remain visible in the original sequence.
6. The user reviews uncertain pages, compares image and text, fixes or retries extraction, or explicitly excludes a page with a reason.
7. Accepted content becomes a revision with stable page provenance and ordered parts. The source title remains the default display title.
8. The user chooses a voice and hears a short sample. Cloud synthesis requires a clearly disclosed, specific processing choice.
9. Playback follows the accepted sequence. A gap caused by excluded pages is visible before listening and at the corresponding boundary.
10. Closing and reopening restores the part and position. Playback resumes only on an explicit play/resume action.

### B Send a text copy to Android

The user pairs the devices, verifies the matching code on both, chooses a document revision, and sees the exact payload: title and accepted text. YATIME states that original PDF/images, audio, and the complete vault are not included. Both devices stay online and unlocked through the documented flow. Android verifies receipt and saves the copy locally before Windows calls it Sent. Interrupted retries preserve transfer identity and do not create unexplained duplicate copies. Reconnection after Windows restart restores a trusted session only after the required authentication checks. Audio continuation across devices remains a future proposal.

### C Capture and turn speech into text

The user opens Capture, chooses file transcription, recording, or live captions, and sees the processing mode. Microphone access is requested at the point of use. Recording has a persistent indicator, duration, pause, and stop. Finalizing saves recoverable audio before transcription completes when audio retention was selected. Partial live text is labeled provisional; final segments retain timestamps. A transcript can be corrected and saved without misrepresenting corrections as the original speech.

### D Search then ask a grounded question

The user searches the library, narrows by language/type/status, and opens a result at the exact source page or timestamp. The assistant receives only the chosen scope. An answer cites relevant passages and identifies excluded or unavailable pages. If the sources do not support the answer, it says so. The user can open the cited evidence, copy the answer, or export it separately from source text.

### E Export with incomplete source coverage

The export dialog previews the selected revision, format, part range, output title, and coverage. If pages are unreviewed, withheld, failed, or excluded, the user must resolve them or explicitly export the accepted subset. The export includes a human-readable coverage report. “Whole document” must never imply complete source coverage when pages are omitted.

## 9 Screen specifications

### 9.1 Onboarding and first use

Start with language and concise purpose, then offer **Import a document**, **Open a sample**, or **Pair a device**. Demonstration content is synthetic and clearly labeled. Do not require microphone access, provider credentials, analytics, or account creation to explore a local library.

Explain local vault setup and the actual recovery limitation before the user entrusts important files. Do not imply account recovery can restore lost vault keys. Then explain three independent choices: interface language, document language assistance, and processing mode. A user may have a French UI and an Arabic document; the system must not collapse these into one setting. A voice must support the actual text language, or show a mismatch warning. Selecting an English voice does not translate Arabic text; translation is separate scope. Optional cloud processing and optional analytics have separate controls and honest defaults.

### 9.2 Library

Desktop defaults to a readable list with optional grid view. Mobile uses compact rows. Each document exposes title, source type, language, date, page/part count, quality status, local availability, and listening progress where applicable. Display only a small number of useful metadata items at once; details remain available.

Primary action: Import. Secondary actions: sort, filter, view toggle, and select. A document context menu provides Open, Rename, Export, Send to device, and Move to trash when implemented. Bulk actions appear after explicit selection; bulk export must still disclose coverage per document.

States: first-use empty, filtered empty, loading, ready, processing, needs review, transfer pending, offline asset unavailable, recoverable failure, and trashed. “No results” preserves filters and offers Clear filters. A failed document remains a row with Retry and Details instead of disappearing.

### 9.3 Import and processing

Provide a compact queue, not a blocking wizard for every file. Each item shows stage, processed pages/total, elapsed time, and available Cancel/Retry actions. Use indeterminate progress when a stage has no reliable denominator. Never animate a fabricated percentage.

For supported camera capture, request camera access only after Scan is chosen. Show original-order thumbnails, rotate/crop controls, Add page, Retake, and accessible Move earlier/later actions. Retain the original capture, preview the final order, and run OCR only after the user confirms the batch. Never treat a crop as permission to delete the original.

Duplicate detection should compare source bytes plus relevant import context. Offer Open existing or Import a separate copy; do not overwrite a document automatically because its filename matches. A batch with one invalid file may continue with valid files after making the failure explicit.

### 9.4 Page quality review

Desktop shows the original page beside extracted text; mobile toggles Source/Text with a stable shared page position. Provide page number, extraction method, language, quality reason, and actions: Accept text, Edit text, Retry extraction, or Exclude page. Exclusion is deliberate, undoable while the revision remains editable, and explained in downstream coverage.

A page rail visually distinguishes accepted, review, failed, and excluded states using icon and text as well as color. “Next page needing review” skips accepted pages without renumbering the source. Bulk acceptance requires a preview of the selected pages and is unavailable for pages with unresolved structural failure.

Edits autosave to a draft, expose undo, and create a new accepted revision when committed. Reprocessing must not overwrite manual corrections silently. Show a comparison or retain both versions.

### 9.5 Reader and document details

The Reader leads with document title and source identity. It offers Text/Source modes, page navigation, font controls, document search, Listen, and an overflow menu. The current page marker is readable prose, not decorative dashed separators. Document details explain original page count, accepted coverage, extraction revision, source file, languages, available audio, and history.

When listening, highlight the current sentence only if accurate alignment exists. Otherwise highlight the paragraph or part and name that granularity. Manual scrolling pauses automatic following until the user selects Follow playback. Selecting text offers Copy, Listen from here when supported, and Ask about selection; it does not invoke AI automatically.

Received title/text copies have a specific **Original source unavailable** state. The current payload does not convey source PDF/images, original source-page deep links, or cryptographically verified original revision provenance. Treat received text as its own locally stored source. Sender-provided coverage prose can be displayed as such, but do not fabricate page previews, source-image navigation, or verified original provenance.

### 9.6 Mini-player and full player

The mini-player shows title, **Part 2 of 8**, play/pause, progress, and an expand control. Desktop may also expose previous/next part. The full player adds position, duration where known, seek controls, part list, speed, voice, sleep timer only if implemented, and Export audio.

Switching document while another plays asks whether to replace the queue when needed. Browsing alone does not replace it. A regenerated voice track preserves a logical text anchor; if an exact audio offset no longer maps, explain the nearest restored passage.

### 9.7 Recording and live captions

Capture has clearly distinct modes. Before recording, show microphone, language, on-device or configured cloud route, and whether audio will be kept. During recording, use a persistent status label, elapsed time, level feedback, Pause, and Stop. Android background live capture is outside the documented MVP; the app must explain and safely handle leaving this screen rather than promise background capture. The user can correct a microphone choice without losing an already saved segment.

Live captions emphasize large, readable text, restrained provisional styling, and a Follow latest toggle. Captions remain selectable without jumping. Connection loss announces that new cloud transcription is paused; whether local buffering continues is explicit. Never imply capture continues after the OS has revoked access or suspended it.

### 9.8 Search

A single search destination covers accepted document text and saved transcripts. Filters include type, language, date, document, and quality state. Results show title, exact snippet, page or timestamp, part label where relevant, and source status. Match highlighting must survive Arabic normalization without corrupting original display text.

Search within document and global search share query behavior but keep distinct scope labels. A query has idle, searching, results, no match, partial index, offline limitation, and error states. A partial index must show which sources are not yet searchable.

### 9.9 AI assistant

The assistant begins with explicit scope: current selection, current document, or selected library items. Show the chosen sources above the input. Suggested actions include Summarize, Explain this passage, and Find key points; avoid implying the assistant can browse or take external action.

Responses show source citations, source coverage limitations, and an optional concise “How this was grounded” panel. Keep generated answers separate from source text. Regenerate creates another answer, not a silent replacement of an accepted note. Cancel stops new work where possible and explains if a provider request already began.

### 9.10 Devices and pairing

Show trusted devices with name, platform, last seen, connection state, and pending text transfers. Pairing uses the documented QR flow and matching 12-character verification code confirmed on both devices. A manual alternative is a proposed accessibility requirement and must receive equivalent protocol review. Display expiry and retry controls without exposing long-lived secrets.

The action is **Send a text copy**. Preview the recipient, selected revision, title/text payload, and coverage limitations. Transfer detail includes actual progress and recipient receipt state. Disconnect, unpair/revoke, and remove a local copy are separate actions. Offline does not mean revoked. Do not label this screen Sync or suggest that deleting the source removes an already received copy.

### 9.11 Export

Export is contextual to a document, transcript, or generated answer. Proposed baseline formats are UTF-8 plain text and MP3 for generated audio, subject to verified platform capability. Android PDF export is unavailable in the documented baseline. Additional formats require separate fidelity tests, and unavailable formats should not appear as working controls.

The dialog includes title, format, whole accepted sequence or specific parts, revision, destination, and coverage. MP3 offers one ordered combined file or numbered files per part. Show estimated size only when measurable. Exporting plaintext or audio outside encrypted storage warns that the output is an ordinary file. Existing files require an overwrite decision.

### 9.12 Settings help and activity

Settings groups: Appearance and language, Reading and playback, Voice and microphone, Processing and providers, Storage and backup, Devices, Privacy, and About. Use plain explanations before technical detail. Help offers keyboard shortcuts, voice command examples, quality troubleshooting, and privacy explanations.

Activity lists durable jobs with scope, current stage, retryability, and outcome. About shows app/build version, component versions needed for support, and supported processing modes. A diagnostic export lets the user inspect sanitized data before sharing it.

## 10 Accessibility and localization

### Accessibility target

**[PROP]** Use WCAG 2.2 AA as the design and testing target, supplemented by native-platform accessibility testing. This is a target, not a declaration of conformance. The [W3C recommendation](https://www.w3.org/TR/WCAG22/) covers complete experiences, so passing contrast calculations alone is insufficient.

Every screen must provide meaningful names, roles, values, state, reading order, visible keyboard focus, and recoverable focus after navigation. Reserve layout space for the persistent player so it cannot cover the last result or focused input. Modal focus stays inside the modal and returns to its trigger. Avoid gesture-only actions and offer a button alternative to drag/reorder.

Test text at 200%, increased OS display scaling, screen magnification, reduced motion, and high-contrast settings. Reflow controls and collapse secondary panes before truncating essential information. Buttons use the stronger product target of 44–48 logical units; do not confuse this with the conditional CSS-pixel minimum in the web standard. Validate Narrator on Windows and TalkBack on Android using complete tasks.

Announce stage changes, failures, and completion without reading every OCR counter or playback timestamp. Recording and listening states need textual status. Audio never autoplays on launch, import completion, or document selection. Provide transcripts for application-owned spoken instructions where necessary.

### Keyboard model

| Shortcut | Intended action | Constraint |
|---|---|---|
| Ctrl+O | Import | Discoverable in menu |
| Ctrl+K | Global search | Preserve current document/player |
| Ctrl+F | Find in document | Use document scope visibly |
| F6 | Move to next major region | Stable, documented region order |
| Escape | Close top overlay | Do not silently cancel a running job |
| Enter | Activate focused item | Respect control semantics |
| Space | Activate focused button; player toggle in an eligible player context | Never steal typing, selection, or screen-reader commands |

Arrow keys navigate a focused composite list; Tab leaves it. Do not create a keyboard trap. Conflicting shortcuts need a documented fallback and should be configurable when practical. Flutter implementations should follow the framework's [accessibility guidance](https://docs.flutter.dev/ui/accessibility) and verify actual native behavior rather than assume semantics are automatic.

### Arabic and mixed-script behavior

UI direction, content language, and voice language are independent. Use logical start/end layout properties and direction per paragraph or run. Isolate filenames, URLs, codes, timestamps, and mixed-language titles. Never reverse stored Arabic strings to make a view look correct. Arabic Windows rendering fixes must preserve logical Unicode text through storage, copy/paste, search, and export.

Mirror navigation placement and directional navigation where appropriate. Do not mirror source images, logos, QR codes, waveforms, or digit sequences. Keep verification codes in an isolated left-to-right block and never translate their characters. Define media-timeline orientation deliberately and test it with Arabic users; shell direction must not accidentally reverse seek semantics.

Use localized prose such as **الجزء ٣ من ١٢** instead of an ambiguous bare fraction. Store canonical numbers independently of display numerals. Arabic typography must support connected shaping and diacritics without Latin letter spacing, forced justification, or clipped line boxes. Search normalization may remove tatweel or optional diacritics in an index, but must retain original text and original-offset mapping.

Use localized resources with plural rules and parameterized strings, following [Flutter internationalization guidance](https://docs.flutter.dev/ui/internationalization). Do not concatenate translated sentence fragments. French expansion and Arabic plural forms need human review.

### Representative interface copy

These are proposed examples, pending linguistic review, rather than a complete translation catalog.

| Intent | English | Français | العربية |
|---|---|---|---|
| Import action | Import a document | Importer un document | استيراد مستند |
| Review action | Review pages | Vérifier les pages | مراجعة الصفحات |
| Part label | Part 3 of 12 | Partie 3 sur 12 | الجزء ٣ من ١٢ |
| Continue | Resume listening | Reprendre la lecture | استئناف الاستماع |
| Needs review | Pages 8 and 19 still need review. | Les pages 8 et 19 restent à vérifier. | لا تزال الصفحتان ٨ و١٩ بحاجة إلى مراجعة. |
| No evidence | I could not find this in the selected sources. | Je n’ai pas trouvé cette information dans les sources sélectionnées. | لم أجد هذه المعلومة في المصادر المحددة. |
| Text transfer | Send a text copy | Envoyer une copie du texte | إرسال نسخة من النص |

Prefer specific recovery copy: “Text is ready. Pages 8 and 19 still need review.” “Audio is based on an earlier text version.” “Your text was saved through 04:32. Recording stopped when the microphone became unavailable.” “This sends a copy of the title and text. The original PDF and audio are not included.”

## 11 Document extraction and review

### Source-preserving pipeline

1. **Intake:** validate type using actual content, enforce documented size/page limits, fingerprint the source locally, and create an immutable page manifest before processing.
2. **Inspect:** detect text layers, page geometry, rotation, likely languages, and content types. A PDF can mix digital and scanned pages.
3. **Choose extraction per page:** prefer usable embedded text; use OCR when needed; flag conflicting or poor text layers instead of blindly trusting them. A page must not be extracted twice into duplicate text.
4. **Recover layout:** preserve reading order using block coordinates, columns, paragraphs, captions, and local script direction. OCR arrival order is not reading order.
5. **Normalize carefully:** fix extraction artifacts without changing meaning. Repeated header/footer removal requires repeated-position evidence across pages, a preserved original, and a reviewable result. Do not delete footnotes or recurring body text merely because they repeat.
6. **Evaluate quality:** flag empty results on nonblank pages, implausible character patterns, uncertain order, language mismatch, and unsupported content. Quality reasons are more helpful than invented precision scores.
7. **Review:** accept, correct, retry, retain as non-text, or intentionally exclude. Never silently discard a refused or failed page.
8. **Publish a text revision:** preserve source-page anchors, coverage, edits, extraction configuration, and ordered parts. Index accepted content and invalidate dependent representations appropriately.

Tables, equations, diagrams, and handwriting require explicit capability boundaries. If faithful interpretation is unavailable, preserve the page image and say that the content is not fully represented in text. Do not convert a complex table into misleading prose or invent an image description.

### Page accounting invariants

For every source revision, the page count equals the sum of represented states: pending, processing, accepted text, accepted non-text/blank, needs review, failed, and explicitly excluded. The categories are mutually exclusive for the current page revision. Original page indices never change because another page failed.

| Page state | Read/search eligibility | Export eligibility | Next action |
|---|---|---|---|
| Pending or processing | Source preview only | No derived text | Wait or cancel |
| Accepted text | Yes | Yes for that revision | Read, edit, listen |
| Accepted blank/non-text | Source and explicit marker | Coverage marker, not invented prose | View source |
| Needs review or withheld | Preview with warning; not trusted search/AI input | No unapproved text | Review, retry, or exclude |
| Failed | Source if available and failure label | No derived text | Retry failed step |
| Explicitly excluded | Source/history remains accessible | Listed as omitted in coverage | Restore or keep exclusion |

Accounted-for pages and text-represented coverage are distinct measures. Accepting a nonblank diagram/table as non-text resolves review, but does not make text/audio semantically complete. Downstream summaries, playback, and export must continue to disclose that unrepresented content.

A document may be partially usable while individual pages remain unresolved. Its status must say so. Proposed header: “24 source pages · 21 accepted · 2 need review · 1 failed.” A generic Done badge is insufficient.

### Revision and part rules

Text edits produce a new revision. Generated audio and summaries retain their source revision and become visibly stale when relevant text changes. The currently playing revision remains immutable until the user switches. Re-extraction preserves manual corrections for comparison.

Parts are created at stable semantic boundaries with provider-length limits considered. Their numerical order follows accepted source order. Every representation uses the same revision-specific part index and total: library, player, search, voice command, and export. A new revision may repartition; retain a mapping when possible and never pretend an old index is the same passage automatically.

The display title defaults to the source title. Sanitizing a filename must not silently rename the document. Plain-text export uses readable page markers and explicit coverage, not decorative dashed separators that obscure provenance.

## 12 Audio playback and voice control

### Playback contract

| State | Meaning | Required behavior |
|---|---|---|
| Unavailable | No usable audio exists | Explain whether generation is possible |
| Queued/generating | Audio is being produced | Show completed parts; avoid fake duration |
| Ready | A playable part exists | Start only on user action |
| Playing | A single media owner is active | Persist a logical anchor and audio offset |
| Paused | Session retained | Resume from last confirmed position |
| Stopped | Narration and assistant speech halted | Retain position; beginning is a separate action |
| Interrupted | Audio focus/device changed | Save position and require safe, predictable resumption |
| Failed part | One asset failed | Retry that part; keep completed assets |
| Stale audio | Text revision changed | Show prior-version label and offer regeneration |

Maintain one media owner across document narration, recorded-audio playback, and assistant speech. Starting recording/live transcription pauses narration and saves its anchor; protect against recording the application's own output. Audio-focus loss, a call, or device disconnect must not cause unexpected loud playback later. Validate actual platform media-session behavior.

Proposed persistence: checkpoint at least every five seconds while playing and on pause, stop, seek, part change, and orderly shutdown. After a crash, restore within the last persisted interval; after an orderly stop, restore the exact saved anchor within player seek tolerance. Never automatically play after restart. Display “Resume Part 3 of 8 · 12:40” when those values are known.

Voice and speed changes should preserve logical location where supported. If regeneration changes timing, restore by segment/page anchor and disclose approximation. Continuous playback skips neither missing parts nor coverage gaps silently.

### MP3 export

The user chooses one ordered MP3 or individual numbered part files. Proposed names: `source_title_part_001_of_012.mp3`; numeric padding prevents lexical misordering. Whole-file assembly follows the same part manifest as playback. Do not concatenate incompatible byte streams without a valid container/encoding process.

Export records the text revision, selected parts, voice configuration, page coverage, and generation date. An incomplete-content audio export includes a short spoken coverage notice plus a companion coverage report. Unreviewed or refused text is excluded. Success appears only after the destination write completes; a disk or permission failure retains reusable generated audio and offers another destination.

### Voice assistant boundaries

**[REQ]** Wake phrase YATIME and Arabic navigation/search commands. **[OBS]** Windows-only wake-word support in the documented baseline. Android offers a visible tap-to-talk control; do not advertise always-listening or background capture there.

Use separate states: Off, Wake word enabled, Listening, Interpreting, Speaking, and Could not complete. Show the recognized command and microphone state. A wake detector being enabled is different from active command recording. The user can disable it immediately.

Supported baseline intents should be narrow: open library, search named content, open a selected result, go to a numbered page/part, play, pause, stop, resume, previous/next part, and adjust supported playback speed. A command targeting part 3 selects numeric part 3, not the third search result. Ambiguity asks a short question. Destructive actions, sharing, and new cloud processing require their normal visible confirmation; voice recognition alone is not broad permission.

Use bounded listening time, no-input expiry, echo/self-trigger protection, and a touch/keyboard equivalent. Do not describe recognition as local/private until the actual provider path is verified. Short acknowledgments may resume previously active narration only under a documented audio-focus rule and never after a stop command.

## 13 Search and grounded assistance

### Search indexing

Index accepted text and finalized transcript segments with document/revision/page/part or timestamp anchors. Preserve original text and store normalized search representations separately. Keep the index protected by the vault architecture; do not create a plaintext shadow index for convenience. Rebuildable indexes are derived data, not the source of record.

Incremental indexing is revision-aware. Results from an old revision must be labeled or removed atomically when the new revision becomes active. A failed or partial indexing job leaves existing usable results intact and displays the limitation. Search tokens, queries, and snippets must not enter analytics or diagnostic logs.

### Assistant grounding contract

Before generation, show scope, processing location, and coverage. The assistant receives only the minimum selected passages and necessary metadata. Answers distinguish direct source content from interpretation, link factual source-based claims to usable anchors, and label AI generation. A citation must identify a real supplied passage; a model-generated page number is not validation.

Examples of appropriate uncertainty: “The selected pages do not state the date.” “This summary covers 21 of 24 source pages.” “The scan is unclear in this passage; review page 8.” Do not infer missing text from a likely pattern. Contradictory sources should be described as contradictory, with both cited.

Document text is untrusted content. Instructions inside a PDF or transcript cannot change system rules, expand source scope, reveal secrets, or trigger tools. The baseline assistant cannot send messages, execute commands, or access unrelated documents. Summarization must remain useful without an autonomous-agent architecture.

Saving an answer creates a separate derived object. Source edits mark it stale. An unavailable provider offers retry or local alternatives only if those alternatives genuinely exist. Do not silently switch providers, upload more sources, or turn a local operation into cloud processing.

## 14 Application architecture

### Preserve compatible boundaries

**[OBS]** The documented direction is Flutter/Dart clients, Rust native core, TypeScript gateway, and encrypted local primary storage. Recover the real repository and dependency versions before choosing libraries or moving code. This specification does not mandate a new state-management package, backend framework, database service, or full rewrite.

**[PROP]** Maintain these logical responsibilities even if current folders differ:

- **Presentation:** adaptive shell, accessible components, localization, view state, and user consent.
- **Application layer:** document workflows, jobs, commands, revision transitions, and capability checks.
- **Domain:** immutable source identities, page/part order, coverage, audio anchors, transfer receipts, and validation rules.
- **Local infrastructure:** encrypted storage, platform key protection, parsing/OCR adapters, audio sessions, search, and durable queues.
- **Gateway:** authenticated request boundaries, provider adapters, quotas, and documented transient processing. Provider credentials stay outside distributed clients.
- **Platform adapters:** microphone, camera, file picker, OS media controls, lifecycle, secure unlock, and accessibility integration.

UI code must not hold provider secrets or bypass domain invariants. A domain action should be testable without a screen. Heavy extraction, cryptography, indexing, and audio work runs off the UI thread with cancellation and resource limits.

### Capability and processing matrix

| Action | Offline promise | External boundary |
|---|---|---|
| Open local source/text | Available after successful local unlock if assets exist | None required |
| Search existing local index | Available if index is present and usable | None required |
| Play downloaded/generated local audio | Available if the file and key exist | None required |
| OCR, STT, TTS, summarization | Only if a verified local engine/model is installed | Otherwise disclose the configured provider dependency |
| Device text transfer | Requires documented online/unlocked link flow | Named paired recipient |
| Cloud processing | Unavailable without a configured/authorized route | Named service and disclosed content |

Paired-host OCR/STT/TTS/AI processing is an unverified future proposal, not a capability established by title/text transfer. Expose it only after separate protocol, consent, and capability acceptance.

Offline is a capability state, not a blanket marketing claim. The UI reads a capability service that reports available, requires model, requires connection, requires unlock, unsupported, or temporarily failed. Never render a working-looking button that has no executable path.

## 15 Data model and processing contracts

### Proposed logical entities

These are **[PROP]** domain contracts to map onto existing storage, not a claim about present database tables.

| Entity | Important fields and invariant |
|---|---|
| Document | Stable ID, display title, source kind, created time, active revision; original source remains immutable |
| SourceAsset | Local protected locator, integrity digest, media type, page count/duration; no content in logs |
| Page | Stable page ID, original index, geometry, state, extraction method, quality reasons |
| TextRevision | Revision ID, parent, accepted blocks, edit provenance, coverage manifest, configuration |
| TextBlock | Stable anchor, page/segment ID, original text, direction, reading order; normalization separate |
| Part | Revision ID, stable ID, numeric index/total, ordered anchor range |
| AudioAsset | Revision/part, voice configuration, duration, protected locator, alignment granularity, state |
| PlaybackCheckpoint | Local document/revision/part/anchor, offset, timestamp, event sequence |
| TranscriptSegment | Session ID, start/end time, provisional/final/corrected state, text, optional verified speaker label |
| GeneratedAnswer | Source revision IDs, coverage, citations, provider/configuration metadata, stale state |
| Job | Operation ID, scope, input version, stage, retry count, checkpoint, sanitized error |
| DeviceTrust | Device identity reference, state, authorization scope, last verification; secrets stored separately |
| TextTransfer | Transfer ID, sender/recipient, source revision, payload digest, approval state, verified receipt |

Time values use an unambiguous interchange representation and localize only for display. Do not rely on wall-clock time alone for ordering competing events. Source content hashes may themselves reveal correlation; keep them out of public telemetry and use scoped identifiers where possible.

### Job lifecycle

`created → queued → running → needs_review | succeeded | failed | cancelled`

A running job may enter `waiting_for_connection`, `waiting_for_unlock`, or `waiting_for_consent`, then return to queued when the condition is met. Pausing is supported only when the worker can checkpoint safely; do not offer a cosmetic Pause button. `needs_review` is an actionable outcome, not a technical crash.

Persist state transitions atomically with relevant outputs. A retry creates a new attempt of the same logical operation and cannot duplicate accepted outputs. Check revision preconditions before publishing results. Cancellation stops future work, removes incomplete temporary output safely, and states which completed work remains; closing a dialog is not cancellation.

### Proposed application API surface

The following operations are **design examples**, not verified endpoints, and must not replace the existing link wire protocol without compatibility review. Implement them as local application services or adapt them to the actual API architecture.

| Operation | Input | Result and important rule |
|---|---|---|
| `ImportSource` | File handle, destination, processing choice, operation ID | Document/job IDs; validate before processing |
| `GetDocumentState` | Document and optional revision | Coverage, capabilities, representations, jobs |
| `CommitPageReview` | Page, base revision, action, edited text/reason | New revision; reject stale base with conflict detail |
| `GenerateAudio` | Revision, selected parts, approved voice/provider, operation ID | Per-part jobs; reuse only exact matching assets |
| `SavePlaybackCheckpoint` | Revision, anchor, offset, increasing local sequence | Durable latest applicable checkpoint |
| `SearchAcceptedContent` | Query, filters, scope, cursor | Anchored results and index coverage |
| `GenerateGroundedAnswer` | Question, explicit source scope, approved processing choice | Answer job plus coverage; no implicit scope expansion |
| `ExportRepresentation` | Revision, parts, format, destination, coverage acknowledgment | Verified export result; no silent overwrite |
| `SendTextCopy` | Trusted recipient, revision, explicit payload approval, transfer ID | Transfer state; success requires verified recipient receipt |

Illustrative operation envelope:

```json
{
  "operation_id": "opaque-operation-id",
  "document_id": "opaque-document-id",
  "base_revision_id": "opaque-revision-id",
  "action": "generate_audio",
  "part_ids": ["opaque-part-id"],
  "processing_choice_id": "previously-approved-choice"
}
```

An operation ID is scoped to the caller and action. Repeating it with identical normalized input returns the existing result; repeating it with different input produces a conflict. It never bypasses authentication or consent. Error envelopes carry a stable code, affected scope, retryability, and safe next action. Suggested codes include `SOURCE_UNSUPPORTED`, `PAGE_REVIEW_REQUIRED`, `REVISION_CONFLICT`, `VAULT_LOCKED`, `PROVIDER_UNAVAILABLE`, `TRANSFER_EXPIRED`, and `DESTINATION_FULL`. Raw provider responses, credentials, and source text must not leak into error messages.

## 16 Privacy and security

### Documented foundation and proposed verification

**[OBS]** Storage ADRs describe SQLCipher/Drift indexing, XChaCha20-Poly1305 chunked blobs, per-file keys, and Argon2id combined with device-protected material. Authentication documentation separates device identity/enrollment and proof of possession from local vault unlock. These descriptions are not proof that every current code path implements them correctly.

**[PROP]** Verify the actual cryptographic library, key lifecycle, authenticated metadata, nonce uniqueness, chunk ordering, tamper handling, and migration behavior. Use maintained implementations and reviewed protocols. Do not invent cryptography, replace an existing construction casually, or treat encryption as a substitute for authorization. Protect temporary files, thumbnails, search artifacts, crash reports, clipboard interactions, and exported copies as well as the main database.

The local device is the documented source of record. The gateway is not a backup. Project documentation calls for transient content processing rather than persistent server content, with bounded metadata/audit records; actual provider retention, training use, subprocessors, deletion behavior, and logging must be verified before making corresponding public promises.

### Data-flow and consent rules

- Local import/read/search requires no cloud upload. Cloud-only features disclose what content will be sent, to which service, for what purpose, and the relevant retention choice before transmission.
- Microphone, camera, wake-word mode, cloud processing, device transfer, and analytics are separate choices. Granting one does not grant the others.
- Limit provider requests to necessary pages, segments, or selected passages. Do not upload the full vault to summarize one document.
- Keep provider credentials gateway-side or in the established protected configuration. Never include secrets in client builds, repository examples, QR payload logs, or support bundles.
- Locking hides sensitive previews and stops new protected operations. Resume only after valid unlock. Biometric availability remains unresolved; provide the actually supported unlock route.
- No remote-wipe promise: revocation blocks future authorized interactions but does not erase an independently retained received copy or export.
- Deletion and cryptographic erasure require a documented lifecycle. Explain which backups, exports, provider copies, and recipient copies cannot be recalled. Do not promise secure overwrite on storage where it cannot be assured.

### Recovery limitations

**[OBS]** Implemented key recovery is not established; encrypted backup is a V2 proposal. Loss of device/profile/key material may make local encrypted data unrecoverable. Say this during setup and relevant destructive actions. Never offer reassuring “Recover account” copy as if it restores document keys.

**[PROP, later phase]** Design encrypted backup/restore with explicit key custody, supported restore environments, integrity checks, version migration, and a real restore drill. Until it passes, do not advertise it. Keeping an original source elsewhere can help re-import that source; it does not recover corrections, recordings, generated assets, or the vault itself.

### Threat-oriented review

Review malformed documents, resource exhaustion, hostile document instructions, untrusted local-network peers, replay, stolen locked devices, compromised gateway credentials, and accidental diagnostic disclosure. Enforce resource limits and isolation appropriate to parsers and converters. Provider timeouts and certificate failures must fail safely; users must never be guided to bypass transport security warnings to make pairing work.

## 17 Device pairing and text transfer

### Current link boundary

**[OBS]** The later device-link documentation describes certificate-DER pinning, custom length-prefixed possession proofs, persisted SQLite nonce tracking, and a 120-second clock-skew allowance. It describes ECDH/HKDF with ChaCha20-Poly1305 for the link payload. Older P-256 identity/enrollment ADRs provide context but their different proof, nonce-store, and timing details must not be mixed into a new “current” protocol.

The implementer must recover exact message serialization, versioning, authenticated fields, expiry, proof validation, receipt semantics, and test vectors from the real implementation. This specification deliberately does not invent a compatible binary protocol.

### Proposed transfer experience and acceptance

1. Initiate pairing explicitly. Show who is pairing, the matching 12-character code, expiry, and cancellation on both devices.
2. Establish trust only after required confirmation and cryptographic verification. QR scanning alone is not user confirmation of the peer.
3. Require the documented online and unlocked state. Distinguish unreachable, locked, expired, denied, and revoked.
4. Preview the recipient and **title/text-only** copy. Carry a visible coverage notice in the text when source content is incomplete; do not silently add raw PDFs/audio or unsupported metadata to the existing payload.
5. Use a stable transfer identity and integrity check. A retry after a lost acknowledgment reconciles the existing receipt before creating another copy.
6. Show Sent only after the recipient's required verification/persistence acknowledgment. “Request accepted” and “Delivered” are different states.
7. After restart/reconnect, revalidate trust and protocol state. Preserve a retryable operation without weakening replay protection or creating duplicates.
8. Revocation denies future requests. Keep or remove local received copies only through an explicit, separately described action.

A changed source creates a new copy/revision decision; it does not overwrite a recipient's independent edits silently. If deduplication is impossible under the current contract, disclose that limitation before retrying rather than inventing success.

### Future synchronization proposal

Audio/PDF transfer, playback-position synchronization, and multi-device editing are outside the documented link baseline. If later approved, define per-object versions, tombstones, identity/ownership, receipt durability, quotas, offline conflict handling, and migration first. Content conflicts preserve both versions for review. Playback conflicts compare meaningful event sequences and ask when sessions diverge; blindly picking the largest timestamp is insufficient. Do not label copy transfer as synchronization while these semantics are absent.

## 18 Reliability performance and recovery

Original sources and accepted revisions survive failed extraction, generation, export, and application restart. Use atomic writes, checksums where appropriate, resumable stage checkpoints, and safe cleanup of incomplete temporary files. A full disk must not corrupt the existing vault. Schema upgrades require a recoverable migration strategy consistent with actual key custody; a rollback of code must not open an incompatible database silently.

Proposed performance budgets must be measured on a declared Windows reference machine and representative Android device before adoption. Target immediate visible response to input, no long processing on the UI thread, and local indexed-search p95 below 500 ms on a declared 10,000-page synthetic corpus. Report hardware, corpus, build, cold/warm state, and sample size. Do not promise universal OCR throughput or transcription latency before measuring the chosen engines.

Use content-free observability: operation ID, stage, component/build version, duration, bounded counts, retry reason, and safe error code. Never log document titles, text, audio, search queries, prompts, provider credentials, pairing secrets, or sensitive local paths. Even metadata has retention and access limits. Diagnostics must be inspectable before the user shares them.

On startup, reconcile interrupted jobs with committed outputs. Offer Resume, Retry failed stage, or Dismiss as appropriate. Do not auto-retransmit content to a provider after the user cancelled or withdrew consent. A crash-recovery notice should say what was saved and what remains, without revealing content before unlock.

## 19 Testing and acceptance

### Evidence standard

Each test report identifies commit/build, platform/OS, device, engine/provider mode, synthetic fixture, expected result, actual result, and attached evidence. Separate unit, integration, simulator, and physical-device results. A checklist with unchecked boxes is not a PASS. Use synthetic or explicitly authorized data; public fixtures and screenshots must contain no real private documents.

### Required fixture families

Use Arabic-only, French-only, English-only, mixed Arabic/Latin, bilingual columns, rotated scans, faint/blurred pages, repeated headers/footers, footnotes, tables, blank/non-text pages, mixed scanned/digital PDFs, corrupt/encrypted files, long documents, interrupted audio, background noise, provider failure, low storage, and denied permissions. Include part counts above 9 to catch lexical sorting bugs.

### Executable acceptance cases

| ID | Scenario | Pass condition |
|---|---|---|
| DOC-01 | Import a mixed digital/scanned PDF | One source manifest; each page extracted once in original order |
| DOC-02 | A middle page fails | Visible gap and exact coverage; no silent complete status/export |
| DOC-03 | Repeated header resembles body text | Header handling is reviewable; body text and footnotes survive |
| DOC-04 | Arabic text on Windows | Shaping, logical storage, copy, search, and UTF-8 export remain correct |
| DOC-05 | Retry after manual correction | Correction preserved or compared; never silently overwritten |
| DOC-06 | Non-text page has no prose | Represented explicitly; source accessible; no invented explanation |
| AUD-01 | Stop in part 3, restart, resume | Correct revision/part and saved position; no autoplay |
| AUD-02 | Navigate during playback | One player continues; selected passage and focus remain usable |
| AUD-03 | Edit text while old audio exists | Old audio labeled stale; current playback not silently mutated |
| AUD-04 | Export 12 parts individually/combined | Numeric order, correct coverage, valid files, no silent overwrite |
| AUD-05 | Call/audio-focus loss | Position saved; safe resumption; no duplicate media owners |
| VOI-01 | Windows Arabic wake/navigation | Device-specific corpus meets agreed threshold; recognized intent shown |
| VOI-02 | Android assistant | Explicit initiation; no unsupported wake/background claim |
| STT-01 | Microphone revoked mid-capture | Saved-through timestamp and gap visible; confirmed segments survive |
| SEA-01 | Arabic normalized match | Original spelling and correct highlighted source offsets preserved |
| SEA-02 | Partial/stale index | Limitation visible; results open correct revisions and anchors |
| AI-01 | Question unsupported by sources | Clear insufficiency response; no invented citation |
| AI-02 | Malicious instruction inside PDF | No expanded access, external action, or secret disclosure |
| AI-03 | Summary of incomplete source | Exact source coverage before generation and on answer |
| LNK-01 | Pair correct/wrong/expired codes | Both-device approval enforced; wrong/expired attempt fails safely |
| LNK-02 | Send selected document | Only documented title/text payload; recipient verified receipt |
| LNK-03 | Disconnect after receive before acknowledgment | Retry reconciles state; no unexplained duplicate |
| LNK-04 | Restart, lock, revocation, replay | Required revalidation and access denial; originals remain intact |
| SEC-01 | Inspect build and diagnostics | No credentials, content, unsafe plaintext cache, or private fixtures |
| REC-01 | Kill process during durable write | Prior committed state intact; interrupted job recoverable |
| A11Y-01 | Keyboard/Narrator/TalkBack journey | Import, review, read, play, search, export reachable and understandable |
| A11Y-02 | 200% text/RTL/high contrast | No blocked action, hidden focus, clipped critical text, or direction error |

Voice and extraction quality thresholds must be agreed using representative corpora. Record character/word error measures where meaningful, but also test reading order, omissions, wrong-language output, and false activations. Do not average away a catastrophic missing-page failure with a good global score.

## 20 Delivery plan and release gates

### Phase 0 Establish the baseline

Inventory actual client/gateway source, builds, dependencies, supported platforms, existing assets, protocol versions, and tests. Resolve documentary contradictions without discarding prior results. Produce a capability matrix and reproducible smoke run. Approve the token system and identify protected existing behaviors. Exit when work can be scoped against evidence rather than guesses.

### Phase 1 Complete the document journey

Implement or refine Library → Import → Page review → Reader → Player → Export. Deliver Arabic/French/English, visible coverage, stable parts, last-position recovery, and accessible interactions together. Keep the current link behavior compatible. Exit with the document/audio acceptance cases passed on the target platforms and no unexplained missing content.

### Phase 2 Complete bounded speech and assistance

Deliver recorded transcription, supported live captions, grounded summaries/search, and Windows voice-command quality as complete slices. Android retains its actual lifecycle limits. Exit with microphone/provider consent, interruption recovery, grounding, and language tests passed. Existing capabilities may be hardened earlier rather than withheld artificially.

### Phase 3 Harden companion and release

Verify authenticated text-copy transfer on physical devices, restart/reconnect, lock/revocation, receipt idempotency, packaging, clean install/upgrade, and support diagnostics. Complete accessibility and security review of the shipped scope. Exit with a signed-off release evidence bundle and accurate known limitations.

### Later proposals

Evaluate encrypted backup/restore before promising recoverability. Consider audio/PDF transfer, position sync, advanced annotations, and broader platforms only through separately accepted requirements. iOS, web, billing, and organizations remain independent projects, not hidden dependencies of the desktop/Android release.

### Release gate

The release owner signs off a specific artifact, not a general “ready” statement. Required evidence includes automated tests, physical Windows/Android acceptance for enabled features, security/privacy review, accessibility review, translation review, clean install/upgrade, migration recovery, package/signing policy, dependency/license review, and an accurate capability/limitation list. Record each skipped test and block claims that depend on it. Public documentation must distinguish current features from preview and planned work.

## 21 Decisions requiring confirmation

| Decision | Proposed default | Must be resolved before |
|---|---|---|
| Official logo and palette | Preserve recovered approved assets; use this token proposal only after review | Final visual implementation |
| Current client/build and link deployment | Inspect and reproduce; do not infer from reports | Integration/release claims |
| Biometrics | Show only verified supported unlock options | Unlock UI acceptance |
| OCR/STT/TTS/AI engines | Retain compatible working adapters first | Performance/privacy commitments |
| Provider retention and routing | Explicit choice, minimal content, no silent fallback | First external processing |
| Supported formats and file limits | Document measured limits per platform | Import/export availability |
| Recovery and backup | Honest current limitation; V2 design separately | Recovery marketing or destructive guidance |
| Arabic voices and normalization | Native-speaker evaluation and reversible indexing | Language acceptance |
| Voice/quality thresholds | Representative corpus and device-specific gates | Feature readiness claim |
| Distribution and licensing | Owner-approved package/license policy | Public release, not merely this design document |

These are real decision points, not permission to leave implemented controls ambiguous. Until resolved, gate the affected capability and explain the limitation.

## 22 Bounded implementation brief

The following prompt can guide a future implementation task. It authorizes no deployment, credentials, publication, or destructive operations by itself.

> Work on the existing YATIME Windows/Android codebase using this specification as the proposed target. First inspect the repository instructions, current Flutter/Dart client, Rust core, gateway contracts, documented device-link protocol, assets, and tests. Report a concise evidence-based capability matrix and the next complete vertical slice. Preserve working behavior and user data. Do not replace the framework or cryptographic protocol merely to match a preferred stack.
>
> Implement the approved slice end to end with accessible Arabic/French/English UI, one token system, explicit page coverage, source-linked revisions, stable ordered parts, and recoverable job states. Keep the persistent player and last-position behavior correct. Preserve title/text-only companion transfer unless an expanded protocol is explicitly approved. Treat wake word as Windows-only and respect Android capture/export limitations.
>
> Add synthetic fixtures and automated tests for the relevant acceptance IDs. Run the available tests, then state which physical-device checks remain. Verify the actual screens at compact/expanded widths, RTL, and 200% text. Keep secrets and user content out of logs, commits, and screenshots. Label proposed contracts and unresolved decisions clearly.
>
> Stop at a reviewable change set and evidence report. Explain implemented scope, tests run, screenshots or artifacts, known limitations, migration impact, and the next smallest step. Ask before actions requiring new credentials, paid services, security changes, destructive migration, external transmission, deployment, or publication. Never claim a PASS for a check that was not performed.

## 23 Reusable definition of done

Use this checklist for each feature and again for the release. Check boxes only with evidence.

- [ ] Scope and platform availability match the approved slice.
- [ ] Current behavior, historical reports, and new proposals remain distinguishable.
- [ ] The feature has complete loading, empty, success, partial, denied, offline, and failure states where relevant.
- [ ] Original sources are preserved; all pages/segments are accounted for.
- [ ] Revisions, citations, parts, search results, playback, and export agree.
- [ ] No silently omitted content or fabricated extraction/AI evidence exists.
- [ ] Restart, cancellation, retry, low-storage, and interruption behavior are tested.
- [ ] Arabic/French/English and mixed-script layout are reviewed.
- [ ] Keyboard, screen-reader, text-scaling, focus, contrast, and touch behavior pass.
- [ ] Microphone, provider, and recipient boundaries are visible and respected.
- [ ] Device transfer is accurately described and receipt/retry behavior verified.
- [ ] No content/secrets enter logs, fixtures, screenshots, or public examples.
- [ ] Recovery limitations and unavailable features are honest.
- [ ] Automated and physical-device evidence are separately recorded.
- [ ] Documentation, release notes, and known limitations match the actual artifact.

A beautiful screen with incomplete behavior is unfinished. A technically passing workflow with unclear omissions, inaccessible controls, or misleading privacy claims is also unfinished. YATIME is ready when people can understand its state, trust its source coverage, and recover their work through the full supported journey.

## 24 References and document maintenance

Project-specific evidence is limited to the four named documents in section 2 and historical owner-supplied requirements/reports. They are not presented as public links, current code audit results, or proof of deployment.

Primary implementation references, checked for this specification:

- [W3C WCAG 2.2](https://www.w3.org/TR/WCAG22/) and [contrast guidance](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
- [Flutter accessibility](https://docs.flutter.dev/ui/accessibility) and [internationalization](https://docs.flutter.dev/ui/internationalization)
- [W3C directionality guidance](https://www.w3.org/International/questions/qa-html-dir) and [bidirectional inline examples](https://www.w3.org/International/articles/inline-bidi-markup/bidi_examples.en)

Update this specification when a decision changes, a protocol evolves, or new implementation evidence supersedes a historical report. Record the document version, reason, affected requirements, and acceptance impact. Do not relabel a proposal as shipped without build-specific evidence. This file does not choose the repository's software or documentation license; that remains an explicit owner decision.
