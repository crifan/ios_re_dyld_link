# dyld_shared_cache_file_path

* 定义
  * `const char* dyld_shared_cache_file_path()`
* 实现
  * [APIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html)
    ```c
    const char* dyld_shared_cache_file_path()
    {
        log_apis("dyld_shared_cache_file_path()\n");

        return gAllImages.dyldCachePath();
    }
    ```
