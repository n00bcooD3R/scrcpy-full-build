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

#### STEP2
```
git clone https://github.com/Genymobile/scrcpy
```

#### STEP3
```
cd scrcpy
mkdir scrcpy-server
cd scrcpy-server
```

```
wget https://github.com/Genymobile/scrcpy/releases/download/v1.21/scrcpy-server-v1.21

echo"dbcccab523ee26796e55ea33652649e4b7af498edae9aa75e4d4d7869c0ab848 $(ls *)" > sum.txt

sha256sum -c sum.txt

rm sum.txt

cd ..
```

```
meson x --buildtype release --strip -Db_lto=true \
    -Dprebuilt_server=/path to server with server name
```

```
ninja -Cx

sudo ninja -Cx install

last scrcpy
```

