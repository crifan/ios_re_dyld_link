# getsectdatafromheader_64

* 所属头文件: `getsect.h`
* 定义
  * 64-bit Mach-O
    * `getsectdatafromheader_64`
      ```c
      extern char *getsectdatafromheader_64(
          const struct mach_header_64 *mhp,
          const char *segname,
          const char *sectname,
          uint64_t *size);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsectdatafromheader_64
==============================================================================*/

char* getsectdatafromheader_64(const struct mach_header_64 *mhp, const char *segname, const char *sectname, uint64_t *size);

%hookf(char*, getsectdatafromheader_64, const struct mach_header_64 *mhp, const char *segname, const char *sectname, uint64_t *size){
    char* retSectDataStr = %orig;
    iosLogInfo("mhp=%p,segname=%{public}s,sectname=%{public}s,*size=%llu -> retSectData=%{public}s", mhp, segname, sectname, *size, retSectDataStr);
    return retSectDataStr;
}
```

