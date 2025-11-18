# MIGAxBM - Epic Breakdown

**Author:** Doughy
**Date:** 2025-11-18
**Project Level:** beginner
**Target Scale:**

---

## Overview

This document provides the complete epic and story breakdown for MIGAxBM, decomposing the requirements from the [PRD](./PRD.md) into implementable stories.

**Living Document Notice:** This is the initial version. It will be updated after UX Design and Architecture workflows add interaction and technical details to stories.

### Epic Summary

**Epic 1: Foundation & Onboarding** - Establishes app setup and gateway mechanism, enables first mindful moments

**Epic 2: Mindful Gateway & Hub** - Core user interaction for social media interception and conscious choice

**Epic 3: Content & Analytics** - Delivers personalized mindfulness content and usage insights

**Epic 4: Settings & Configuration** - Provides user control over timer preferences and app management

---

## Functional Requirements Inventory

FR1: App Onboarding Experience
- Welcome sequence introducing MIGA's purpose and mission

FR2: Hub Screen & App Shortcuts
- Grid display of selected social app shortcuts
- Tapping launches mindful gateway intercept

FR3: Gateway Screen & Mindfulness Timer
- 15-45 second configurable timer with content display
- Three actions: Exit (returns to home), Read Next (resets timer), Proceed (launches app)

FR4: Content Management System
- Firebase Firestore content collection with caching
- Random non-repeating rotation of quotes/articles
- Offline fallback to local bundle content

FR5: Analytics & Usage Tracking
- Firebase + Core Data metrics collection
- Track gateway completions, exits, content reads, streaks
- Display statistics: hours reclaimed, intentionality score

FR6: Settings & Configuration
- Timer duration preferences, app list management
- Monetization settings, content category preferences

FR7: Social App Integration
- Custom URL scheme launching for Instagram, TikTok, Facebook
- Graceful handling when apps not installed

FR8: Performance & Quality Standards
- <1s screen load times, <1 crash per 100 sessions
- Accessibility compliance (WCAG 2.1 AA)

---

## FR Coverage Map

Foundation & Onboarding (FR1): Covers setup and initial activation
Mindful Gateway & Hub (FR2, FR3, FR7): Addresses core mindful interception experience
Content & Analytics (FR4, FR5): Provides content delivery and usage insights
Settings & Configuration (FR6): Enables user preferences and app management
Foundation & Onboarding (FR8): Establishes performance baseline

---

## Epic 1: Foundation & Onboarding

Enable new users to set up MIGA and understand the mindful gateway mechanism, establishing the foundation for conscious digital habits.

### Story 1.1: Onboarding Flow

As a new user discovering mindful technology,
I want to experience a 12-step value-driven onboarding sequence,
So that I understand MIGA's purpose and benefits before my first gateway experience.

**Acceptance Criteria:**

**Given** user has never opened MIGA before
**When** user launches app for first time
**Then** welcome screen displays with "The Intentional Life Starts Now"

**And** when user taps "Begin My Journey"
**Then** step-by-step value proposition flow guides through: time awareness, behavioral goals, credibility through science, risk-free trial

**And** when user completes commitment slider and content interests selection
**Then** proceeds to app hub with first-run experience overlay

**Prerequisites:** None

**Technical Notes:** Implement 12 sequential onboarding screens using NavigationStack with enum-based routing, persisting completion state in UserDefaults, include personalization elements (readiness slider, content tag selection)

### Story 1.2: Project Infrastructure Setup

As a new user setting up MIGA for first time,
I want the app to initialize core infrastructure automatically,
So that my device is ready for mindful gateway experiences.

**Acceptance Criteria:**

**Given** user is in app setup phase
**When** user completes onboarding and enters first-run experience
**Then** initialize Core Data schema for analytics and content caching

**And** configure Firebase anonymous authentication
**Then** prepopulate 10-20 default mindfulness content entries in local cache

**And** validate compatible social apps are available for selection
**Then** present hub readiness confirmation

**Prerequisites:** Complete Story 1.1

**Technical Notes:** Core Data initialization with content and analytics entities, Firebase remote config for feature flags, validate URL scheme compatibility, establish Combine timer services

### Story 1.3: Social App Hiding Setup

As a new user in first-run experience,
I want guided instructions to manually hide selected social app icons,
So that MIGA can intercept launches and create mindful pause opportunities.

**Acceptance Criteria:**

**Given** user selects social apps to monitor in F.R.E. flow
**When** user proceeds to Phase 2 setup
**Then** display platform-specific app hiding instructions for iOS

**And** guide through: "Exit MIGA to Home Screen", app icon security setup, Face ID/Passcode configuration
**Then** confirm hiding completion and return to MIGA hub

**And** verify interception works when tapping hidden app icon
**Then** hub becomes fully functional with active gateway protection

**Prerequisites:** Complete Story 1.2

**Technical Notes:** iOS Face ID/biometric passcode enforcement, custom URL scheme registration for social apps, graceful fallback handling

