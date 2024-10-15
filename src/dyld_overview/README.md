# dyld概述

* `dyld`
  * 名词解析
    * =`DYLD` = **DY**namic **L**oa**D**ing
    * =the dynamic link editor=苹果的动态链接器
  * 作用
    * Apple系统（iOS、Mac等）中的应用app和动态库的（主要格式是Mach-O）的加载和解析
  * 主要包括
    * 二进制的: `dyld`
      * 典型位置：`/usr/lib/dyld`
    * 动态库的：`libdyld.dylib`
      * 典型位置：`/usr/lib/system/libdyld.dylib`

