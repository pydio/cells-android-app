# Cells Android Application

The Cells application for Android is a light client for the [Pydio Cells](https://pydio.com)
server that provides basic features to simply access and manage your files on your Android device.  
It supports both Cells (v2 to v4) and the legacy Pydio PHP (Pydio 6, 7, 8, 9) servers.  
We also support Android devices until Lollipop (Android 5, API 21).

Current repository is intended for **developers** and/or hackers.  
It provides a vanilla packaging of necessary modules to simply clone and build the application, e.g
in Android Studio.

If you are a user, rather refer to
the [main Android Client repository](https://github.com/pydio/cells-android-client) or directly to
the [application page on the Google Play store](https://play.google.com/store/apps/details?id=com.pydio.android.Client)

> Note: this application is **useless if you have no access to any Pydio server**.  
> If you don't know us, [discover what we do](https://pydio.com)!

## Set-up development environment

### Pre-requisite

- Java JDK > 1.8
- A valid `local.properties` file. We will soon find a safer/better strategy to also manage our
  builds, but for the time being, only copy `local.properties.sample` file to `local.properties` and
  adapt to your setup.
- Android Studio (optional): plug your own device or start a virtual one, and simply install and
  start the app you've just built.

### Build with the command line

```sh
git clone --recursive https://github.com/pydio/cells-android-app.git

# Clone the submodules at correct location
git submodule add -b main https://github.com/pydio/cells-sdk-java.git sdk-java
git submodule add -b main https://github.com/pydio/cells-android-client.git

# Configure local.properties file
cp local.properties.sample local.properties
vi local.properties
```

## Code architecture

- The corner stone of the app is in the `cells-client` folder. Android developers should also have a
  look at [the corresponding repository](https://github.com/pydio/cells-android-client)
- The `sdk-java` adds a [thin layer](https://github.com/pydio/cells-sdk-java) above the generated
  swagger generated code and provides a unified API to communicate with Cells but also with legacy
  Pydio PHP servers. It has a business friendly Apache 2 license and can directly be used from Java
  or Kotlin.

## License

This project is licensed under the GNU GPL V3 License - see the LICENSE file for details.

Copyright 2022 Abstrium SAS.
