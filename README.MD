Dragon:
********


match case & Regular ex. should be enabled
(magisk)|(Magisk)|(MAGISK)|(zygisk)|(Zygisk)|(ZYGISK)|(debug_ramdisk)|(/data/adb)|(/adb)
(?1dragon)(?2Dragon)(?3DRAGON)(?4rabbit)(?5Rabbit)(?6RABBIT)(?7dev/moto)(?8/data/dev)(?9/dev)

G:\Androids\projects\Magisk\kitsune-source-code-10Aug2024\app\build.gradle.kts
implementation("com.github.topjohnwu:jtar:1.0.0")

=========================
RELESE
	apktool steps
	all "su" are changed to "si" (total 2 count)
============

G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\init\rootdir.cpp
    void DragonInit::patch_ro_root() {
    mount_list.emplace_back("/data");
    parse_config_file();

    string tmp_dir;

    if (access("/sbin", F_OK) == 0) {
        tmp_dir = "/sbin";
    } else {
		xmkdir("/dev/moto", 0755);  <- ADD THIS LINE*********************************
        tmp_dir = "/dev/moto";
        xmkdir("/data/dev/moto", 0);
        xmount("/dev/moto", "/data/dev/moto", nullptr, MS_MOVE, nullptr);
    }

G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\core\applets.cpp
	constexpr Applet applets[] = {
		{ "si", su_client_main },  		  <- ADD THIS LINE ***************************
		{ "su", su_client_main },  		  <- DEL THIS LINE ****************************
		{ "resetprop", resetprop_main },
		{ "dragonprop", resetprop_main }, <- ADD THIS LINE ****************************
		{ "dragonhide", denylist_cli },
	};

G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\core\module.cpp
        static void inject_dragon_bins(dir_node *system) {
				auto bin = system->get_child<inter_node>("bin");
				if (!bin) {
					bin = new inter_node("bin");
					system->insert(bin);
				}
				//const char *bins[] = { "dragon", "dragonpolicy", "supolicy", nullptr };  <- COMMENT THIS LINE WHICH mounts in /system/bin *********************
				// for (int i = 0; bins[i]; ++i)								<- COMMENT THIS LINE WHICH mounts in /system/bin *********************
				//     bin->insert(new dragon_node(bins[i]));					<- COMMENT THIS LINE WHICH mounts in /system/bin *********************
																				
				//for (int i = 0; applet_names[i]; ++i)					        <- COMMENT THIS LINE WHICH mounts in /system/bin *********************
				//    bin->insert(new dragon_node(applet_names[i]));			<- COMMENT THIS LINE WHICH mounts in /system/bin *********************
				}

G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\init\mount.cpp
	    for (int i = 0; applet_names[i]; ++i)
			xsymlink("./dragon", applet_names[i]);
		xsymlink("./dragon64", "si"); <- ADD THIS LINE **********************************************
		xsymlink("./dragon64", "dragonprop"); <- ADD THIS LINE **********************************************
		xsymlink("./dragonpolicy", "supolicy");
G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\include\consts.hpp
	constexpr const char *applet_names[] = { "si", "resetprop", nullptr };   <- UPDATE this LINE ******************
G:\Androids\projects\Magisk\Dragon-10Aug2024\app\shared\src\main\AndroidManifest.xml
		android:label="Dragon"             <- UPDATE this LINE ******************
G:\Androids\projects\Magisk\Dragon-10Aug2024\app\build.gradle.kts
	        applicationId = "zoo.dragon.rabbit"						<- UPDATE this LINE ******************
G:\Androids\projects\Magisk\Dragon-10Aug2024\native\src\include\consts.hpp
			#define JAVA_PACKAGE_NAME "zoo.dragon.rabbit"			<- UPDATE this LINE ******************
G:\Androids\projects\Magisk\Dragon-10Aug2024\stub\build.gradle.kts
			applicationId = "zoo.dragon.rabbit"						<- UPDATE this LINE ******************
G:\Androids\projects\Magisk\Dragon-10Aug2024\app\src\main\java\com\topjohnwu\dragon\core\tasks\HideAPK.kt
        val repack = File(activity.cacheDir, "patched.apk")
        val pkg = "zoo.dragon.rabbithidy"                    <- UPDATE this LINE ******************


update both main app and tub app wth AppM and update stub to main app and sign. due ot this hiding apk will fail, but AppM modifed stub apk will save in boot.img.










============================

![](docs/images/logo.png)

[![Downloads](https://img.shields.io/badge/dynamic/json?color=green&label=Downloads&query=totalString&url=https%3A%2F%2Fraw.githubusercontent.com%2Ftopjohnwu%2Fmagisk-files%2Fcount%2Fcount.json&cacheSeconds=1800)](https://raw.githubusercontent.com/topjohnwu/magisk-files/count/count.json)

#### This is not an officially supported Google product

## Introduction

Magisk is a suite of open source software for customizing Android, supporting devices higher than Android 6.0.<br>
Some highlight features:

- **MagiskSU**: Provide root access for applications
- **Magisk Modules**: Modify read-only partitions by installing modules
- **MagiskBoot**: The most complete tool for unpacking and repacking Android boot images
- **Zygisk**: Run code in every Android applications' processes

## Downloads

[Github](https://github.com/topjohnwu/Magisk/) is the only source where you can get official Magisk information and downloads.

[![](https://img.shields.io/badge/Magisk-v26.4-blue)](https://github.com/topjohnwu/Magisk/releases/tag/v26.4)
[![](https://img.shields.io/badge/Magisk%20Beta-v26.4-blue)](https://github.com/topjohnwu/Magisk/releases/tag/v26.4)
[![](https://img.shields.io/badge/Magisk-Canary-red)](https://raw.githubusercontent.com/topjohnwu/magisk-files/canary/app-release.apk)
[![](https://img.shields.io/badge/Magisk-Debug-red)](https://raw.githubusercontent.com/topjohnwu/magisk-files/canary/app-debug.apk)

## Useful Links

- [Installation Instruction](https://topjohnwu.github.io/Magisk/install.html)
- [Building and Development](https://topjohnwu.github.io/Magisk/build.html)
- [Magisk Documentation](https://topjohnwu.github.io/Magisk/)

## Bug Reports

**Only bug reports from Debug builds will be accepted.**

For installation issues, upload both boot image and install logs.<br>
For Magisk issues, upload boot logcat or dmesg.<br>
For Magisk app crashes, record and upload the logcat when the crash occurs.

## Translation Contributions

Default string resources for the Magisk app and its stub APK are located here:

- `app/src/main/res/values/strings.xml`
- `stub/src/main/res/values/strings.xml`

Translate each and place them in the respective locations (`[module]/src/main/res/values-[lang]/strings.xml`).

## License

    Magisk, including all git submodules are free software:
    you can redistribute it and/or modify it under the terms of the
    GNU General Public License as published by the Free Software Foundation,
    either version 3 of the License, or (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
