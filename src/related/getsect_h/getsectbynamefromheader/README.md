# getsectbynamefromheader

* 所属头文件: `getsect.h`
* 定义
  * 32-bit Mach-O
    * `getsectdatafromheader`
      ```c
      extern const struct section *getsectbynamefromheader(
          const struct mach_header *mhp,
          const char *segname,
          const char *sectname);
      ```
