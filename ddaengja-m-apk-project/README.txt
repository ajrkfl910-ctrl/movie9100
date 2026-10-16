name: Build APK
on: [push, workflow_dispatch]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: {node-version: 20}
      - uses: actions/setup-java@v4
        with: {distribution: temurin, java-version: 17}
      - run: npm install
      - run: npx cap add android
      - name: 가로 고정 + 전체화면
        run: |
          M=android/app/src/main/AndroidManifest.xml
          sed -i 's/<activity /<activity android:screenOrientation="sensorLandscape" /' $M
          S=android/app/src/main/res/values/styles.xml
          sed -i 's#</style>#<item name="android:windowFullscreen">true</item><item name="android:windowLayoutInDisplayCutoutMode">shortEdges</item></style>#' $S
      - run: npx cap sync android
      - run: cd android && chmod +x gradlew && ./gradlew assembleDebug
      - uses: actions/upload-artifact@v4
        with:
          name: ddaengja-m-apk
          path: android/app/build/outputs/apk/debug/app-debug.apk
