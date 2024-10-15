# dladdr

* 所属头文件: `dlfcn.h`
* 语法定义
  ```c
  int dladdr(const void* addr, Dl_info* info);
  ```

## man page

```bash
DLADDR(3)                BSD Library Functions Manual                DLADDR(3)

NAME
     dladdr -- find the image containing a given address

SYNOPSIS
     #include <dlfcn.h>

     int
     dladdr(const void* addr, Dl_info* info);

DESCRIPTION
     The dladdr() function queries dyld (the dynamic linker) for information
     about the image containing the address addr.  The information is returned
     in the structure specified by info.  The structure contains at least the
     following members:

     const char* dli_fname     The pathname of the shared object containing
                               the address.

     void* dli_fbase           The base address (mach_header) at which the
                               image is mapped into the address space of the
                               calling process.

     const char* dli_sname     The name of the nearest run-time symbol with a
                               value less than or equal to addr.

     void* dli_saddr           The value of the symbol returned in dli_sname.

     The dladdr() function is available only in dynamically linked programs.

ERRORS
     If an image containing addr cannot be found, dladdr() returns 0.  On suc-cess, success,
     cess, a non-zero value is returned.

     If the image containing addr is found, but no nearest symbol was found,
     the dli_sname and dli_saddr fields are set to NULL.

SEE ALSO
     dyld(3), dlopen(3)

HISTORY
     The dladdr() function first appeared in the Solaris operating system.

AUTHORS
     Mac OS X 10.3 incorporated the dlcompat package written by Jorge Acereda
     <jacereda@users.sourceforge.net> and Peter O'Gorman <ogor-
     man@users.sourceforge.net>.

     In Mac OS X 10.4, dlopen was rewritten to be a native part of dyld.

     This man page was borrowed from FreeBSD and modified.

BUGS
     This implementation is almost bug-compatible with the Solaris implementa-tion. implementation.
     tion.  The following bugs are present:

     oo   Returning 0 as an indication of failure goes against long-standing
         Unix tradition.

BSD                           September 24, 2004                           BSD
```

## 越狱检测

### 最新的逻辑

可以用代码：

```c
    Dl_info dylib_info;
    int (*func_stat)(const char *, struct stat *) = stat;
    int ret = dladdr(func_stat, &dylib_info);
```

可以解析出动态库的信息了：

```bash
usr/lib/system/libsystem_kernel.dylib
```

类似的：

```c
    FILE* (*func_fopen)(const char *filename, const char *mode) = fopen;
    int ret = dladdr(func_fopen, &dylib_info);
```

是：

```bash
/usr/lib/system/libsystem_c.dylib
```

此时：

* 非越狱手机（iOS 12.5.5的iPhone6P）
  * 返回的是：（符合预期的）系统的库

但是：

* 已越狱手机（iOS 13.6.1的iPhone7 和 iOS 12.4.5的iPhone6） + 已hook对应函数：stat、fopen等
  * 也是返回系统的库：
    * 和预期不一样，应该返回：非系统库
      * 后续才能用这个逻辑，去判断是否越狱
  * 现在就是：此办法，无法用于检测是否是越狱了

目前没找到根本原因

推测是：苹果iOS系统早已升级dladdr的内部实现机制

使得，之前（多年前）生效的用dladdr检测是否是越狱的机制，现已失效。

最新的结论是：

是用

* `_dyld_image_count`
* `_dyld_get_image_name`
* `_dyld_get_image_header`
* `_dyld_get_image_vmaddr_slide`

成套的hook，去实现的：

* 越狱检测
  * 检测是否有反越狱库

其相关代码是：

