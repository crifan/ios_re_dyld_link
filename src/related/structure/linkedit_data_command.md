# linkedit_data_command

* 定义

* `EXTERNAL_HEADERS/mach-o/loader.h`

```c
#define LC_BUILD_VERSION 0x32 /* build for platform min OS version */
#define LC_DYLD_EXPORTS_TRIE (0x33 | LC_REQ_DYLD) /* used with linkedit_data_command, payload is trie */
#define LC_DYLD_CHAINED_FIXUPS (0x34 | LC_REQ_DYLD) /* used with linkedit_data_command */
#define LC_FILESET_ENTRY      (0x35 | LC_REQ_DYLD) /* used with fileset_entry_command */
...

/*
 * The linkedit_data_command contains the offsets and sizes of a blob
 * of data in the __LINKEDIT segment.  
 */
struct linkedit_data_command {
    uint32_t    cmd;        /* LC_CODE_SIGNATURE, LC_SEGMENT_SPLIT_INFO,
                                   LC_FUNCTION_STARTS, LC_DATA_IN_CODE,
                   LC_DYLIB_CODE_SIGN_DRS,
                   LC_LINKER_OPTIMIZATION_HINT,
                   LC_DYLD_EXPORTS_TRIE, or
                   LC_DYLD_CHAINED_FIXUPS. */
    uint32_t    cmdsize;    /* sizeof(struct linkedit_data_command) */
    uint32_t    dataoff;    /* file offset of data in __LINKEDIT segment */
    uint32_t    datasize;    /* file size of data in __LINKEDIT segment  */
};
```
