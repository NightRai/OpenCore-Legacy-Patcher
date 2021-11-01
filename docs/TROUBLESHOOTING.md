# Troubleshooting

Here are some common errors users may experience while using this patcher:

* [Stuck on `This version of Mac OS X is not supported on this platform`](#stuck-on-this-version-of-mac-os-x-is-not-supported-on-this-platform)
* [Cannot boot macOS without the USB](#cannot-boot-macos-without-the-usb)
* [Infinite Recovery OS Booting](#infinite-recovery-os-reboot)
* [Reboot when entering Hibernation (`Sleep Wake Failure`)](#reboot-when-entering-hibernation-sleep-wake-failure)
* [Booting with a non-flashed GPU](#booting-with-a-non-flashed-gpu)
* [How to Boot Big Sur Recovery](#how-to-boot-big-sur-recovery)
* [Stuck on "Your Mac needs a firmware update"](#stuck-on-your-mac-needs-a-firmware-update)
* [No Brightness Control](#no-brightness-control)
* [Cannot connect Wi-Fi on Monterey with legacy cards](#cannot-connect-Wi-Fi-on-Monterey-with-legacy-cards)
* [No Graphics Acceleration on Intel Ivy Bridge and Nvidia Kepler GPUs](#no-graphics-acceleration-on-intel-ivy-bridge-and-nvidia-kepler-gpus)

## Stuck on `This version of Mac OS X is not supported on this platform`

This means macOS has detected a SMBIOS it does not support, to resolve this ensure you're booting OpenCore **before** the macOS installer in the boot picker. Reminder the option will be called `EFI Boot`

Once you've booted OpenCore at least once, your hardware should now auto boot it until either NVRAM reset or you remove the drive with OpenCore installed.

## Cannot boot macOS without the USB

By default, the OpenCore Patcher won't install OpenCore onto the internal drive itself during installs. Instead, you'll need to either [manually transfer](https://dortania.github.io/OpenCore-Post-Install/universal/oc2hdd.html) OpenCore to the internal drive's EFI or run this patcher's Option 2 again but select your internal drive.

Reminder that once this is done, you'll need to select OpenCore in the boot picker again for your hardware to remember this entry and auto boot from then on.

## Infinite Recovery OS Booting

With OpenCore Legacy Patcher, we rely on Apple Secure Boot to ensure OS updates work correctly and reliably with Big Sur. However this installs NVRAM variables that will confuse your Mac if not running with OpenCore. To resolve, simply uninstall OpenCore and [reset NVRAM](https://support.apple.com/en-mide/HT201255).

* Note: Machines with modded root volumes will also result in infinite recovery until integrity is restored

## Reboot when entering Hibernation (`Sleep Wake Failure`)

[Known issue on some models](https://github.com/dortania/Opencore-Legacy-Patcher/issues/72), temporary fix is to disable Hibernation:

```
sudo pmset -a hibernatemode 0
```

## Booting with a non-flashed GPU

For Mac Pro, Xserve and iMac users with non-flashed GPUs, you can still easily boot OpenCore and view the entire boot process. To do so, make sure SIP is disabled(to allow NVRAM write access) and run the following:

```sh
sudo bless --verbose --file /Volumes/VOLNAME/EFI/OC/OpenCore.efi --folder /Volumes/VOLNAME/EFI/OC --setBoot
```

* Note you will need to replace `VOLNAME` with the Volume name of your USB or hard drive with OpenCore
* Note 2: Once done, you can re-enable SIP
* Note 3: The EFI partition OpenCore was installed on must be mounted, if it's unmounted simply rerun "Install OpenCore to drive"

Once you boot OpenCore for the first time, LauncherOption will install itself as the top boot priority making OpenCore always launch. Combined with `RequestBootVar`, all boot options must go through OpenCore ensuring seamless usage even with OS installation and updates.

## How to Boot Big Sur Recovery

By default, the patcher will try to hide extra boot options such as recovery from the user. To make them appear, simply press the "Spacebar" inside OpenCore's Picker to list all boot options.

## Stuck on "Your Mac needs a firmware update"

Full error: "Your Mac needs a firmware update in order to install to this Volume. Please select a Mac OS Extended (Journaled) volume instead."

This error occurs when macOS determines the firmware to not have full APFS support. To resolve is quite simple, when building OpenCore head to "Patcher Settings" and enable "Moderate SMBIOS Patching" or higher. This will ensure that the firmware reported will show as supporting full APFS capabilities.

## No Brightness Control

With OCLP v0.0.22, we've added support for brightness control on many models. However some users may have noticed that their brightness keys do not work.

To work-around, we recommend user try out the below app:

* [Brightness Slider](https://actproductions.net/free-apps/brightness-slider/)

## Cannot connect Wi-Fi on Monterey with legacy cards

With OCLP v0.2.5, we've added support for legacy Wi-Fi on Monterey. However some users may have noticed that they can't connect to wireless networks.

To work-around, we recommend users to manually connect using the "other" option on the Wi-Fi menu bar or manually adding the network on the "Network" preference pane.

## No Graphics Acceleration on Intel Ivy Bridge and Nvidia Kepler GPUs

With macOS Monterey, Apple removed Graphics Drivers for both Intel Ivy Bride and Nvidia Kepler. To re-enable acceleration, simply run the Post Install Root Volume patches.

Once rebooted, acceleration will be re-enabled as well as brightness control for laptops.