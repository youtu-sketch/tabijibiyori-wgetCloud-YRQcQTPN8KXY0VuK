
## 背景


由于 Natasha 及周边项目发版任务多，文件结构也不简单，之前一直使用基于 Github 管道脚本和 XUnit 来发版。这个方案对于发版环境与条件依赖性较强，且不够灵活，因此萌生出做一个本地管理 Nuget 发版工具的想法，取名为 Jester.


下载地址：[https://github.com/NMSAzulX/Jester.Tools.Nuget/releases/tag/1\.0\.0\.0](https://github.com)
若出现问题可在本篇文章下留言，或在仓储地址中[提交 ISSUE](https://github.com):[FlowerCloud机场节点订阅](https://dahelaoshi.com).


## 运行环境


1. 采用独立打包。
2. Win64。


## 安全说明


目前不打算公开这种工具类的源代码，因此列出相关依赖项以保证用户自主评估安全风险。


### 依赖项列表：


1. `Microsoft.CodeAnalysis.CSharp` Roslyn 的 C\# 语言构建库, 作者：Microsoft
2. `NuGet.Versioning` Semantic Versioning 的操作库，作者: Microsoft
3. `Spectre.Console` 控制台界面，作者: Patrik Svensson, Phil Scott, Nils Andresen, Cedric Luthi, Frank Ray.



> Spectre.Console 项目地址：[https://github.com/spectreconsole/spectre.console](https://github.com)
> 个人觉得不太好用，还有 BUG.


### 网络使用：


1. 使用 "pl" 命令查看打包信息列表时，向 NUGET 请求包信息。
2. 使用 "pack" 命令打包时，向 NUGET 请求包信息以做校验本地包与服务器包上的版本信息。
3. 使用 "push" 命令推送包时，向用户指定源服务器或 NUGET 官方源推送包。


### 文件改变：


1. 使用 "pack" 命令时，会更新或创建 csproj 文件的版本信息。
2. 使用 "pack" 命令时，会更新或创建 csproj 文件中的 None Include 节点信息。


## 工具使用


### 界面与输入端


![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212013299-377158665.png)


### 命令支持


输入命令 {?} 可以查看改工具支持的命令。
![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212103736-121960685.png)


### Nuget Key 操作


![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212705438-87308925.png)



```
#查看当前 nuget key 列表
nl

#添加一个 nuget key 发布方案
na publishTest oy2nabcdefghiojxv http://公司的nuget地址
#不指定 source 使用 nuget 官方的地址
na publishTest oy2nabcdefghiojxv 

#删除发布方案
nd publishTest


```

### Solution 操作


![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209220145548-21667874.png)


该工具以 sln 解决方案为单位，支持添加、删除、更新 NUGET KEY 等操作。


以下为命令使用案例：



```
#查看当前存在的解决方案
sl 

#增加一个解决方案
sa c://mysolution

#删除列表中第一个方案
sd 0

#让该方案在发布时使用某个 NUGET KEY
#锁定第一个方案
sc 0


```

### 检查与打包


该工具以 CHANGELOG.MD 文件为打包发版依据。



```
#查看当前解决方案列表
sl

#锁定你要操作的解决方案
#锁定列表中第一个解决方案 
#若没有 CHANGELOG 文件工具将自动生成一个文件
#CHANGELOG 中有相应的版本说明和使用案例
sc 0

#按照文件中的案例格式编辑被打开的 CHANGELOG 文件

#使用 pl 查看当前 CHANGELOG 文件与当前解决方案的匹配信息【非必要】
pl

#确认无误，打包
pack

#若不发布隐式 using 文件，则
pack --no-using

#若不合并 targets 文件，则
pack --no-combine

#若有多个 targets 文件，则
#1. 去掉文件中的  节点标签，以便 Jester 合并内容到同一个  节点下。
#2. 不要使用 Jester.Usings.targets 来命名文件，该文件预留给了 Jester 输出隐式命名空间用。
#3. 不要使用 Jester.Combine.targets 来命名文件，该文件预留给了 Jester 合并所有 targets 文件的输出文件使用。

#后续会根据反馈对 pack 策略做出调整。

```


> pl 命令会自动拉去 nuget 官方记录的版本信息，local 为本地工程文件中的版本信息，next 为 CHANGELOG 被打包的版本信息


![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209194606661-1235031892.png)


### 发布



```
#如果之前已经锁定解决方案则不用重新锁定了
#若被锁定的解决方案没有关联发布策略
su publishTest

#查看打包好的包 在 your_sln_folder/nugets 文件夹中

#发布
push

```

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209210350097-90044594.png)


![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209195226912-1947652434.png)


### 退出


按 Q 退出