```c
static NSString* checkImageResult = @"未发现越狱库 -> 非越狱手机";
NSMutableArray *checkImageFoundJbLibList = NULL;

+ (void)load {
  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
      checkImageFoundJbLibList = [NSMutableArray array];
      _dyld_register_func_for_add_image(_check_image);
  });
}

+ (instancetype)sharedInstance {
    static id sharedInstance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [self new];
    });
    return sharedInstance;
}

static void _check_image(const struct mach_header *header, intptr_t slide) {
    Dl_info info;
    size_t dlInfoSize = sizeof(Dl_info);
    memset(&info, 0, dlInfoSize);

    dladdr(header, &info);
    const char* curImgName = info.dli_fname;
    if(curImgName != NULL) {
        if (isJailbreakDylib(curImgName)) {
            NSString *curImgNameNs = [NSString stringWithUTF8String: curImgName];
            [checkImageFoundJbLibList addObject: curImgNameNs];
            NSString *jbLibListStr = [CrifanLibiOS nsStrListToStr:checkImageFoundJbLibList isSortList:TRUE isAddIndexPrefix:TRUE];
            checkImageResult = [NSString stringWithFormat: @"发现越狱动态库 -> 越狱手机\n%@", jbLibListStr];
            NSLog(@"%@", checkImageResult);
            // "Found Jailbreak dylib: /usr/lib/substitute-inserter.dylib -> 越狱手机"
        }
    }
    return;
}
```

最新完整代码，详见：

