
1. 下载代码, 注意用命令行
git clone --recurse-submodules https://github.com/blakeyi/quickjs-cmake-mingw

2. 检查mingw和cmake版本

cmake直接去百度下载即可
```shell
cmake -version
cmake version 3.31.0-rc3

CMake suite maintained and supported by Kitware (kitware.com/cmake).

mingw没有的话, 因为下载起来比较麻烦, 而且sourceforge上在比较新的版本后不再提供二进制版本,只有源码版本, 可以考虑在github上下载编译好的
release,参考这个地址 `https://github.com/niXman/mingw-builds-binaries/releases/tag/8.5.0-rt_v10-rev0`, 解压后注意配置环境变量

```shell
gcc -v
Using built-in specs.
COLLECT_GCC=C:\Program Files (x86)\mingw64\bin\gcc.exe
COLLECT_LTO_WRAPPER=C:/Program\ Files\ (x86)/mingw64/bin/../libexec/gcc/x86_64-w64-mingw32/8.5.0/lto-wrapper.exe
Target: x86_64-w64-mingw32

Thread model: win32
gcc version 8.5.0 (x86_64-win32-seh-rev0, Built by MinGW-W64 project)

```

3. 编译执行
注意这里指定使用mingw,不然会默认使用mvsc

```shell
mkdir build
cd build
cmake -G "MinGW Makefiles" -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ ..
make
```
这里正常libqjs.a和qjsc.exe都能编译成功, 但是qjs-app.exe一般会编译失败,错误可能是如下, 原因是少了两个源文件repl.c和qjscalc.c, 这两个文件需要使用qjsc.exe进行编译得到, 先把对应js
文件中的`"use strip";`注释掉, 然后执行
`.\qjsc.exe -c .\repl.js -o repl.c`和`.\qjsc.exe -c .\qjscalc.js -o qjscalc.c`, 注意生成后的文件, 需要进行二次修改,把c文件中对应的变量名字里多余的下划线去除, 保留一个就行,比如
`qjsc___repl_size` 改成 `qjsc_repl_size` , 修完完后的文件需要放到quickjs源码目录, 然后把CMakeLists里注释掉的repl.c和qjscalc.c取消

```shell
[100%] Linking C executable qjs-app.exe
C:/PROGRA~2/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.5.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles\qjs-app.dir/objects.a(qjs.c.obj):qjs.c:(.rdata$.refptr.qjsc_repl[.refptr.qjsc_repl]+0x0): undefined reference to `qjsc_repl'
C:/PROGRA~2/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.5.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles\qjs-app.dir/objects.a(qjs.c.obj):qjs.c:(.rdata$.refptr.qjsc_repl_size[.refptr.qjsc_repl_size]+0x0): undefined reference to `qjsc_repl_size'
C:/PROGRA~2/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.5.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles\qjs-app.dir/objects.a(qjs.c.obj):qjs.c:(.rdata$.refptr.qjsc_qjscalc[.refptr.qjsc_qjscalc]+0x0): undefined reference to `qjsc_qjscalc'
C:/PROGRA~2/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.5.0/../../../../x86_64-w64-mingw32/bin/ld.exe: CMakeFiles\qjs-app.dir/objects.a(qjs.c.obj):qjs.c:(.rdata$.refptr.qjsc_qjscalc_size[.refptr.qjsc_qjscalc_size]+0x0): undefined reference to `qjsc_qjscalc_size'
collect2.exe: error: ld returned 1 exit status
make[2]: *** [CMakeFiles\qjs-app.dir\build.make:134: qjs-app.exe] Error 1
make[1]: *** [CMakeFiles\Makefile2:154: CMakeFiles/qjs-app.dir/all] Error 2
make: *** [Makefile:90: all] Error 
```

参考文档:
- https://github.com/septbr/quickjs-cmake-mingw
- https://blog.csdn.net/weixin_50674989/article/details/136763247

