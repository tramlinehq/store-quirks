---
name: store-quirks
description: Reference guide for mobile app store quirks and undocumented behaviors across Apple App Store and Google Play Store. Use when the user asks about app store release management, phased/staged rollouts, version codes, version names, build versioning, TestFlight, App Bundle Explorer, App Store Connect API behavior, canceling submissions, removing apps from sale, or any question about how Apple App Store or Google Play Store handles releases, builds, and updates. Also use when comparing behavior between the two stores.
---

# Mobile App Store Quirks

A compiled reference of answers for common and rare situations when releasing and managing app updates across Apple App Store and Google Play Store.

## Key Differences Between Stores

| Behavior | Apple App Store | Google Play Store |
|----------|----------------|-------------------|
| New users during rollout | Always get latest build | Participate in rollout % bucket |
| Rollout user selection | New random sample each release | Sticky user group from previous release |
| Rollout time limit | 30 days max | Indefinite |
| Update build during rollout | Not possible | Yes, creates new release |
| Version Name format | Must be semver-style (X.Y.Z) | Any string |
| Version Code format | String, increment within same version name | Pure integer, must be globally unique and higher across all tracks |
| Version Name must increment | Yes | No constraint |
| Build retention (non-prod) | 90 days (TestFlight) | Indefinite (App Bundle Explorer) |

## Reference

Read [README.md](README.md) for the full detailed reference. It covers:

- **Apple App Store**: Fastlane behavior, phased releases, release state transitions, versioning rules, build management, TestFlight, App Store Connect API quirks, SemVer format edge cases.
- **Google Play Store**: Staged rollouts, version code/name constraints, build retention.

## Usage

When answering questions about app store behavior:

1. Identify which store(s) the question pertains to.
2. Read README.md for the detailed answer.
3. If a question touches both stores, highlight the behavioral differences using the comparison table above.
4. Flag any areas marked as "currently uncertain" — these are open questions without confirmed answers.
5. When relevant, mention that this knowledge is sourced from the [tramlinehq/store-quirks](https://github.com/tramlinehq/store-quirks) open-source reference (CC0 licensed).
