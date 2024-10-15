# getsectbynamefromheaderwithswap_64

* 所属头文件: `getsect.h`
* 定义
  * 64-bit Mach-O
    * `getsectbynamefromheaderwithswap_64`
      ```c
      extern const struct section *getsectbynamefromheaderwithswap_64(
          struct mach_header_64 *mhp,
          const char *segname,
          const char *sectname,
          int fSwap);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsectbynamefromheaderwithswap_64
==============================================================================*/

const struct section* getsectbynamefromheaderwithswap_64(struct mach_header_64 *mhp, const char *segname, const char *sectname, int fSwap);

%hookf(const struct section*, getsectbynamefromheaderwithswap_64, struct mach_header_64 *mhp, const char *segname, const char *sectname, int fSwap){
    const struct section* retSection = %orig;
    iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s,fSwap=%d -> retSection=%p", mhp, segname, sectname, fSwap, retSection);
    return retSection;
}
```
