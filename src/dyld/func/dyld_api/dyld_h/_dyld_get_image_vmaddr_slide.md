# _dyld_get_image_vmaddr_slide

* 语法
  ```c
  intptr_t _dyld_get_image_vmaddr_slide(uint32_t image_index);
  ```

## 反越狱检测

代码和效果详见：

* [crifan/iOSBypassJailbreak: 越狱iOS的hook插件，实现反越狱检测](https://github.com/crifan/iOSBypassJailbreak)
  * [hook_dyld.xm](https://github.com/crifan/iOSBypassJailbreak/blob/main/iOSBypassJailbreak/hook_dyld.xm)
