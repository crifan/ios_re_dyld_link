# dlclose

* 所属头文件: `dlfcn.h`
* 语法定义
  ```c
  int dlclose(void* handle);
  ```

## man page = help

```bash
DLCLOSE(3)               BSD Library Functions Manual               DLCLOSE(3)

NAME
     dlclose -- close a dynamic library or bundle

SYNOPSIS
     #include <dlfcn.h>

     int
     dlclose(void* handle);

DESCRIPTION
     dlclose() releases a reference to the dynamic library or bundle refer-enced referenced
     enced by handle.  If the reference count drops to 0, the bundle is
     removed from the address space, and handle is rendered invalid.  Just
     before removing a dynamic library or bundle in this way, any termination
     routines in it are called.  handle is the value returned by a previous
     call to dlopen.

     Prior to Mac OS X 10.5, only bundles could be unloaded.  Starting in Mac
     OS X 10.5, dynamic libraries may also be unloaded.  There are a couple of
     cases in which a dynamic library will never be unloaded: 1) the main exe-cutable executable
     cutable links against it, 2) An API that does not supoort unloading (e.g.
     NSAddImage()) was used to load it or some other dynnamic library that
     depends on it, 3) the dynamic library is in dyld's shared cache.

RETURN VALUES
     If dlclose() is successful, it returns a value of 0.  Otherwise it
     returns -1, and sets an error string that can be retrived with dlerror().

SEE ALSO
     dlopen(3) dlsym(3) dlerror(3) dyld(3) ld(1) cc(1)

                                  Nov 6, 2006
```

## 反越狱检测

最新代码详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dylib.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dylib.xm)
