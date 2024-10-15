# NSVersionOfLinkTimeLibrary

* 语法
  ```c
  int32_t NSVersionOfLinkTimeLibrary(const char* libraryName);
  ```

## 反越狱检测

之前代码：

```c
%hookf(int32_t, NSVersionOfLinkTimeLibrary, const char* libraryName){
    int32_t rtLtLibVer = %orig;
    iosLogInfo("libraryName=%s -> rtLtLibVer=%d", libraryName, rtLtLibVer);
    return rtLtLibVer;
}
```

输出举例：

```bash
默认    15:34:41.972821+0800    Aweme    hook_dyld.xm NSVersionOfLinkTimeLibrary: libraryName=UIKit -> rtLtLibVer=-1

默认    15:34:43.737029+0800    Aweme    hook_dyld.xm NSVersionOfLinkTimeLibrary: libraryName=System -> rtLtLibVer=85917696

默认    15:34:46.257838+0800    Aweme    hook_dyld.xm NSVersionOfLinkTimeLibrary: libraryName=AudioToolbox -> rtLtLibVer=-1

默认    15:34:49.359575+0800    Aweme    hook_dyld.xm NSVersionOfLinkTimeLibrary: libraryName=System -> rtLtLibVer=85917696
默认    15:34:49.359627+0800    Aweme    hook_dyld.xm NSVersionOfLinkTimeLibrary: libraryName=CoreFoundation -> rtLtLibVer=-1
```

最新代码详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dyld.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dyld.xm)
