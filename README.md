# ukg-workforce-root-detection-list
A list of ways I found the UKG Workforce (formally known as Kronos) app uses to check for root access.

*Sincerely,*

*A former supermarket employee forced to use Kronos for checking timesheets.*

(by the way android root users can still use your web dashboard which does the same thing as your app 😱😱😱)

## Ways that the Kronos (or UKG workforce) app checks for root or modifications to device:
- Checks if your phone is in `release` mode and if USB debugging is active (AKA `adb`).
	- Might be checking for release mode via `getprop ro.build.type`!=user or `getprop ro.debuggable`!=0?
- Checks if you are emulating or spoofing anything.
	- Checks if your location setting is spoofed or uses a mock provider `isFromMockProvider`.
	- Could be doing other things as well but I didn't want to spend too much time looking into this.
- Checks if your device is using test keys by looking for `"test-keys"` in the props string. 
	- Likely does this by either by reading some `build.prop` properties or through `getprop ro.build.fingerprint`.
- Checks if your device has SU binaries or files associated with root access accessible anywhere commonly known.
	- Specifically, it checks for: `/system/app/Superuser.apk`, `/sbin/su`, `/system/bin/su`, `/system/xbin/su`, `/data/local/xbin/su`, `/data/local/bin/su`, `/system/sd/xbin/su`, `/system/bin/failsafe/su`, `/data/local/su`, `/su/bin/su`, `/data/local/tmp/frida-server-15.1.1-android-x86`, `/data/local/tmp/frida-core-devkit-15.1.3-android-arm.tar.xz`, `/data/local/tmp/frida-core-devkit-15.1.3-android-arm64.tar.xz`, `/data/local/tmp/frida-core-devkit-15.1.3-android-x86.tar.xz`, `/data/local/tmp/frida-core-devkit-15.1.3-android-x86_64.tar.xz`, `/data/local/tmp/frida-gadget-15.1.3-android-arm.so.xz`, `/data/local/tmp/frida-gadget-15.1.3-android-arm64.so.xz`, `/data/local/tmp/frida-gadget-15.1.3-android-x86.so.xz`, `/data/local/tmp/frida-gadget-15.1.3-android-x86_64.so.xz`, `/data/local/tmp/frida-gum-devkit-15.1.3-android-arm.tar.xz`, `/data/local/tmp/frida-gum-devkit-15.1.3-android-arm64.tar.xz`, `/data/local/tmp/frida-gum-devkit-15.1.3-android-x86.tar.xz`, `/data/local/tmp/frida-gum-devkit-15.1.3-android-x86_64.tar.xz`, `/data/local/tmp/frida-gumjs-devkit-15.1.3-android-arm.tar.xz`, `/data/local/tmp/frida-gumjs-devkit-15.1.3-android-arm64.tar.xz`, `/data/local/tmp/frida-gumjs-devkit-15.1.3-android-x86.tar.xz`, `/data/local/tmp/frida-gumjs-devkit-15.1.3-android-x86_64.tar.xz`, `/data/local/tmp/frida-inject-15.1.3-android-arm.xz`, `/data/local/tmp/frida-inject-15.1.3-android-arm64.xz`, `/data/local/tmp/frida-inject-15.1.3-android-x86.xz`, `/data/local/tmp/frida-inject-15.1.3-android-x86_64.xz`, `/data/local/tmp/frida-server-15.1.3-android-arm.xz`, `/data/local/tmp/frida-server-15.1.3-android-arm64.xz`, `/data/local/tmp/frida-server-15.1.3-android-x86.xz`, `/data/local/tmp/frida-server-15.1.3-android-x86_64.xz`
- Checks if you have any "dangerous" props set.
	- Checks if `ro.debuggable` is `1`.
	- Checks if `ro.secure` is `0` (I think anyways, `ro.secure` being `1` means device is in a "secure" state with `0` meaning it's running with disabled security features or some debug mode.
- Checks if any of the following directories are accessible/can be opened?
	- Checks for these directories: `/data/local/`, `/data/local/bin/`, `/data/local/xbin/`, `/sbin/`, `/su/bin/`, `/system/bin/`, `/system/bin/.ext/`, `/system/bin/failsafe/`, `/system/sd/xbin/`, `/system/usr/we-need-root/`, `/system/xbin/`, `/cache`, `/data`, `/dev`, `/data/dalvik-cache`, `/data/app`, `/system/app`, `/system/usr`, `/data/data`.
	- I'm not certain if this is what it does since the logging is a bit misleading, but from experience a normal app should not be allowed to access these directories, nor should some of them like `we-need-root` exist so just make sure all of these either return `Permission denied` or `No file or directory`.
- Checks if the `mount` command exists and returns any interesting paths that are mounted as `rw`.
	- Checks for these specifically: `/system`, `/system/bin`, `/system/sbin`, `/system/xbin`, `/vendor/bin`, `/sbin`, `/etc`.
	- In most devices this command shouldn't be available in a non-root context so as long as you can't use it you should be fine, the app aborts the check if the `mount` command doesn't exist or errors.
- Checks if `which su` command exists and has a non-empty output.
	- Again, in most devices the `which` command doesn't exist in a non-root context and the check aborts if `which su` doesn't exist or returns nothing.
- Checks if any "untrusted" apps are installed on the device via the `PackageManager`.
	- These include: `com.koushikdutta.rommanager`, `com.koushikdutta.rommanager.license`, `com.dimonvideo.luckypatcher`, `com.chelpus.lackypatch`, `com.ramdroid.appquarantine`, `com.ramdroid.appquarantinepro`, `com.android.vending.billing.InAppBillingService.COIN`, `com.chelpus.luckypatcher`, `com.noshufou.android.su`, `com.noshufou.android.su.elite`, `eu.chainfire.supersu`, `com.koushikdutta.superuser`, `com.thirdparty.superuser`, `com.yellowes.su`, `com.topjohnwu.magisk`, `com.devadvance.rootcloak`, `com.devadvance.rootcloakplus`, `de.robv.android.xposed.installer`, `com.saurik.substrate`, `com.zachspong.temprootremovejb`, `com.amphoras.hidemyroot`, `com.amphoras.hidemyrootadfree`, `com.formyhm.hiderootPremium`, `com.formyhm.hideroot`, `me.phh.superuser`, `eu.chainfire.supersu.pro`, `com.kingouser.com`, `com.jrummy.root.browserfree`, `com.oasisfeng.greenifiy`, `com.jrummy.apps.build.prop.editor`, `com.grarak.kerneladiutor`, `org.namelessrom.devicecontrol`, `com.jumobile.manager.systemapp`.
	- I'd like to also point out some of these aren't even root-exclusive apps, for example Greenify has a non-root mode so I guess if you're a non-root Greenify user you're being discriminated against as well.
- Checks if any known tools/names are mapped into the apps process by reading `/proc/PID/maps`, if it's readable.
	- Checks if the read dump contains the strings: `frida`, `frida-agent`, `gum-js-loop`, `gmain`.
	- By the way, just wanted to mention that this was terribly written and is incredibly inefficient and does the same checks on large string buffers multiple times; while it doesn't really matter for one-time start-up root checks, come on, these developers are likely being paid a fortune to write terrible code that I've seen GCSE students (aged 16 & under) write much butter, not even the Java build optimiser was able to save this abomination of a function.
- Checks if any known tools/names (primarily Magisk-related checks) are mounted into the apps process by reading `/proc/PID/mounts`, if it's readable.
	- Checks if the read dump contains the strings: `magisk`, `core/mirror`, `core/img`.
	- You can hide this by adding the app to Magisk Hide.
