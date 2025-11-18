# Story 1.1: Onboarding Flow

Status: ready-for-dev

## Story

As a new user discovering mindful technology,
I want to experience a 12-step value-driven onboarding sequence,
So that I understand MIGA's purpose and benefits before my first gateway experience.

## Acceptance Criteria

1. **Given** user has never opened MIGA before
   **When** user launches app for first time
   **Then** welcome screen displays with "The Intentional Life Starts Now"

2. **And** when user taps "Begin My Journey"
   **Then** step-by-step value proposition flow guides through: time awareness, behavioral goals, credibility through science, risk-free trial

3. **And** when user completes commitment slider and content interests selection
   **Then** proceeds to app hub with first-run experience overlay

## Tasks / Subtasks

- [ ] Implement OnboardingModule with NavigationStack enum-based routing
  - [ ] Create 12 sequential onboarding screens using NavigationStack
  - [ ] Implement enum-based routing for step progression
  - [ ] Add @State path management for navigation state
- [ ] Design welcome screen with "The Intentional Life Starts Now"
  - [ ] Create WelcomeView component
  - [ ] Implement "Begin My Journey" CTA button
  - [ ] Add proper styling and calming UI elements
- [ ] Build value proposition flow (steps 1-6)
  - [ ] Core Mission step: "Your Attention Is Valuable"
  - [ ] Time Wasted step: "What's Autopilot Costing You?" with hourglass visual
  - [ ] Time Saved step: "Your Time. Your Purpose" with aspirational imagery
  - [ ] Behavioral Goal step: "Stop Reacting. Start Choosing"
  - [ ] Credibility step: "Habit Change Backed by Science" with 14-day reference
  - [ ] Trial Policy step: "Risk-Free Clarity. Try MIGA Today"
- [ ] Implement personalization steps (steps 7-8)
  - [ ] Commitment slider step (1-10 scale) for readiness score
  - [ ] Content interests selection with tag selection UI
  - [ ] Save selections to UserDefaults for later recommendations
- [ ] Create flow explanation and completion steps (steps 9-10)
  - [ ] Journey step: "Your 4 Steps to Freedom" with visual flow
  - [ ] Final Confirm step: "You're Ready to Reclaim Your Focus" with completion CTA
  - [ ] Navigate to hub after completion
- [ ] Integrate onboarding state persistence
  - [ ] Persist completion state in UserDefaults
  - [ ] Track onboarding progression with Core Data entities
  - [ ] Block hub access until onboarding.isComplete == true
- [ ] Add first-run experience overlay to hub
  - [ ] Display overlay on first hub visit post-onboarding
  - [ ] Guide through app hiding setup process
  - [ ] Handle setup completion and overlay dismissal
- [ ] Implement accessibility and performance requirements
  - [ ] Add VoiceOver labels and Dynamic Type support
  - [ ] Ensure <1s load times for onboarding screens
  - [ ] Add proper contrast ratios (4.5:1) and readable typography

## Dev Notes

### Relevant Tech Spec Context

Following Epic 1 Technical Specification for comprehensive onboarding implementation. The OnboardingModule uses NavigationStack with enum-based routing and state persistence in UserDefaults. Core mission introduces MIGA's purpose through value-driven 12-step sequence covering: time awareness, behavioral goals, credibility through science, and risk-free trial policy. Personalization captures readiness score (1-10 slider) and content interests selection using tag-based UI. First-run experience overlay on hub guides through app hiding setup after onboarding completion.

### Architecture Alignment

Aligns with SwiftUI + MVVM + ObservableObject pattern. OnboardingModule manages 12-step sequence through NavigationStack-based routing with enum-driven state progression. User preferences and completion state persist via UserDefaults with @AppStorage reactive updates. Follows MVVM structure with OnboardingViewModel coordinating onboarding logic.

### Testing Standards

Unit test onboarding progression logic and state management. Integration test complete flow from welcome to hub activation. UI test critical user paths including slider interaction and tag selection. Performance test ensuring <1s screen transitions and memory usage <50MB.

## Project Structure Notes

### Project Structure Alignment

Onboarding screens located in Views/Onboarding/ directory following established MVVM pattern:
- OnboardingModule handles main flow coordination
- WelcomeView, OnboardingStepView, OnboardingFlowView for individual screens
- OnboardingViewModel manages state and progression logic

### Source Documents

- [Source: docs/PRD.md#5.1-App-Structure] - Onboarding screen requirements
- [Source: docs/ux-design.md#C.-Flow-1-Onboarding-Sequence] - Complete 12-step screen specification
- [Source: docs/epics.md#Epic-1-Story-1.1] - Story acceptance criteria and technical notes
- [Source: docs/architecture.md#Decision-Summary-Table] - Onboarding navigation pattern using NavigationStack
- [Source: docs/sprint-artifacts/tech-spec-epic-1.md#Detailed-Design] - Complete OnboardingModule specification and data models

## Dev Agent Record

### Context Reference

- Path: docs/sprint-artifacts/1-1-onboarding-flow.context.xml

### Agent Model Used
bm-dev-agent-v1.0

### Debug Log References

### Completion Notes List

### File List

# Change Log

- **2025-11-18**: Initial story creation from epic breakdown and technical specifications
- **Draft Status**: Ready for development following Epic 1 Tech Spec requirements
