# dyld.h

## 相关

### `dyld3/APIs.h`的内容

* [dyld3/APIs.h](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.h.auto.html)

```c
/*
 * Copyright (c) 2017 Apple Inc. All rights reserved.
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



#ifndef __DYLD_APIS_H__
#define __DYLD_APIS_H__

#include <string.h>
#include <stdint.h>
#include <pthread.h>
#include <uuid/uuid.h>
#include <mach-o/dyld_priv.h>

#include "dlfcn.h"


#define TEMP_HIDDEN __attribute__((visibility("hidden")))

//
// The implementation of all dyld load/unload API's must hold a global lock
// so that the next load/unload does start until the current is complete.
// This lock is recursive so that initializers can call dlopen().
// This is done using the macros DYLD_LOCK_THIS_BLOCK.
// Example:
//
//  void dyld_load_api() {
//        DYLD_LOAD_LOCK_THIS_BLOCK;
//        // free to do stuff here
//        // that accesses dyld internal data structures
//    }
//
//

#define DYLD_LOAD_LOCK_THIS_BLOCK            RecursiveAutoLock _dyld_load_lock;

namespace dyld3 {

class __attribute__((visibility("hidden"))) RecursiveAutoLock
{
public:
    RecursiveAutoLock() {
        pthread_mutex_lock(&_sMutex);
    }
    ~RecursiveAutoLock() {
        pthread_mutex_unlock(&_sMutex);
    }
private:
    static pthread_mutex_t _sMutex;
};




uint32_t _dyld_image_count() TEMP_HIDDEN;

const mach_header* _dyld_get_image_header(uint32_t imageIndex) TEMP_HIDDEN;

intptr_t _dyld_get_image_slide(const mach_header* mh) TEMP_HIDDEN;

intptr_t _dyld_get_image_vmaddr_slide(uint32_t imageIndex) TEMP_HIDDEN;

const char* _dyld_get_image_name(uint32_t imageIndex) TEMP_HIDDEN;

const struct mach_header * _dyld_get_prog_image_header() TEMP_HIDDEN;

int32_t NSVersionOfLinkTimeLibrary(const char* libraryName) TEMP_HIDDEN;

int32_t NSVersionOfRunTimeLibrary(const char* libraryName) TEMP_HIDDEN;

uint32_t dyld_get_program_sdk_watch_os_version() TEMP_HIDDEN;
uint32_t dyld_get_program_min_watch_os_version() TEMP_HIDDEN;

uint32_t dyld_get_program_sdk_bridge_os_version() TEMP_HIDDEN;
uint32_t dyld_get_program_min_bridge_os_version() TEMP_HIDDEN;


uint32_t dyld_get_sdk_version(const mach_header* mh) TEMP_HIDDEN;


uint32_t dyld_get_program_sdk_version() TEMP_HIDDEN;
uint32_t dyld_get_min_os_version(const mach_header* mh) TEMP_HIDDEN;

uint32_t dyld_get_program_min_os_version() TEMP_HIDDEN;

dyld_platform_t dyld_get_active_platform(void) TEMP_HIDDEN;
dyld_platform_t dyld_get_base_platform(dyld_platform_t platform) TEMP_HIDDEN;
bool dyld_is_simulator_platform(dyld_platform_t platform) TEMP_HIDDEN;
bool dyld_sdk_at_least(const struct mach_header* mh, dyld_build_version_t version) TEMP_HIDDEN;
bool dyld_minos_at_least(const struct mach_header* mh, dyld_build_version_t version) TEMP_HIDDEN;
bool dyld_program_sdk_at_least(dyld_build_version_t version) TEMP_HIDDEN;
bool dyld_program_minos_at_least(dyld_build_version_t version) TEMP_HIDDEN;
void dyld_get_image_versions(const struct mach_header* mh, void (^callback)(dyld_platform_t platform, uint32_t sdk_version, uint32_t min_version)) TEMP_HIDDEN;

bool _dyld_get_image_uuid(const mach_header* mh, uuid_t uuid) TEMP_HIDDEN;

int _NSGetExecutablePath(char* buf, uint32_t* bufsize) TEMP_HIDDEN;

void _dyld_register_func_for_add_image(void (*func)(const mach_header *mh, intptr_t vmaddr_slide)) TEMP_HIDDEN;

void _dyld_register_func_for_remove_image(void (*func)(const mach_header *mh, intptr_t vmaddr_slide)) TEMP_HIDDEN;

void _dyld_objc_notify_register(_dyld_objc_notify_mapped    mapped,
                                _dyld_objc_notify_init      init,
                                _dyld_objc_notify_unmapped  unmapped) TEMP_HIDDEN;

const mach_header* dyld_image_header_containing_address(const void* addr) TEMP_HIDDEN;

const mach_header* _dyld_get_image_header_containing_address(const void* address) TEMP_HIDDEN;

bool _dyld_image_containing_address(const void* address) TEMP_HIDDEN;

const char* dyld_image_path_containing_address(const void* addr) TEMP_HIDDEN;

bool _dyld_is_memory_immutable(const void* addr, size_t length) TEMP_HIDDEN;


int dladdr(const void* addr, Dl_info* info) TEMP_HIDDEN;

char* dlerror() TEMP_HIDDEN;

int dlclose(void* handle) TEMP_HIDDEN;

void* dlopen_internal(const char* path, int mode, void* callerAddress) TEMP_HIDDEN;

bool dlopen_preflight_internal(const char* path) TEMP_HIDDEN;

void* dlsym_internal(void* handle, const char* symbolName, void* callerAddress) TEMP_HIDDEN;

const struct dyld_all_image_infos* _dyld_get_all_image_infos() TEMP_HIDDEN;

bool dyld_shared_cache_some_image_overridden() TEMP_HIDDEN;

bool _dyld_get_shared_cache_uuid(uuid_t uuid) TEMP_HIDDEN;

const void* _dyld_get_shared_cache_range(size_t* length) TEMP_HIDDEN;

bool _dyld_shared_cache_optimized() TEMP_HIDDEN;

bool _dyld_shared_cache_is_locally_built() TEMP_HIDDEN;
    
uint32_t _dyld_launch_mode() TEMP_HIDDEN;

bool dyld_need_closure(const char* execPath, const char* dataContainerRootDir) TEMP_HIDDEN;

void _dyld_images_for_addresses(unsigned count, const void* addresses[], struct dyld_image_uuid_offset infos[]) TEMP_HIDDEN;

void _dyld_register_for_image_loads(void (*func)(const mach_header* mh, const char* path, bool unloadable)) TEMP_HIDDEN;

void _dyld_register_for_bulk_image_loads(void (*func)(unsigned imageCount, const struct mach_header* mhs[], const char* paths[])) TEMP_HIDDEN;

bool _dyld_find_unwind_sections(void* addr, dyld_unwind_sections* info) TEMP_HIDDEN;

bool dyld_process_is_restricted() TEMP_HIDDEN;

const char* dyld_shared_cache_file_path() TEMP_HIDDEN;

bool dyld_has_inserted_or_interposing_libraries() TEMP_HIDDEN;

void dyld_dynamic_interpose(const mach_header* mh, const dyld_interpose_tuple array[], size_t count) TEMP_HIDDEN;

int dyld_shared_cache_find_iterate_text(const uuid_t cacheUuid, const char* extraSearchDirs[], void (^callback)(const dyld_shared_cache_dylib_text_info* info)) TEMP_HIDDEN;

int dyld_shared_cache_iterate_text(const uuid_t cacheUuid, void (^callback)(const dyld_shared_cache_dylib_text_info* info)) TEMP_HIDDEN;

void _dyld_atfork_prepare() TEMP_HIDDEN;
void _dyld_atfork_parent() TEMP_HIDDEN;
void _dyld_fork_child() TEMP_HIDDEN;

void _dyld_missing_symbol_abort() TEMP_HIDDEN;

const char* _dyld_get_objc_selector(const char* selName) TEMP_HIDDEN;

void _dyld_for_each_objc_class(const char* className,
                               void (^callback)(void* classPtr, bool isLoaded, bool* stop)) TEMP_HIDDEN;

void _dyld_for_each_objc_protocol(const char* protocolName,
                                  void (^callback)(void* protocolPtr, bool isLoaded, bool* stop)) TEMP_HIDDEN;

void _dyld_register_driverkit_main(void (*mainFunc)())TEMP_HIDDEN;

// only in macOS and deprecated 
#if TARGET_OS_OSX
NSObjectFileImageReturnCode NSCreateObjectFileImageFromFile(const char* pathName, NSObjectFileImage *objectFileImage) TEMP_HIDDEN;
NSObjectFileImageReturnCode NSCreateObjectFileImageFromMemory(const void *address, size_t size, NSObjectFileImage *objectFileImage) TEMP_HIDDEN;
bool NSDestroyObjectFileImage(NSObjectFileImage objectFileImage) TEMP_HIDDEN;
uint32_t NSSymbolDefinitionCountInObjectFileImage(NSObjectFileImage objectFileImage) TEMP_HIDDEN;
const char*  NSSymbolDefinitionNameInObjectFileImage(NSObjectFileImage objectFileImage, uint32_t ordinal) TEMP_HIDDEN;
uint32_t NSSymbolReferenceCountInObjectFileImage(NSObjectFileImage objectFileImage) TEMP_HIDDEN;
const char*  NSSymbolReferenceNameInObjectFileImage(NSObjectFileImage objectFileImage, uint32_t ordinal, bool *tentative_definition) TEMP_HIDDEN;
bool NSIsSymbolDefinedInObjectFileImage(NSObjectFileImage objectFileImage, const char* symbolName) TEMP_HIDDEN;
void* NSGetSectionDataInObjectFileImage(NSObjectFileImage objectFileImage, const char* segmentName, const char* sectionName, size_t *size) TEMP_HIDDEN;
const char* NSNameOfModule(NSModule m) TEMP_HIDDEN;
const char* NSLibraryNameForModule(NSModule m) TEMP_HIDDEN;
NSModule NSLinkModule(NSObjectFileImage objectFileImage, const char* moduleName, uint32_t options) TEMP_HIDDEN;
bool NSUnLinkModule(NSModule module, uint32_t options) TEMP_HIDDEN;
bool NSIsSymbolNameDefined(const char* symbolName) TEMP_HIDDEN;
bool NSIsSymbolNameDefinedWithHint(const char* symbolName, const char* libraryNameHint) TEMP_HIDDEN;
bool NSIsSymbolNameDefinedInImage(const struct mach_header* image, const char* symbolName) TEMP_HIDDEN;
NSSymbol NSLookupAndBindSymbol(const char* symbolName) TEMP_HIDDEN;
NSSymbol NSLookupAndBindSymbolWithHint(const char* symbolName, const char* libraryNameHint) TEMP_HIDDEN;
NSSymbol NSLookupSymbolInModule(NSModule module, const char* symbolName) TEMP_HIDDEN;
NSSymbol NSLookupSymbolInImage(const struct mach_header* image, const char* symbolName, uint32_t options) TEMP_HIDDEN;
const char*  NSNameOfSymbol(NSSymbol symbol) TEMP_HIDDEN;
void* NSAddressOfSymbol(NSSymbol symbol) TEMP_HIDDEN;
NSModule NSModuleForSymbol(NSSymbol symbol) TEMP_HIDDEN;
void NSLinkEditError(NSLinkEditErrors *c, int *errorNumber, const char** fileName, const char** errorString) TEMP_HIDDEN;
bool  NSAddLibrary(const char* pathName) TEMP_HIDDEN;
bool NSAddLibraryWithSearching(const char* pathName) TEMP_HIDDEN;
const struct mach_header* NSAddImage(const char* image_name, uint32_t options) TEMP_HIDDEN;
void NSInstallLinkEditErrorHandlers(const NSLinkEditErrorHandlers *handlers) TEMP_HIDDEN;
bool _dyld_present(void) TEMP_HIDDEN;
bool _dyld_launched_prebound(void)   TEMP_HIDDEN;
bool _dyld_all_twolevel_modules_prebound(void) TEMP_HIDDEN;
bool _dyld_bind_fully_image_containing_address(const void* address)  TEMP_HIDDEN;
bool _dyld_image_containing_address(const void* address) TEMP_HIDDEN;
void _dyld_lookup_and_bind(const char* symbol_name, void **address, NSModule* module) TEMP_HIDDEN;
void _dyld_lookup_and_bind_with_hint(const char* symbol_name, const char* library_name_hint, void** address, NSModule* module) TEMP_HIDDEN;
void _dyld_lookup_and_bind_fully(const char* symbol_name, void** address, NSModule* module) TEMP_HIDDEN;
const struct mach_header*  _dyld_get_image_header_containing_address(const void* address) TEMP_HIDDEN;
#endif

} // namespace dyld3

#endif // __DYLD_APIS_H__
```

