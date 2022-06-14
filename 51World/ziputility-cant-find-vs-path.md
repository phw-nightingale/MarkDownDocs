## 项目编译时遇到: ZipUtility Error: can't find VS path
---

### 错误信息
```
ZipUtility Error: can't find VS path: System.IO.DirectoryNotFoundException: 未能找到路径“C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC”的一部分。
     在 System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)
     在 System.IO.FileSystemEnumerableIterator`1.CommonInit()
     在 System.IO.FileSystemEnumerableIterator`1..ctor(String path, String originalUserPath, String searchPattern, SearchOption searchOption, SearchResultHandler`1 resultHandler, Boolean checkHost)
     在 System.IO.Directory.GetDirectories(String path)
     在 UrbanDataboardEditor.get_ATLPath() 位置 d:\AntLibs\WS\4.24_2.3_ABG\Engine\Plugins\51Hitech\UrbanDataBoard\Source\UrbanDataboardEditor\UrBanDataboardEditor.Build.cs:行号 55
```

### 原因
`模板项目中的UrbanDataboard插件引用了ZipUtility插件，而ZipUtility插件的默认编译工具是VS2017，它会从注册表中读取VS2017的路径，如果你之前安装过VS2017又卸载了，就会出现注册表中的VS2017路径找不到的问题`

### 解决方法
```c++
// 在注册表中添加VS2019的安装路径
// 全局搜索"can't find VS path", 得到UrbanDataboardEditor.Build.cs文件

string regPath = (string)Registry.GetValue(@"HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\VisualStudio\SxS\VS7\", "16.0", "");

// 将这个路径的注册表值新增VS2019的版本并替换对应的键，重新编译
```