## Epic 2: Mindful Gateway & Hub

Deliver the core mindful interception experience that lets users pause and reflect before accessing social media.

### Story 2.1: App Hub Display

As a returning user seeking mindful access,
I want to see my selected social app shortcuts in an organized hub,
So that I can consciously choose which social media to engage with.

**Acceptance Criteria:**

**Given** user has completed setup and hiding process
**When** user opens MIGA
**Then** display grid of selected social app icons with labels

**And** show motivational banner: "🔥 [X]-Day Control Streak. Keep the momentum going!"
**Then** tapping any app icon triggers gateway screen modal

**And** hub supports 2-10 app shortcuts with consistent 44x44px touch targets per WCAG 2.1 AA
**Then** hub loads in <1 second and dynamically updates based on usage analytics

**Prerequisites:** Complete Epic 1 Stories 1.1-1.3

**Technical Notes:** SwiftUI grid layout with @StateObject for hub state, Core Data streak calculation, gesture recognition for app launches, optimized for iOS 17+ performance

### Story 2.2: Gateway Timer Experience

As a user triggered by attempting social media access,
I want to experience a 15-45 second mindful pause with content,
So that I can reflect and make a conscious choice before proceeding.

**Acceptance Criteria:**

**Given** user taps social app shortcut in hub
**When** gateway modal appears
**Then** display circular progress timer starting from configured duration (default 30s)

**And** show mindfulness content (quote/article) in largest readable font with calming typography
**Then** present three action buttons: Exit (enabled immediately), Read Next (resets timer), Proceed (disabled until timer = 0)

**And** when timer completes
**Then** proceed button enables showing "Proceed to [App Name]"

**And** tapping proceed launches external app via URL scheme
**Then** gateway tracks completion event and updates streak metrics

**Prerequisites:** Complete Story 2.1

**Technical Notes:** Timer.publisher with Combine for reactive countdown, full-screen .sheet() presentation, custom circular progress component, Firebase event tracking

### Story 2.3: Gateway Action Handling

As a user in the mindful pause moment,
I want three clear choices that validate my conscious decision,
So that my action reinforces or celebrates intentional behavior.

**Acceptance Criteria:**

**Given** gateway timer is active
**When** user can tap actions at any time
**Then** "Exit to Focus" immediately closes gateway and returns to device home screen

**And** trigger "Control Reclaimed. That's a Win." celebration animation (UX spec appendix C.2)
**Then** log exit event and increment daily streak counter

**And** "Deepen the Pause" resets timer and loads new content
**Then** tracks content consumption for personalization recommendations

**And** "Proceed to [App Name]" only enables when timer reaches zero
**Then** executes URL scheme launch for targeted social app

**Prerequisites:** Complete Story 2.2

**Technical Notes:** URL scheme execution with error handling for uninstalled apps, Core Data event persistence, SwiftUI animations for celebration states, Combine publishers for timer state management

### Story 2.4: Social App Integration

As a user with diverse social media habits,
I want reliable launching of my selected platforms,
So that the gateway seamlessly connects to my actual social media usage.

**Acceptance Criteria:**

**Given** MIGA is configured for multiple social platforms
**When** user triggers proceed action from gateway
**Then** launch Instagram, TikTok, Facebook, or other selected apps via custom URL schemes

**And** if app is not installed
**Then** show graceful error message and return to hub

**And** track launch success/failure for analytics
**Then** accurately measure time from gateway completion to actual app usage

**And** support minimum viable set plus extensible configuration
**Then** URL scheme validation occurs during setup process

**Prerequisites:** Complete Story 2.3

**Technical Notes:** Custom URL scheme implementation with UIApplication.shared.open(), error handling for App Store redirects, launch validation testing

## Epic 3: Content & Analytics

Deliver personalized, cached mindfulness content and meaningful usage insights that help users understand their digital habits.

### Story 3.1: Content Caching Setup

As a user wanting offline mindfulness support,
I want relevant quotes and articles cached locally,
So that I receive meaningful content even without internet connection.

**Acceptance Criteria:**

**Given** Firebase Firestore content collection exists
**When** app initializes or syncs
**Then** download and cache 20 mindfulness content items with metadata

**And** store locally using Core Data with content type, text, category, and length
**Then** implement random non-repeating rotation algorithm

**And** when offline
**Then** fallback immediately to cached content without UI disruption

**And** content syncs automatically when connectivity returns
**Then** support categories: stoicism, productivity, mindfulness, motivation, reflection

**Prerequisites:** Complete Epic 1 infrastructure setup

**Technical Notes:** Firestore collection structure (/content), Core Data content entity schema, background sync with Combine, offline detection using NWPathMonitor

### Story 3.2: Usage Analytics Dashboard

As a user interested in my mindful habits,
I want to see meaningful statistics about my behavior,
So that I can track progress and stay motivated.

**Acceptance Criteria:**

**Given** gateway usage across multiple sessions
**When** user views Statistics tab
**Then** display lifetime metrics: total conscious exits, gateway completions, content reads