* [crifan/iOSJailbreakDetection: iOS的ObjC的app，实现iOS越狱检测](https://github.com/crifan/iOSJailbreakDetection)
  * [JbDetectOtherViewController.m](https://github.com/crifan/iOSJailbreakDetection/blob/main/iOSJailbreakDetection/JbDetectOtherViewController.m)
    * `static void _check_image(const struct mach_header *header, intptr_t slide)`

### 别人的相关代码

```c
#import <dlfcn.h> 
 
void checkInject(void) 
{ 
    int ret ; 
    Dl_info dylib_info; 
    int (*func_stat)(const charchar *, struct stat *) = stat; 
    if ((ret = dladdr(func_stat, &dylib_info))) { 
        NSLog(@"lib :%s", dylib_info.dli_fname); 
    } 
}
```

```c
#import <dlfcn.h>
#if！TARGET IPHONE SIMULATOR
//check hook stat
    int ret；
    Dl info dylib info；
    int(*func_stat)(const char*，struct stat*)=stat；

    if((ret=dladdr(func_stat，&dylib info))){
        NSLog(@"lib：s"，dylib info.dli_fname)；
        char *kernal="/usr/lib/system/libsystem_kernel.dylib"；
        if(strcmp(dylib_info.dli_fname，kernal)!=0){
            //device is jail break
            //Let it go
        }
    }
#endif
```

```c
#import <mach-o/dyld.h>
#import <dlfcn.h>

#if !TARGET_IPHONE_SIMULATOR
    //check hook stat
    int ret ;
    Dl_info dylib_info;
    int (*func_stat)(const char *, struct stat *) = stat;
    if ((ret = dladdr(func_stat, &dylib_info))) {
        NSLog(@"lib :%s", dylib_info.dli_fname);
        char* kernal = "/usr/lib/system/libsystem_kernel.dylib";//real device
        //usr/lib/system/libsystem_sim_kernel.dylib  //simulator
        if (strcmp(dylib_info.dli_fname,kernal)!=0) {
            return YES;
        }
    }
...
#endif
```

```c
#pragma mark 使用stat通过检测一些越狱后的关键文件是否可以访问来判断是否越狱，hook stat 方法和dladdr可以绕过
BOOL isStatNotSystemLib() {
    if(TARGET_IPHONE_SIMULATOR)return NO;
    int ret ;
    Dl_info dylib_info;
    int (*func_stat)(const char *, struct stat *) = stat;
    if ((ret = dladdr(func_stat, &dylib_info))) {
        NSString *fName = [NSString stringWithUTF8String: dylib_info.dli_fname];
        if(![fName isEqualToString:@"/usr/lib/system/libsystem_kernel.dylib"]){
            return YES;
        }
    }
...
```

```c
#include <dlfcn.h>
#include <objc/objc.h>
#include <objc/runtime.h>
#include <stdio.h>
#include <string.h>


Dl_info info;
IMP imp;
Method orginalMethod = class_getClassMethod([NSArray class], @selector(description));
imp = method_getImplementation(orginalMethod);
if (dladdr(imp, &info)) {
    printf("dli_fname: %s\n", info.dli_fname);
    printf("dli_sname: %s\n", info.dli_sname);
    printf("dli_fbase: %p\n", info.dli_fbase);
    printf("dli_saddr: %p\n", info.dli_saddr);
} else {
    printf("error: can't find that symbol.\n");
}

...

        if(!dladdr(imp, &info)){
            free(methods);
            return NO;
        }
        /*Validate image path*/
        if(!strstr(info.dli_fname, fnamePre)){
            goto FAIL;
        }
```

```c
Dl_info info;
if (dladdr(exit, &info)) {
    printf("dli_fname: %s\n", info.dli_fname);
    printf("dli_sname: %s\n", info.dli_sname);
    printf("dli_fbase: %p\n", info.dli_fbase);
    printf("dli_saddr: %p\n", info.dli_saddr);
} else {
    printf("error: can't find that symbol.\n");
}


if (dladdr(syscall, &info)) {
    printf("dli_fname: %s\n", info.dli_fname);
    printf("dli_sname: %s\n", info.dli_sname);
    printf("dli_fbase: %p\n", info.dli_fbase);
    printf("dli_saddr: %p\n", info.dli_saddr);
} else {
    printf("error: can't find that symbol.\n");
}


if (dladdr(sysctl, &info)) {
    printf("dli_fname: %s\n", info.dli_fname);
    printf("dli_sname: %s\n", info.dli_sname);
    printf("dli_fbase: %p\n", info.dli_fbase);
    printf("dli_saddr: %p\n", info.dli_saddr);
} else {
    printf("error: can't find that symbol.\n");
}


================================================
dli_fname: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Library/Developer/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/usr/lib/system/libsystem_c.dylib
dli_sname: exit
dli_fbase: 0x7fff51a76000
dli_saddr: 0x7fff51ad0046
dli_fname: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Library/Developer/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/usr/lib/system/libsystem_kernel.dylib
dli_sname: __syscall
dli_fbase: 0x7fff51b5a000
dli_saddr: 0x7fff51b5c9f0
dli_fname: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Library/Developer/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/usr/lib/system/libsystem_c.dylib
dli_sname: sysctl
dli_fbase: 0x7fff51a76000
dli_saddr: 0x7fff51aa1304
```

```c
#include <dlfcn.h>
#include <objc/objc.h>
#include <objc/runtime.h>
#include <stdio.h>
#include <string.h>


+ (void)test {
    Dl_info info;
    IMP imp;
    Method orginalMethod = class_getClassMethod([NSObject class], @selector(load));
    imp = method_getImplementation(orginalMethod);
    if (dladdr(imp, &info)) {
        printf("dli_fname: %s\n", info.dli_fname);
        printf("dli_sname: %s\n", info.dli_sname);
        printf("dli_fbase: %p\n", info.dli_fbase);
        printf("dli_saddr: %p\n", info.dli_saddr);
    } else {
        printf("error: can't find that symbol.\n");
    }
}
====================================================
dli_fname: /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Library/Developer/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/System/Library/Frameworks/Foundation.framework/Foundation
dli_sname: +[NSObject(NSObject) load]
dli_fbase: 0x7fff25888000 //模块地址
dli_saddr: 0x7fff2591bb43 //函数地址
```

## 反越狱检测

之前旧的代码：

```c
#include <dlfcn.h>
#include <mach-o/dyld.h>

/*==============================================================================
    hook dladdr()
==============================================================================*/

void* generateHookedDladdrAddress(void *origAddr);

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

static bool isHookedDladdrAddress(const void *addr){
    bool isHookedAddr = false;
    long addrLong = (long) addr;
//    if ((addrLong > DLADDR_HOOKED_ADDRESS_BASE) && (addrLong < DLADDR_HOOKED_ADDRESS_MAX)) {
    if (addrLong > DLADDR_HOOKED_ADDRESS_BASE) {
        isHookedAddr = true;
    }

    return isHookedAddr;
}

static void* hookedToOrigDladdrAddr(const void *hookedAddr){
    return (void*) ( (long)hookedAddr - DLADDR_HOOKED_ADDRESS_BASE );
}

const int DLADDR_FAILED = 0;

int dladdr(const void *, Dl_info *);
//int dladdr(void *, Dl_info *);
//extern int dladdr(const void *, Dl_info *);

//%hookf(int, dladdr, void *addr, Dl_info *info){
%hookf(int, dladdr, const void *addr, Dl_info *info){
    iosLogDebug("addr=%p,info=%p", addr, info);

    void* origAddr = (void*)addr;

    bool isHookedAddr = isHookedDladdrAddress(addr);
    if (isHookedAddr) {
        origAddr = hookedToOrigDladdrAddr(addr);

        iosLogDebug("addr=%p -> isHookedAddr=%s -> origAddr=%p", addr, boolToStr(isHookedAddr), origAddr);
    }
    
//    int origRet = %orig;
    int origRet = %orig(origAddr, info);

    int finalRet = origRet;

    bool isNotHookedAddr = !isHookedAddr;
    bool isNeedHook = cfgHookEnable_dylib_dladdr && isNotHookedAddr;
    if (isNeedHook) {
        //    if (dladdrRetInt > 0) {
        if (DLADDR_FAILED != origRet) {
            if (NULL != info) {
                const char* curImageName = info->dli_fname;
                bool isJbDyib = isJailbreakDylib(curImageName);
                if (isJbDyib) {
                    finalRet = DLADDR_FAILED;

                    iosLogInfo("addr=%p -> origRet=%d -> dli_fname=%{public}s, dli_fbase=%p, dli_sname=%{public}s, dli_saddr=%p -> isJbDyib=%s -> finalRet=%d", addr, origRet, info->dli_fname, info->dli_fbase, info->dli_sname, info->dli_saddr, boolToStr(isJbDyib), finalRet);
//                    iosLogInfo("isJbDyib=%s", boolToStr(isJbDyib));
//                    iosLogInfo("addr=%p -> origRet=%d", addr, origRet);
//                    iosLogInfo("dli_fname=%{public}s, dli_fbase=%p, dli_sname=%{public}s, dli_saddr=%p", info->dli_fname, info->dli_fbase, info->dli_sname, info->dli_saddr);
//                    iosLogInfo("finalRet=%d", finalRet);

                    size_t dlInfoSize = sizeof(Dl_info);
                    memset(info, 0, dlInfoSize);
                }
            }
        }
    }

    return finalRet;
}
```

实现了：

* 当有越狱库：返回0，且清空Dl_info
* 正常库：正常返回

输出举例：

```bash
默认    14:49:59.788331+0800    Aweme    hook_dylib.xm dladdr: addr=0x10148c000 -> origRet=1 -> dli_fname=/usr/lib/substitute-inserter.dylib, dli_fbase=0x10148c000, dli_sname=__dso_handle, dli_saddr=0x10148c000 -> isJbDylib=True -> finalRet=0
默认    14:49:59.831705+0800    Aweme    hook_dylib.xm dladdr: addr=0x1150d0000 -> origRet=1 -> dli_fname=/usr/lib/libsubstitute.dylib, dli_fbase=0x1150d0000, dli_sname=__dso_handle, dli_saddr=0x1150d0000 -> isJbDylib=True -> finalRet=0
默认    14:49:59.831804+0800    Aweme    hook_dylib.xm dladdr: addr=0x116104000 -> origRet=1 -> dli_fname=/usr/lib/substitute-loader.dylib, dli_fbase=0x116104000, dli_sname=__dso_handle, dli_saddr=0x116104000 -> isJbDylib=True -> finalRet=0
默认    14:49:59.831856+0800    Aweme    hook_dylib.xm dladdr: addr=0x11506c000 -> origRet=1 -> dli_fname=/usr/lib/libsubstrate.dylib, dli_fbase=0x11506c000, dli_sname=__dso_handle, dli_saddr=0x11506c000 -> isJbDylib=True -> finalRet=0
默认    14:49:59.831907+0800    Aweme    hook_dylib.xm dladdr: addr=0x115740000 -> origRet=1 -> dli_fname=/Library/MobileSubstrate/DynamicLibraries/AppSyncUnified-FrontBoard.dylib, dli_fbase=0x115740000, dli_sname=__dso_handle, dli_saddr=0x115740000 -> isJbDylib=True -> finalRet=0
默认    14:49:59.831954+0800    Aweme    hook_dylib.xm dladdr: addr=0x115750000 -> origRet=1 -> dli_fname=/Library/MobileSubstrate/DynamicLibraries/XxxTweak.dylib, dli_fbase=0x115750000, dli_sname=__dso_handle, dli_saddr=0x115750000 -> isJbDylib=True -> finalRet=0
```

最新代码详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dylib.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dylib.xm)
