# NSVersionOfRunTimeLibrary

* 语法
  ```c
  int32_t NSVersionOfRunTimeLibrary(const char* libraryName);
  ```

## 反越狱检测

之前代码：

```c
int32_t NSVersionOfRunTimeLibrary(const char* libraryName);

%hookf(int32_t, NSVersionOfRunTimeLibrary, const char* libraryName){
    int32_t rtLibVer = %orig;
    iosLogInfo("libraryName=%s -> rtLibVer=%d", libraryName, rtLibVer);
    return rtLibVer;
}
```

最新代码详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dyld.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dyld.xm)
