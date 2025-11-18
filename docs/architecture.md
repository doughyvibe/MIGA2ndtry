---
generated: "2025-11-18"
project: "MIGAxBM"
technology_stack: "iOS, SwiftUI, Core Data, Firebase"
---

# Architecture Decision Document: MIGA Mindfulness App

## Project Context Understanding

Project Type: iOS wellness/mindfulness app (MIGA - Mindful Gateway to Social Media)
Primary Purpose: Behavioral friction app creating intentional pauses before social media use
Target Users: Individuals seeking conscious digital habits

Key Complexity Drivers:
- Timer-based gateway interception (15-45 seconds)
- External app URL scheme launching after mindful pause
- Content rotation and caching system
- Analytics tracking conscious vs. autopilot behavior
- App hiding mechanism using iOS Face ID/Passcode features

Current Assets:
- Complete PRD specification
- Detailed UX design with 12-step onboarding flow
- Existing iOS project structure (Xcode workspace with SwiftUI foundation)

Adjustments:
- Focus on core technical architecture (UI state management, data persistence, external app integration)
- De-emphasize behavioral science implementation details in favor of engineering patterns

## Starter Template Evaluation

Based on MIGA's requirements for complex state management, timer flows, and behavioral analytics, I evaluated several architectural approaches:

### Option 1: The Composable Architecture (TCA) Template
**Template:** ethanhuang13/swiftui-tca-template
**Benefits for MIGA:**
- Excellent for complex state machines (timer flows, modal sequences, app lifecycle)
- Strong side effects handling (URL launches, analytics events, app hiding)
- Built-in testability for complex workflows
- Prevents AI agent conflicts through rigid patterns
- Perfect for behavioral state management

**Considerations:**
- Learning curve for beginners
- Additional dependency (TCA framework)

### Option 2: Clean Architecture for SwiftUI
**Template:** nalexn/clean-architecture-swiftui
**Benefits for MIGA:**
- Clear separation of concerns (Presentation ↔ Business Logic ↔ Data Access)
- Redux-like AppState for centralized state management
- Testable business logic layer
- Familiar MVVM-style patterns
- Good for analytics and data persistence

**Considerations:**
- Less rigid than TCA for preventing conflicts
- Manual state management vs TCA's structured approach

### Option 3: Native SwiftUI MVVM (Recommended for Beginners)
**Template:** Standard Apple SwiftUI project structure
**Benefits for MIGA:**
- Familiar patterns, easy to understand
- ObservableObject for shared state
- Native SwiftUI navigation and modals
- No external dependencies
- Quick to get started

**Considerations:**
- Less structure, more potential for inconsistencies
- More manual testing requirements

**Decision:** Use Native SwiftUI MVVM foundation as the architectural approach.

## Decision Summary Table

| Decision Category | Chosen Option | Reasoning | Version |
|------------------|----------------|-----------|---------|
| Architecture Pattern | SwiftUI + MVVM + ObservableObject | Beginner-friendly, existing Xcode project uses this foundation | iOS 17+ |
| State Management | @Published + @EnvironmentObject | Simple for shared app state, timer state, and user preferences | SwiftUI native |
| Data Persistence | Core Data + UserDefaults | Core Data for analytics/metrics, UserDefaults for settings | iOS native |
| Timer Management | Timer.publisher + Combine | Reactive timer for countdown with pause/resume capability | Combine framework |
| Navigation | NavigationStack + NavigationPath | Type-safe navigation for onboarding, hub, settings flow | SwiftUI native |
| App Interception | Custom URL Schemes | Standard iOS approach for external app launching | iOS URL schemes |
| Content Management | Firestore + Local Core Data cache | Real-time content updates with offline caching | Firebase iOS SDK 10.x |
| Analytics | Firebase Analytics + Core Data mirror | Event tracking with local fallback | Firebase iOS SDK 10.x |
| Onboarding | View-based state machine | Simple state progression through 12-step flow | SwiftUI native |
| Gateway Modal | Full-screen .sheet() presentation | Blocking modal for mindful pause experience | SwiftUI native |
</content>
## Project Structure & Boundaries

