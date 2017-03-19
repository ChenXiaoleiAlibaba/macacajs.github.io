# 使用开放 CI 服务

## Travis-CI

| Platform   | Status                                          |  Repo              |
| ---------- | ----------------------------------------------- | ------------------ |
| Node.js | [![build status][travis-image-0]][travis-url-0] | [sample-nodejs](//github.com/macaca-sample/sample-nodejs)                   |

[travis-image-0]: https://img.shields.io/travis/macaca-sample/desktop-browser-sample-nodejs.svg?style=flat-square
[travis-url-0]: https://travis-ci.org/macaca-sample/desktop-browser-sample-nodejs

Add config to your `.travis.yml`:

``` yml
os:
  - osx
  - linux
env:
  - TEST_SUITE=ios
  - TEST_SUITE=android
  - TEST_SUITE=ios-browser
  - TEST_SUITE=android-browser
  - TEST_SUITE=pc-browser
matrix:
    fast_finish: true
    exclude:
      - os: osx
        env: TEST_SUITE=ios
      - os: osx
        env: TEST_SUITE=android
      - os: osx
        env: TEST_SUITE=ios-browser
      - os: osx
        env: TEST_SUITE=android-browser
      - os: osx
        env: TEST_SUITE=pc-browser
      - os: linux
        env: TEST_SUITE=ios
      - os: linux
        env: TEST_SUITE=android
      - os: linux
        env: TEST_SUITE=ios-browser
      - os: linux
        env: TEST_SUITE=android-browser
      - os: linux
        env: TEST_SUITE=pc-browser
    include:
      - os: osx
        env: TEST_SUITE=ios
        osx_image: xcode8
        language: objective-c
        before_install:
          - brew update
          - brew install nvm
          - source $(brew --prefix nvm)/nvm.sh
          - nvm install 7
          - brew install ios-webkit-debug-proxy
        script:
          - make travis-ios
      - os: osx
        env: TEST_SUITE=ios-browser
        osx_image: xcode8
        language: objective-c
        before_install:
          - brew update
          - brew install nvm
          - source $(brew --prefix nvm)/nvm.sh
          - nvm install 7
          - brew install ios-webkit-debug-proxy
        script:
          - make travis-ios-safari
      - os: linux
        env: TEST_SUITE=android
        jdk: openjdk7
        language: android
        android:
          components:
            - build-tools-23.0.2
            - android-23
            - extra-android-m2repository
            - extra-android-support
        sudo: false
        before_install:
          - export CHROME_BIN=chromium-browser
          - export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
          - export ANDROID_HOME=/usr/local/android-sdk
          - echo yes | android update sdk --all --filter build-tools-23.0.2 --no-ui --force
          - export DISPLAY=:99.0
          - sh -e /etc/init.d/xvfb start
        before_script:
          - . $HOME/.nvm/nvm.sh
          - nvm install 7
          - echo no | android create avd --force -n test -t android-19 --abi armeabi-v7a
          - emulator -avd test -no-audio -no-window &
          - android-wait-for-emulator
          - adb shell input keyevent 82 &
        script:
          - make travis-android
      - os: linux
        env: TEST_SUITE=android-browser
        jdk: openjdk7
        language: android
        android:
          components:
            - build-tools-23.0.2
            - android-23
            - extra-android-m2repository
            - extra-android-support
        sudo: false
        before_install:
          - export CHROME_BIN=chromium-browser
          - export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
          - export ANDROID_HOME=/usr/local/android-sdk
          - echo yes | android update sdk --all --filter build-tools-23.0.2 --no-ui --force
          - export DISPLAY=:99.0
          - sh -e /etc/init.d/xvfb start
        before_script:
          - . $HOME/.nvm/nvm.sh
          - nvm install 7
          - echo no | android create avd --force -n test -t android-19 --abi armeabi-v7a
          - emulator -avd test -no-audio -no-window &
          - android-wait-for-emulator
          - adb shell input keyevent 82 &
        script:
          - make travis-android-chrome
      - os: linux
        env: TEST_SUITE=pc-browser
        language: node_js
        sudo: false
        node_js:
          - "7"
        addons:
          apt:
            packages:
              - xvfb
        before_install:
          - export DISPLAY=':99.0'
          - Xvfb :99 -screen 0 1366x768x24 > /dev/null 2>&1 &
        script:
          - make travis-pc-electron
```
