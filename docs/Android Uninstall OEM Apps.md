---
share: true
---


Install ADB. The windows download is just a zip with binaries, not something that has to be installed permanently.

Youtube example:
```
$ ./adb.exe shell
oriole:/ $ pm list packages | grep youtube
package:com.google.android.apps.youtube.music
package:com.google.android.youtube
oriole:/ $ pm uninstall -k --user 0 com.google.android.youtube
Success
oriole:/ $ pm uninstall -k --user 0 com.google.android.apps.youtube.music
Success
oriole:/ $ exit
```

https://www.xda-developers.com/uninstall-carrier-oem-bloatware-without-root-access/
