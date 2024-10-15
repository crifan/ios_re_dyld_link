# getsectbynamefromheaderwithswap

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsectbynamefromheaderwithswap`
      ```c
      extern const struct section *getsectbynamefromheaderwithswap(
          struct mach_header *mhp,
          const char *segname,
          const char *sectname,
          int fSwap);
      ```
