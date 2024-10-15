# getsectbynamefromheader_64

* 所属头文件: `getsect.h`
* 定义
  * 64-bit Mach-O
    * `getsectbynamefromheader_64`
      ```c
      extern const struct section_64 *getsectbynamefromheader_64(
          const struct mach_header_64 *mhp,
          const char *segname,
          const char *sectname);
      ```

## 反汇编代码

```asm
libmacho.dylib`getsectbynamefromheader_64:
->  0x1d939e44c <+0>:   stp    x28, x27, [sp, #-0x60]!
    0x1d939e450 <+4>:   stp    x26, x25, [sp, #0x10]
    0x1d939e454 <+8>:   stp    x24, x23, [sp, #0x20]
    0x1d939e458 <+12>:  stp    x22, x21, [sp, #0x30]
    0x1d939e45c <+16>:  stp    x20, x19, [sp, #0x40]
    0x1d939e460 <+20>:  stp    x29, x30, [sp, #0x50]
    0x1d939e464 <+24>:  add    x29, sp, #0x50            ; =0x50 
    0x1d939e468 <+28>:  ldr    w23, [x0, #0x10]
    0x1d939e46c <+32>:  cbz    w23, 0x1d939e50c          ; <+192>
    0x1d939e470 <+36>:  mov    x19, x2
    0x1d939e474 <+40>:  mov    x20, x1
    0x1d939e478 <+44>:  mov    x21, x0
    0x1d939e47c <+48>:  mov    w24, #0x0
    0x1d939e480 <+52>:  add    x25, x0, #0x20            ; =0x20 
    0x1d939e484 <+56>:  ldr    w8, [x25]
    0x1d939e488 <+60>:  cmp    w8, #0x19                 ; =0x19 
    0x1d939e48c <+64>:  b.ne   0x1d939e4f8              ; <+172>
    0x1d939e490 <+68>:  add    x0, x25, #0x8             ; =0x8 
    0x1d939e494 <+72>:  mov    x1, x20
    0x1d939e498 <+76>:  orr    w2, wzr, #0x10
    0x1d939e49c <+80>:  bl     0x1d93a19a8              ; symbol stub for: strncmp
    0x1d939e4a0 <+84>:  cbz    w0, 0x1d939e4b0          ; <+100>
    0x1d939e4a4 <+88>:  ldr    w8, [x21, #0xc]
    0x1d939e4a8 <+92>:  cmp    w8, #0x1                  ; =0x1 
    0x1d939e4ac <+96>:  b.ne   0x1d939e4f8              ; <+172>
    0x1d939e4b0 <+100>: ldr    w26, [x25, #0x40]
    0x1d939e4b4 <+104>: cbz    w26, 0x1d939e4f8          ; <+172>
    0x1d939e4b8 <+108>: mov    w27, #0x0
    0x1d939e4bc <+112>: add    x22, x25, #0x48           ; =0x48 
    0x1d939e4c0 <+116>: mov    x0, x22
    0x1d939e4c4 <+120>: mov    x1, x19
    0x1d939e4c8 <+124>: orr    w2, wzr, #0x10
    0x1d939e4cc <+128>: bl     0x1d93a19a8              ; symbol stub for: strncmp
    0x1d939e4d0 <+132>: cbnz   w0, 0x1d939e4e8          ; <+156>
    0x1d939e4d4 <+136>: add    x0, x22, #0x10            ; =0x10 
    0x1d939e4d8 <+140>: mov    x1, x20
    0x1d939e4dc <+144>: orr    w2, wzr, #0x10
    0x1d939e4e0 <+148>: bl     0x1d93a19a8              ; symbol stub for: strncmp
    0x1d939e4e4 <+152>: cbz    w0, 0x1d939e510          ; <+196>
    0x1d939e4e8 <+156>: add    x22, x22, #0x50           ; =0x50 
    0x1d939e4ec <+160>: add    w27, w27, #0x1            ; =0x1 
    0x1d939e4f0 <+164>: cmp    w27, w26
    0x1d939e4f4 <+168>: b.lo   0x1d939e4c0              ; <+116>
    0x1d939e4f8 <+172>: ldr    w8, [x25, #0x4]
    0x1d939e4fc <+176>: add    x25, x25, x8
    0x1d939e500 <+180>: add    w24, w24, #0x1            ; =0x1 
    0x1d939e504 <+184>: cmp    w24, w23
    0x1d939e508 <+188>: b.lo   0x1d939e484              ; <+56>
    0x1d939e50c <+192>: mov    x22, #0x0
    0x1d939e510 <+196>: mov    x0, x22
    0x1d939e514 <+200>: ldp    x29, x30, [sp, #0x50]
    0x1d939e518 <+204>: ldp    x20, x19, [sp, #0x40]
    0x1d939e51c <+208>: ldp    x22, x21, [sp, #0x30]
    0x1d939e520 <+212>: ldp    x24, x23, [sp, #0x20]
    0x1d939e524 <+216>: ldp    x26, x25, [sp, #0x10]
    0x1d939e528 <+220>: ldp    x28, x27, [sp], #0x60
    0x1d939e52c <+224>: ret    
```

![getsectbynamefromheader_64_dis_asm_code](../../../../assets/img/getsectbynamefromheader_64_dis_asm_code.png)

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

#import "CommonConfig.h"
#import "CrifanLibiOS.h"
#import "CrifanLib.h"
#import "JailbreakPathList.h"

const long DLADDR_HOOKED_ADDRESS_BASE = 0xF00000000000;
//const unsigned long DLADDR_HOOKED_ADDRESS_MAX = 0xFFFF000000000000;

void* generateHookedDladdrAddress(void *origAddr) {
//    if ((long)origAddr < (long)DLADDR_HOOKED_ADDRESS_MAX) {
    void* hookedAddr = origAddr;
    if ((long)origAddr > (long)DLADDR_HOOKED_ADDRESS_BASE) {
        hookedAddr = origAddr;
    } else {
        hookedAddr = (void*)((long)origAddr + DLADDR_HOOKED_ADDRESS_BASE);
    }
    return hookedAddr;
}

/*==============================================================================
 Hook: getsectbynamefromheader getsectbynamefromheader_64
==============================================================================*/

// Not found: Aweme call getsectbynamefromheader
//const struct section* getsectbynamefromheader(const struct mach_header *mhp, const char *segname, const char *sectname);
//
//%hookf(const struct section*, getsectbynamefromheader, const struct mach_header *mhp, const char *segname, const char *sectname){
//    const struct section* retSection = %orig;
//    iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s -> retSection=%p", mhp, segname, sectname, retSection);
//    return retSection;
//}

const struct section_64* getsectbynamefromheader_64(const struct mach_header_64 *mhp, const char *segname, const char *sectname);

%hookf(const struct section_64 *, getsectbynamefromheader_64, const struct mach_header_64 *mhp, const char *segname, const char *sectname){
//    const struct section_64* retSection64 = %orig;
     const struct section_64* retSection64 = %orig;
    
     bool isJbLib = false;

     Dl_info info;
     size_t dlInfoSize = sizeof(Dl_info);
     memset(&info, 0, dlInfoSize);
     
//        dladdr(mhp, &info);
     void* hookedAddr = generateHookedDladdrAddress((void*)mhp);
     dladdr(hookedAddr, &info);

     const char* curImgName = info.dli_fname;
     if(curImgName != NULL) {
         isJbLib = isJailbreakDylib(curImgName);
     }

     if (isJbLib) {
         iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s -> retSection64=%p -> isJbLib=%s", mhp, segname, sectname, retSection64, boolToStr(isJbLib));
         retSection64 = NULL;
    } else {
        iosLogDebug("mhp=%p,segname=%{public}s,sectname=%{public}s -> retSection64=%p", mhp, segname, sectname, retSection64);
    }

     return retSection64;
}
```

hook输出举例：

有输出：

```bash
默认    17:06:43.506840+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__TEXT,sectname=__unwind_info -> retSection64=0x1b92432e8
默认    17:06:43.506874+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__TEXT,sectname=__eh_frame -> retSection64=0x0
默认    17:06:43.506901+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__DATA,sectname=__crash_info -> retSection64=0x0
默认    17:06:43.506983+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__TEXT,sectname=__unwind_info -> retSection64=0x1b92432e8
默认    17:06:43.507030+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__TEXT,sectname=__eh_frame -> retSection64=0x0
默认    17:06:43.510524+0800    Aweme    hook_misc.xm getsectbynamefromheader_64: mhp=0x1b9243000,segname=__DATA,sectname=__crash_info -> retSection64=0x0
```

-》但是也只有这几个：

* __TEXT
  * __unwind_info
  * __eh_frame
* __DATA
  * __crash_info
