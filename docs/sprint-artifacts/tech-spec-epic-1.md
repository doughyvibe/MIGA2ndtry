# Epic Technical Specification: Foundation & Onboarding

Date: 2025-11-18
Author: Doughy
Epic ID: epic-1
Status: Draft

---

## Overview

Epic 1 establishes the foundational user experience for MIGA (Mindful Gateway to Social Media), focusing on the critical first interactions that transform new users into engaged participants in the intentional digital wellbeing journey. As outlined in the PRD, this epic addresses the "App Onboarding Experience" functional requirement (FR1) by creating a comprehensive 12-step value-driven onboarding sequence that introduces MIGA's mission of conscious digital habits while setting up the technical infrastructure for mindful gateway interception.

The epic enables users to experience their first "mindful moments" by establishing app setup processes (Core Data initialization, Firebase configuration) and guiding manual configuration of iOS app-hiding mechanisms using Face ID/Passcode security. This creates the essential behavioral intercept that serves as the foundation for all subsequent gateway experiences, ensuring users understand the value of intentional technology before their first conscious social media choice.

## Objectives and Scope

### In Scope
- Complete 12-screen onboarding sequence with value-driven content and user personalization
- Core Data schema initialization for analytics content caching during first-run setup
- Firebase anonymous authentication configuration for content and analytics access
- Social app selection and platform compatibility validation
- iOS-specific app hiding workflow with Face ID/Passcode integration
- Robust error handling and fallback messaging for unsupported environments
- User preferences persistence and onboarding completion state tracking

### Out of Scope
- Gateway timer and mindfulness content display functionality
- App hub grid and social media shortcut management
- Analytics dashboard and metrics calculations
- Settings configuration screens beyond first-run setup
- External URL scheme execution and app launching
- Advanced personalization or content filtering algorithms

## System Architecture Alignment

The implementation leverages the selected SwiftUI + MVVM + ObservableObject pattern established in the architecture decision records, with OnboardingModule handling the 12-step sequence through NavigationStack-based routing and enum-driven state progression. Core infrastructure setup directly maps to the ContentModule and AnalyticsModule foundation, utilizing Core Data for local persistence and Firebase for remote capabilities as specified in AD005 (Content Caching Strategy) and AD003 (Data Persistence Strategy). The app hiding mechanism aligns with AD004 (Navigation Architecture) through programmatic modal presentations, ensuring the iOS security model integration follows the authentication patterns defined in the architecture document.

## Detailed Design

### Services and Modules

| Module/Service | Responsibilities | Key Methods | Dependencies |
|----------------|------------------|-------------|--------------|
| **OnboardingModule** | Manages 12-step onboarding sequence with state persistence | `nextStep()`, `completeOnboarding()`, `getCurrentStep()` | UserDefaults, NavigationStack |
| **InfrastructureSetupService** | Handles Core Data schema initialization and Firebase configuration | `initializeCoreData()`, `configureFirebase()`, `validateAppCompatibility()` | Core Data, Firebase SDK |
| **AppHidingService** | Provides iOS-specific app hiding instructions and validation | `generateHidingInstructions()`, `validateHidingCompletion()`, `checkBiometricAvailability()` | LocalAuthentication framework |
| **ContentCacheManager** | Pre-loads initial mindfulness content entries | `preloadDefaultContent()`, `setupContentSchema()` | Core Data, Firebase Firestore |
| **UserPreferencesService** | Manages onboarding personalization selections | `saveOnboardingPreferences()`, `loadUserSelections()` | UserDefaults |

### Data Models and Contracts

#### Core Data Entities

**ContentCache** Entity:
- Fields: `id` (UUID), `type` (String), `text` (String), `category` (String), `length` (Int16), `cachedAt` (Date)
- Relationships: None
- Usage: Stores pre-loaded mindfulness content for offline access

**OnboardingState** Entity:
- Fields: `completionDate` (Date), `currentStep` (Int16), `readinessScore` (Int16, optional)
- Relationships: One-to-many with `SelectedCategories`
- Usage: Tracks onboarding progress and personalization data

**SelectedCategories** Entity:
- Fields: `category` (String), `selectedAt` (Date)
- Relationships: Many-to-one with `OnboardingState`
- Usage: Stores user's content preference selections from onboarding

#### UserDefaults Contracts

