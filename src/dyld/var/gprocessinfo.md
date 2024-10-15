# gProcessInfo

* gProcessInfo
  * 变量来源
    * dyld/dyld-dyld-1042.1/dyld/DebuggerSupport.cpp
      * `struct dyld_all_image_infos* gProcessInfo = &dyld_all_image_infos;`
        * 注：
          * `struct dyld_all_image_infos`的定义详见
            * [dyld_all_image_infos](../../dyld_libdyld/definition/dyld_images_h/dyld_all_image_infos.md)

## gProcessInfo的源码中的来源

至此，暂时没有彻底搞清楚：

此处的

`iOS 15.1`，`iPhone11`中的：`/usr/lib/dyld`

导出后，用IDA分析，确认版本是：

* dyld-932.4

然后对于其中的gProcessInfo：

参考dyld源码：

* `dyld-852.2`
  * 函数定义=写法
    * `dyld::gProcessInfo`
  * 编译为：
    * `__ZN4dyld12gProcessInfoE`
* `dyld-940` == `dyld-1042.1`
  * 函数定义=写法
    * `gProcessInfo`
  * 编译为：
    * `_gProcessInfo`

-》介于`dyld-852.2`和`dyld-940`之间的：

* `dyld-932.4`
  * 要么是，旧的：
    * `dyld::gProcessInfo`
      * `__ZN4dyld12gProcessInfoE`
  * 要么是，新的
    * `gProcessInfo`
      * `_gProcessInfo`

-》但是实际上（`rabin2`、`jtool2`）查看发现symbol中的`gProcessInfo`却是：

* `__ZN5dyld412gProcessInfoE`
  * （demangle后）对应的是：`dyld4::gProcessInfo`
    * 和上述的推断，都对不上

-》暂时不清楚根本原因是什么

而对于

* https://opensource.apple.com/source/dyld/dyld-732.8
* https://opensource.apple.com/source/dyld/dyld-733.6

中的

`src/dyld_debugger.cpp`

中的定义都是一样的：

```c
    namespace dyld {
        struct dyld_all_image_infos* gProcessInfo = &dyld_all_image_infos;
    }
```

都是一样的

-》推断出：介于`dyld-732.8`和`733.6`的`dyld-733.3.1`，也是一样的

-》所以应该是：

* `dyld-733.3.1`
  * 变量定义：`dyld::gProcessInfo`
  * 编译生成：`__ZN4dyld12gProcessInfoE`

->是和前面的调试分析对的上的。

说明自己的理解也是对的，是没问题的。

## 涉及到的例子

### register read

```bash
(lldb) register read -f d

General Purpose Registers:
        x0 = 1
        x1 = 7208627091  "ibclang_rt.tsan"
        x2 = 16
        x3 = -43
        x4 = 65
        x5 = 108
        x6 = 0
        x7 = 480
        x8 = 4337565856  dyld`initialPoolContent + 96
        x9 = 47
       x10 = 4337565856  dyld`initialPoolContent + 96
       x11 = 0
       x12 = 83
       x13 = 4294967277
       x14 = 7208626807  "NSDeallocateZombies"
       x15 = 78
       x16 = 7204937648  libsystem_platform.dylib`_platform_strncmp
       x17 = 4
       x18 = 0
       x19 = 7208627090  "libclang_rt.tsan"
       x20 = 596
       x21 = 1
       x22 = 4337565760  dyld`initialPoolContent
       x23 = 8498683904  CoreFoundation`_CFMachPortQueue.__CFMachPortQueue
       x24 = 8498679808  __CFRuntimeClassTable + 14384
       x25 = 6135330952
       x26 = 0
       x27 = 6135299808
       x28 = 4337551168  dyld::gProcessInfo
        fp = 6135297568
        lr = 7208042740  CoreFoundation`_CFGetHandleForLoadedLibrary + 44
        sp = 6135297536
        pc = 7205967128  libdyld.dylib`_dyld_get_image_name
      cpsr = -2147483648
```

### frida-core

* `frida/frida-core/src/fruity/injector.vala`

```c
        private async void ensure_libsystem_initialized_for_dyld_v4_and_above (uint64 libdyld_initialize,
                Cancellable? cancellable) throws GLib.Error {
            uint64? process_info_ptr = dyld_symbols["_gProcessInfo"];
            if (process_info_ptr == null)
                throw new Error.UNSUPPORTED ("Missing gProcessInfo");
...
```

* `frida/frida-core/src/fruity/helpers/symbol-fetcher.c`

```c
frida_fetch_dyld_symbols (char * output_buffer, const void * dyld_load_address)
...
        frida_str_equals (name, "_gProcessInfo") ||
```

### mangled name

`iPhone11_151/dyld_rabin2_s_symbols.txt`

```bash
2185 0x00064008 0x00064008 LOCAL  FUNC 0        dyld4::gProcessInfo
```

其中的：

* `dyld4::gProcessInfo`

对应的转换之前的，编译后的，mangled name是：

* `__ZN5dyld412gProcessInfoE`
