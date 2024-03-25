# dyld_stub_binder

## 反汇编代码

```asm
libdyld.dylib`dyld_stub_binder:
->  0x18d122dc0 <+0>:   stp    x29, x30, [sp, #-0x10]!
    0x18d122dc4 <+4>:   mov    x29, sp
    0x18d122dc8 <+8>:   sub    sp, sp, #0xf0             ; =0xf0 
    0x18d122dcc <+12>:  stp    x0, x1, [x29, #-0x10]
    0x18d122dd0 <+16>:  stp    x2, x3, [x29, #-0x20]
    0x18d122dd4 <+20>:  stp    x4, x5, [x29, #-0x30]
    0x18d122dd8 <+24>:  stp    x6, x7, [x29, #-0x40]
    0x18d122ddc <+28>:  stp    x8, x9, [x29, #-0x50]
    0x18d122de0 <+32>:  stp    q0, q1, [x29, #-0x80]
    0x18d122de4 <+36>:  stp    q2, q3, [x29, #-0xa0]
    0x18d122de8 <+40>:  stp    q4, q5, [x29, #-0xc0]
    0x18d122dec <+44>:  stp    q6, q7, [x29, #-0xe0]
    0x18d122df0 <+48>:  ldr    x0, [x29, #0x18]
    0x18d122df4 <+52>:  ldr    x1, [x29, #0x10]
    0x18d122df8 <+56>:  bl     0x18d1246e4              ; _dyld_fast_stub_entry(void*, long)
    0x18d122dfc <+60>:  mov    x16, x0
    0x18d122e00 <+64>:  ldp    x0, x1, [x29, #-0x10]
    0x18d122e04 <+68>:  ldp    x2, x3, [x29, #-0x20]
    0x18d122e08 <+72>:  ldp    x4, x5, [x29, #-0x30]
    0x18d122e0c <+76>:  ldp    x6, x7, [x29, #-0x40]
    0x18d122e10 <+80>:  ldp    x8, x9, [x29, #-0x50]
    0x18d122e14 <+84>:  ldp    q0, q1, [x29, #-0x80]
    0x18d122e18 <+88>:  ldp    q2, q3, [x29, #-0xa0]
    0x18d122e1c <+92>:  ldp    q4, q5, [x29, #-0xc0]
    0x18d122e20 <+96>:  ldp    q6, q7, [x29, #-0xe0]
    0x18d122e24 <+100>: mov    sp, x29
    0x18d122e28 <+104>: ldp    x29, x30, [sp], #0x10
    0x18d122e2c <+108>: add    sp, sp, #0x10             ; =0x10 
    0x18d122e30 <+112>: br     x16
```