```swift
struct OnboardingPreferences {
    var isOnboardingComplete: Bool
    var onboardingCompletedAt: Date?
    var selectedCategories: [String]
    var readinessScore: Int?
}
```

### APIs and Interfaces

#### Firebase Firestore Integration

**Content Collection API**:
- Collection: `/content`
- Document Structure: `{type: string, text: string, category: string, length: number}`
- Read Operation: Anonymous auth, cached locally, offline fallback to bundle content
- Usage: Pre-load 10-20 entries during infrastructure setup

**Remote Config API**:
- Config Keys: `content_sync_enabled`, `onboarding_flow_version`
- Fallback Values: Content sync disabled, version "1.0"
- Usage: Feature flag control during setup phase

#### iOS System APIs

**Local Authentication Framework**:
```swift
enum BiometricType {
    case faceID
    case touchID
    case passcode
    case none
}

func canEvaluatePolicy(_ policy: LAPolicy,
                      error: NSErrorPointer) -> Bool
```
- Usage: App hiding setup validation and biometric availability checks

### Workflows and Sequencing

#### Story 1.1: Onboarding Flow Sequence

```
1. App Launch → First Run Detection
2. WelcomeView ("The Intentional Life Starts Now")
3. OnboardingStepView (12 enumerated steps):
   - Core Mission → Value Proposition
   - Time Wasted → Quantify Problem
   - Time Saved → Quantify Benefit
   - Behavioral Goal → Motivation
   - Credibility → Authority Building
   - Trial Policy → Anxiety Reduction
   - Determination → Readiness Slider (1-10)
   - Content Interest → Category Selection
   - Journey → Flow Explanation
   - Final Confirm → Completion
4. NavigationStack enum-based routing with @State path management
5. UserDefaults persistence on each step completion

Critical Path: Block hub access until onboarding.isComplete == true
```

#### Story 1.2: Infrastructure Setup Flow

```
1. Onboarding Complete → Infrastructure Setup Trigger
2. Core Data Context Creation
   → ContentCache entity schema validation
   → Analytics entities preparation
3. Firebase Anonymous Authentication
   → Privacy-safe UID generation
   → Remote config fetch with fallback
4. App Compatibility Validation
   → URL scheme support detection
   → Platform capability checks
5. Initial Content Preloading
   → Firestore /content collection query
   → Local Core Data cache population (10-20 entries)
6. Setup Completion Validation
   → All services operational confirmation
   → Hub readiness flag setting

Error Handling: Graceful degradation, setup retry capability, user guidance on failures
```

#### Story 1.3: App Hiding Setup Flow

```
1. Hub First Run Experience (F.R.E.) detection
2. Phase 1: App Selection
   → Display supported social apps (Instagram, TikTok, Facebook, etc.)
   → Pill-box selection UI with minimum 1 app requirement
   → UserDefaults persistence of selected apps
3. Phase 2: Manual Hiding Instructions
   → iOS-specific Face ID/Passcode guidance
   → Step-by-step Home Screen manipulation instructions
   → App closure to enable manual setup
4. Post-Hiding Validation
   → Return-to-app detection
   → Icon verification and accessibility confirmations
   → Gateway interception capability testing
5. Hub Activation
   → Full functionality enablement
   → Motivational banner display with streak initialization

Fallback: Unsupported device handling, alternative setup instructions for non-standard iOS configurations
```

## Non-Functional Requirements

### Performance

- **Onboarding Flow Load Time**: Initial onboarding sequence screens must load in <1 second after app launch
- **Infrastructure Setup Duration**: Core Data schema initialization and Firebase configuration must complete within 3 seconds during first-run setup
- **Content Preloading Time**: Must cache 10-20 content entries within 2 seconds during infrastructure setup phase
- **App Compatibility Validation**: URL scheme and biometric capability checks must complete within 500ms
- **Memory Usage**: Onboarding screens must maintain <50MB memory footprint across the full sequence
- **Core Data Write Performance**: Onboarding state persistence operations must complete in <100ms

### Security

- **Authentication**: Implement Firebase anonymous authentication for content access with privacy-safe UID generation (no personally identifiable information)
- **Data Protection**: All local user selections (readiness scores, category preferences) stored via iOS Data Protection API with NSFileProtectionComplete
- **Biometric Authorization**: Face ID/Touch ID usage follows Apple's Local Authentication framework best practices with proper error handling
- **Remote Config Security**: Firebase Remote Config parameters treated as untrusted input with local validation and fallback defaults
- **Privacy Compliance**: No user data stored remotely, local-only identifiers for analytics tracking
- **Certificate Pinning**: Firebase connections should implement SSL certificate validation (inherited from Firebase SDK defaults)

