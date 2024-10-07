### This project contains the device configuration for AOSP for the Android Automotive Demonstrator.

---

## Setup

The device configuration is based on the [Raspberry Vanilla](https://github.com/raspberry-vanilla) project,
which provides all the setup we need to run Android on a Raspberry Pi 5.

1. Set up your build environment and install repo. The instructions can be found 
[here](https://source.android.com/docs/setup/start/requirements#repo).
2. Install additional packages:

```shell
sudo apt-get install bc coreutils dosfstools e2fsprogs fdisk kpartx mtools ninja-build pkg-config python3-pip
sudo pip3 install meson mako jinja2 ply pyyaml dataclasses
```

3. Initialize the repository using repo:

```shell
repo init -u https://android.googlesource.com/platform/manifest -b android-14.0.0_r22 --depth=1
```

4. Additionaly, add the manifests for Raspberry Vanilla and the demonstrator project:

```shell
curl -o .repo/local_manifests/manifest_brcm_rpi.xml -L https://raw.githubusercontent.com/raspberry-vanilla/android_local_manifest/android-14.0.0_r22/manifest_brcm_rpi.xml --create-dirs
curl -o .repo/local_manifests/remove_projects.xml -L https://raw.githubusercontent.com/raspberry-vanilla/android_local_manifest/android-14.0.0_r22/remove_projects.xml
curl -o .repo/local_manifests/manifest_jambit_rpi_demonstrator.xml https://raw.githubusercontent.com/Katric/android_local_manifest/refs/heads/android-14.0.0_r22/manifest_jambit_rpi_demonstrator.xml
```

5. Download the source code. (might take a few minutes depending on your internet speed)

```shell
repo sync
```

6. Set up the Android build environment:

```shell
source build/envsetup.sh
```

7. Select the build target using `lunch`.
   `lunch` will show you all available options. For the demonstrator, enter:

```shell
lunch aosp_demonstrator_rpi5_car-userdebug
```

8. Compile the source code. This might take some time depending on your computer.
On an AMD Ryzen 5700X CPU and 64 GB RAM it took about 2 hours.

```shell
make bootimage systemimage vendorimage -j$(nproc)
```

9. Create a flashable image for the demonstrator. 
This is the same script as in Raspberry Vanilla, but the output directory is adjusted.

```shell
./demonstrator-mkimg.sh
```

---

## Change screen resolution
To set up the correct screen resolution, edit the system property `debug.drm.mode.force` `/device/brcm/rpi5/vendor.prop`.
For the demonstrator, it must be set to `debug.drm.mode.force=1280x800`

