# Jerk Installer
First of all, this isn't the insult or malicious word.
It is in homage to the wonderful software [Snap](https://en.wikipedia.org/wiki/Snap_(software)) and [Crackle](https://gitlab.com/tuxecure/crackle-apt/crackle) 😁 I also love physics! No, not really...... 😂

Jerk is a script for installing custom patches in Ubuntu Touch devices with ease.
This is specially useful for patches that only require changes to text-based files such as QML files.
It basically copies files from a specifically structured tar file into the system and backs up the original files.
Sample patches are changes in the UI in Lomiri and new features in the on-screen keyboard.

## Warning
* This tool offers convenience to the user but it doesn't mean it should be used by everyone. The processes done by the script is dangerous and could possibly break your system. Custom patches are not supported by Ubuntu Touch.
* Please only use this tool if you know what you are doing and/or you accept the risk of breaking your device's system. In case of breakage, you can use the [UBports Installer](https://devices.ubuntu-touch.io/installer/ "UBports Installer") to reflash your device without wiping (to retain all your data and apps) or whichever steps you did to flash your device with Ubuntu Touch.
* Please use packages only from trusted sources. Checking the contents of the package is also highly recommended if you know your way around these things.
* Compatibility check is simplistic and is NOT a sure thing since the script does not actually analyze the codes.
* Installing multiple packages in one component is also NOT recommended since the script isn't capable of keeping track of installed packages and their compatibilities. You can still try but you are on your own 😄
* Installing a package automatically blocks OTA updates to avoid users to install an update that may have conflict with the installed package(s). Running `jerk reset all` is highly recommended before installing an OTA update. Or run `jerk unblock-ota` to unblock OTA udpates.
* Running via SSH is highly recommended so it is easy to reset in case of breakage especially when installing packages for Lomiri.
* Lastly, this script was created by a person with minimum bash scripting skills so beware 😆

## How to use
1.  Download the whole repo (_sample_packages_ folder is optional)
2. Make sure `jerk` file is allowed to be executed as a program or executable.
3. Run `jerk check <Package Name/Path>` to check if the package is compatible to your system.
4. Run `jerk install <Package Name/Path>` to directly install the package. Compatibility will still be checked but the result won't be as detailed as the `check` command.
5. To uninstall, run `jerk uninstall <Package Name/Path>` or `jerk reset <component name i.e. lomiri>`
6. To unblock OTA updates, run `jerk unblock-ota` or `jerk reset all` to reset all known components and unblock OTA updates.

See **Help** section for more details on how to use the script.

## How to create packages
Packages are tar files in `gzip (tar.gz)` format. Only this format is known to work.
We are limited to tar files that Ubuntu Touch can extract out of the box. `tar.xz` is  known to be NOT supported.

### File structure
#### Top Level
* **ORIG** (Directory)
 - Contains all the vanilla/original files that will be modified by the package
 - All files in this directory should be present in the `MOD` directory otherwise, it'll be detected as invalid.
 - The files in this directory are used to determine compatibility so it's important that all files exactly match their original state.
 - Structure of the directory must be patterned with the `Target path`
* **MOD** (Directory)
 - Contains all the modified files that will be installed in the system
 - Make sure all modified files have their original files counterpart in the `ORIG` directory.
 - New files, as expected, don't have original files counterpart.
 - Make sure ONLY actual new files have no counterpart in the `ORIG` directory, all files with no `ORIG` counterpart will be DELETED upon uninstallation which may cause system breakage if system files are deleted unexpectedly.
 - Structure of the directory must be patterned with the `Target path`
* **config** (File)
 - Contains configuration values of the package
 ##### Config values:
   - _target_component_   
         The name of the target component of the package.
		 Components have pre-configured paths, post install command, etc.
         Valid component names: (See _components_ folder for more up to date list)
         -- bluetooth_conf (Bluetooth configuration)
         -- device-configuration (Device configuration)
         -- lomiri (Lomiri - Desktop Environment)
        -- maliit-keyboard (On-screen Keyboard)
		
		Example: _lomiri_
   - _package_description_
      Contains the description of the package. Put anything here that coule be helpful to the user.
	  New features, fixes and changes are the recommended contents.
	  Use _\n_ to insert a new line
	  
	  Example: _This package adds a floating mode in the keyboard_
   - _custom_target_path_
    Use this if the target component isn't in the pre-configured components yet or there are special reasons to do so.
	Example: _/usr/share/unity8_

## Help
### Commands
* **block-ota**
  - Installs a package for the system settings app that blocks OTA updates
  - This is to avoid installing an OTA update that can possibly have conflict with installed packages
  - No argument needed

    Example: `jerk block-ota`
* **describe**
 -  Display details about the specified package if there's any.
  - This may include new features, fixes and changes implemented by the package
  - Accepts _Package Name_ as argument

    Example: `jerk describe Keyboard_Kugi_169.tar.gz`
* **check**
 - Check if the package is compatible with the system
 - It checks if the original files in the package match the current system files.
 - Compatibility is NOT a sure thing since the codes are not actually analyzed by the script.
 - Accepts _Package Name_ as argument

   Example: `jerk check Keyboard_Kugi_169.tar.gz`
* **install**
 - Install the specified package to the system.
 - It also checks if the package is compatible to the system before proceeding.
 - System files are backed up with the extension _.JERKORIG_
 - Accepts _Package Name_ as argument

   Example: `jerk install Keyboard_Kugi_169.tar.gz`
 
* **reset**
 - Revert all changes in the target path of the specified package or component.
 - Restores files using the `.JERKORIG` files.
 - New files installed by packages won't be removed
 - `uninstall` is recommended for a cleaner uninstallation
 - `jerk reset all` will reset all known components
 - Accepts _Package Name_ or _Component name_ or _all_ as argument
  - See **Config values** section for the list of valid component names or  _components_ folder for more up to date list

   Examples:
   `jerk reset Keyboard_Kugi_169.tar.gz`
   `jerk reset maliit-keyboard`
* **unblock-ota**
  - Removes the package that blocks OTA updates
  - This is only recommended if you're sure there's no more installed packages
  - Running `jerk reset all` is recommended which also unblocks OTA updates

     Example: `jerk unblock-ota`
* **uninstall**
 - Revert changes based on the files in the specified package.
 - Restore files using the `.JERKORIG` files.
  - Accepts _Package Name_ as argument
  
   Example: `jerk uninstall Keyboard_Kugi_169.tar.gz`
   
### Options
* **--help, -h**
 - Print help text.
* **--version, -V**
 - Print version of the script.
* **--verbose, -v**
 - Verbose mode.
 - Displays more details on what the script is doing
* **--say-yes, -y**
 - Skip all prompts, if any, by saying yes to all.
 - Be careful when using
* **--force, -f**
 - Ignore incompatibilities and force installation or uninstallation
* **--debug, -d**
 - Debug mode.
 - Verbose and display more things
 - Actions are not committed
