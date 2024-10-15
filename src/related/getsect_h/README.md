# getsect.h

* 相关
  * 头文件
    * cctools-895
      * [getsect.h](https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/getsect.h.auto.html)
  * 具体实现
    * cctools-895
      * https://opensource.apple.com/source/cctools/cctools-895/libmacho/
        * [getsecbyname.c](https://opensource.apple.com/source/cctools/cctools-895/libmacho/getsecbyname.c.auto.html)
        * [getsegbyname.c](https://opensource.apple.com/source/cctools/cctools-895/libmacho/getsegbyname.c.auto.html)

## hook代码相关

后续hook代码中，通用的调用了头文件：

```c
#import "mach-o/getsect.h"
#import <dlfcn.h>
#import <mach-o/dyld.h>
#import "objc/runtime.h"

#import "CommonConfig.h"
#import "CrifanLibiOS.h"
#import "CrifanLib.h"
#import "JailbreakPathList.h"
```

其中相关代码，详见：
* JailbreakPathList.h
  * https://github.com/crifan/crifanLib/blob/master/c/JailbreakPathList.h
* CrifanLibiOS.h
  * https://github.com/crifan/crifanLib/blob/master/iOS/CrifanLibiOS.h
* CrifanLib.h
  * https://github.com/crifan/crifanLib/blob/master/c/CrifanLib.h

## man page

```bash
GETSECTBYNAME(3)					     Library Functions Manual						  GETSECTBYNAME(3)

NAME

       getsectbyname, getsectdata - get the section information for the named section

SYNOPSIS

       #include <mach-o/getsect.h>

       const struct section *getsectbynamefromheader(
       const struct mach_header *mhp,
       const char *segname,
       const char *sectname)
       const struct section *getsectbyname(
       const char *segname,
       const char *sectname)
       char *getsectdatafromheader(
       const struct mach_header *mhp,
       const char *segname,
       const char *sectname,
       unsigned long *size)
       char *getsectdata(
       const char *segname,
       const char *sectname,
       unsigned long *size)
       char *getsectdatafromFramework(
       const char *FrameworkName,
       const char *segname,
       const char *sectname,
       unsigned long *size)

DESCRIPTION

       Getsectbynamefromheader	returns the section structure for the named section in the named segment if it exists in the specified Mach header
       otherwise it returns NULL.  If the specified Mach header comes from a dynamic library as returned  by  _dyld_get_image_header(3)  the  addr
       field in the section structure will have to have _dyld_get_image_vmaddr_slide(3) added to it to make it a valid pointer.

       Getsectbyname is the same as getsectbynamefromheader with its first argument being the link editor defined symbol _mh_execute_header.

       Getsectdatafromheader returns the address to the data for the named section in the named segment if it exists in the specified Mach header.
       Also it returns the size of the section data indirectly through the pointer size.  Otherwise it returns NULL for the pointer and  zero  for
       the  size.   If	the  specified	Mach  header comes from a dynamic library as returned by _dyld_get_image_header(3) the address to the data
       returned will have to have _dyld_get_image_vmaddr_slide(3) added to it to make it a valid pointer.

       Getsectdata is the same as getsectdatafromheader with its first argument being the link editor defined symbol _mh_execute_header.

       getsectdatafromFramework is used to get the named section data from the named Framework.  For example, the framework name ``Appkit''  would
       be  used  for  /System/Library/Frameworks/Appkit.framework/Versions/C/Appkit.   If  that  Framework isn't being used by the program then it
       returns NULL for the pointer and zero for the size.

SEE ALSO

       dyld(3), getsegbyname(3)

Apple Computer, Inc.						  April 10, 1998						  GETSECTBYNAME(3)
```

## `getsect.h`的内容

[getsect.h](https://opensource.apple.com/source/cctools/cctools-973.0.1/include/mach-o/getsect.h.auto.html)

```c
/*
 * Copyright (c) 2004 Apple Computer, Inc. All rights reserved.
 *
 * @APPLE_LICENSE_HEADER_START@
 * 
 * This file contains Original Code and/or Modifications of Original Code
 * as defined in and that are subject to the Apple Public Source License
 * Version 2.0 (the 'License'). You may not use this file except in
 * compliance with the License. Please obtain a copy of the License at
 * http://www.opensource.apple.com/apsl/ and read it before using this
 * file.
 * 
 * The Original Code and all software distributed under the License are
 * distributed on an 'AS IS' basis, WITHOUT WARRANTY OF ANY KIND, EITHER
 * EXPRESS OR IMPLIED, AND APPLE HEREBY DISCLAIMS ALL SUCH WARRANTIES,
 * INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE, QUIET ENJOYMENT OR NON-INFRINGEMENT.
 * Please see the License for the specific language governing rights and
 * limitations under the License.
 * 
 * @APPLE_LICENSE_HEADER_END@
 */
#ifndef _MACH_O_GETSECT_H_
#define _MACH_O_GETSECT_H_

#include <stdint.h>
#include <mach-o/loader.h>

#ifdef __cplusplus
extern "C" {
#endif /* __cplusplus */

/*
 * Runtime interfaces for Mach-O programs.  For both 32-bit and 64-bit programs,
 * where the sizes returned will be 32-bit or 64-bit based on the size of
 * 'unsigned long'.
 */
extern char *getsectdata(
    const char *segname,
    const char *sectname,
    unsigned long *size);

extern char *getsectdatafromFramework(
    const char *FrameworkName,
    const char *segname,
    const char *sectname,
    unsigned long *size);

extern unsigned long get_end(void);
extern unsigned long get_etext(void);
extern unsigned long get_edata(void);

#ifndef __LP64__
/*
 * Runtime interfaces for 32-bit Mach-O programs.
 */
extern const struct section *getsectbyname(
    const char *segname,
    const char *sectname);

extern uint8_t *getsectiondata(
    const struct mach_header *mhp,
    const char *segname,
    const char *sectname,
    unsigned long *size);

extern const struct segment_command *getsegbyname(
    const char *segname);

extern uint8_t *getsegmentdata(
    const struct mach_header *mhp,
    const char *segname,
    unsigned long *size);

#else /* defined(__LP64__) */
/*
 * Runtime interfaces for 64-bit Mach-O programs.
 */
extern const struct section_64 *getsectbyname(
    const char *segname,
    const char *sectname);

extern uint8_t *getsectiondata(
    const struct mach_header_64 *mhp,
    const char *segname,
    const char *sectname,
    unsigned long *size);

extern const struct segment_command_64 *getsegbyname(
    const char *segname);

extern uint8_t *getsegmentdata(
    const struct mach_header_64 *mhp,
    const char *segname,
    unsigned long *size);

#endif /* defined(__LP64__) */

/*
 * Interfaces for tools working with 32-bit Mach-O files.
 */
extern char *getsectdatafromheader(
    const struct mach_header *mhp,
    const char *segname,
    const char *sectname,
    uint32_t *size);

extern const struct section *getsectbynamefromheader(
    const struct mach_header *mhp,
    const char *segname,
    const char *sectname);

extern const struct section *getsectbynamefromheaderwithswap(
    struct mach_header *mhp,
    const char *segname,
    const char *sectname,
    int fSwap);

/*
 * Interfaces for tools working with 64-bit Mach-O files.
 */
extern char *getsectdatafromheader_64(
    const struct mach_header_64 *mhp,
    const char *segname,
    const char *sectname,
    uint64_t *size);

extern const struct section_64 *getsectbynamefromheader_64(
    const struct mach_header_64 *mhp,
    const char *segname,
    const char *sectname);

extern const struct section *getsectbynamefromheaderwithswap_64(
    struct mach_header_64 *mhp,
    const char *segname,
    const char *sectname,
    int fSwap);

#ifdef __cplusplus
}
#endif /* __cplusplus */

#endif /* _MACH_O_GETSECT_H_ */
```
