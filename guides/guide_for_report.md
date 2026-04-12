# 编写好fuzz driver后流程


## 烧制ohos环境：
服务器鸿蒙环境镜像路径：`/home/azureuser/OpenHarmony/OpenHarmony-5.0-Release/out/rk3568/packages/phone/images`
tar打包，然后scp传到本地上面

用到大禹这个工具，路径在`C:\Users\ISER\Documents\devbounty\HiHope_DAYU200\HiHope_DAYU200\烧写工具及指南\windows\RKDevTool`
开发板`reset`加`音量+键`同时按下，然后先放`reset`，再放`音量+键`，进入`loader`模式
管理员身份运行，右键导入配置，配置在image下载到本地的`config.cfg`
* **记得一个个改路径**，然后执行烧制操作

## 编译+脚本交互生成覆盖率报告流程：
每个项目目录下会有一个“项目名.gni”的配置文件，要先设置一下，在文件里面的第一行加上下面这个
`import getenv("HOME") + "/devbounty/config.gni"`

再把我们写好的fuzz driver文件夹放到项目的`/test/fuzztest/`下

改掉项目的`/test/fuzztest/`下的BUILD.gn文件，在dep列表最下方加一行
"fuzz driver文件夹名：fuzz driver里面BUILD.gn里面的group名"

* 编译：利用/devbounty/build_ohos.sh脚本来编译
`sh build_ohos.sh 项目文件夹路径 编译目标 编译依赖`

* 编译目标是指你写的fuzz driver所在文件夹里面的BUILD.gn文件里面，ohos_fuzztest("编译目标名")
* 编译依赖是指BUILD.gn里面deps列表里面冒号后面的这个名字
如：
deps = [
    "//foundation/resourceschedule/soc_perf/interfaces/inner_api/socperf_client:socperf_client",
  ]
编译依赖就是socperf_client

编译过程中可能会报错，一般是因为test/fuzztest/下所有的fuzz driver文件夹里面的BUILD.gn里面，原本的cflags是“=”号，现在全部要改成+=号（现在是人工一个个去改，后续可以用脚本去改）
**（注意：现在老师已经在build_ohos.sh脚本里面添加了对cflags的修改，所以不用人工去改了）**

编译过程中，信息中出现"{}"的时候就开始真正的编译过程了
build success就代表编译好了

编译完毕之后，插桩好的动态库、target可执行文件、dep依赖和corpus会放在服务器的devbounty文件夹里面
可以通过
`arm-linux-gnueabi-objdump -d -S xxx.so | grep llvm_profile `来看汇编里面有没有符号和插桩
`arm-linux-gnueabi-readelf -S xxx.so | grep llvm_cov` 看动态链接库里面的节

* 然后回到主机上面来，执行sync_from_server.bat脚本，可以从服务器上面把target xxx.so dep依赖和corpus传到本地的devbounty文件夹

* 再用sync_with_device.bat脚本，将这几个传到开发板上面，跑出来covdata，然后再传回主机得到covdata

* 最后用sync_to_server.bat将covdata传到服务器上面，跑出来covhtml, 再传回主机

之后就点开html文件就可以看覆盖率报告了



## 无脚本时生成编译后可执行测试程序的覆盖率报告流程(已不再需要)

将编译好的fuzz driver导入到开发板上面，需要一个工具，路径在：`C:\Users\ISER\Documents\devbounty\ohos-sdk\windows\toolchains-windows-x64-4.0.10.5-Release\toolchains\hdc.exe`
`.\hdc.exe shell` 进入开发板
`.\hdc.exe file send src des` 上传文件 （在服务器上面已经编译好的fuzz程序target，先下载到本地，然后再上传到开发板上面`/data/local/tmp/`）(输入语料库也要上传到这个`/data/local/tmp`位置，名字叫`corpus`)

在shell进来之后，到`/data/local/tmp/`里面，创建covdata(覆盖率报告)，findings(发现的漏洞)两个文件夹，给target加可执行权限

设置环境变量：`export LLVM_PROFILE_FILE=/data/local/tmp/covdata/coverage-%p.profraw`

运行fuzz程序：`./target -runs=次数 corpus findings`
运行结果会在`/data/local/tmp/covdata`里面

然后文件接受回来这个profraw结果
`.\hdc.exe file recv /data/local/tmp/covdata . `
再把covdata放到远程服务器上面
`scp -r -i 密钥 .\covdata\ azureuser@ip:/home/azureuser/devbounty`

进到服务器的devbounty里面之后，先执行clear脚本把之前的测试报告删了
target那个可执行程序也得在devbounty里面
再运行showcoverage脚本，生成一个html的覆盖率报告

最后把html文件传回本地
`scp -r -i 密钥 azureuser@ip:/home/azureuser/devbounty/covhtml . `

然后就可以在本地打开html文件看覆盖率了



