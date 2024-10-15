# _dyld_get_all_image_infos

* 定义
  * [dyld3/APIs.h](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.h.auto.html)
    * `const struct dyld_all_image_infos* _dyld_get_all_image_infos() TEMP_HIDDEN;`
  * [mach-o/dyld_priv.h](https://opensource.apple.com/source/dyld/dyld-421.1/include/mach-o/dyld_priv.h.auto.html)
    ```c
    // get pointer to this process's dyld_all_image_infos
    // Exists in Mac OS X 10.4 and later through _dyld_func_lookup()
    // Exists in Mac OS X 10.6 and later through libSystem.dylib
    //
    const struct dyld_all_image_infos* _dyld_get_all_image_infos() __attribute__((deprecated));
    ```
* 实现
  * [dyldAPIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/src/dyldAPIs.cpp.auto.html)
    ```c
    const struct dyld_all_image_infos* _dyld_get_all_image_infos()
    {
      return dyld::gProcessInfo;
    }
    ```
  * [APIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html)
    ```c
    const struct dyld_all_image_infos* _dyld_get_all_image_infos()
    {
        return gAllImages.oldAllImageInfo();
    }
    ```

## 相关

### `dyld_debug.c`

```c
// SPI into dyld to get address of _dyld_get_all_image_infos data structure
static const struct dyld_all_image_infos* dyld_get_all_image_infos()
{
    static const struct dyld_all_image_infos* (*p)() = NULL;

  if ( p == NULL )
      _dyld_func_lookup("__dyld_get_all_image_infos", (void**)&p);

  if ( p != NULL )
    return p();
  else
    return NULL;
}
```

## 用法举例

```c
/* Example using struct dyld_all_image_infos */

#include <stdio.h>
#include <dlfcn.h>

#include <mach-o/dyld_images.h>
#include <mach-o/dyld.h>
#include <mach-o/dyld_priv.h>

int
main(void)
{
	int i;
	const struct dyld_all_image_infos *dyld_all_image_infos;

	dyld_all_image_infos = _dyld_get_all_image_infos();

	for(i=0;i<dyld_all_image_infos->infoArrayCount;i++) {
		printf("image: %s %d\n",
			dyld_all_image_infos->infoArray[i].imageFilePath,
			(int)dyld_all_image_infos->infoArray[i].imageFileModDate
		);
	}
	
	return 0;
}
```

## 涉及到地方

### nm

```bash
$ nm /usr/lib/system/libdyld.dylib | grep __dyld_get_all_image_infos
0000000000003479 t __dyld_get_all_image_infos
```

### libsubstrate.dylib

```asm
libsubstrate.dylib`___lldb_unnamed_symbol14$$libsubstrate.dylib:
    0x104449178 <+0>:   stp    x29, x30, [sp, #-0x10]!
...
    0x1044491bc <+68>:  b      0x104449270              ; <+248>
    0x1044491c0 <+72>:  bl     0x104458ab0              ; symbol stub for: _dyld_get_all_image_infos
```
