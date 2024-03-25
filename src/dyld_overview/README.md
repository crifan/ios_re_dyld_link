# dyld概述

* `dyld`=`DYLD` = **DY**namic **L**oa**D**ing

## dyld库中的函数

* dyld库中的函数
  * dyld库文件：文件位置
    * `/cores/dyld`
    * `/usr/lib/dyld`
  * dyld库文件：内部结构
    * section
      * `dyld.__TEXT.__text`
  * dyld库 内部的
    * 函数
      * C函数
        * NSVersionOfRunTimeLibrary
        * NSVersionOfLinkTimeLibrary
        * _NSGetExecutablePath
        * 
        * statfs64
        * 
        * dyld开头的函数
          * dyld_stub_binder
          * dyld_program_sdk_at_least
          * dyld_shared_cache_file_path
        * _dyld开头的函数
          * _dyld_start
          * _dyld_get_all_image_infos
      * 类的函数
        * dyld类的函数
          * dyld::notifySingle
          * dyld::initializeMainExecutable
          * dyld::_main
          * dyld::registerAddCallback
        * ImageLoaderMachO类的函数
          * ImageLoaderMachO::doModInitFunctions
          * ImageLoaderMachO::doInitialization
        * ImageLoader类的函数
          * ImageLoader::recursiveInitialization
          * ImageLoader::processInitializers
          * ImageLoader::runInitializers
        * dyldbootstrap类的函数
          * dyldbootstrap::start
        * dyld3类的函数
          * dyld3::dladdr
          * dyld3::AllImages::dlopen
          * dyld3::OverflowSafeArray
          * dyld3::MachOFile::forEachLoadCommand
          * dyld3::MachOFile::getUuid
        * dyld4类的函数
          * dyld4::RuntimeState::initialize
          * dyld4::APIs::_libdyld_initialize
    * 全局变量
      * dyld`_main_thread
      * dyld`initialPoolContent
      * gProcessInfo
        * dyld4::gProcessInfo
        * dyld::gProcessInfo
  * 相关
    * **libdyld.dylib** 动态库
      * 文件位置
        * `/usr/lib/system/libdyld.dylib`
      * 内部函数
        * dlopen
        * dlopen_internal
        * dlopen_preflight
        * dladdr
        * _dyld_image_count
        * _dyld_get_image_name
        * _dyld_get_image_header
        * _dyld_image_slide
        * _dyld_register_func_for_add_image
        * _dyld_register_func_for_remove_image
        * __dyld_private ?
    * getsectdata
