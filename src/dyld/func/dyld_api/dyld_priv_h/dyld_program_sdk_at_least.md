# dyld_program_sdk_at_least

* 定义
  ```c
  bool dyld_program_sdk_at_least(dyld_build_version_t version)
  ```
* 实现
  * [APIs.cpp](https://opensource.apple.com/source/dyld/dyld-852.2/dyld3/APIs.cpp.auto.html)
    ```c
    bool dyld_program_sdk_at_least(dyld_build_version_t version) {
        return VersionSPIDispatcher::dyld_program_sdk_at_least(version);
    }
    ```

## 涉及到的地方

### dyld_program_sdk_at_least

```bash
CFNetwork`___lldb_unnamed_symbol941$$CFNetwork:
->  0x1820d22a8 <+0>:   pacibsp
...
    0x1820d242c <+388>: bl     0x1815c36c4              ; dyld_program_sdk_at_least
...
```
