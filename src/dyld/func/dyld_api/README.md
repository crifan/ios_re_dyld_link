# dyld的API

关于`dyld`的各种函数，主要都是

* 定义在
  * [dyld3/APIs.h](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.h.auto.html)
* 实现在：
  * 最新的：`dyld4` ?
    * dyld/dyld-852.2
      * dyldAPIs
        * [dyldAPIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/src/dyldAPIs.cpp.auto.html)
    * opensource-apple/dyld
      * [dyldAPIs.cpp](https://github.com/opensource-apple/dyld/blob/master/src/dyldAPIs.cpp)
      * [dyldAPIsInLibSystem.cpp](https://github.com/opensource-apple/dyld/blob/master/src/dyldAPIsInLibSystem.cpp)
  * 之前的：`dyld3`
    * dyld/dyld-852.2
      * [dyld3/APIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html)
* 别处引用在
  * dyld.h
    * dyld/dyld-852.2
      * [mach-o/dyld.h](https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld.h.auto.html)
    * cctools/cctools-895
      * [mach-o/dyld.h](https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/dyld.h.auto.html)
  * dyld_priv.h
    * dyld/dyld-852.2
      * [mach-o/dyld_priv.h](https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld_priv.h.auto.html)
    * opensource-apple/dyld
      * [dyld/include/mach-o/dyld_priv.h](https://github.com/opensource-apple/dyld/blob/master/include/mach-o/dyld_priv.h)

下面具体介绍：
