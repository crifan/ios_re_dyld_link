# dyld源码

* dyld源码
  * opensource.apple.com
    * 在线浏览
      * https://opensource.apple.com/source/dyld/
        * [dyld-852.2](https://opensource.apple.com/source/dyld/dyld-852.2/)
    * 离线下载
      * https://opensource.apple.com/tarballs/dyld/ ==(已自动跳转到) https://github.com/apple-oss-distributions/dyld/tags
        * [dyld-1125.5](https://github.com/apple-oss-distributions/dyld/releases/tag/dyld-1125.5)
          * `zip`: https://github.com/apple-oss-distributions/dyld/archive/refs/tags/dyld-1125.5.zip
          * `tag.gz`: https://github.com/apple-oss-distributions/dyld/archive/refs/tags/dyld-1125.5.tar.gz
        * [dyld-732.8](https://github.com/apple-oss-distributions/dyld/releases/tag/dyld-732.8)
          * https://opensource.apple.com/tarballs/dyld/dyld-732.8.tar.gz
            * == https://github.com/apple-oss-distributions/dyld/archive/refs/tags/dyld-732.8.tar.gz
  * github
    * https://github.com/opensource-apple/dyld

## 相关核心代码

* 相关核心代码
  * 头文件
    * 常用头文件: `dyld.h`、`dyld_priv.h`、`dyld_images.h`、`dyld-interposing.h`、`dlfcn.h`
      * github.com
        * https://github.com/opensource-apple/dyld/blob/master/include/mach-o/dyld.h
        * https://github.com/opensource-apple/dyld/blob/master/include/mach-o/dyld_priv.h
        * https://github.com/opensource-apple/dyld/blob/master/include/mach-o/dyld_images.h
        * https://github.com/opensource-apple/dyld/blob/master/include/mach-o/dyld-interposing.h
        * https://github.com/opensource-apple/dyld/blob/master/include/dlfcn.h
      * opensource.apple.com
        * dyld
          * https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld.h.auto.html
          * https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld_priv.h.auto.html
          * https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld_images.h.auto.html
          * https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld-interposing.h.auto.html
          * https://opensource.apple.com/source/dyld/dyld-852.2/include/dlfcn.h.auto.html
        * cctools
          * https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/dyld.h.auto.html
          * https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/dyld_priv.h.auto.html
    * 其他头文件
      * dyld3/APIs.h
        * https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.h.auto.html
      * mach-o/getsect.h
        * https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/getsect.h.auto.html
  * 具体实现
    * dyld3
      * `dyld3/APIs.cpp`、`dyldAPIs.cpp`
        * https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html
        * https://opensource.apple.com/source/dyld/dyld-852.2/src/dyldAPIs.cpp.auto.html
    * dyld4
      * `dyld.cpp`、`dyldAPIsInLibSystem.cpp`、`dyldAPIs.cpp`
        * https://github.com/opensource-apple/dyld/blob/master/src/dyld.cpp
        * https://github.com/opensource-apple/dyld/blob/master/src/dyldAPIsInLibSystem.cpp
        * https://github.com/opensource-apple/dyld/blob/master/src/dyldAPIs.cpp
