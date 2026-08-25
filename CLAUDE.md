# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Structure

This is a **wrapper/packaging repository** with two Git submodules:

- **`cells-client/`** — The Android app module (`com.pydio.android.Client`), containing all UI, ViewModel, service, and database code.
- **`sdk-java/`** — The Java SDK for communicating with Pydio Cells servers. Has its own `CLAUDE.md`.

Clone with submodules:

```sh
git clone --recursive https://github.com/pydio/cells-android-app.git
# Or after cloning:
git submodule update --init --recursive
```

Copy and configure local properties before building:

```sh
cp local.properties.sample local.properties
# Set sdk.dir to your Android SDK path
```

## Build Commands

```sh
./gradlew :cells-client:assembleDebug           # Build debug APK
./gradlew :cells-client:assembleRelease         # Build release APK (requires signing config)
./gradlew :cells-client:bundleRelease           # Build release AAB
./gradlew :cells-client:test                    # Run unit tests
./gradlew :cells-client:connectedAndroidTest    # Run instrumented tests (device/emulator required)
```

Run a single test class:

```sh
./gradlew :cells-client:test --tests "com.pydio.android.cells.SomeTestClass"
```

## Versions and Configuration

All dependency versions are declared in **`gradle/libs.versions.toml`** (the single source of truth). App-specific versions (versionCode, versionName, minSdk, compileSdk, targetSdk) live in **`versions.properties`**.

The `sdk-java` submodule is used as a local source dependency by default. Setting `useSdkSubmodule=false` in `local.properties` switches to the published Maven artifact (`java.sdk` version from `versions.properties`).

Release signing: set `keystore.path`, `keystore.pwd`, `signkey.alias`, `signkey.pwd` in `local.properties` or via environment variables `ANDROID_KEYSTORE_PATH`, `ANDROID_KEYSTORE_PWD`, `ANDROID_SIGNKEY_ALIAS`, `ANDROID_SIGNKEY_PWD`.

## Architecture

The app follows **MVVM with a Service layer**, implemented in `cells-client/src/main/java/com/pydio/android/cells/`:

```conf
ui/          ← Jetpack Compose screens and composables, organized by feature
  browse/    ← File browser (main feature area)
  login/     ← Authentication flow
  account/   ← Account management
  share/     ← Share-to-app intent handling
  system/    ← Settings, jobs/logs, landing screen
  core/      ← Shared composables, CellsNavigation.kt (nav graph), top bar, drawer
  models/    ← ViewModels (all extend AbstractCellsVM : ViewModel(), KoinComponent)
services/    ← Business logic: NodeService, AccountService, AuthService,
               TransferService, OfflineService, ConnectionService, NetworkService
db/          ← Room databases: AccountDB, AuthDB, TreeNodeDB, RuntimeDB
di/          ← Koin DI wiring in Modules.kt
transfer/    ← S3 file transfer, Glide image loading integration
```

**Key patterns:**

- ViewModels expose state via `StateFlow` / `Flow`; Room DAOs return `Flow<List<...>>` consumed directly by ViewModels.
- All ViewModels are Koin components — use `by viewModel<SomethingVM>()` in Compose, not constructor injection.
- Navigation is centralized in `ui/core/CellsNavigation.kt`.
- DI is entirely Koin (not Hilt); modules are declared in `di/Modules.kt`.
- Background work uses WorkManager.
- Image loading uses Glide with Compose integration.
- Remote API calls go through the `sdk-java` layer (`cells-sdk-java`); the service layer in `services/` wraps SDK calls and handles persistence.
