
name: Build 4 Numbers APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout project
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: '17'

      - name: Extract Android project
        run: |
          mkdir -p android_project
          unzip -q 4Numbers_Android_Project.zip -d android_project

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.7'

      - name: Build Android APK
        run: |
          SETTINGS_FILE="$(find android_project -type f -name settings.gradle -print -quit)"
          if [ -z "$SETTINGS_FILE" ]; then
            echo "ERROR: settings.gradle was not found"
            find android_project -maxdepth 4 -type f | sort
            exit 1
          fi
          cd "$(dirname "$SETTINGS_FILE")"
          gradle assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: 4-Numbers-APK
          path: android_project/**/build/outputs/apk/debug/*.apk