### Source Organization
```
MIGAxBM/
├── Models/
│   ├── MindfulnessContent.swift     # Firestore content models
│   ├── AnalyticsEvent.swift        # Event tracking models
│   ├── UserPreferences.swift       # Settings and configuration
│   └── GatewaySession.swift        # Timer and pause session state
├── Views/
│   ├── Onboarding/
│   │   ├── WelcomeView.swift
│   │   └── OnboardingFlowView.swift
│   ├── Gateway/
│   │   ├── MindfulnessGatewayView.swift  # Timer + content modal
│   │   └── ContentView.swift             # Quote/article display
│   ├── Hub/
│   │   ├── AppHubView.swift          # Social app shortcuts grid
│   │   └── AppIconView.swift         # Individual app launcher
│   ├── Statistics/
│   │   ├── StatisticsView.swift      # Analytics dashboard
│   │   └── MetricsChartView.swift    # Charts and visualizations
│   └── Settings/
│       └── SettingsView.swift        # Configuration screen
├── ViewModels/
│   ├── AppViewModel.swift           # Main app state coordinator
│   ├── GatewayViewModel.swift       # Timer and content logic
│   ├── AnalyticsViewModel.swift     # Metrics and statistics
│   └── OnboardingViewModel.swift    # Setup flow state
├── Services/
│   ├── ContentService.swift         # Firestore integration
│   ├── AnalyticsService.swift       # Event tracking and persistence
│   ├── AppInterceptionService.swift # URL scheme handling
│   └── TimerService.swift           # Countdown timer management
├── Persistence/
│   ├── ContentCacheManager.swift    # Core Data caching
│   └── UserDefaultsManager.swift    # Settings persistence
└── Utilities/
    ├── URLSchemeHelper.swift        # App launching utilities
    └── NotificationManager.swift    # Local notifications
```

### Integration Points
- **App Launch → Gateway Modal**: App shortcuts trigger NavigationStack programmatic presentation
- **Timer Flow → External Launch**: Combine timer completion triggers URL scheme execution
- **Analytics Events**: Distributed throughout app lifecycle with Core Data + Firebase sync
- **Settings → App Behavior**: UserDefaults observable triggers immediate UI reconfiguration

## Cross-Cutting Concerns

### Error Handling Strategy
- **User-Facing Errors**: Non-blocking alerts with actionable guidance
- **Silent Failures**: Analytics logging with app continuation (network issues, cache failures)
- **App-Lifecycle Events**: Graceful degradation (Firebase offline mode)

### Logging Strategy
- **Development**: Console logging with structured data
- **Production**: Firebase Crashlytics for errors, Analytics for user behavior
- **Privacy**: No personally identifiable information in logs

### Date/Time Handling
- **User Display**: Local timezone formatting with 12/24-hour preference
- **Data Storage**: UTC timestamps for analytics consistency
- **Session Tracking**: Combine publishers for accurate pause duration measurement

### Authentication Pattern
- **App Security**: iOS device biometrics (Face ID/Passcode) for app hiding
- **Firebase**: Anonymous authentication for content and analytics access
- **User Identity**: Local, privacy-focused UUID generation

### API Response Format Consistency
- **Firebase Firestore**: Standard document structure with error handling
- **Local Cache**: Core Data mirrored schema for offline compatibility

## Implementation Patterns (Conflict Prevention)

### Naming Conventions
- **ViewModels**: `[Feature]ViewModel` (GatewayViewModel, AnalyticsViewModel)
- **Services**: `[Domain]Service` (ContentService, AnalyticsService)
- **Models**: PascalCase for types (e.g., MindfulnessContent, UserPreferences)

### File Organization
- Feature-based grouping (Views/, ViewModels/, Services/)
- Related files co-located (TimerService with GatewayViewModel)
- Clear separation of iOS-specific code from cross-platform logic

### State Management Consistency
- **ObservableObject Pattern**: @Published for reactive updates, @EnvironmentObject for shared state
- **Single Source of Truth**: All state flows through proper ViewModel hierarchy
- **Avoid Direct Model Mutation**: All changes through dedicated service methods

### Testing Strategy
- **Unit Tests**: ViewModels and Services with mock dependencies
- **UI Tests**: Critical user flows (gateway timer, app launching)
- **Integration Tests**: Firebase sync and Core Data persistence

## Technology Stack Details

- **iOS Target**: iOS 17.0+ (latest SwiftUI features)
- **Programming Language**: Swift 5.9+ with SwiftUI and Combine
- **UI Framework**: SwiftUI with programmatic navigation
- **Data Layer**: Core Data for local persistence, Firebase for remote content
- **Reactive Programming**: Combine for event streams and async operations
- **Analytics**: Firebase Analytics with local Core Data mirror
- **Dependency Management**: Swift Package Manager (native Xcode)

## Development Environment

- **IDE**: Xcode 15+ with SwiftUI previews
- **Version Control**: Git with feature branch workflow
- **Testing**: Xcode Test Navigator with CI/CD pipeline
- **Code Quality**: SwiftLint for style consistency

## Architecture Decision Records

1. **AD001 - Timer Management**: Chose Timer.publisher over custom DispatchQueue solutions for reactive, testable timer operations with pause/resume capability.

2. **AD002 - State Management**: Selected Native SwiftUI MVVM over TCA due to beginner skill level and existing project foundation. TCA adoption possible in future releases.

3. **AD003 - Data Persistence Strategy**: Combined Core Data + UserDefaults approach - Core Data for structured analytics/metrics, UserDefaults for simple settings.

4. **AD004 - Navigation Architecture**: NavigationStack with enum-based routing for type-safe, deep-linkable navigation flows.

5. **AD005 - Content Caching**: Hybrid Firestore + Core Data strategy for real-time content updates with offline-first capabilities.

</content>