### `mach-o/dyld.h`头文件内容

#### `dyld`的`mach-o/dyld.h`

* dyld
  * [mach-o/dyld.h](https://opensource.apple.com/source/dyld/dyld-852.2/include/mach-o/dyld.h.auto.html)

```c
/*
 * Copyright (c) 1999-2008 Apple Inc. All rights reserved.
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
#ifndef _MACH_O_DYLD_H_
#define _MACH_O_DYLD_H_

#include <stddef.h>
#include <stdint.h>
#include <stdbool.h>

#include <mach-o/loader.h>
#include <Availability.h>

#if __cplusplus
extern "C" {
#endif 

#ifdef __DRIVERKIT_19_0
 #define DYLD_DRIVERKIT_UNAVAILABLE __API_UNAVAILABLE(driverkit)
#else
 #define DYLD_DRIVERKIT_UNAVAILABLE
#endif

/*
 * The following functions allow you to iterate through all loaded images.  
 * This is not a thread safe operation.  Another thread can add or remove
 * an image during the iteration.  
 *
 * Many uses of these routines can be replace by a call to dladdr() which 
 * will return the mach_header and name of an image, given an address in 
 * the image. dladdr() is thread safe.
 */
extern uint32_t                    _dyld_image_count(void)                              __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern const struct mach_header*   _dyld_get_image_header(uint32_t image_index)         __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern intptr_t                    _dyld_get_image_vmaddr_slide(uint32_t image_index)   __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern const char*                 _dyld_get_image_name(uint32_t image_index)           __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);

/*
 * The following functions allow you to install callbacks which will be called   
 * by dyld whenever an image is loaded or unloaded.  During a call to _dyld_register_func_for_add_image()
 * the callback func is called for every existing image.  Later, it is called as each new image
 * is loaded and bound (but initializers not yet run).  The callback registered with
 * _dyld_register_func_for_remove_image() is called after any terminators in an image are run
 * and before the image is un-memory-mapped.
 */
extern void _dyld_register_func_for_add_image(void (*func)(const struct mach_header* mh, intptr_t vmaddr_slide))    __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);
extern void _dyld_register_func_for_remove_image(void (*func)(const struct mach_header* mh, intptr_t vmaddr_slide)) __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);

/*
 * NSVersionOfRunTimeLibrary() returns the current_version number of the currently dylib 
 * specifed by the libraryName.  The libraryName parameter would be "bar" for /path/libbar.3.dylib and
 * "Foo" for /path/Foo.framework/Versions/A/Foo.  It returns -1 if no such library is loaded.
 */
extern int32_t NSVersionOfRunTimeLibrary(const char* libraryName)            __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);

/*
 * NSVersionOfLinkTimeLibrary() returns the current_version number that the main executable was linked
 * against at build time.  The libraryName parameter would be "bar" for /path/libbar.3.dylib and
 * "Foo" for /path/Foo.framework/Versions/A/Foo.  It returns -1 if the main executable did not link
 * against the specified library.
 */
extern int32_t NSVersionOfLinkTimeLibrary(const char* libraryName)           __OSX_AVAILABLE_STARTING(__MAC_10_1, __IPHONE_2_0);

/*
 * _NSGetExecutablePath() copies the path of the main executable into the buffer. The bufsize parameter
 * should initially be the size of the buffer.  The function returns 0 if the path was successfully copied,
 * and *bufsize is left unchanged. It returns -1 if the buffer is not large enough, and *bufsize is set 
 * to the size required. 
 * 
 * Note that _NSGetExecutablePath will return "a path" to the executable not a "real path" to the executable. 
 * That is the path may be a symbolic link and not the real file. With deep directories the total bufsize 
 * needed could be more than MAXPATHLEN.
 */
extern int _NSGetExecutablePath(char* buf, uint32_t* bufsize)                 __OSX_AVAILABLE_STARTING(__MAC_10_2, __IPHONE_2_0);

/*
 * Registers a function to be called when the current thread terminates.
 * Called by c++ compiler to implement destructors on thread_local object variables.
 */
extern void _tlv_atexit(void (*termFunc)(void* objAddr), void* objAddr)      __OSX_AVAILABLE_STARTING(__MAC_10_10, __IPHONE_8_0);

/*
 * Never called. On-disk thread local variables contain a pointer to this.  Once
 * the thread local is prepared, the pointer changes to a real handler such as tlv_get_addr.
 */
extern void _tlv_bootstrap(void)                                             __OSX_AVAILABLE_STARTING(__MAC_10_10, __IPHONE_8_0) DYLD_DRIVERKIT_UNAVAILABLE ;

/*
 * Dylibs that are incorporated into the dyld cache are removed from disk. That means code
 * cannot stat() the file to see if it "exists".  This function is like a stat() call that checks if a
 * path is to a dylib that was removed from disk and is incorporated into the active dyld cache.
 */
extern bool _dyld_shared_cache_contains_path(const char* path)               __API_AVAILABLE(macos(11.0), ios(14.0), watchos(7.0), tvos(14.0), bridgeos(5.0)) DYLD_DRIVERKIT_UNAVAILABLE;

/*
 * The following dyld API's are deprecated as of Mac OS X 10.5.  They are either  
 * no longer necessary or are superceeded by dlopen and friends in <dlfcn.h>.
 * dlopen/dlsym/dlclose have been available since Mac OS X 10.3 and work with 
 * dylibs and bundles.  
 *
 *    NSAddImage                           -> dlopen
 *    NSLookupSymbolInImage                -> dlsym
 *    NSCreateObjectFileImageFromFile      -> dlopen
 *    NSDestroyObjectFileImage             -> dlclose
 *    NSLinkModule                         -> not needed when dlopen used
 *    NSUnLinkModule                       -> not needed when dlclose used
 *    NSLookupSymbolInModule               -> dlsym
 *    _dyld_image_containing_address       -> dladdr
 *    NSLinkEditError                      -> dlerror
 *
 */

#ifndef ENUM_DYLD_BOOL
#define ENUM_DYLD_BOOL
  #undef FALSE
  #undef TRUE
  enum DYLD_BOOL { FALSE, TRUE };
#endif /* ENUM_DYLD_BOOL */

/* Object file image API */
typedef enum {
    NSObjectFileImageFailure, /* for this a message is printed on stderr */
    NSObjectFileImageSuccess,
    NSObjectFileImageInappropriateFile,
    NSObjectFileImageArch,
    NSObjectFileImageFormat, /* for this a message is printed on stderr */
    NSObjectFileImageAccess
} NSObjectFileImageReturnCode;

typedef struct __NSObjectFileImage* NSObjectFileImage;

/* NSObjectFileImage can only be used with MH_BUNDLE files */
extern NSObjectFileImageReturnCode NSCreateObjectFileImageFromFile(const char* pathName, NSObjectFileImage *objectFileImage)               __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlopen()");
extern NSObjectFileImageReturnCode NSCreateObjectFileImageFromMemory(const void *address, size_t size, NSObjectFileImage *objectFileImage) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern bool                        NSDestroyObjectFileImage(NSObjectFileImage objectFileImage)                                             __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlclose()");

extern uint32_t     NSSymbolDefinitionCountInObjectFileImage(NSObjectFileImage objectFileImage)                   __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern const char*  NSSymbolDefinitionNameInObjectFileImage(NSObjectFileImage objectFileImage, uint32_t ordinal)  __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern uint32_t     NSSymbolReferenceCountInObjectFileImage(NSObjectFileImage objectFileImage)                    __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern const char*  NSSymbolReferenceNameInObjectFileImage(NSObjectFileImage objectFileImage, uint32_t ordinal, bool *tentative_definition) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern bool         NSIsSymbolDefinedInObjectFileImage(NSObjectFileImage objectFileImage, const char* symbolName) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern void*        NSGetSectionDataInObjectFileImage(NSObjectFileImage objectFileImage, const char* segmentName, const char* sectionName, size_t *size) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "getsectiondata()");

typedef struct __NSModule* NSModule;
extern const char*  NSNameOfModule(NSModule m)         __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern const char*  NSLibraryNameForModule(NSModule m) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");

extern NSModule NSLinkModule(NSObjectFileImage objectFileImage, const char* moduleName, uint32_t options) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlopen()");
#define NSLINKMODULE_OPTION_NONE                         0x0
#define NSLINKMODULE_OPTION_BINDNOW                      0x1
#define NSLINKMODULE_OPTION_PRIVATE                      0x2
#define NSLINKMODULE_OPTION_RETURN_ON_ERROR              0x4
#define NSLINKMODULE_OPTION_DONT_CALL_MOD_INIT_ROUTINES  0x8
#define NSLINKMODULE_OPTION_TRAILING_PHYS_NAME          0x10

extern bool NSUnLinkModule(NSModule module, uint32_t options) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
#define NSUNLINKMODULE_OPTION_NONE                  0x0
#define NSUNLINKMODULE_OPTION_KEEP_MEMORY_MAPPED    0x1
#define NSUNLINKMODULE_OPTION_RESET_LAZY_REFERENCES	0x2

/* symbol API */
typedef struct __NSSymbol* NSSymbol;
extern bool     NSIsSymbolNameDefined(const char* symbolName)                                                    __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern bool     NSIsSymbolNameDefinedWithHint(const char* symbolName, const char* libraryNameHint)               __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern bool     NSIsSymbolNameDefinedInImage(const struct mach_header* image, const char* symbolName)            __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern NSSymbol NSLookupAndBindSymbol(const char* symbolName)                                                    __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern NSSymbol NSLookupAndBindSymbolWithHint(const char* symbolName, const char* libraryNameHint)               __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern NSSymbol NSLookupSymbolInModule(NSModule module, const char* symbolName)                                  __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlsym()");
extern NSSymbol NSLookupSymbolInImage(const struct mach_header* image, const char* symbolName, uint32_t options) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlsym()");
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND            0x0
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND_NOW        0x1
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND_FULLY      0x2
#define NSLOOKUPSYMBOLINIMAGE_OPTION_RETURN_ON_ERROR 0x4
extern const char*  NSNameOfSymbol(NSSymbol symbol)    __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");
extern void *       NSAddressOfSymbol(NSSymbol symbol) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlsym()");
extern NSModule     NSModuleForSymbol(NSSymbol symbol) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dladdr()");

/* error handling API */
typedef enum {
    NSLinkEditFileAccessError,
    NSLinkEditFileFormatError,
    NSLinkEditMachResourceError,
    NSLinkEditUnixResourceError,
    NSLinkEditOtherError,
    NSLinkEditWarningError,
    NSLinkEditMultiplyDefinedError,
    NSLinkEditUndefinedError
} NSLinkEditErrors;

/*
 * For the NSLinkEditErrors value NSLinkEditOtherError these are the values
 * passed to the link edit error handler as the errorNumber (what would be an
 * errno value for NSLinkEditUnixResourceError or a kern_return_t value for
 * NSLinkEditMachResourceError).
 */
typedef enum {
    NSOtherErrorRelocation, 
    NSOtherErrorLazyBind,
    NSOtherErrorIndrLoop,
    NSOtherErrorLazyInit,
    NSOtherErrorInvalidArgs
} NSOtherErrorNumbers;

extern void NSLinkEditError(NSLinkEditErrors *c, int *errorNumber, const char** fileName, const char** errorString) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlerror()");

typedef struct {
     void     (*undefined)(const char* symbolName);
     NSModule (*multiple)(NSSymbol s, NSModule oldModule, NSModule newModule); 
     void     (*linkEdit)(NSLinkEditErrors errorClass, int errorNumber,
                          const char* fileName, const char* errorString);
} NSLinkEditErrorHandlers;

extern void NSInstallLinkEditErrorHandlers(const NSLinkEditErrorHandlers *handlers) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "");

extern bool                      NSAddLibrary(const char* pathName)                   __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlopen()");
extern bool                      NSAddLibraryWithSearching(const char* pathName)      __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlopen()");
extern const struct mach_header* NSAddImage(const char* image_name, uint32_t options) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlopen()");
#define NSADDIMAGE_OPTION_NONE                  	0x0
#define NSADDIMAGE_OPTION_RETURN_ON_ERROR       	0x1
#define NSADDIMAGE_OPTION_WITH_SEARCHING        	0x2
#define NSADDIMAGE_OPTION_RETURN_ONLY_IF_LOADED 	0x4
#define NSADDIMAGE_OPTION_MATCH_FILENAME_BY_INSTALLNAME	0x8

extern bool _dyld_present(void)                                                              __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "always true");
extern bool _dyld_launched_prebound(void)                                                    __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "moot");
extern bool _dyld_all_twolevel_modules_prebound(void)                                        __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.3, 10.5, "moot");
extern bool _dyld_bind_fully_image_containing_address(const void* address)                   __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlopen(RTLD_NOW)");
extern bool _dyld_image_containing_address(const void* address)                              __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.3, 10.5, "dladdr()");
extern void _dyld_lookup_and_bind(const char* symbol_name, void **address, NSModule* module) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern void _dyld_lookup_and_bind_with_hint(const char* symbol_name, const char* library_name_hint, void** address, NSModule* module) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.4, "dlsym()");
extern void _dyld_lookup_and_bind_fully(const char* symbol_name, void** address, NSModule* module) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.1, 10.5, "dlsym()");

extern const struct mach_header*  _dyld_get_image_header_containing_address(const void* address) __API_UNAVAILABLE(ios, tvos, watchos) __API_UNAVAILABLE(bridgeos) DYLD_DRIVERKIT_UNAVAILABLE  __OSX_DEPRECATED(10.3, 10.5, "dladdr()");

#if __cplusplus
}
#endif 

#endif /* _MACH_O_DYLD_H_ */
```

#### `cctools`的`mach-o/dyld.h`

* `cctools`
  * [mach-o/dyld.h](https://opensource.apple.com/source/cctools/cctools-895/include/mach-o/dyld.h.auto.html)

```c
/*
 * Copyright (c) 1999 Apple Computer, Inc. All rights reserved.
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
#ifndef _MACH_O_DYLD_H_
#define _MACH_O_DYLD_H_

#ifdef __cplusplus
extern "C" {
#endif /* __cplusplus */

#if defined(__MWERKS__) && !defined(__private_extern__)
#define __private_extern__ __declspec(private_extern)
#endif

#include <mach-o/loader.h>
#include <AvailabilityMacros.h>
#ifndef AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER
#define AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER
#endif

#ifndef ENUM_DYLD_BOOL
#define ENUM_DYLD_BOOL
#undef FALSE
#undef TRUE
enum DYLD_BOOL {
    FALSE,
    TRUE
};
#endif /* ENUM_DYLD_BOOL */

/*
 * The high level NS... API.
 */

/* Object file image API */
typedef enum {
    NSObjectFileImageFailure, /* for this a message is printed on stderr */
    NSObjectFileImageSuccess,
    NSObjectFileImageInappropriateFile,
    NSObjectFileImageArch,
    NSObjectFileImageFormat, /* for this a message is printed on stderr */
    NSObjectFileImageAccess
} NSObjectFileImageReturnCode;

typedef void * NSObjectFileImage;

/* limited implementation, only MH_BUNDLE files can be used */
extern NSObjectFileImageReturnCode NSCreateObjectFileImageFromFile(
    const char *pathName,
    NSObjectFileImage *objectFileImage);
extern NSObjectFileImageReturnCode NSCreateCoreFileImageFromFile(
    const char *pathName,
    NSObjectFileImage *objectFileImage);
/* not yet implemented */
extern NSObjectFileImageReturnCode NSCreateObjectFileImageFromMemory(
    void *address,
    unsigned long size, 
    NSObjectFileImage *objectFileImage);
extern enum DYLD_BOOL NSDestroyObjectFileImage(
    NSObjectFileImage objectFileImage);
/*
 * API on NSObjectFileImage's for:
 *   "for Each Symbol Definition In Object File Image" (for Dynamic Bundles)
 *   and the same thing for references
 */
extern unsigned long NSSymbolDefinitionCountInObjectFileImage(
    NSObjectFileImage objectFileImage);
extern const char * NSSymbolDefinitionNameInObjectFileImage(
    NSObjectFileImage objectFileImage,
    unsigned long ordinal);
extern unsigned long NSSymbolReferenceCountInObjectFileImage(
    NSObjectFileImage objectFileImage);
extern const char * NSSymbolReferenceNameInObjectFileImage(
    NSObjectFileImage objectFileImage,
    unsigned long ordinal,
    enum DYLD_BOOL *tentative_definition); /* can be NULL */
/*
 * API on NSObjectFileImage:
 *   "does Object File Image define symbol name X" (using sorted symbol table)
 *   and a way to get the named objective-C section
 */
extern enum DYLD_BOOL NSIsSymbolDefinedInObjectFileImage(
    NSObjectFileImage objectFileImage,
    const char *symbolName);
extern void * NSGetSectionDataInObjectFileImage(
    NSObjectFileImage objectFileImage,
    const char *segmentName,
    const char *sectionName,
    unsigned long *size); /* can be NULL */
/* SPI first appeared in Mac OS X 10.3 */
extern enum DYLD_BOOL NSHasModInitObjectFileImage(
    NSObjectFileImage objectFileImage)
    AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;

/* module API */
typedef void * NSModule;
extern const char * NSNameOfModule(
    NSModule m); 
extern const char * NSLibraryNameForModule(
    NSModule m);

/* limited implementation, only MH_BUNDLE files can be linked */
extern NSModule NSLinkModule(
    NSObjectFileImage objectFileImage, 
    const char *moduleName,
    unsigned long options);
#define NSLINKMODULE_OPTION_NONE		0x0
#define NSLINKMODULE_OPTION_BINDNOW		0x1
#define NSLINKMODULE_OPTION_PRIVATE		0x2
#define NSLINKMODULE_OPTION_RETURN_ON_ERROR	0x4
#define NSLINKMODULE_OPTION_DONT_CALL_MOD_INIT_ROUTINES 0x8
#define NSLINKMODULE_OPTION_TRAILING_PHYS_NAME	0x10

/* limited implementation, only modules loaded with NSLinkModule() can be
   unlinked */
extern enum DYLD_BOOL NSUnLinkModule(
    NSModule module, 
    unsigned long options);
#define NSUNLINKMODULE_OPTION_NONE			0x0
#define NSUNLINKMODULE_OPTION_KEEP_MEMORY_MAPPED	0x1
#define NSUNLINKMODULE_OPTION_RESET_LAZY_REFERENCES	0x2

/* not yet implemented */
extern NSModule NSReplaceModule(
    NSModule moduleToReplace,
    NSObjectFileImage newObjectFileImage, 
    unsigned long options);

/* symbol API */
typedef void * NSSymbol;
extern enum DYLD_BOOL NSIsSymbolNameDefined(
    const char *symbolName);
extern enum DYLD_BOOL NSIsSymbolNameDefinedWithHint(
    const char *symbolName,
    const char *libraryNameHint);
extern enum DYLD_BOOL NSIsSymbolNameDefinedInImage(
    const struct mach_header *image,
    const char *symbolName);
extern NSSymbol NSLookupAndBindSymbol(
    const char *symbolName);
extern NSSymbol NSLookupAndBindSymbolWithHint(
    const char *symbolName,
    const char *libraryNameHint);
extern NSSymbol NSLookupSymbolInModule(
    NSModule module,
    const char *symbolName);
extern NSSymbol NSLookupSymbolInImage(
    const struct mach_header *image,
    const char *symbolName,
    unsigned long options);
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND            0x0
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND_NOW        0x1
#define NSLOOKUPSYMBOLINIMAGE_OPTION_BIND_FULLY      0x2
#define NSLOOKUPSYMBOLINIMAGE_OPTION_RETURN_ON_ERROR 0x4
extern const char * NSNameOfSymbol(
    NSSymbol symbol);
extern void * NSAddressOfSymbol(
    NSSymbol symbol);
extern NSModule NSModuleForSymbol(
    NSSymbol symbol);

/* error handling API */
typedef enum {
    NSLinkEditFileAccessError,
    NSLinkEditFileFormatError,
    NSLinkEditMachResourceError,
    NSLinkEditUnixResourceError,
    NSLinkEditOtherError,
    NSLinkEditWarningError,
    NSLinkEditMultiplyDefinedError,
    NSLinkEditUndefinedError
} NSLinkEditErrors;

/*
 * For the NSLinkEditErrors value NSLinkEditOtherError these are the values
 * passed to the link edit error handler as the errorNumber (what would be an
 * errno value for NSLinkEditUnixResourceError or a kern_return_t value for
 * NSLinkEditMachResourceError).
 */
typedef enum {
    NSOtherErrorRelocation, 
    NSOtherErrorLazyBind,
    NSOtherErrorIndrLoop,
    NSOtherErrorLazyInit,
    NSOtherErrorInvalidArgs
} NSOtherErrorNumbers;

extern void NSLinkEditError(
    NSLinkEditErrors *c,
    int *errorNumber, 
    const char **fileName,
    const char **errorString);

typedef struct {
     void     (*undefined)(const char *symbolName);
     NSModule (*multiple)(NSSymbol s, NSModule oldModule, NSModule newModule); 
     void     (*linkEdit)(NSLinkEditErrors errorClass, int errorNumber,
                          const char *fileName, const char *errorString);
} NSLinkEditErrorHandlers;

extern void NSInstallLinkEditErrorHandlers(
    NSLinkEditErrorHandlers *handlers);

/* other API */
extern enum DYLD_BOOL NSAddLibrary(
    const char *pathName);
extern enum DYLD_BOOL NSAddLibraryWithSearching(
    const char *pathName);
extern const struct mach_header * NSAddImage(
    const char *image_name,
    unsigned long options);
#define NSADDIMAGE_OPTION_NONE                  	0x0
#define NSADDIMAGE_OPTION_RETURN_ON_ERROR       	0x1
#define NSADDIMAGE_OPTION_WITH_SEARCHING        	0x2
#define NSADDIMAGE_OPTION_RETURN_ONLY_IF_LOADED 	0x4
#define NSADDIMAGE_OPTION_MATCH_FILENAME_BY_INSTALLNAME	0x8
extern long NSVersionOfRunTimeLibrary(
    const char *libraryName);
extern long NSVersionOfLinkTimeLibrary(
    const char *libraryName);
extern int _NSGetExecutablePath( /* SPI first appeared in Mac OS X 10.2 */
    char *buf,
    uint32_t *bufsize);

/*
 * The low level _dyld_... API.
 * (used by the objective-C runtime primarily)
 */
extern unsigned long _dyld_present(
    void);

extern unsigned long _dyld_image_count(
    void);
#ifdef __LP64__
extern struct mach_header_64 * _dyld_get_image_header(
    uint32_t image_index);
#else /* !defined(__LP64__) */
extern struct mach_header * _dyld_get_image_header(
    unsigned long image_index);
#endif /* !defined(__LP64__) */
extern unsigned long _dyld_get_image_vmaddr_slide(
    unsigned long image_index);
extern char * _dyld_get_image_name(
    unsigned long image_index);

extern void _dyld_register_func_for_add_image(
    void (*func)(struct mach_header *mh, unsigned long vmaddr_slide));
extern void _dyld_register_func_for_remove_image(
    void (*func)(struct mach_header *mh, unsigned long vmaddr_slide));
extern void _dyld_register_func_for_link_module(
    void (*func)(NSModule module));
/* not yet implemented */
extern void _dyld_register_func_for_unlink_module(
    void (*func)(NSModule module));
/* not yet implemented */
extern void _dyld_register_func_for_replace_module(
    void (*func)(NSModule oldmodule, NSModule newmodule));
extern void _dyld_get_objc_module_sect_for_module(
    NSModule module,
    void **objc_module,
    unsigned long *size);
extern void _dyld_bind_objc_module(
    void *objc_module);
extern enum DYLD_BOOL _dyld_bind_fully_image_containing_address(
    unsigned long *address);
extern enum DYLD_BOOL _dyld_image_containing_address(
    unsigned long address);
/* SPI first appeared in Mac OS X 10.3 */
extern struct mach_header * _dyld_get_image_header_containing_address(
    unsigned long address)
    AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;

extern void _dyld_moninit(
    void (*monaddition)(char *lowpc, char *highpc));
extern enum DYLD_BOOL _dyld_launched_prebound(
    void);
/* SPI first appeared in Mac OS X 10.3 */
extern enum DYLD_BOOL _dyld_all_twolevel_modules_prebound(
    void)
    AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;

extern void _dyld_lookup_and_bind(
    const char *symbol_name,
    unsigned long *address,
    void **module);
extern void _dyld_lookup_and_bind_with_hint(
    const char *symbol_name,
    const char *library_name_hint,
    unsigned long *address,
    void **module);
extern void _dyld_lookup_and_bind_objc(
    const char *symbol_name,
    unsigned long *address,
    void **module);
extern void _dyld_lookup_and_bind_fully(
    const char *symbol_name,
    unsigned long *address,
    void **module);

__private_extern__ int _dyld_func_lookup(
    const char *dyld_func_name,
    unsigned long *address);

#ifdef __cplusplus
}
#endif /* __cplusplus */

#endif /* _MACH_O_DYLD_H_ */
```