#  CI/CD Workflows Collection

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)
![DevOps](https://img.shields.io/badge/DevOps-Automation-orange?style=for-the-badge)

A centralized repository containing reusable **GitHub Actions workflows** designed to automate the Android development lifecycle. These pipelines handle tasks such as building APKs, running unit tests, linting code, and creating GitHub releases.

---

## Available Workflows

| Workflow Name | File Name | Trigger | Description |
| :--- | :--- | :--- | :--- |
| **Build & Release APK** | `on_dispatch.yml` | `Tag Push (v*)` | Builds a Debug/Release APK, renames it, and uploads it to GitHub Releases automatically. |

---

##  How to Use

To use any of these workflows in your Android project:

1.  Create a `.github/workflows` directory in the root of your project.
2.  Copy the desired `.yml` file content from this repository.
3.  Paste it into a new file inside your project (e.g., `.github/workflows/release.yml`).
4.  Configure the required **Secrets** (see below).

---

## Required Secrets

Some workflows (like the **Release** workflow) require secrets to access external services (like APIs) or to sign the app.

Go to `Settings` > `Secrets and variables` > `Actions` in your target repository and add:

| Secret Name | Required For | Description |
| :--- | :--- | :--- |
| `NEWS_API_KEY` | `release.yml` | Your API Key for fetching news (injected into `local.properties`). |
| `KEYSTORE_PASSWORD` | `release-signed.yml` | *(Optional)* Password for your signing Keystore (if using signed release). |
| `KEY_ALIAS` | `release-signed.yml` | *(Optional)* Key alias for signing. |

---

## Example: Build & Release Workflow

This is the standard workflow used to generate an APK and attach it to a release tag.

```yaml
name: Android Build & Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      
      # Create local.properties with API Key
      - name: Create local.properties
        run: echo "NEWS_API_KEY=${{ secrets.NEWS_API_KEY }}" > local.properties

      - name: Build APK
        run: ./gradlew assembleDebug
        
      # Upload to Release...
