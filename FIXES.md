# CloudGallery Fixed Build

This is an unofficial, targeted fix build based on [AKS-Labs/CloudGallery](https://github.com/AKS-Labs/CloudGallery), version 0.7. The app's package ID remains `com.akslabs.cloudgallery`; this build is version `0.7.1` (version code 9).

## Fixes in this build

1. **Use the newly entered Telegram bot token immediately.** The previous onboarding flow saved the user's token after the shared Telegram bot client had already been constructed, so validation could succeed while uploads continued using the old token. After valid credentials are saved, the app now stops the old polling client and rebuilds the Telegram client from the saved token.
2. **Do not mark an unreadable photo URI as successfully uploaded.** A null media stream previously returned `false` without an exception, while the upload worker could still mark the item complete. Missing streams and unsupported MIME types now throw a clear failure; streams are closed and temporary files cleaned up.
3. **Continue after granting photo access.** The permission route now navigates into the main app after photo permission becomes available, including after returning from system settings.
4. **Do not print credential values while migrating preferences.** The migration log now records only the preference key, not its value, which could include a Telegram bot token.

## Known limits

This is not a complete rewrite or a claim that every upstream issue is fixed. In particular, the separate feature that discovers older Telegram messages still relies on the Bot API `getUpdates` mechanism and has pagination/history limitations. Background uploads remain subject to Android/WorkManager scheduling. Report any remaining upload problem with the app version, Android version, exact error text, and whether the photos appear in the Local Photos screen. Never post your Telegram bot token in a public issue or chat.

## Install

Download `app-release.apk` from this repository's latest GitHub Release. The app requires Android 10 or newer. Android may ask you to allow installation from the browser or file manager used to open the APK.

The build uses a locally generated Android debug signing key, which differs from the signing certificate on the upstream APK. Android will therefore reject it as an update over the original app. **Before installing this fixed build, export/protect any local database or records, then uninstall the original CloudGallery and install this APK.** Uninstalling removes the original app's on-device settings, credentials, and local database. Photos already uploaded to Telegram are separate and remain in Telegram. The fixed build uses the same application ID (`com.akslabs.cloudgallery`) and can later update this fixed build when signed with the same key; it cannot update the upstream-signed APK.

## Build notes

The release asset is generated from the `main` branch using Gradle's `assembleRelease` task. `local.properties` is machine-specific and must not be committed. The APK is a sideloadable Android package, not a Play Store release.
