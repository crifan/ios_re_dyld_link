# dyld_image_header_containing_address

* 定义
  * [dyld3/APIs.h](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.h.auto.html)
    * `const mach_header* dyld_image_header_containing_address(const void* addr) TEMP_HIDDEN;`
* 引用
  * [mach-o/dyld_priv.h](https://opensource.apple.com/source/dyld/dyld-421.1/include/mach-o/dyld_priv.h.auto.html)
    ```c
    // This is an optimized form of dladdr() that only returns the dli_fbase field.
    // Return NULL, if address is not in any image tracked by dyld.
    //
    // Exists in Mac OS X 10.11 and later
    extern const struct mach_header* dyld_image_header_containing_address(const void* addr);
    ```
* 实现
  * [dyldAPIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/src/dyldAPIs.cpp.auto.html)
    ```c
    const struct mach_header * dyld_image_header_containing_address(const void* address)
    {
      if ( dyld::gLogAPIs )
        dyld::log("%s(%p)\n", __func__, address);
      address = stripPointer(address);
    #if SUPPORT_ACCELERATE_TABLES
      const mach_header* mh;
      const char* path;
      if ( dyld::addressInCache(address, &mh, &path) )
        return mh;
    #endif
      ImageLoader* image = dyld::findImageContainingAddress(address);
      if ( image != NULL )
        return image->machHeader();
      return NULL;
    }
    ```
  * [APIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html)
    ```c
    const mach_header* dyld_image_header_containing_address(const void* addr)
    {
        log_apis("dyld_image_header_containing_address(%p)\n", addr);

        addr = stripPointer(addr);

        const MachOLoaded* ml;
        if ( gAllImages.infoForImageMappedAt(addr, &ml, nullptr, nullptr) )
            return ml;

        return nullptr;
    }
    ```
  * [dyldAPIsInLibSystem.cpp](https://opensource.apple.com/source/dyld/dyld-832.7.3/src/dyldAPIsInLibSystem.cpp.auto.html)
    ```c
    const struct mach_header* dyld_image_header_containing_address(const void* addr)
    {
      if ( gUseDyld3 )
        return dyld3::dyld_image_header_containing_address(addr);

      DYLD_NO_LOCK_THIS_BLOCK;
        static const mach_header* (*p)(const void*) = NULL;

      if(p == NULL)
          _dyld_func_lookup("__dyld_get_image_header_containing_address", (void**)&p);
      return p(addr);
    }
    ```

## 涉及到的地方

### _os_log_impl

```asm
    0x19c1d0b90 <+2044>: bl     0x1996d0a00              ; symbol stub for: dyld_image_header_containing_address
    0x19c1d0b94 <+2048>: cbz    x0, 0x19c1d0be0          ; <+2124>
    0x19c1d0b98 <+2052>: add    x2, sp, #0xe10            ; =0xe10 
    0x19c1d0b9c <+2056>: adrp   x1, 21
    0x19c1d0ba0 <+2060>: add    x1, x1, #0x52d            ; =0x52d 
    0x19c1d0ba4 <+2064>: bl     0x19a0db874              ; symbol stub for: getsegmentdata
```

### 杂项

```c
  *_XXKitImage = dyld_image_header_containing_address(objc_getClass("UIView"));
  *_CoreFoundationImage = dyld_image_header_containing_address(_CFArrayGetCount);
```