### Reliability/Availability

- **Setup Failure Resilience**: Infrastructure setup must gracefully handle Firebase service unavailability with local-only fallback mode
- **Content Fallback**: Robust offline capability with bundled default content if Firestore connectivity fails during initial preload
- **Biometric Fallback**: Graceful degradation to passcode-only app hiding setup if Face ID/Touch ID unavailable
- **Data Integrity**: Core Data transactions ensure atomic onboarding state changes with rollback capability on setup failures
- **Recovery Behavior**: Failed app hiding setup provides clear retry instructions without losing onboarding progress
- **Platform Compatibility**: Detect and handle iOS version compatibility issues with appropriate user messaging

### Observability

- **Onboarding Analytics**: Track completion rates, drop-off points, and user flow progression through Firebase Analytics
- **Setup Performance Metrics**: Measure infrastructure setup duration, success/failure rates, and common failure points
- **User Behavior Signals**: Monitor readiness score distributions, selected category preferences, and biometric setup choices
- **Error Tracking**: Implement structured error logging for infrastructure failures with user impact assessment
- **App Launch Metrics**: Track time-to-interactive after onboarding completion and hub readiness status
- **Platform Logs**: iOS system logs integration for biometric authentication failures and app hiding state validation

## Dependencies and Integrations

### External Dependencies (Swift Package Manager)

| Dependency | Version Constraint | Purpose | Criticality |
|------------|-------------------|---------|-------------|
| **Firebase iOS SDK** | `10.x` | Anonymous authentication, Firestore content access, Remote Config, Analytics | Critical - Blocks content preloading and analytics tracking |
| **Firebase/Firestore** | Included in SDK 10.x | Real-time content syncing and offline caching | Critical - Core MVP feature dependency |
| **Firebase/RemoteConfig** | Included in SDK 10.x | Feature flag management (content_sync_enabled, onboarding_flow_version) | Medium - Graceful fallback to local defaults |
| **Firebase/Analytics** | Included in SDK 10.x | User behavior tracking and onboarding completion metrics | Medium - Local Core Data mirror provides backup |

### iOS System Frameworks

| Framework | Purpose | Integration Point | Version Requirement |
|-----------|---------|------------------|-------------------|
| **SwiftUI** | Primary UI framework for onboarding sequence | NavigationStack enum-based routing, step progression views | iOS 17.0+ |
| **Core Data** | Local data persistence for analytics and content caching | Schema initialization, onboarding state tracker, content preloader | iOS 17.0+ |
| **LocalAuthentication** | Biometric authentication for app hiding validation | Face ID/Touch ID capability detection, LAContext evaluation | iOS 17.0+ |
| **Combine** | Reactive programming for async operations | Infrastructure setup progress monitoring, Firebase operation handling | iOS 17.0+ |
| **UserDefaults** | Light-weight preference and state persistence | Onboarding completion flags, user selections storage | iOS 17.0+ |

### Integration Points

#### Firebase Services Integration
- **Anonymous Authentication Flow**: Automatic initialization during infrastructure setup with retry logic (handles network unavailability)
- **Firestore Content Collection**: `/content` collection dependency with offline-first caching strategy
- **Remote Config Synchronization**: Feature flag fetching with local fallback values and validation
- **Analytics Event Tracking**: Structured event logging for onboarding progression and setup metrics

#### iOS Platform Integrations
- **Biometric Authentication**: Seamless integration with Face ID/Touch ID for app hiding workflow validation
- **URL Scheme Compatibility**: Runtime validation of social app URL scheme availability
- **Data Protection**: Automatic NSFileProtectionComplete encryption for all local user data
- **Background Task Scheduling**: Content sync and retry operations during app lifecycle transitions

#### Third-Party Service Dependencies
- **Firebase Infrastructure**: Requires active Firebase project with Firestore, Remote Config, and Analytics enabled
- **iOS Developer Program**: App Groups entitlement for potential future cross-app data sharing
- **URL Scheme Registration**: Custom URL scheme declarations in project configuration for app interception

### Dependency Resolution Strategy

#### Build-Time Dependencies
- Swift Package Manager resolves Firebase SDK during project build
- Framework integrations validated during compilation and linking phases

