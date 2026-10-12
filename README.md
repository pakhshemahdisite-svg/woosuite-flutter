# AB WooSuite Flutter — public CI only

This is the **public build repository**, not the Flutter source repository.

- **Private source of truth:** [woosuite-flutter-source](https://github.com/pakhshemahdisite-svg/woosuite-flutter-source)
- **Build workflow:** [Android APK](.github/workflows/android-apk.yml)
- **Version imported:** WooSuite Manager Flutter v0.7.0
- **Artifact:** debug APK and SHA-256 checksum only. Do not treat a debug APK as a signed production release.

## One-time secure access setup

1. In GitHub, create a **fine-grained personal access token** for the account that owns the private source repository. Set Repository access to **Only select repositories** and select **woosuite-flutter-source** only.
2. Under Repository permissions, grant **Contents: Read-only**; do not grant write access.
3. In **this public repository**, go to Settings → Secrets and variables → Actions → New repository secret. Name it exactly **WOOSUITE_SOURCE_READ_TOKEN** and paste the token directly in the GitHub UI (never in an issue, commit, chat message, or source file).
4. Open [Actions → WooSuite Flutter APK (private source)](https://github.com/pakhshemahdisite-svg/woosuite-flutter/actions/workflows/android-apk.yml), select **Run workflow**, branch **main**.
5. After success, download **WooSuite-Manager-Android-Debug** from the workflow Artifacts section. The archive contains `app-debug.apk` and its `sha256` file.

## Alternative: request a build by commit

Commit/update `.ci/build-request.txt` on **main** (a simple version marker). Only that explicit path and manual workflow dispatch trigger the public runner; changes in the private source repository **do not automatically trigger** the build. A bot or maintainer can request a build by updating that marker.

## Security rules

- **Never upload** `source.zip`, `lib/`, signing keys, service credentials, application passwords, or production configuration here.
- The CI job checks out the private repository using a read-only token, unpacks the archive within the ephemeral runner, and uploads **only** the APK and checksum.
- Because this build runs in a **public** repository, job logs and APK artifacts are public. Build errors may print excerpts of source code. For stronger confidentiality, build privately on a self-hosted runner instead.
- The original public `source.zip` was removed from the main branch, **but historical commits may still expose its bytes**. Rewriting/removing Git history requires a separate cleanup.
- Use the debug APK for testing. Release builds need a protected production signing key and a separate release pipeline.

## Troubleshooting

- `Configure Actions secret WOOSUITE_SOURCE_READ_TOKEN`: add the missing secret.
- `Repository not found`: token is invalid, expired, wrong owner, or missing private repository Contents read permission.
- `Missing pubspec.yaml`: private `source.zip` does not contain the expected Flutter project.
- Flutter analysis/tests fail: fix source in the **private** repository; do not copy source files to public CI.
