# Mach-O中Load Command

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

## 涉及到的地方

### /usr/lib/dyld

#### akd rabin2

```bash
crifan@licrifandeMacBook-Pro  AppleStore/AuthKit_akd/AuthKit.framework  rabin2 -I akd > akd_rabin2_I_identification.txt
```

-> akd_rabin2_I_identification.txt

```bash
intrp    /usr/lib/dyld
```

#### Preferences rabin2

Preferences_rabin2_I_identification.coffee

```bash
intrp    /usr/lib/dyld
```

#### Undecember

RzGame_rabin2_I_identification.coffee

```bash
intrp    /usr/lib/dyld
```
