# DYLD_LIBRARY_PATH

下面整理涉及到`DYLD_LIBRARY_PATH`的相关内容：

## launchctl的参数

iPhone中launchctl的参数含义和典型用法

中的语法介绍：

```bash
SUBCOMMANDS

     bootstrap domain-target [service-path service-path2 ...] | service-target
...
              --introspection-libraries
                       Adds /usr/lib/system/introspection to the DYLD_LIBRARY_PATH environment variable
                       for the service. This causes the system to prefer the introspection
                       variants of libraries if they exist.
```

## AppleStore

```bash
dyld4 config: DYLD_LIBRARY_PATH=/usr/lib/system/introspection ...
```

## 初始化Capstone

```bash
  $ export DYLD_LIBRARY_PATH=/usr/local/opt/capstone/lib/:$DYLD_LIBRARY_PATH
```

-》去加到启动脚本中：

```bash
✘ crifan@licrifandeMacBook-Pro  ~  vi ~/.zshrc

 crifan@licrifandeMacBook-Pro  ~  cat ~/.zshrc | grep DYLD_LIBRARY_PATH
export DYLD_LIBRARY_PATH=/usr/local/opt/capstone/lib/:/usr/local/opt/unicorn/lib/:$DYLD_LIBRARY_PATH

 crifan@licrifandeMacBook-Pro  ~  source ~/.zshrc

 crifan@licrifandeMacBook-Pro  ~  echo $DYLD_LIBRARY_PATH
/usr/local/opt/capstone/lib/:/usr/local/opt/unicorn/lib/:/usr/local/opt/unicorn/lib/:
```

## Mac中安装unicorn

```bash
crifan@licrifandeMacBook-Pro  ~  echo $DYLD_LIBRARY_PATH

```

此处DYLD_LIBRARY_PATH是空的。

去把

```bash
export DYLD_LIBRARY_PATH=/usr/local/opt/unicorn/lib/:$DYLD_LIBRARY_PATH
```

加到启动脚本，此处是`.zshrc`，中：

```bash
✘ crifan@licrifandeMacBook-Pro  ~  vi ~/.zshrc
 crifan@licrifandeMacBook-Pro  ~  source ~/.zshrc
 crifan@licrifandeMacBook-Pro  ~  cat ~/.zshrc | grep unicorn
export DYLD_LIBRARY_PATH=/usr/local/opt/unicorn/lib/:$DYLD_LIBRARY_PATH
```

再去看看：

```bash
crifan@licrifandeMacBook-Pro  ~  echo $DYLD_LIBRARY_PATH
/usr/local/opt/unicorn/lib/:
 crifan@licrifandeMacBook-Pro  ~  which unicorn
unicorn not found
```
