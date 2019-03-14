<details>
<summary>
<i>info about CRITICAL VULNERABILITY IN BEAM WALLET 9.1.2019 20:20 GMT </i>
</summary>
Critical Vulnerability was found in Beam Wallet today.

Vulnerability was discovered by Beam Dev Team and not reported anywhere else.

Vulnerability affects all previously released Beam Wallets both Dekstop and CLI.

DO NOT DELETE THE DATABASE or any other wallet data.

DO NOT USE WALLETS YOU HAVE BUILT FROM SOURCE ON MAINNET UNTIL FURTHER NOTICE

The vulnerability DOES NOT affect wallet data, secret keys or passwords

All Beam users are REQUIRED to follow the procedure below IMMEDIATELY!!!


1. Stop your currently running Beam Wallets immediately

2. Uinstall or delete your Beam Wallet application and executables from all machines. 

DO NOT DELETE THE DATABASE or any other wallet data

3. Make sure the application was deleted. Check the documentation for the location of Wallet application files (https://beam-docs.readthedocs.io/en/latest/rtd_pages/user_files_and_locations.html)

4. Download the Beam Wallet again from the website only (beam.mw/downloads) 
 
It will have THE SAME version numbers as previously published archives
Make sure the SHA256 of the archive matches with the one published on the website.

5. Install the new application



Details for the vulnerability and the CVE will be published within a week to avoid exploits.

</details>













# Windows
1. Install Visual Studio >= 2017 with CMake support.
1. Download and install Boost prebuilt binaries https://sourceforge.net/projects/boost/files/boost-binaries/1.68.0/boost_1_68_0-msvc-14.1-64.exe, also add `BOOST_ROOT` to the _Environment Variables_.
1. Download and install OpenSSL prebuilt binaries https://slproweb.com/products/Win32OpenSSL.html (`Win64 OpenSSL v1.1.0h` for example) and add `OPENSSL_ROOT_DIR` to the _Environment Variables_.
1. Download and install QT 5.11 https://download.qt.io/official_releases/qt/5.11/5.11.0/qt-opensource-windows-x86-5.11.0.exe.mirrorlist and add `QT5_ROOT_DIR` to the _Environment Variables_ (usually it looks like `.../5.11.0/msvc2017_64`), also add `QML_IMPORT_PATH` (it should look like `%QT5_ROOT_DIR%\qml`). BTW disabling system antivirus on Windows makes QT installing process much faster.
1. Add `.../qt511/5.11.1/msvc2017_64/bin` and `.../boost_1_68_0/lib64-msvc-14.1` to the _System Path_.
1. Open project folder in Visual Studio, select your target (`Release-x64` for example, if you downloaded 64bit Boost and OpenSSL) and select `CMake -> Build All`.
1. Go to `CMake -> Cache -> Open Cache Folder -> beam` (you'll find `beam.exe` in the `beam` subfolder, `beam-wallet.exe` in `ui` subfolder).

# Linux 

## Ubuntu 14.04
1. Install `gcc7` `boost` `ssl` packages.
```
  sudo add-apt-repository ppa:ubuntu-toolchain-r/test
  sudo apt update
  sudo apt install g++-7 libboost-all-dev libssl-dev -y
```
2. Set it up so the symbolic links `gcc`, `g++` point to the newer version:
```
  sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 60 \
                           --slave /usr/bin/g++ g++ /usr/bin/g++-7 
  sudo update-alternatives --config gcc
  gcc --version
  g++ --version
```
3. Install latest CMake 
```
  wget "https://cmake.org/files/v3.12/cmake-3.12.0-Linux-x86_64.sh"
  sudo sh cmake-3.12.0-Linux-x86_64.sh --skip-license --prefix=/usr
```
4. Add proper QT 5.11 repository depending on your system https://launchpad.net/~beineri (for example, choose `Qt 5.10.1 for /opt Trusty` if you have Ubuntu 14.04), install `sudo apt-get install qt510declarative qt510svg` packages and add `export PATH=/opt/qt511/bin:$PATH`.
5. Go to Beam project folder and call `cmake -DCMAKE_BUILD_TYPE=Release . && make -j4`.
6. You'll find _Beam_ binary in `bin` folder, `beam-wallet` in `ui` subfolder.

## Fedora 29
1. Install dependencies
```
sudo dnf install gcc-c++ libstdc++-static boost-devel openssl-devel qt5-devel 
```
2. Go to the Beam project folder and start the release build
```
cmake -DCMAKE_BUILD_TYPE=Release . && make -j4
```
3. You'll find the the `beam-node` binary (node) in the `beam` folder, `beam-wallet` (cli wallet) in the `wallet` folder and `BeamWallet` (GUI wallet) in the `ui` folder.

At the moment step #2 can take a lot of time (a few hours) due to the bug in QT: [QTBUG-27936](https://bugreports.qt.io/browse/QTBUG-27936).
Use `-DBEAM_NO_QT_UI_WALLET=On` to skip building the UI. 


# Mac
1. Install Brew Package Manager.
1. Installed necessary packages using `brew install openssl boost cmake qt5` command.
1. Add `export OPENSSL_ROOT_DIR="/usr/local/opt/openssl"` and `export PATH=/usr/local/opt/qt/bin:$PATH` to the _Environment Variables_.
1. Go to Beam project folder and call `cmake -DCMAKE_BUILD_TYPE=Release . && make -j4`.
1. You'll find _Beam_ binary in `bin` folder, `beam-wallet` in `ui` subfolder.

If you don't want to build UI don't install QT5 and add `-DBEAM_NO_QT_UI_WALLET=On` command line parameter when you are calling `cmake`.
