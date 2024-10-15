# getsectdata

* 所属头文件: `getsect.h`
* 定义
  ```c
  extern char *getsectdata(
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
 Hook: getsectdata
==============================================================================*/

extern char* getsectdata(const char *segname, const char *sectname, unsigned long *size);

%hookf(char*, getsectdata, const char *segname, const char *sectname, unsigned long *size){
    char* sectDataStr = %orig;
    iosLogInfo("segname=%{public}s,sectname=%{public}s,*size=%lu -> sectDataStr=%s", segname, sectname, *size, sectDataStr);
    return sectDataStr;
}
```
