# DYLD_IMAGE_SUFFIX

* `DYLD_IMAGE_SUFFIX`
  * 是dyld的相关参数之一
  * 常用值：
    * `DYLD_IMAGE_SUFFIX = _debug`
  * 作用是
    * 想要调试一个`Framework`=动态库，比如叫：`xxx.dylib`
      * 则：额外弄一个，带调试信息的，然后名字叫做 `xxx_debug.dylib`
    * This is set to a string of a suffix to try to be used for all shared libraries used by the program.  For libraries ending in ".dylib" the suffix is applied just before the ".dylib".  For all other libraries the suffix is appended to the library name.  This is useful for using conventional "_profile" and "_debug" libraries and frameworks.

## 涉及到的内容

下面整理涉及到`DYLD_IMAGE_SUFFIX`的相关内容：

### launchctl的参数

iPhone中launchctl的参数含义和典型用法

中的语法介绍：

```bash
SUBCOMMANDS

     bootstrap domain-target [service-path service-path2 ...] | service-target
...
              --debug-libraries
                       Sets the DYLD_IMAGE_SUFFIX for the service to "_debug",  which prefers the debug variants
                       of libraries if they exist. See dyld(1) for more information.
```
