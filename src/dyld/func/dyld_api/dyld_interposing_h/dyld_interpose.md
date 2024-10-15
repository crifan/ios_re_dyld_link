# DYLD_INTERPOSE

* `DYLD_INTERPOSE`
  * 是什么：宏定义的函数
  * 所属头文件：`dyld-interposing.h`
  * 定义
    ```c
    #define DYLD_INTERPOSE(_replacement,_replacee) \
      __attribute__((used)) static struct{ const void* replacement; const void* replacee; } _interpose_##_replacee \
                __attribute__ ((section ("__DATA,__interpose"))) = { (const void*)(unsigned long)&_replacement, (const void*)(unsigned long)&_replacee };
    ```
  * 用法举例
    ```c
    static int my_open(const char* path, int flags, mode_t mode){
      int value;
      // do stuff before open (including changing the arguments)
      value = open(path, flags, mode);
      // do stuff after open (including changing the return value(s))
      return value;
    }

    DYLD_INTERPOSE(my_open, open)
    ```

## 涉及到的内容

### FDInterposing.cpp

[FDInterposing.cpp](https://opensource.apple.com/source/lldb/lldb-159/examples/interposing/darwin/fd_interposing/FDInterposing.cpp)

```c
DYLD_INTERPOSE(__open_extended$__interposed__, __open_extended);
```

### fishhook

[facebook/fishhook](https://github.com/facebook/fishhook)

fishhook is a very simple library that enables dynamically rebinding symbols in Mach-O binaries running on iOS in the simulator and on device. This provides functionality that is similar to using `DYLD_INTERPOSE` on OS X. At Facebook, we've found it useful as a way to hook calls in libSystem for debugging/tracing purposes (for example, auditing for double-close issues with file descriptors).
