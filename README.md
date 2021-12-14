#### Debian/Ubuntu

```bash
# runtime dependencies
sudo apt install ffmpeg libsdl2-2.0-0 adb libusb-1.0-0

# client build dependencies
sudo apt install gcc git pkg-config meson ninja-build libsdl2-dev \
                 libavcodec-dev libavdevice-dev libavformat-dev libavutil-dev \
                 libusb-1.0-0-dev

# server build dependencies
sudo apt install openjdk-11-jdk
```

On old versions (like Ubuntu 16.04), `meson` is too old. In that case, install
it from `pip3`:

```bash
sudo apt install python3-pip
pip3 install meson
```


#### Fedora

```bash
# enable RPM fusion free
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm

# client build dependencies
sudo dnf install SDL2-devel ffms2-devel libusb-devel meson gcc make

# server build dependencies
sudo dnf install java-devel
```



### Windows

#### Cross-compile from Linux

This is the preferred method (and the way the release is built).

From _Debian_, install _mingw_:

```bash
sudo apt install mingw-w64 mingw-w64-tools
```

You also need the JDK to build the server:

```bash
sudo apt install openjdk-11-jdk
```

Then generate the releases:

```bash
./release.sh
```

It will generate win32 and win64 releases into `dist/`.


#### In MSYS2

From Windows, you need [MSYS2] to build the project. From an MSYS2 terminal,
install the required packages:

[MSYS2]: http://www.msys2.org/

```bash
# runtime dependencies
pacman -S mingw-w64-x86_64-SDL2 \
          mingw-w64-x86_64-ffmpeg

# client build dependencies
pacman -S mingw-w64-x86_64-make \
          mingw-w64-x86_64-gcc \
          mingw-w64-x86_64-pkg-config \
          mingw-w64-x86_64-meson
```

For a 32 bits version, replace `x86_64` by `i686`:

```bash
# runtime dependencies
pacman -S mingw-w64-i686-SDL2 \
          mingw-w64-i686-ffmpeg

# client build dependencies
pacman -S mingw-w64-i686-make \
          mingw-w64-i686-gcc \
          mingw-w64-i686-pkg-config \
          mingw-w64-i686-meson
```

Java (>= 7) is not available in MSYS2, so if you plan to build the server,
install it manually and make it available from the `PATH`:

```bash
export PATH="$JAVA_HOME/bin:$PATH"
```

### Mac OS

Install the packages with [Homebrew]:

[Homebrew]: https://brew.sh/

```bash
# runtime dependencies
brew install sdl2 ffmpeg

# client build dependencies
brew install pkg-config meson
```

Additionally, if you want to build the server, install Java 8 from Caskroom, and
make it available from the `PATH`:

```bash
brew tap homebrew/cask-versions
brew install adoptopenjdk/openjdk/adoptopenjdk11
export JAVA_HOME="$(/usr/libexec/java_home --version 1.11)"
export PATH="$JAVA_HOME/bin:$PATH"
```

### Docker

See [pierlon/scrcpy-docker](https://github.com/pierlon/scrcpy-docker).


## Common steps

**As a non-root user**, clone the project:

```bash
git clone https://github.com/Genymobile/scrcpy
cd scrcpy
```


### Build

You may want to build only the client: the server binary, which will be pushed
to the Android device, does not depend on your system and architecture. In that
case, use the [prebuilt server] (so you will not need Java or the Android SDK).

[prebuilt server]: #option-2-use-prebuilt-server


#### Option 1: Build everything from sources

Install the [Android SDK] (_Android Studio_), and set `ANDROID_SDK_ROOT` to its
directory. For example:

[Android SDK]: https://developer.android.com/studio/index.html

```bash
# Linux
export ANDROID_SDK_ROOT=~/Android/Sdk
# Mac
export ANDROID_SDK_ROOT=~/Library/Android/sdk
# Windows
set ANDROID_SDK_ROOT=%LOCALAPPDATA%\Android\sdk
```

Then, build:

```bash
meson x --buildtype release --strip -Db_lto=true
ninja -Cx  # DO NOT RUN AS ROOT
```

_Note: `ninja` [must][ninja-user] be run as a non-root user (only `ninja
install` must be run as root)._

[ninja-user]: https://github.com/Genymobile/scrcpy/commit/4c49b27e9f6be02b8e63b508b60535426bd0291a


#### Option 2: Use prebuilt server

 - [`scrcpy-server-v1.21`][direct-scrcpy-server]  
   _(SHA-256: dbcccab523ee26796e55ea33652649e4b7af498edae9aa75e4d4d7869c0ab848)_

[direct-scrcpy-server]: https://github.com/Genymobile/scrcpy/releases/download/v1.21/scrcpy-server-v1.21

Download the prebuilt server somewhere, and specify its path during the Meson
configuration:

```bash
meson x --buildtype release --strip -Db_lto=true \
    -Dprebuilt_server=/path/to/scrcpy-server
ninja -Cx  # DO NOT RUN AS ROOT
```

The server only works with a matching client version (this server works with the
`master` branch).


### Run without installing:

```bash
./run x [options]
```


### Install

After a successful build, you can install _scrcpy_ on the system:

```bash
sudo ninja -Cx install    # without sudo on Windows
```

This installs three files:

 - `/usr/local/bin/scrcpy`
 - `/usr/local/share/scrcpy/scrcpy-server`
 - `/usr/local/share/man/man1/scrcpy.1`

You can then [run](README.md#run) _scrcpy_.

### Uninstall

```bash
sudo ninja -Cx uninstall  # without sudo on Windows
```
