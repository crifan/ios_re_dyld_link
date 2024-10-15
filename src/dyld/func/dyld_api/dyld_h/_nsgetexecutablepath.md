# _NSGetExecutablePath

* 语法
  ```c
  int _NSGetExecutablePath(char* buf, uint32_t* bufsize);
  ```

## 反越狱检测

之前代码：

```c
int _NSGetExecutablePath(char* buf, uint32_t* bufsize);

%hookf(int, _NSGetExecutablePath, char* buf, uint32_t* bufsize){
    int extPathCpSize = %orig;
    // iosLogInfo("buf=%s,*bufsize=%d -> extPathCpSize=%d", libraryName, *bufsize, extPathCpSize);
    iosLogInfo("buf=%{public}s,*bufsize=%d -> extPathCpSize=%d", buf, *bufsize, extPathCpSize);
    return extPathCpSize;
}
```

输出举例：

```bash
默认    15:38:39.329370+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=/private/var/containers/Bundle/Application/49BD25E2-7CC2-4B8D-8953-5E0DD5427B4C/Aweme.app/Aweme,*bufsize=1023 -> extPathCpSize=0

默认    15:38:39.348166+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=(null),*bufsize=96 -> extPathCpSize=-1
默认    15:38:39.348270+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=/private/var/containers/Bundle/Application/49BD25E2-7CC2-4B8D-8953-5E0DD5427B4C/Aweme.app/Aweme,*bufsize=96 -> extPathCpSize=0

默认    15:38:42.712959+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=/private/var/containers/Bundle/Application/49BD25E2-7CC2-4B8D-8953-5E0DD5427B4C/Aweme.app/Aweme,*bufsize=1024 -> extPathCpSize=0
默认    15:38:43.279102+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=/private/var/containers/Bundle/Application/49BD25E2-7CC2-4B8D-8953-5E0DD5427B4C/Aweme.app/Aweme,*bufsize=1023 -> extPathCpSize=0

默认    15:38:43.304791+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=(null),*bufsize=96 -> extPathCpSize=-1
默认    15:38:43.304954+0800    Aweme    hook_dyld.xm _NSGetExecutablePath: buf=/private/var/containers/Bundle/Application/49BD25E2-7CC2-4B8D-8953-5E0DD5427B4C/Aweme.app/Aweme,*bufsize=96 -> extPathCpSize=0
```

最新代码详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dyld.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dyld.xm)
