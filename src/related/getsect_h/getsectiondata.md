# getsectiondata

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsectiondata`
      ```c
      extern uint8_t *getsectiondata(
          const struct mach_header *mhp,
          const char *segname,
          const char *sectname,
          unsigned long *size);
      ```
  * 64-bit Mach-O
    * `getsectiondata`
      ```c
      extern uint8_t *getsectiondata(
          const struct mach_header_64 *mhp,
          const char *segname,
          const char *sectname,
          unsigned long *size);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsectiondata
==============================================================================*/

extern uint8_t *getsectiondata(
     const struct mach_header_64 *mhp,
     const char *segname,
     const char *sectname,
     unsigned long *size);

//extern uint8_t *getsectiondata(
//    const struct mach_header *mhp,
//    const char *segname,
//    const char *sectname,
//    unsigned long *size);

%hookf(uint8_t*, getsectiondata, const struct mach_header_64 *mhp, const char *segname, const char *sectname, unsigned long *size){
     iosLogDebug("mhp=%p,segname=%{public}s,sectname=%{public}s,size=%p", mhp, segname, sectname, size);

     uint8_t* origRetIntP = %orig;
     
     if (cfgHookEnable_macho) {
         bool isJbLib = false;
         bool isShowLog = false;

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
     //        isShowLog = true;
             if( size && (*size > 0) ) {
                 isShowLog = true;

//#ifdef XCODE_DEBUG
                 // Note: MUST filter out following log, otherwise Aweme will crash

//                // getsectiondata: mhp=0x114af0000,segname=__TEXT,sectname=__swift5_replace,size=0x16fbf7df8 ===> *size=6169788088, curImgName=/Library/MobileSubstrate/DynamicLibraries/AppSyncUnified-FrontBoard.dylib, isJbLib=True
                 if (
                     strstr(curImgName, "AppSyncUnified") && \
                     (0==strcmp(segname, "__TEXT"))
//                     ( (0==strcmp(sectname, "__swift5_replace")) || (0==strcmp(sectname, "__swift5_types")) ) \
                 ) {
                     isShowLog = false;
                 }

                 if (strstr(curImgName, "XxxTweak")) {
                     isShowLog = false;
                 }

                 // "/Library/MobileSubstrate/DynamicLibraries/   Choicy.dylib"
                 if (strstr(curImgName, "Choicy")) {
                     isShowLog = false;
                 }

                 // /usr/lib/librocketbootstrap.dylib
                 if (strstr(curImgName, "librocketbootstrap")) {
                     isShowLog = false;
                 }
//#endif

                 if (isShowLog) {
                     iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s,size=%p ===> *size=%lu, curImgName=%{public}s, isJbLib=%s", mhp, segname, sectname, size, size ? *size : 0, curImgName, boolToStr(isJbLib));
                 }
             }
         }

         if (isJbLib) {
             origRetIntP = NULL;
             if (NULL != size) {
                 *size = 0;
             }
         }

     //    if (NULL != size) {
     //        if (*size > 0) {
     //            isShowLog = true;
     //        }
     //    }

     //    if (isShowLog) {
     //        iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s,size=%p ===> *size=%lu, curImgName=%{public}s, isJbLib=%s", mhp, segname, sectname, size, size ? *size : 0, curImgName, boolToStr(isJbLib));
     //    }
     }

     return origRetIntP;
}
```

输出效果和举例：

getsectiondata内部调用到了 dladdr，得到dylib库的原始信息，通过名字判断是越狱库，从而去hook掉了

```bash
默认    18:05:22.563411+0800    Aweme    hook_aweme.xm _RxAnnotationInlineLoader$load:
默认    18:05:22.563558+0800    Aweme    hook_dyld.xm _dyld_register_func_for_add_image: func=0x114c4ea84
默认    18:05:22.563673+0800    Aweme    hook_misc.xm getsectiondata: mhp=0x1034b8000,segname=__DATA,sectname=RxAnnotation,size=0x16ce27fb0 ===> *size=4349803960, curImgName=/usr/lib/substitute-inserter.dylib, isJbLib=True
默认    18:05:22.563927+0800    Aweme    hook_misc.xm NSClassFromString: aClassName=__RxApplicationServiceAnnotationSectionProcessor -> origRet=__RxApplicationServiceAnnotationSectionProcessor
默认    18:05:22.564013+0800    Aweme    hook_misc.xm NSClassFromString: aClassName=__RxInjectorAnnotationSectionProcessor -> origRet=__RxInjectorAnnotationSectionProcessor
默认    18:05:22.709165+0800    Aweme    hook_misc.xm getsectiondata: mhp=0x1172a0000,segname=__DATA,sectname=RxAnnotation,size=0x16ce27fb0 ===> *size=80, curImgName=/usr/lib/libsubstitute.dylib, isJbLib=True
默认    18:05:22.709268+0800    Aweme    hook_misc.xm getsectiondata: mhp=0x1172a0000,segname=__DATA,sectname=RxAppService,size=0x16ce27fe8 ===> *size=49240, curImgName=/usr/lib/libsubstitute.dylib, isJbLib=True
默认    18:05:22.710563+0800    Aweme    hook_dyld.xm _dyld_register_func_for_remove_image: func=0x114c4f0bc
```

另外，本身dladdr在别处被调用时，也会hook掉越狱的库：

```bash
默认    18:05:23.410387+0800    Aweme    hook_dyld.xm _dyld_register_func_for_add_image: func=0x11187cc58
默认    18:05:23.410583+0800    Aweme    hook_dylib.xm dladdr: addr=0x1034b8000 -> origRet=1 -> dli_fname=/usr/lib/substitute-inserter.dylib, dli_fbase=0x1034b8000, dli_sname=__dso_handle, dli_saddr=0x1034b8000 -> isJbDyib=True -> finalRet=0
默认    18:05:24.607409+0800    Aweme    hook_dylib.xm dladdr: addr=0x1172a0000 -> origRet=1 -> dli_fname=/usr/lib/libsubstitute.dylib, dli_fbase=0x1172a0000, dli_sname=__dso_handle, dli_saddr=0x1172a0000 -> isJbDyib=True -> finalRet=0
默认    18:05:24.607573+0800    Aweme    hook_dylib.xm dladdr: addr=0x118800000 -> origRet=1 -> dli_fname=/usr/lib/substitute-loader.dylib, dli_fbase=0x118800000, dli_sname=__dso_handle, dli_saddr=0x118800000 -> isJbDyib=True -> finalRet=0
默认    18:05:24.607649+0800    Aweme    hook_dylib.xm dladdr: addr=0x1172cc000 -> origRet=1 -> dli_fname=/usr/lib/libsubstrate.dylib, dli_fbase=0x1172cc000, dli_sname=__dso_handle, dli_saddr=0x1172cc000 -> isJbDyib=True -> finalRet=0
默认    18:05:24.607769+0800    Aweme    hook_dylib.xm dladdr: addr=0x1172e0000 -> origRet=1 -> dli_fname=/Library/MobileSubstrate/DynamicLibraries/AppSyncUnified-FrontBoard.dylib, dli_fbase=0x1172e0000, dli_sname=__dso_handle, dli_saddr=0x1172e0000 -> isJbDyib=True -> finalRet=0
默认    18:05:24.607829+0800    Aweme    hook_dylib.xm dladdr: addr=0x117500000 -> origRet=1 -> dli_fname=/Library/MobileSubstrate/DynamicLibraries/MuJiaBaiHuoTweak.dylib, dli_fbase=0x117500000, dli_sname=__dso_handle, dli_saddr=0x117500000 -> isJbDyib=True -> finalRet=0
```
