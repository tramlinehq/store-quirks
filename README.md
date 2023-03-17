# A Collection of Mobile App Store Quirks
[![License](https://img.shields.io/github/license/tramlinehq/store-quirks)](https://github.com/tramlinehq/store-quirks/blob/master/LICENSE)

## What?

This reference is a compilation of answers for common and rare situations in an attempt to increase transparency. It is compiled from experience, developer forums, Stack Overflow, and various other sources of developer documentation. We hope contributions from other developers will grow this resource further.

> **Note:** A few of these might seem obvious, but they exist to draw a contrast between the stores.

## Why?

As mobile developers, we face unique challenges when it comes to releasing and managing updates for our apps across different app stores. One of the primary reasons for this difficulty is the scattered and insufficient documentation available, which lacks the necessary level of detail and nuance to provide a clear understanding of the process.

Additionally, the interfaces and tools provided by these stores for managing releases are often opaque and and don't offer much insight into how things work behind the scenes, which further complicates the process.

## Glossary

The words use might mean slightly different things on different stores, so this table tries to clarify what a particular word means.

| | App Store | Play Store |
|-|-----------|------------|
| Version Name | [Bundle Short Version String](https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleshortversionstring). The release or version number of the bundle. String on the right: <br/> ![Screenshot 2023-03-17 at 7 45 01 PM](https://user-images.githubusercontent.com/50663/225929999-30a37e10-9c3b-449c-9887-8dec16227f27.png) | [This](https://developer.android.com/studio/publish/versioning#versioningsettings) is `versionName`. Unlike App Store, it needn't necessarily be a "semver-style" string, it can be anything. |
| Version Code | [Bundle Version String](https://developer.apple.com/documentation/bundleresources/information_property_list/cfbundleversion) (aka. build string). The version of the build that identifies an iteration of the bundle. This number is the one generally incremented through a CI pipeline. Note that this technically needn't be an integer, it just needs to be incremented every time. Number on the left: <br/> ![Screenshot 2023-03-17 at 7 45 01 PM](https://user-images.githubusercontent.com/50663/225929999-30a37e10-9c3b-449c-9887-8dec16227f27.png) | [This](https://developer.android.com/studio/publish/versioning#versioningsettings) is `versionCode`. Unlike App Store, it is not a string, it is a pure integer that must be incremented for every new build. |
| Release Version | This is typically the Version Name (but it can be different). In the APIs, you'd find this under [PreReleaseVersion](https://developer.apple.com/documentation/appstoreconnectapi/prereleaseversion/attributes) and/or [App Store Version](https://developer.apple.com/documentation/appstoreconnectapi/appstoreversion/attributes). This is what is shown to the users on the store page. | This is typically the Version Name, but can be different. This is what is shown to the users on the store page. |

## App Store 📱

### Can I swap a build on a running phased rollout?

You can’t do this. There is no UI for it on the App Store Connect dashboard.

If you try to do this using the API, you get the following error:

```
A relationship value is not acceptable for the current resource state.
The specified pre-release build could not be added.
```

Also from [https://developer.apple.com/help/app-store-connect/manage-builds/choose-a-build-to-submit](https://developer.apple.com/help/app-store-connect/manage-builds/choose-a-build-to-submit):

```
However, you can change the build as often as you want until
you submit the version to App Review.
```

Also see: [Can you revert to an older version of the app if the current version (being phased) has a bug?](#can-you-revert-to-an-older-version-of-the-app-if-the-current-version-being-phased-has-a-bug)

The fact that you cannot do this **during a phased rollout** isn’t explicitly documented, but the clues above are enough evidence to suggest the impossibility of it.

### Can I swap a build after a release has been reviewed but has not been submitted for sale?

This cannot be done. The build can only be swapped before submission for review. After which you have to explicitly cancel the release and make a new one to be able to change builds.

![cancel release](img/cancel_release.png)

### Does the previous Ready For Sale in phased release automatically halt or stop when a new one is attempted to distribute after approval?

When the Phased Release was paused: the older version moves to `REPLACED_WITH_NEW_VERSION` status with its phased release as `COMPLETE` after a new version is released.

When the Phased Release was active: our assumption is same as above. but TO BE CONFIRMED.

### When can you create a new release for an app?

You can only do this when the last release is in `READY_FOR SALE` (it has been released to at least some users, phased or full) or `DEVELOPER_REMOVED_FROM_SALE`.

Ref – [https://developer.apple.com/help/app-store-connect/update-your-app/create-a-new-version](https://developer.apple.com/help/app-store-connect/update-your-app/create-a-new-version)

### What is `DEVELOPER_REMOVED_FROM_SALE` and how do you get to that state?

This state is reached when you have completely removed your app from the store. You can do this by going to the "Pricing and Availability" section in the App Store Connect dashboard and setting the availability to "Not for Sale". Please refer to the screenshot below:

![remove from sale](img/remove_from_sale.png)

### Can you revert to an older version of the app if the current version (being phased) has a bug?

No, it is not possible to revert to a previous version on the App Store if you have an issue with your app. You must create and submit a new version.

Ref – [https://developer.apple.com/help/app-store-connect/update-your-app/create-a-new-version](https://developer.apple.com/help/app-store-connect/update-your-app/create-a-new-version)

### Does creating a new build on TestFlight auto-create a new release in `READY_FOR_SUBMISSION` state on app store (production)?

Yes. Elaborate more on this with evidence.

### If there's already a phased release and another pending release on app store, does pushing to TestFlight, update the build on pending release?

No, it does not. It automatically changes the version string to represent the "latest" version (in some sort of dubious semver-type of way). This only changes the name, of course. The build remains the same and is **not** swapped.

### During a phased release, what version is presented to users downloading for the 1st time?

Phased rollout is only for automatic updates, new users will always download the latest build. Existing users can also go manually update the build from the app store.

From ref – [https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases](https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases)

```
Keep in mind that apps and app updates in phased release can be manually
downloaded from the App Store by anyone at any time.
```

### How does the release of a version update work during a phased release?

For example, will version 2.0.1 be released only to the same 2% of the users that already received version 2.0.0? Or it will be delivered to a completely new 2% of my users?

It will be delivered to a completely new random sample of 2% users, no correlation.

### Is there a way to halt a phased release?

In a way, yes. A release can be paused any number of times during a phased rollout, but the halt is permanent and that version is removed from the store

Ref - [https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases](https://developer.apple.com/help/app-store-connect/update-your-app/release-a-version-update-in-phases)

```
While your app is in phased release, you can choose to pause the release for a total of 30 days.
There’s no limit to the number of pauses.

If you remove your app from sale, phased release will stop
and won’t be available for that version again.
```

You can make that version available again by flipping the switch. It can take some time to become available again. [See](#what-is-developer_removed_from_sale-and-how-do-you-get-to-that-state).

### Can you start a new release (start its distribution) while another release is in a phased release?

Yes. [See](#does-the-previous-ready-for-sale-in-phased-release-automatically-halt-or-stop-when-a-new-one-is-attempted-to-distribute-after-approval).

When the Phased Release was active: our assumption is same as above. but TO BE CONFIRMED.

### Can you start a new release (start its distribution) while another release is in phased release, but paused?

Yes. [See](#does-the-previous-ready-for-sale-in-phased-release-automatically-halt-or-stop-when-a-new-one-is-attempted-to-distribute-after-approval).

### How long can you shepherd a phased release?

Build is removed after 30 days.

### How long are non-production builds retained for (in TestFlight)?

They expire after 90 days.

### What happens to the phased release when it is paused and the 30-day time has passed?

Currently uncertain.

### Until what state can you remove from review/cancel the release?

One can remove it from review even after the build is `In Review`. After approvals and before a Developer Release, you can cancel the release.

![remove from review](img/remove_from_review.png)
![cancel before release](img/cancel_before_release.png)

## Google Play Store 🤖

### Can I swap a build for an active staged rollout?

What Play Store does here is that it appears to immediately set the current release to be in a `Not Live` state and the new one is sort-of auto-promoted to whatever release state it was in previously.

### During a staged rollout, what version is presented to users downloading for the 1st time?

New users also participate in the randomization off of the release % bucket.

### How does the release of a version update work during a staged rollout?

For example, will version 2.0.1 be released only to the same 2% of the users that already received version 2.0.0? Or it will be delivered to a completely new 2% of my users?

From – [https://support.google.com/googleplay/android-developer/answer/6346149?hl=en](https://support.google.com/googleplay/android-developer/answer/6346149?hl=en)

```
When you do a staged rollout of a new release before completing the rollout
of the previous release, the new release will use the same group of users
as the previous release (depending on the percentage of the rollout).
```

In play store, depending on the percentage rollout the user group is sticky with respect to the selection from the previous release.

### How long can you shepherd a staged rollout?

Indefinitely.

### How long are non-production builds retained for (in App Bundle Explorer)?

Forever.

## Help and contribution

<img src="https://images.unsplash.com/photo-1531379410502-63bfe8cdaf6f?ixlib=rb-4.0.3&q=80&fm=jpg&crop=entropy&cs=tinysrgb" width="20%" alt="help" />

If you have a correction, a question or an answer to a question that you’d like to submit, drop in an [issue](https://github.com/tramlinehq/store-quirks/issues)! Feel free to swing by the [discord](https://discord.gg/u7VwyvBV2Z) if you'd like to discuss.
