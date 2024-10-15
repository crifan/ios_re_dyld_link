# DYLD_PRINT_LIBRARIES

调试时：

* `export DYLD_PRINT_LIBRARIES=1`
  * 或：
    * `export DYLD_PRINT_LIBRARIES=YES`

输出举例

### 举例1

```bash
dyld: loaded: /usr/lib/libiconv.2.dylib
dyld: loaded: /System/Library/Frameworks/Security.framework/Versions/A/Security
dyld: loaded: /System/Library/Frameworks/CoreFoundation.framework/Versions/A/CoreFoundation
dyld: loaded: /usr/lib/libz.1.dylib
dyld: loaded: /usr/lib/libSystem.B.dylib
dyld: loaded: /usr/lib/libresolv.9.dylib
dyld: loaded: /usr/lib/system/libcache.dylib
dyld: loaded: /usr/lib/system/libcommonCrypto.dylib
dyld: loaded: /usr/lib/system/libcompiler_rt.dylib
```
