# dyldinfo

* dyldinfo
  * 相关源码
    * dyldinfo.cpp
      * ld64
        * https://opensource.apple.com/source/ld64/ld64-253.9/src/other/dyldinfo.cpp.auto.html
      * dyld
        * https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/shared-cache/dyldinfo.cpp.auto.html

## man page

```bash
dyldinfo(1)						    BSD General Commands Manual 					       dyldinfo(1)

NAME

     dyldinfo -- Displays information used by dyld in an executable

SYNOPSIS

     dyldinfo [-arch arch-name] [-rebase] [-bind] [-weak_bind] [-lazy_bind] [-export] [-opcodes] file(s)

DESCRIPTION

     Executables built for Mac OS X 10.6 and later have a new format for the information in the __LINKEDIT segment.  The dyldinfo tool will dis-
     play that information.

     The options are as follows:

     -arch arch
	     Only display the specified architecture.  Other architectures in a universal image are ignored.

     -rebase
	     Display the table of rebasing information.  Rebasing is what dyld does when an image is not loaded at its preferred address.  Typi-
	     cally, this involves updating pointers in the __DATA segment which are point within the image.

     -bind   Display the table of binding information.	These are the symbolic fix ups that dyld must do when an image is loaded.

     -weak_bind
	     Display the table of weak binding information.  Typically, only C++ progams will have any weak binding.  These are symbols which dyld
	     must unique accross all images.

     -lazy_bind
	     Display the table of lazy binding information. These are symbols which dyld delays binding until they are first used.  Lazy binding
	     is automatically used for all function calls to functions in some external dylib.

     -export
	     Display the table symbols which this image exports.

     -opcodes
	     Display the low level opcodes used to encode all rebase and binding information.

SEE ALSO

     otool(1) nm(1)

Darwin								 November 20, 2008							    Darwin
```

## 涉及到的例子

### Xcode中内部有dyldinfo

```bash
➜  ~ ll /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
。。。
-rwxr-xr-x  1 root  wheel   497K  9  3 05:16 dyldinfo
```

### nm

```bash
➜ nm --help

  --add-dyldinfo     - Add symbols from the dyldinfo not already in the symbol table, Mach-O only

  --dyldinfo-only    - Show only symbols from the dyldinfo, Mach-O only

  --no-dyldinfo      - Don't add any symbols from the dyldinfo, Mach-O only
```
