# dyld的：man page=help=语法

## dyld(1)

```bash
dyld(1)                                                                dyld(1)

NAME
       dyld - the dynamic link editor

SYNOPSIS
       DYLD_FRAMEWORK_PATH
       DYLD_FALLBACK_FRAMEWORK_PATH
       DYLD_VERSIONED_FRAMEWORK_PATH
       DYLD_LIBRARY_PATH
       DYLD_FALLBACK_LIBRARY_PATH
       DYLD_VERSIONED_LIBRARY_PATH
       DYLD_ROOT_PATH
       DYLD_SHARED_REGION
       DYLD_INSERT_LIBRARIES
       DYLD_FORCE_FLAT_NAMESPACE
       DYLD_IMAGE_SUFFIX
       DYLD_PRINT_OPTS
       DYLD_PRINT_ENV
       DYLD_PRINT_LIBRARIES
       DYLD_PRINT_LIBRARIES_POST_LAUNCH
       DYLD_BIND_AT_LAUNCH
       DYLD_NO_FIX_PREBINDING
       DYLD_DISABLE_DOFS
       DYLD_PRINT_APIS
       DYLD_PRINT_BINDINGS
       DYLD_PRINT_INITIALIZERS
       DYLD_PRINT_REBASINGS
       DYLD_PRINT_SEGMENTS
       DYLD_PRINT_STATISTICS
       DYLD_PRINT_DOFS
       DYLD_PRINT_RPATHS
       DYLD_SHARED_CACHE_DIR
       DYLD_SHARED_CACHE_DONT_VALIDATE

DESCRIPTION
       The  dynamic  linker  uses  the  following environment variables.  They
       affect any program that uses the dynamic linker.

       DYLD_FRAMEWORK_PATH
              This is a colon  separated  list  of  directories  that  contain
              frameworks.   The  dynamic  linker  searches  these  directories
              before it searches for the framework by its  install  name.   It
              allows  you  to  test  new  versions  of existing frameworks. (A
              framework is a library  install  name  that  ends  in  the  form
              XXX.framework/Versions/YYY/XXX  or  XXX.framework/XXX, where XXX
              and YYY are any name.)

              For each framework that a program uses, the dynamic linker looks
              for  the  framework  in each directory in DYLD_FRAMEWORK_PATH in
              turn. If it looks in all the  directories  and  can't  find  the
              framework,  it  searches the directories in DYLD_LIBRARY_PATH in
              turn. If it still can't find the  framework,  it  then  searches
              DYLD_FALLBACK_FRAMEWORK_PATH  and  DYLD_FALLBACK_LIBRARY_PATH in
              turn.

              Use the -L option to otool(1).  to discover the  frameworks  and
              shared libraries that the executable is linked against.

       DYLD_FALLBACK_FRAMEWORK_PATH
              This  is  a  colon  separated  list  of directories that contain
              frameworks.  It is used as the default location  for  frameworks
              not found in their install path.

              By    default,    it   is   set   to   /Library/Frameworks:/Net-
              work/Library/Frameworks:/System/Library/Frameworks

       DYLD_VERSIONED_FRAMEWORK_PATH
              This is a colon  separated  list  of  directories  that  contain
              potential  override  frameworks.   The  dynamic  linker searches
              these directories for frameworks.  For each framework found dyld
              looks  at  its  LC_ID_DYLIB  and  gets  the  current_version and
              install name.  Dyld then looks for the framework at the  install
              name  path.  Whichever has the larger current_version value will
              be used in the process whenever a framework  with  that  install
              name is required.  This is similar to DYLD_FRAMEWORK_PATH except
              instead of always overriding, it only overrides is the  supplied
              framework  is  newer.  Note: dyld does not check the framework's
              Info.plist to find its version.   Dyld  only  checks  the  -cur-
              rrent_version number supplied when the framework was created.

       DYLD_LIBRARY_PATH
              This  is  a  colon  separated  list  of directories that contain
              libraries. The dynamic linker searches these directories  before
              it  searches  the default locations for libraries. It allows you
              to test new versions of existing libraries.

              For each library that a program uses, the dynamic  linker  looks
              for  it  in  each  directory in DYLD_LIBRARY_PATH in turn. If it
              still can't  find  the  library,  it  then  searches  DYLD_FALL-
              BACK_FRAMEWORK_PATH and DYLD_FALLBACK_LIBRARY_PATH in turn.

              Use  the  -L option to otool(1).  to discover the frameworks and
              shared libraries that the executable is linked against.

       DYLD_FALLBACK_LIBRARY_PATH
              This is a colon  separated  list  of  directories  that  contain
              libraries.  It is used as the default location for libraries not
              found  in  their  install  path.   By  default,  it  is  set  to
              $(HOME)/lib:/usr/local/lib:/lib:/usr/lib.

       DYLD_VERSIONED_LIBRARY_PATH
              This  is  a  colon  separated  list  of directories that contain
              potential override libraries.  The dynamic linker searches these
              directories  for dynamic libraries.  For each library found dyld
              looks at  its  LC_ID_DYLIB  and  gets  the  current_version  and
              install  name.   Dyld  then looks for the library at the install
              name path.  Whichever has the larger current_version value  will
              be  used  in the process whenever a dylib with that install name
              is  required.   This  is  similar  to  DYLD_LIBRARY_PATH  except
              instead  of always overriding, it only overrides is the supplied
              library is newer.

       DYLD_ROOT_PATH
              This is a colon separated  list  of  directories.   The  dynamic
              linker  will prepend each of this directory paths to every image
              access until a file is found.

       DYLD_SHARED_REGION
              This can be "use" (the default), "avoid", or "private".  Setting
              it  to  "avoid"  tells dyld to not use the shared cache.  All OS
              dylibs are loaded dynamically just like every other dylib.  Set-
              ting it to "private" tells dyld to remove the shared region from
              the process address space and mmap() back in a private  copy  of
              the  dyld  shared cache in the shared region address range. This
              is only useful if the shared cache on disk has been updated  and
              is different than the shared cache in use.

       DYLD_INSERT_LIBRARIES
              This  is  a  colon  separated  list of dynamic libraries to load
              before the ones specified in the program.  This  lets  you  test
              new  modules  of existing dynamic shared libraries that are used
              in flat-namespace images by loading a temporary  dynamic  shared
              library with just the new modules.  Note that this has no effect
              on images built a two-level namespace  images  using  a  dynamic
              shared library unless DYLD_FORCE_FLAT_NAMESPACE is also used.

       DYLD_FORCE_FLAT_NAMESPACE
              Force  all  images in the program to be linked as flat-namespace
              images and ignore any two-level namespace  bindings.   This  may
              cause programs to fail to execute with a multiply defined symbol
              error if two-level namespace images are used to allow the images
              to have multiply defined symbols.

       DYLD_IMAGE_SUFFIX
              This  is  set  to a string of a suffix to try to be used for all
              shared libraries used by the program.  For libraries  ending  in
              ".dylib"  the  suffix  is applied just before the ".dylib".  For
              all other libraries the suffix is appended to the library  name.
              This  is  useful  for using conventional "_profile" and "_debug"
              libraries and frameworks.

       DYLD_PRINT_OPTS
              When this is set, the dynamic linker writes to file descriptor 2
              (normally standard error) the command line options.

       DYLD_PRINT_ENV
              When this is set, the dynamic linker writes to file descriptor 2
              (normally standard error) the environment variables.

       DYLD_PRINT_LIBRARIES
              When this is set, the dynamic linker writes to file descriptor 2
              (normally  standard  error)  the  filenames of the libraries the
              program is using.  This is useful to make sure that the  use  of
              DYLD_LIBRARY_PATH is getting what you want.

       DYLD_PRINT_LIBRARIES_POST_LAUNCH
              This  does  the  same  as  DYLD_PRINT_LIBRARIES but the printing
              starts after the program gets to its entry point.

       DYLD_BIND_AT_LAUNCH
              When this is set, the dynamic linker binds all undefined symbols
              the program needs at launch time. This includes function symbols
              that can are normally lazily bound at the time  of  their  first
              call.

       DYLD_PRINT_STATISTICS
              Right  before  the  process's  main() is called, dyld prints out
              information about how dyld spent its time.  Useful for analyzing
              launch performance.

       DYLD_NO_FIX_PREBINDING
              Normally, dyld will trigger the dyld shared cache to be regener-
              ated if it notices the cache is out of date  while  launching  a
              process.   If  this  environment  variable is set, dyld will not
              trigger a cache rebuild.  This is useful to set while installing
              a large set of OS dylibs, to ensure the cache is not regenerated
              until the install is complete.

       DYLD_DISABLE_DOFS
              Causes dyld not register dtrace static probes with the kernel.

       DYLD_PRINT_INITIALIZERS
              Causes dyld to print out a line when running  each  initializers
              in  every image.  Initializers run by dyld included constructors
              for C++ statically  allocated  objects,  functions  marked  with
              __attribute__((constructor)), and -init functions.

       DYLD_PRINT_APIS
              Causes  dyld to print a line whenever a dyld API is called (e.g.
              NSAddImage()).

       DYLD_PRINT_SEGMENTS
              Causes dyld to print out a line containing the name and  address
              range  of  each  mach-o  segment that dyld maps.  In addition it
              prints information about if the image was from the  dyld  shared
              cache.

       DYLD_PRINT_BINDINGS
              Causes  dyld to print a line each time a symbolic name is bound.

       DYLD_PRINT_DOFS
              Causes dyld to print out information about dtrace static  probes
              registered with the kernel.

       DYLD_PRINT_RPATHS
              Cause dyld  to print a line each time it expands an @rpath vari-
              able and whether that expansion was successful or not.

       DYLD_SHARED_CACHE_DIR
              This is a directory containing dyld shared  cache  files.   This
              variable can be used in conjunction with DYLD_SHARED_REGION=pri-
              vate and DYLD_SHARED_CACHE_DONT_VALIDATE to run a  process  with
              an alternate shared cache.

       DYLD_SHARED_CACHE_DONT_VALIDATE
              Causes dyld to not check that the inode and mod-time of files in
              the shared cache match the requested dylib on disk. Thus a  pro-
              gram  can be made to run with the dylib in the shared cache even
              though the real dylib has been updated on disk.


       DYNAMIC LIBRARY LOADING
              Unlike many other operating  systems,  Darwin  does  not  locate
              dependent  dynamic  libraries via their leaf file name.  Instead
              the full path to  each  dylib  is  used  (e.g.  /usr/lib/libSys-
              tem.B.dylib).   But  there  are  times  when  a full path is not
              appropriate; for instance, may want your binaries to be  instal-
              lable in anywhere on the disk.  To support that, there are three
              @xxx/ variables that can be used as a path prefix.   At  runtime
              dyld substitutes a dynamically generated path for the @xxx/ pre-
              fix.

       @executable_path/
              This variable is replaced with the path to  the  directory  con-
              taining the main executable for the process.  This is useful for
              loading dylibs/frameworks embedded in a .app directory.  If  the
              main  executable  file is at /some/path/My.app/Contents/MacOS/My
              and  a  framework  dylib  file  is   at   /some/path/My.app/Con-
              tents/Frameworks/Foo.framework/Versions/A/Foo,  then  the frame-
              work load path could be  encoded  as  @executable_path/../Frame-
              works/Foo.framework/Versions/A/Foo  and the .app directory could
              be moved around in the file system and dyld will still  be  able
              to load the embedded framework.

       @loader_path/
              This  variable  is  replaced with the path to the directory con-
              taining the mach-o binary which contains the load command  using
              @loader_path.  Thus, in every binary, @loader_path resolves to a
              different path, whereas @executable_path always resolves to  the
              same  path. @loader_path is useful as the load path for a frame-
              work/dylib embedded in a plug-in, if the final file system loca-
              tion of the plugin-in unknown (so absolute paths cannot be used)
              or if the plug-in is used by  multiple  applications  (so  @exe-
              cutable_path  cannot  be used). If the plug-in mach-o file is at
              /some/path/Myfilter.plugin/Contents/MacOS/Myfilter and a  frame-
              work dylib file is at /some/path/Myfilter.plugin/Contents/Frame-
              works/Foo.framework/Versions/A/Foo, then the framework load path
              could   be   encoded   as  @loader_path/../Frameworks/Foo.frame-
              work/Versions/A/Foo and the Myfilter.plugin directory  could  be
              moved  around  in the file system and dyld will still be able to
              load the embedded framework.

       @rpath/
              Dyld maintains a current stack of  paths  called  the  run  path
              list.   When  @rpath  is encountered it is substituted with each
              path in the run path list until a loadable dylib if found.   The
              run  path  stack is built from the LC_RPATH load commands in the
              depencency chain that lead to the current dylib load.   You  can
              add  an LC_RPATH load command to an image with the -rpath option
              to ld(1).  You can even add a LC_RPATH load  command  path  that
              starts  with  @loader_path/,  and it will push a path on the run
              path stack that relative to the image containing  the  LC_RPATH.
              The  use of @rpath is most useful when you have a complex direc-
              tory structure of programs and dylibs  which  can  be  installed
              anywhere,  but  keep  their  relative  positions.  This scenario
              could be implemented using @loader_path, but every client  of  a
              dylib  could  need  a  different  load path because its relative
              position in the file system is  different.  The  use  of  @rpath
              introduces  a  level  of  indirection that simplies things.  You
              pick a location in your directory structure as an anchor  point.
              Each dylib then gets an install path that starts with @rpath and
              is the path to the dylib relative to the anchor point. Each main
              executable  is linked with -rpath @loader_path/zzz, where zzz is
              the path from the executable to the anchor  point.   At  runtime
              dyld sets it run path to be the anchor point, then each dylib is
              found relative to the anchor point.

SEE ALSO
       libtool(1), ld(1), otool(1)

Apple Inc.                     December 14, 2009                       dyld(1)
Mac OS X 10.7 - Generated Wed Aug 3 09:33:33 CDT 2011
```
