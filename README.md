name: Build APK

on:
  workflow_dispatch:

jobs:
  apk:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v5

      - name: Extract Android project
        run: unzip -o xspeed-android.zip

      - name: Download Three.js for offline use
        run: |
          curl -fL https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js -o app/src/main/assets/three.min.js
          sed -i 's|https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js|three.min.js|' app/src/main/assets/xspeed.html

      - uses: actions/setup-java@v5
        with:
          distribution: temurin
          java-version: '17'

      - uses: gradle/actions/setup-gradle@v5
        with:
          gradle-version: '8.7'

      - name: Build APK
        run: gradle assembleDebug

      - uses: actions/upload-artifact@v6
        with:
          name: xspeed-apk
          path: app/build/outputs/apk/debug/app-debug.apk
