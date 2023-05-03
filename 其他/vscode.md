```
vscode启动不了，折腾了半天发现已经不支持win7

今天上午下载vscode 1.71版本，顺利安装，可是怎么也启动不了。在百度上研究了半天，试了各种方法，都无果。

在DOS下报错，很奇怪的原因，百度也找不到这个问题的解决方案，难道我这么幸运是天下遇见这问题的第一人？


C:\Users\user\AppData\Local\Programs\Microsoft VS Code>Code.exe
[5580:1025/163551.638:ERROR:gl_surface_egl.cc(852)] EGL Driver message (Critical) eglInitialize: No available renderers.
[5580:1025/163551.638:ERROR:gl_surface_egl.cc(1489)] eglInitialize D3D11 failed with error EGL_NOT_INITIALIZED, trying next display type
[main 2022-10-25T08:35:51.661Z] SystemError [ERR_SYSTEM_ERROR]: A system error occurred: uv_os_getho stname returned ENOSYS (function not implemented)
    at Pt.initServices (C:\Users\user\AppData\Local\Programs\Microsoft VS Code\resources\app\out\vs\code\electron-main\main.js:83:69858)
    at Pt.startup (C:\Users\user\AppData\Local\Programs\Microsoft VS Code\resources\app\out\vs\code\electron-main\main.js:83:65416)
    at process.processTicksAndRejections (node:internal/process/task_queues:96:5)
    
    
就这样郁闷了一个上午，怎么也找不到解决方案，百度的结果都翻遍了。下午不死心，上VSCODE官方想重新下一个安装包。赫然发现，VSCODE 1.71居然只支持win8以上的系统，这才恍然大悟，一上午净瞎折腾了。

果然按贴吧上的网友提示的，安装旧版1.69，顺利运行。

vscode1.69最新版zip版下载地址，官网下载太慢，直接点链接下载
https://vscode.cdn.azure.cn/stable/92d25e35d9bf1a6b16f7d0758f25d48ace11e5b9/VSCode-win32-x64-1.69.0.zip
```

