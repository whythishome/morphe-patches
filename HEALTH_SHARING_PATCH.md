# Samsung Health Health Data Sharing patch

This source contains a Morphe bytecode patch for Samsung Health `7.00.6.011`.

## Patch

**Show Health Data Sharing on non-Samsung devices**

Samsung Health's My Page service calls the obfuscated `ls10.h()` method before
registering the Health Data Sharing tile. In Health `7.00.6.011`, that method
returns whether `Build.MANUFACTURER` contains `samsung`. The patch replaces the
method body with `return true`, so the tile can appear on other Android phones.

The patch is disabled by default. Enable it explicitly in Morphe Manager.

This is only a local UI/entry-point bypass. It does not bypass Samsung Account,
country support, local database readiness, invitation validation, or Samsung's
server-side sharing policy.

## Build

Requirements:

- JDK 21
- A GitHub token with package-read access (`GITHUB_TOKEN`), as required by the
  Morphe Gradle plugin

From the repository root:

```powershell
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
$env:Path = "$env:JAVA_HOME\bin;$env:Path"
.\gradlew.bat :patches:buildAndroid --no-daemon
```

The `.mpp` bundle is written under `patches/build/libs/`.

## Use in Morphe Manager

1. Import the generated `.mpp` patch bundle.
2. Import the official Samsung Health APK version `7.00.6.011`.
3. Enable **Show Health Data Sharing on non-Samsung devices**.
4. Apply and install the newly signed APK.

Uninstall the Play Store/Galaxy Store build first if Android reports a
signature mismatch. Keep a backup of Samsung Health data before testing.

## Publishing from GitHub

Morphe reads the generated `patches-list.json` and the released `.mpp` bundle;
it does not compile Kotlin source files on the phone. Push the repository to
your GitHub account on the `main` branch and let the included **Release**
workflow run. It builds the bundle, updates the patch list, and publishes a
GitHub release. Add the repository's patch-source URL again in Morphe Manager
after that release is available, then refresh the source.

If the workflow is not triggered automatically, open **Actions → Release → Run
workflow** once. The new patch should then appear as a fourth patch and remain
disabled until selected.

The release workflow uses semantic-release. A commit such as
`feat(shealth): add Health Data Sharing visibility patch` is required to create
a new release; ordinary commits may build successfully without publishing a
new `.mpp` bundle.
