# dlinfo

* `dlinfo`
  * 概述：`Linux`系统中，`ELF`相关函数，用于获取动态链接库的信息
  * 所属头文件: `dlfcn.h`
    * 相关头文件: `link.h`
  * 定义
    ```c
    int dlinfo(void *restrict handle, int request, void *restrict info);
    ```
  * 实现
    * [glibc/dlfcn/dlinfo.c at master · lattera/glibc](https://github.com/lattera/glibc/blob/master/dlfcn/dlinfo.c)

## man page

### Linux

[dlinfo(3) - Linux manual page](https://man7.org/linux/man-pages/man3/dlinfo.3.html)

```bash
dlinfo(3)               Library Functions Manual               dlinfo(3)

NAME         top
       dlinfo - obtain information about a dynamically loaded object

LIBRARY         top
       Dynamic linking library (libdl, -ldl)

SYNOPSIS         top
       #define _GNU_SOURCE
       #include <link.h>
       #include <dlfcn.h>

       int dlinfo(void *restrict handle, int request, void *restrict info);

DESCRIPTION         top
       The dlinfo() function obtains information about the dynamically
       loaded object referred to by handle (typically obtained by an
       earlier call to dlopen(3) or dlmopen(3)).  The request argument
       specifies which information is to be returned.  The info argument
       is a pointer to a buffer used to store information returned by
       the call; the type of this argument depends on request.

       The following values are supported for request (with the
       corresponding type for info shown in parentheses):

       RTLD_DI_LMID (Lmid_t *)
              Obtain the ID of the link-map list (namespace) in which
              handle is loaded.

       RTLD_DI_LINKMAP (struct link_map **)
              Obtain a pointer to the link_map structure corresponding
              to handle.  The info argument points to a pointer to a
              link_map structure, defined in <link.h> as:

                  struct link_map {
                      ElfW(Addr) l_addr;  /* Difference between the
                                             address in the ELF file and
                                             the address in memory */
                      char      *l_name;  /* Absolute pathname where
                                             object was found */
                      ElfW(Dyn) *l_ld;    /* Dynamic section of the
                                             shared object */
                      struct link_map *l_next, *l_prev;
                                          /* Chain of loaded objects */

                      /* Plus additional fields private to the
                         implementation */
                  };

       RTLD_DI_ORIGIN (char *)
              Copy the pathname of the origin of the shared object
              corresponding to handle to the location pointed to by
              info.

       RTLD_DI_SERINFO (Dl_serinfo *)
              Obtain the library search paths for the shared object
              referred to by handle.  The info argument is a pointer to
              a Dl_serinfo that contains the search paths.  Because the
              number of search paths may vary, the size of the structure
              pointed to by info can vary.  The RTLD_DI_SERINFOSIZE
              request described below allows applications to size the
              buffer suitably.  The caller must perform the following
              steps:

              (1)  Use a RTLD_DI_SERINFOSIZE request to populate a
                   Dl_serinfo structure with the size (dls_size) of the
                   structure needed for the subsequent RTLD_DI_SERINFO
                   request.

              (2)  Allocate a Dl_serinfo buffer of the correct size
                   (dls_size).

              (3)  Use a further RTLD_DI_SERINFOSIZE request to populate
                   the dls_size and dls_cnt fields of the buffer
                   allocated in the previous step.

              (4)  Use a RTLD_DI_SERINFO to obtain the library search
                   paths.

              The Dl_serinfo structure is defined as follows:

                  typedef struct {
                      size_t dls_size;           /* Size in bytes of
                                                    the whole buffer */
                      unsigned int dls_cnt;      /* Number of elements
                                                    in 'dls_serpath' */
                      Dl_serpath dls_serpath[1]; /* Actually longer,
                                                    'dls_cnt' elements */
                  } Dl_serinfo;

              Each of the dls_serpath elements in the above structure is
              a structure of the following form:

                  typedef struct {
                      char *dls_name;            /* Name of library search
                                                    path directory */
                      unsigned int dls_flags;    /* Indicates where this
                                                    directory came from */
                  } Dl_serpath;

              The dls_flags field is currently unused, and always
              contains zero.

       RTLD_DI_SERINFOSIZE (Dl_serinfo *)
              Populate the dls_size and dls_cnt fields of the Dl_serinfo
              structure pointed to by info with values suitable for
              allocating a buffer for use in a subsequent
              RTLD_DI_SERINFO request.

       RTLD_DI_TLS_MODID (size_t *, since glibc 2.4)
              Obtain the module ID of this shared object's TLS (thread-
              local storage) segment, as used in TLS relocations.  If
              this object does not define a TLS segment, zero is placed
              in *info.

       RTLD_DI_TLS_DATA (void **, since glibc 2.4)
              Obtain a pointer to the calling thread's TLS block
              corresponding to this shared object's TLS segment.  If
              this object does not define a PT_TLS segment, or if the
              calling thread has not allocated a block for it, NULL is
              placed in *info.
RETURN VALUE         top
       On success, dlinfo() returns 0.  On failure, it returns -1; the
       cause of the error can be diagnosed using dlerror(3).
ATTRIBUTES         top
       For an explanation of the terms used in this section, see
       attributes(7).
       ┌─────────────────────────────────────┬───────────────┬─────────┐
       │ Interface                           │ Attribute     │ Value   │
       ├─────────────────────────────────────┼───────────────┼─────────┤
       │ dlinfo()                            │ Thread safety │ MT-Safe │
       └─────────────────────────────────────┴───────────────┴─────────┘
VERSIONS         top
       The sets of requests supported by the various implementations
       overlaps only partially.
STANDARDS         top
       GNU.
HISTORY         top
       glibc 2.3.3.  Solaris.
EXAMPLES         top
       The program below opens a shared objects using dlopen(3) and then
       uses the RTLD_DI_SERINFOSIZE and RTLD_DI_SERINFO requests to
       obtain the library search path list for the library.  Here is an
       example of what we might see when running the program:

           $ ./a.out /lib64/libm.so.6
           dls_serpath[0].dls_name = /lib64
           dls_serpath[1].dls_name = /usr/lib64

   Program source

       #define _GNU_SOURCE
       #include <dlfcn.h>
       #include <link.h>
       #include <stdio.h>
       #include <stdlib.h>

       int
       main(int argc, char *argv[])
       {
           void *handle;
           Dl_serinfo serinfo;
           Dl_serinfo *sip;

           if (argc != 2) {
               fprintf(stderr, "Usage: %s <libpath>\n", argv[0]);
               exit(EXIT_FAILURE);
           }

           /* Obtain a handle for shared object specified on command line. */

           handle = dlopen(argv[1], RTLD_NOW);
           if (handle == NULL) {
               fprintf(stderr, "dlopen() failed: %s\n", dlerror());
               exit(EXIT_FAILURE);
           }

           /* Discover the size of the buffer that we must pass to
              RTLD_DI_SERINFO. */

           if (dlinfo(handle, RTLD_DI_SERINFOSIZE, &serinfo) == -1) {
               fprintf(stderr, "RTLD_DI_SERINFOSIZE failed: %s\n", dlerror());
               exit(EXIT_FAILURE);
           }

           /* Allocate the buffer for use with RTLD_DI_SERINFO. */

           sip = malloc(serinfo.dls_size);
           if (sip == NULL) {
               perror("malloc");
               exit(EXIT_FAILURE);
           }

           /* Initialize the 'dls_size' and 'dls_cnt' fields in the newly
              allocated buffer. */

           if (dlinfo(handle, RTLD_DI_SERINFOSIZE, sip) == -1) {
               fprintf(stderr, "RTLD_DI_SERINFOSIZE failed: %s\n", dlerror());
               exit(EXIT_FAILURE);
           }

           /* Fetch and print library search list. */

           if (dlinfo(handle, RTLD_DI_SERINFO, sip) == -1) {
               fprintf(stderr, "RTLD_DI_SERINFO failed: %s\n", dlerror());
               exit(EXIT_FAILURE);
           }

           for (size_t j = 0; j < serinfo.dls_cnt; j++)
               printf("dls_serpath[%zu].dls_name = %s\n",
                      j, sip->dls_serpath[j].dls_name);

           exit(EXIT_SUCCESS);
       }
SEE ALSO         top
       dl_iterate_phdr(3), dladdr(3), dlerror(3), dlopen(3), dlsym(3),
       ld.so(8)
COLOPHON         top
       This page is part of the man-pages (Linux kernel and C library
       user-space interface documentation) project.  Information about
       the project can be found at 
       ⟨https://www.kernel.org/doc/man-pages/⟩.  If you have a bug report
       for this manual page, see
       ⟨https://git.kernel.org/pub/scm/docs/man-pages/man-pages.git/tree/CONTRIBUTING⟩.
       This page was obtained from the tarball man-pages-6.9.1.tar.gz
       fetched from
       ⟨https://mirrors.edge.kernel.org/pub/linux/docs/man-pages/⟩ on
       2024-06-26.  If you discover any rendering problems in this HTML
       version of the page, or you believe there is a better or more up-
       to-date source for the page, or you have corrections or
       improvements to the information in this COLOPHON (which is not
       part of the original manual page), send a mail to
       man-pages@man7.org

Linux man-pages 6.9.1          2024-06-15                      dlinfo(3)
```

### FreeBSD

[dlinfo(3)](https://man.freebsd.org/cgi/man.cgi?query=dlinfo&sektion=3&manpath=FreeBSD+12.1-RELEASE+and+Ports)

```bash
DLINFO(3)		    Library Functions Manual		     DLINFO(3)

NAME
       dlinfo -- information about dynamically loaded object

LIBRARY
       Standard	C Library (libc, -lc)

SYNOPSIS
       #include	<link.h>
       #include	<dlfcn.h>

       int
       dlinfo(void * restrict handle, int request, void	* restrict p);

DESCRIPTION
       The dlinfo() function provides information about	dynamically loaded ob-
       ject.  The action taken by dlinfo() and exact meaning and type of p ar-
       gument depend on	value of the request argument provided by caller.

       The  handle  argument  is  either the value returned from the dlopen(3)
       function	call or	special	handle RTLD_SELF.  If handle is	the value  re-
       turned  from  dlopen(3),	the information	returned by the	dlinfo() func-
       tion pertains to	the specified object.  If handle is the	special	handle
       RTLD_SELF, the information returned pertains to the caller itself.

       Possible	values for the request argument	are:

       RTLD_DI_LINKMAP
	       Retrieve	the Link_map (struct link_map) structure  pointer  for
	       the  specified handle.  On successful return, the p argument is
	       filled with the pointer to  the	Link_map  structure  (Link_map
	       **p)  describing	 a shared object specified by the handle argu-
	       ment.  The Link_map  structures	are  maintained	 as  a	doubly
	       linked  list  by	 ld.so(1),  in the same	order as dlopen(3) and
	       dlclose(3) are called.  See "EXAMPLES", example 1.

	       The Link_map structure is defined in <link.h> and has the  fol-
	       lowing members:

		     caddr_t	     l_addr;	/* Base	Address	of library */
		     const char	     *l_name;	/* Absolute Path to Library */
		     const void	     *l_ld;	/* Pointer to .dynamic in memory */
		     struct link_map *l_next,	/* linked list of mapped libs */
				     *l_prev;

	       l_addr  The base	address	of the object loaded into memory.

	       l_name  The full	name of	the loaded shared object.

	       l_ld    The  address of the dynamic linking information segment
		       (PT_DYNAMIC) loaded into	memory.

	       l_next  The next	Link_map structure on the link-map list.

	       l_prev  The previous Link_map structure on the link-map list.

       RTLD_DI_SERINFO
	       Retrieve	the library search paths  associated  with  the	 given
	       handle  argument.   The	p  argument should point to Dl_serinfo
	       structure buffer	(Dl_serinfo  *p).   The	 Dl_serinfo  structure
	       must be initialized first with the RTLD_DI_SERINFOSIZE request.

	       The  returned  Dl_serinfo structure contains dls_cnt Dl_serpath
	       entries.	 Each entry's dlp_name	field  points  to  the	search
	       path.   The  corresponding  dlp_info field contains one of more
	       flags indicating	the origin of the path (see the	LA_SER_* flags
	       defined in the <link.h> header file).  See "EXAMPLES",  example
	       2, for a	usage example.

       RTLD_DI_SERINFOSIZE
	       Initialize  a Dl_serinfo	structure for use in a RTLD_DI_SERINFO
	       request.	 Both the dls_cnt and dls_size fields are returned  to
	       indicate	 the  number of	search paths applicable	to the handle,
	       and the total size of a	Dl_serinfo  buffer  required  to  hold
	       dls_cnt	Dl_serpath  entries  and  the  associated  search path
	       strings.	 See "EXAMPLES", example 2, for	a usage	example.

       RTLD_DI_ORIGIN
	       Retrieve	the origin of the dynamic object associated  with  the
	       handle.	 On  successful	 return, p argument is filled with the
	       char pointer (char *p).

RETURN VALUES
       The dlinfo() function returns 0 on success, or -1 if an error occurred.
       Whenever	an error has been detected, a message detailing	it can be  re-
       trieved via a call to dlerror(3).

EXAMPLES
       Example 1: Using	dlinfo() to retrieve Link_map structure.

       The  following example shows how	dynamic	library	can detect the list of
       shared libraries	loaded after  caller's	one.   For  simplicity,	 error
       checking	has been omitted.

	     Link_map *map;

	     dlinfo(RTLD_SELF, RTLD_DI_LINKMAP,	&map);

	     while (map	!= NULL) {
		     printf("%p: %s\n",	map->l_addr, map->l_name);
		     map = map->l_next;
	     }

       Example 2: Using	dlinfo() to retrieve the library search	paths.

       The  following  example	shows how a dynamic object can inspect the li-
       brary search paths that would be	used to	locate a simple	filename  with
       dlopen(3).  For simplicity, error checking has been omitted.

	     Dl_serinfo	      _info, *info = &_info;
	     Dl_serpath	     *path;
	     unsigned int     cnt;

	     /*	determine search path count and	required buffer	size */
	     dlinfo(RTLD_SELF, RTLD_DI_SERINFOSIZE, (void *)info);

	     /*	allocate new buffer and	initialize */
	     info = malloc(_info.dls_size);
	     info->dls_size = _info.dls_size;
	     info->dls_cnt = _info.dls_cnt;

	     /*	obtain sarch path information */
	     dlinfo(RTLD_SELF, RTLD_DI_SERINFO,	(void *)info);

	     path = &info->dls_serpath[0];

	     for (cnt =	1; cnt <= info->dls_cnt; cnt++,	path++)	{
		     (void) printf("%2d: %s\n",	cnt, path->dls_name);
	     }

SEE ALSO
       rtld(1),	dladdr(3), dlopen(3), dlsym(3)

HISTORY
       The  dlinfo()  function first appeared in the Solaris operating system.
       In FreeBSD, it first appeared in	FreeBSD	4.8.

AUTHORS
       The FreeBSD implementation of  the  dlinfo()  function  was  originally
       written	by  Alexey Zelkin <phantom@FreeBSD.org>	and later extended and
       improved	by Alexander Kabaev <kan@FreeBSD.org>.

       The manual  page	 for  this  function  was  written  by	Alexey	Zelkin
       <phantom@FreeBSD.org>.

FreeBSD	13.2		       February	14, 2003		     DLINFO(3)
```
