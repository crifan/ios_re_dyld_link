# iOS逆向开发：dyld动态链接

* 最新版本：`v1.1.0`
* 更新时间：`20241022`

## 简介

整理关于iOS逆向期间涉及到的dyld动态链接的各种内容。先是dyld概述；然后是分别详细介绍dyld和libdyld.dylib的具体内容；dyld包括，二进制工具的man page；如何查看dyld的版本；哪里可以找到dyld的源码；dyld的app的加载过程；以及相关函数，包括dyld的API，具体有dlfcn.h的dladdr、dlsym、dlinfo、dlopen、dlclose、dlopen_preflight；dyld.h的_dyld_image_count、_dyld_get_image_header、_dyld_get_image_vmaddr_slide、_dyld_get_image_name、_dyld_register_func_for_add_image、_dyld_register_func_for_remove_image、NSVersionOfRunTimeLibrary、NSVersionOfLinkTimeLibrary、_NSGetExecutablePath；dyld_priv.h的dyld_image_header_containing_address、dyld_program_sdk_at_least、dyld_shared_cache_file_path、_dyld_get_all_image_infos；dyld-interposing.h的DYLD_INTERPOSE，以及一些其他函数start、_dyld_start和相关变量，比如gProcessInfo和一些定义dyld_all_image_infos等；以及相关涉及到的地方；再介绍libdyld.dylib中的内容，包括man page，相关函数dyld_stub_binder等；以及其他dyld相关的内容，包括getsect.h的getsectdata、getsectdatafromFramework、getsectiondata、getsectbyname、getsegbyname、getsegmentdata、getsectdatafromheader、getsectdatafromheader_64、getsectbynamefromheader、getsectbynamefromheader_64、getsectbynamefromheaderwithswap、getsectbynamefromheaderwithswap_64；DYLD环境变量的DYLD_LIBRARY_PATH、DYLD_INSERT_LIBRARIES、DYLD_PRINT_LIBRARIES、DYLD_PRINT_STATISTICS、DYLD_IMAGE_SUFFIX、DYLD_PRINT_APIS、DYLD_PRINT_WARNINGS；相关工具dyldinfo等；Mach-O中Load Command的详细定义和具体command，比如LC_DYLD_CHAINED_FIXUPS，和其他涉及到的地方；以及相关结构体dyld_info_command、linkedit_data_command等定义。

## 源码+浏览+下载

本书的各种源码、在线浏览地址、多种格式文件下载如下：

### HonKit源码

* [crifan/ios_re_dyld_link: iOS逆向开发：dyld动态链接](https://github.com/crifan/ios_re_dyld_link)

#### 如何使用此HonKit源码去生成发布为电子书

详见：[crifan/honkit_template: demo how to use crifan honkit template and demo](https://github.com/crifan/honkit_template)

### 在线浏览

* [iOS逆向开发：dyld动态链接 book.crifan.org](https://book.crifan.org/books/ios_re_dyld_link/website/)
* [iOS逆向开发：dyld动态链接 crifan.github.io](https://crifan.github.io/ios_re_dyld_link/website/)

### 离线下载阅读

* [iOS逆向开发：dyld动态链接 PDF](https://book.crifan.org/books/ios_re_dyld_link/pdf/ios_re_dyld_link.pdf)
* [iOS逆向开发：dyld动态链接 ePub](https://book.crifan.org/books/ios_re_dyld_link/epub/ios_re_dyld_link.epub)
* [iOS逆向开发：dyld动态链接 Mobi](https://book.crifan.org/books/ios_re_dyld_link/mobi/ios_re_dyld_link.mobi)

## 版权和用途说明

此电子书教程的全部内容，如无特别说明，均为本人原创。其中部分内容参考自网络，均已备注了出处。如发现有侵权，请通过邮箱联系我 `admin 艾特 crifan.com`，我会尽快删除。谢谢合作。

各种技术类教程，仅作为学习和研究使用。请勿用于任何非法用途。如有非法用途，均与本人无关。

## 鸣谢

感谢我的老婆**陈雪**的包容理解和悉心照料，才使得我`crifan`有更多精力去专注技术专研和整理归纳出这些电子书和技术教程，特此鸣谢。

## 其他

### 作者的其他电子书

本人`crifan`还写了其他`150+`本电子书教程，感兴趣可移步至：

[crifan/crifan_ebook_readme: Crifan的电子书的使用说明](https://github.com/crifan/crifan_ebook_readme)

### 关于作者

关于作者更多介绍，详见：

[关于CrifanLi李茂 – 在路上](https://www.crifan.org/about/)