#### Runtime Dependencies
- Firebase services availability checked at runtime with graceful degradation
- Biometric capabilities dynamically detected with fallback to passcode-only workflows
- Content delivery prioritizes local cache over network-dependent Firebase calls

### Compatibility Matrix

- **iOS Target**: 17.0+ (required for latest SwiftUI NavigationStack and Combine features)
- **Firebase SDK**: v10.x minimum for optimized Swift concurrency support
- **Xcode Version**: 15+ for Swift 5.9+ and package manager compatibility
- **Deployment Target**: iOS 17.0+ to ensure all framework features are available

## Acceptance Criteria (Authoritative)

### Story 1.1: Onboarding Flow
1. **Onboarding Entry**: Given user has never opened MIGA before, when user launches app for first time, then welcome screen displays with "The Intentional Life Starts Now"
2. **Value Journey Start**: Given user taps "Begin My Journey", when flow begins, then step-by-step value proposition guides through time awareness, behavioral goals, credibility through science, risk-free trial
3. **Personalization Completion**: Given user completes commitment slider and content interests selection, when proceeding, then app hub becomes accessible with first-run experience overlay

### Story 1.2: Project Infrastructure Setup
1. **Core Data Initialization**: Given user completes onboarding, when entering first-run experience, then Core Data schema initializes for analytics and content caching
2. **Firebase Authentication Setup**: Given infrastructure initialization, when configuring authentication, then anonymous Firebase authentication establishes successfully
3. **Content Preloading**: Given Firebase connection established, when prepopulating cache, then 10-20 default mindfulness content entries load into local cache
4. **App Compatibility Validation**: Given content preloading complete, when validating social apps, then compatible apps are identified for selection with graceful handling of unsupported platforms

### Story 1.3: Social App Hiding Setup
1. **App Selection Interface**: Given user selects social apps in setup flow, when proceeding to Phase 2, then display platform-specific app hiding instructions for iOS with clear numbered steps
2. **Biometric App Hiding Process**: Given app selection confirmed, when user follows instructions, then Face ID/Passcode app hiding setup enables via "Require Face ID" and "Hide and Require Face ID" actions
3. **Setup Validation**: Given hiding process completes, when user returns to MIGA, then interception capability verified and hub becomes fully functional
4. **Fallback Handling**: Given unsupported device configuration, when setup attempted, then graceful error handling provides alternative instructions without blocking app access

## Traceability Mapping

### Acceptance Criteria → Technical Specification → Implementation Components

| AC Reference | Tech Spec Section | Implementation Components | Test Coverage |
|-------------|------------------|------------------------|---------------|
| AC 1.1 #1 | Overview, Detailed Design (OnboardingModule) | WelcomeView, OnboardingStepView, UserDefaults persistence | UI integration tests, first-launch flow |
| AC 1.1 #2-3 | Detailed Design (OnboardingModule, UserPreferencesService) | NavigationStack routing, readiness slider, category selection | User flow tests, state persistence validation |
| AC 1.2 #1-2 | Detailed Design (InfrastructureSetupService) | Core Data schema, Firebase anonymous auth, LAContext biometric validation | Infrastructure initialization tests, Firebase integration tests |
| AC 1.2 #3-4 | Detailed Design (ContentCacheManager, AppHidingService) | Firestore content preloading, URL scheme validation | Content caching tests, platform compatibility tests |
| AC 1.3 #1 | System Architecture Alignment, Detailed Design (AppHidingService) | First Run Experience (F.R.E.) UI, biometric detection logic | Setup UI tests, device compatibility matrix |
| AC 1.3 #2-4 | Detailed Design (AppHidingService), Workflows (App Hiding Setup Flow) | LAContext evaluation, Home Screen manipulation instructions | Manual QA validation, integration acceptance tests |

### Functional Requirements → Story AC Mapping

| FR Reference | Story ACs | Tech Spec Coverage |
|-------------|-----------|-------------------|
| FR1 (App Onboarding) | AC 1.1 #1-3, AC 1.2 #1-4, AC 1.3 #1-4 | Complete coverage - 12-step onboarding, infrastructure setup, app hiding workflow |
| FR8 (Reliability/Quality) | AC 1.2 #4, AC 1.3 #4 | Fallback handling, platform compatibility, error recovery |

## Risks, Assumptions, Open Questions

