# Mach-O中

* Mach-O中有关dyld的内容
  * Header的flag
    * `MH_DYLDLINK`
  * LC_开头的dyld相关的Load Command
    * `LC_DYLD_CHAINED_FIXUPS`
    * `LC_DYLD_EXPORTS_TRIE`
    * `LC_LOAD_DYLINKER`
      * `/usr/lib/dyld`
    * `LC_DYLD_INFO`
    * `LC_DYLD_INFO_ONLY`
    * `LC_DYLD_ENVIRONMENT`
