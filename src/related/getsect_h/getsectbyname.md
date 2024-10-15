# getsectbyname

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsectbyname`
      ```c
      extern const struct section *getsectbyname(
          const char *segname,
          const char *sectname);
      ```
  * 64-bit Mach-O
    * `getsectbyname`
      ```c
      extern const struct section_64 *getsectbyname(
          const char *segname,
          const char *sectname);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsectbyname
==============================================================================*/

const struct section_64* getsectbyname(const char *segname, const char *sectname);

%hookf(const struct section_64*, getsectbyname, const char *segname, const char *sectname){
     const struct section_64* retSection = %orig;
     iosLogInfo("segname=%{public}s,sectname=%{public}s -> retSection=%p", segname, sectname, retSection);
     return retSection;
}
```