### Risks
- **Firebase Service Availability**: If Firebase services experience extended outages during onboarding window, users may receive degraded experience with local-only fallback mode. *Mitigation*: Implement robust offline-first architecture with local bundle content, retry mechanisms, and clear user communication about temporary limitations.
- **Biometric Platform Variations**: iOS biometric implementations vary by device generation and configuration, potentially causing inconsistent app hiding setup experiences. *Mitigation*: Comprehensive device compatibility testing, fallback to passcode-only workflows, and user education about device-specific requirements.
- **URL Scheme Evolution**: Social media apps may change or remove custom URL schemes in future updates, breaking interception capability. *Mitigation*: Runtime validation during setup, fallback messaging, and app store monitoring for scheme changes.
- **iOS Permission Model Changes**: Future iOS updates may restrict biometric authentication or app hiding mechanisms required by MIGA. *Mitigation*: Regular iOS beta testing, Apple developer program monitoring, and alternative authentication pathways.

### Assumptions
- **Firebase Anonymous Authentication**: Assumes Firebase project configuration allows anonymous authentication without additional identity verification. *Next Step*: Validate with Firebase project settings and privacy policy compliance.
- **iOS Face ID/Passcode Consistency**: Assumes Face ID and passcode workflows remain consistent across iOS versions 17+. *Next Step*: Multi-version compatibility testing pending iOS 18 release.
- **Content Collection Availability**: Assumes Firestore /content collection exists with appropriate permissions for anonymous read access. *Next Step*: Database schema and security rules validation.
- **App Store Approval**: Assumes current app hiding mechanism aligns with App Store Review Guidelines. *Next Step*: Submit initial test builds for approval validation.

### Open Questions
- **Long-term Biometric Reliability**: How will app hiding effectiveness degrade over time as users become familiar with the bypass mechanism? *Next Step*: Monitor post-launch analytics for hiding effectiveness.
- **Content Freshness Requirements**: What is the acceptable staleness threshold for cached content before forcing network refresh? *Next Step*: User research on content engagement vs freshness preferences.
- **Internationalization Needs**: Should onboarding flow adapt for different cultural contexts or languages beyond English? *Next Step*: Multi-language market research if targeting international users.
- **Onboarding Optimization**: After initial launch data, which onboarding steps provide highest retention value vs friction? *Next Step*: A/B testing infrastructure for onboarding flow optimization.

## Test Strategy Summary

### Unit Testing (Developer Focus)
- **OnboardingModule**: Navigation state management, step progression logic, UserDefaults persistence operations (95% code coverage target)
- **InfrastructureSetupService**: Core Data schema validation, Firebase authentication flows, error handling scenarios
- **ContentCacheManager**: Offline content loading, Firestore API interactions, data transformation logic
- **AppHidingService**: Biometric capability detection, iOS version compatibility checks, error message generation

### Integration Testing (QA Focus)
- **Firebase Infrastructure**: Anonymous auth → content fetching → caching pipeline end-to-end validation
- **iOS Platform Integration**: Face ID workflow → app hiding → interception capability verification
- **Data Persistence**: UserDefaults → Core Data → Firebase synchronization and conflict resolution
- **Network Degradation**: Offline mode functionality, cache fallback behavior, sync retry mechanisms

### UI Testing (Automation Priority)
- **Onboarding Flow**: Complete 12-step sequence with various input combinations and edge cases
- **Setup Workflow**: App selection → hiding instructions → validation sequence automation
- **Error Scenarios**: Network failures, biometric unavailability, incompatible device handling

### Manual Testing (Critical User Paths)
- **Device Compatibility Matrix**: Testing across supported iOS versions, device types, and biometric configurations
- **First Run Experience**: End-to-end evaluation of onboarding completeness and hub readiness
- **Accessibility Validation**: VoiceOver navigation, Dynamic Type responsiveness, motor impairment accommodations

### Performance Testing
- **Launch Performance**: Cold start to onboarding display (<1s), infrastructure setup completion (<3s)
- **Memory Management**: Onboarding sequence memory footprint (<50MB), no memory leaks during navigation
- **Offline Operation**: Graceful degradation when Firebase services unavailable, cached content availability

### Security Testing
- **Privacy Compliance**: No unauthorized data collection, proper Firebase anonymous authentication usage
- **Data Protection**: Encryption validation for local user preferences and cached content
- **Biometric Security**: Proper LocalAuthentication framework usage without security bypasses
