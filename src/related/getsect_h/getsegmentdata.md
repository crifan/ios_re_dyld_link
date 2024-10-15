# getsegmentdata

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsegmentdata`
      ```c
      extern uint8_t *getsegmentdata(
          const struct mach_header *mhp,
          const char *segname,
          unsigned long *size);
      ```
  * 64-bit Mach-O
    * `getsegmentdata`
      ```c
      extern uint8_t *getsegmentdata(
          const struct mach_header_64 *mhp,
          const char *segname,
          unsigned long *size);
      ```

## Hook代码

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

/*==============================================================================
 Hook: getsegmentdata
==============================================================================*/

// Note: if add log, Aweme will crash

uint8_t* getsegmentdata(const struct mach_header_64 *mhp, const char *segname, unsigned long *size);

%hookf(uint8_t*, getsegmentdata, const struct mach_header_64 *mhp, const char *segname, unsigned long *size){
//    iosLogInfo("mhp=%p,segname=%{public}s,size=%p", mhp, segname, size);
    uint8_t* retSegData = %orig;
//    iosLogInfo("mhp=%p,segname=%{public}s,*size=%lu -> retSegCmd=%p", mhp, segname, *size, retSegData);
    return retSegData;
}
```

hook效果：

* 如果hook中加上log，会导致递归调用死循环，最终导致抖音崩溃
  * 详见：
    * 【未解决】XCode调试找原因：hook函数getsegmentdata会导致抖音崩溃
  * 具体原因：未知
