# getsegbyname

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsegbyname`
      ```c
      extern const struct segment_command *getsegbyname(
          const char *segname);
      ```
  * 64-bit Mach-O
    * `getsegbyname`
      ```c
      extern const struct segment_command_64 *getsegbyname(
          const char *segname);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsegbyname
==============================================================================*/

const struct segment_command_64* getsegbyname(const char *segname);

%hookf(const struct segment_command_64*, getsegbyname, const char *segname){
     const struct segment_command_64* retSegCmd = %orig;
     iosLogInfo("segname=%{public}s -> retSegCmd=%p", segname, retSegCmd);
     return retSegCmd;
}
```
