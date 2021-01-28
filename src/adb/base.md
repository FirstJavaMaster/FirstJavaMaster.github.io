## ADB 杂项记录

### 从手机拿到已安装应用的安装包

查看应用列表

    > adb shell pm list package

或直接筛选：

    > adb.exe shell pm list package | findstr qq

此时会显示具体的包名，如：

    package:com.tencent.mobileqq

然后找具体的所在目录：

    > adb.exe shell pm path com.tencent.mobileqq

此时会显示具体目录：

    package:/data/app/com.tencent.mobileqq-qYHJ1lhGSqxZPjAckgjrVg==/base.apk

现在直接拷贝出来即可，会拷贝到当前目录下：

    > adb.exe pull /data/app/com.tencent.mobileqq-qYHJ1lhGSqxZPjAckgjrVg==/base.apk