# getsectdatafromFramework

* 所属头文件: `getsect.h`
* 定义
  ```c
  extern char *getsectdatafromFramework(
      const char *FrameworkName,
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
 Hook: getsectdatafromFramework
==============================================================================*/

char* getsectdatafromFramework(const char *FrameworkName, const char *segname, const char *sectname, unsigned long *size);

%hookf(char *, getsectdatafromFramework, const char *FrameworkName, const char *segname, const char *sectname, unsigned long *size){
    char* sectDataFrameworkStr = %orig;
    iosLogInfo("FrameworkName=%{public}s,segname=%{public}s,sectname=%{public}s,*size=%lu -> sectDataFrameworkStr=%s", FrameworkName, segname, sectname, *size, sectDataFrameworkStr);
    return sectDataFrameworkStr;
}
```