**And** show calculated metrics: intentionality score, hours reclaimed, streak counters
**Then** include trend chart with daily conscious exits vs. daily attempts ratio

**And** display proof point: "This is working" header with quantitative impact
**Then** metrics load quickly and update in real-time after gateway sessions

**Prerequisites:** Complete Stories 2.1-2.2 for event generation

**Technical Notes:** Core Data analytics queries with NSPredicate, SwiftUI Charts for trend visualization, computed property calculations for derived metrics, @Published reactive updates

### Story 3.3: Analytics Event Tracking System

As an organization measuring behavior change impact,
I want comprehensive anonymous usage data,
So that we can validate the mindfulness intervention effectiveness.

**Acceptance Criteria:**

**Given** user interactions throughout gateway flow
**When** events occur
**Then** track Firebase Analytics + local Core Data mirror for:

**Analytics Events:**
session_start (app launch), gateway_completed (timer finish), exit_selected, open_selected, content_read, content_reread, streak_started, streak_broken

**And** include metadata: session_duration, content_category, timer_length, app_target
**Then** support offline event queuing with upload when connected

**And** mirror all events locally for user-facing statistics
**Then** enable feature flag control for additional experimental metrics

**Prerequisites:** Complete Story 3.2 dashboard for data consumption

**Technical Notes:** Firebase Analytics event logging with custom parameters, Core Data event entity with relationship to sessions, background upload task for offline.queue, privacy-safe event structure

## Epic 4: Settings & Configuration

Deliver user control over gateway behavior, content preferences, and app management.

### Story 4.1: Timer Configuration

As a user with personalized mindfulness needs,
I want to adjust gateway timer duration,
So that the pause length matches my comfort and effectiveness preferences.

**Acceptance Criteria:**

**Given** configurable timer requirements from UX
**When** user accesses Settings screen
**Then** provide 15-45 second range selector with step increments

**And** save preference immediately to UserDefaults
**Then** gateway respects user choice for all future sessions

**And** display current setting with clear labeling
**Then** optional preview shows what selected duration looks like

**Prerequisites:** Complete Epic 2 Stories for timer implementation

**Technical Notes:** UserDefaults persistence with @AppStorage for reactive updates, validation for min/max ranges, immediate effect on active gateway if running

### Story 4.2: App Selection Management

As a user refining digital boundaries,
I want to modify which apps trigger mindfulness,
So that I can adapt my protection scope as habits and needs change.

**Acceptance Criteria:**

**Given** initial app selection from onboarding
**When** user accesses settings screen
**Then** display current selected apps with ability to add/remove

**And** provide searchable list of supported social platforms
**Then** additions require hiding setup confirmation

**And** removals update hub layout immediately
**Then** support minimum 1 app, maximum platform-dependent limit

**Prerequisites:** Complete Story 2.1 hub implementation

**Technical Notes:** Pill-box style SwiftUI selection UI, app availability validation, hiding workflow re-trigger for new apps, hub grid layout recalculation

### Story 4.3: Content Preferences

As a user seeking resonant mindfulness,
I want to prioritize content categories,
So that quotes and articles align with my personal growth interests.

**Acceptance Criteria:**

**Given** available content categories
**When** user configures preferences
**Then** provide category selection interface for: stoicism, productivity, mindfulness, motivation, reflection

**And** influence content rotation algorithm weighting
**Then** save preferences locally and sync to Firebase if available

**And** categories become active immediately in gateway rotation
**Then** support multiple category selection for mixed content streams

**Prerequisites:** Complete Story 3.1 content caching

**Technical Notes:** UserDefaults array storage, content filtering logic enhancement, RandomAccessCollection manipulation for weighted selection

---

## FR Coverage Matrix

FR1: App Onboarding Experience → Epic 1, Story 1.1
FR2: Hub Screen & App Shortcuts → Epic 2, Story 2.1
FR3: Gateway Screen & Mindfulness Timer → Epic 2, Stories 2.2-2.3
FR4: Content Management System → Epic 3, Story 3.1
FR5: Analytics & Usage Tracking → Epic 3, Stories 3.2-3.3
FR6: Settings & Configuration → Epic 4, Stories 4.1-4.3
FR7: Social App Integration → Epic 2, Story 2.4
FR8: Performance & Quality Standards → Epic 1, Story 1.2 (infrastructure), all epics (implementation requirements)

---

## Summary

**FR Coverage:** All 8 functional requirements from PRD mapped to specific stories across 4 user-value epics
**Context Incorporated:** PRD requirements with UX design interaction patterns and architecture technical decisions
**Structure:** Epic-first approach ensuring incremental user value delivery, proper story sequencing within epics
**Ready for Phase 4:** Epic breakdown complete with detailed BDD acceptance criteria and implementation guidance

---

_For implementation: Use the `create-story` workflow to generate individual story implementation plans from this epic breakdown._

_This document will be updated after UX Design and Architecture workflows to incorporate interaction details and technical decisions._
