# dyld的lib：man page

```bash

dyld(3)                  BSD Library Functions Manual                  dyld(3)

NAME
     _dyld_image_count, _dyld_get_image_header, _dyld_get_image_vmaddr_slide,
     _dyld_get_image_name, _dyld_register_func_for_add_image,
     _dyld_register_func_for_remove_image, NSVersionOfRunTimeLibrary,
     NSVersionOfLinkTimeLibrary _NSGetExecutablePath

SYNOPSIS
     #include <mach-o/dyld.h>

     uint32_t
     _dyld_image_count(void);

     intptr_t
     _dyld_get_image_vmaddr_slide(uint32_t image_index);

     const char*
     _dyld_get_image_name(uint32_t image_index);

     void
     _dyld_register_func_for_add_image(void (*func)(const struct mach_header* mh, intptr_t vmaddr_slide));

     void
     _dyld_register_func_for_remove_image(void (*func)(const struct mach_header* mh, intptr_t vmaddr_slide));

     int32_t
     NSVersionOfRunTimeLibrary(const char* libraryName);

     int32_t
     NSVersionOfLinkTimeLibrary(const char* libraryName);

     int
     _NSGetExecutablePath(char* buf, uint32_t* bufsize);

DESCRIPTION
     These routines provide additional introspection of dyld beyond that pro-vided provided
     vided by dlopen() and dladdr()

     _dyld_image_count() returns the current number of images mapped in by
     dyld. Note that using this count to iterate all images is not thread
     safe, because another thread may be adding or removing images during the
     iteration.

     _dyld_get_image_header() returns a pointer to the mach header of the
     image indexed by image_index.  If image_index isout of range, NULL is
     returned.

     _dyld_get_image_vmaddr_slide() returns the virtural memory address slide
     amount of the image indexed by image_index. If image_index is out of
     range zero is returned.

     _dyld_get_image_name() returns the name of the image indexed by
     image_index. The C-string continues to be owned by dyld and should not
     deleted.  If image_index is out of range NULL is returned.

     _dyld_register_func_for_add_image() registers the specified function to
     be called when a new image is added (a bundle or a dynamic shared
     library) to the program.  When this function is first registered it is
     called for once for each image that is currently part of the process.

     _dyld_register_func_for_remove_image() registers the specified function
     to be called when an image is removed (a bundle or a dynamic shared
     library) from the process.

     NSVersionOfRunTimeLibrary() returns the current_version number of the
     currently loaded dylib specifed by the libraryName.  The libraryName
     parameter would be "bar" for /path/libbar.3.dylib and "Foo" for
     /path/Foo.framework/Versions/A/Foo.  This function returns -1 if no such
     library is loaded.

     NSVersionOfLinkTimeLibrary() returns the current_version number that the
     main executable was linked against at build time.  The libraryName param-eter parameter
     eter would be "bar" for /path/libbar.3.dylib and "Foo" for
     /path/Foo.framework/Versions/A/Foo.  This function returns -1 if the main
     executable did not link against the specified library.

     _NSGetExecutablePath() copies the path of the main executable into the
     buffer buf.  The bufsize parameter should initially be the size of the
     buffer.  This function returns 0 if the path was successfully copied.  It
     returns -1 if the buffer is not large enough, and * bufsize is set to the
     size required.  Note that _NSGetExecutablePath() will return "a path" to
     the executable not a "real path" to the executable.  That is, the path
     may be a symbolic link and not the real file. With deep directories the
     total bufsize needed could be more than MAXPATHLEN.

SEE ALSO
     dlopen(3) dladdr(3) dyld(1) http://developer.apple.com/documenta-
     tion/DeveloperTools/Conceptual/MachOTopics/index.html

                                August 16, 2006
```
