# Maven Setting.xml 配置问题

😁😀😉


## 文件结构

- settings
    - servers 一般用来设置远程仓库的账户密码的，**server 的id必须跟远程仓库的id对应**

    - mirrors 其实是repository的拦截器，拦截的重点是 MirrorOf 标签值为准，它的作用是拦截你MirrorOf中的远程仓库，到url地址去拿依赖，其实是没有太多用处的。

    - profiles 是maven环境的标签 比如我们开发会有公司开发环境，家里的开发环境，私活的外包环境 子标签的profile id 是非常的重要的，一定要配置

        - repositories 可以配置多个repository仓库，仓库一般都会有标签有 id,name,url,releases,snapshots这些标签
    
    - activeProfiles 是默认使用哪个配置环境 使用activeProfile 然后就是profiles 的id值就行了。


## 再说说 maven-wrapper 原理吧
    
所有 Spring boot 生产的项目都会有 maven-wrapper.properties 文件

```properties
distributionUrl=https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.6.3/apache-maven-3.6.3-bin.zip
wrapperUrl=https://repo.maven.apache.org/maven2/io/takari/maven-wrapper/0.5.6/maven-wrapper-0.5.6.jar
```
 
其中最重要的是 **distributionUrl** 这个属性了，这个属性会在执行 maven-wrapper.jar 包的时候被加密生产md5摘要文件夹

```java
  private String rootDirName(String distName, WrapperConfiguration configuration) {
      // 这个 configuration 是在程序开始的时候会读取这个文件，并把属性set进去
    String urlHash = getMd5Hash(configuration.getDistribution().toString());
    return String.format("%s/%s", distName, urlHash);
  }

  // 然后通过MD5加密这个值，这样就可以相同的版本的项目就可以使用相同的版本
  // 就不会重新下载了
  private String getMd5Hash(String string) {
    try {
      MessageDigest messageDigest = MessageDigest.getInstance("MD5");
      byte[] bytes = string.getBytes();
      messageDigest.update(bytes);
      return new BigInteger(1, messageDigest.digest()).toString(32);
    } catch (Exception e) {
      throw new RuntimeException("Could not hash input string.", e);
    }
  }
```

其他的逻辑就一般般了。

就是执行读取maven-wrapper.properties配置文件信息，然后就是下载压缩文件，解压缩。

## 那么就有一个问题，那我执行 mvnw.cmd 为什么就是mvn.cmd 命令呢？

mvnw.cmd 转发的功能 

```powershell
%MAVEN_JAVA_EXE% %JVM_CONFIG_MAVEN_PROPS% %MAVEN_OPTS% %MAVEN_DEBUG_OPTS% -classpath %WRAPPER_JAR% "-Dmaven.multiModuleProjectDirectory=%MAVEN_PROJECTBASEDIR%" %WRAPPER_LAUNCHER% %MAVEN_CONFIG% %*
```

就是通过转发参数把所有的要执行的还是maven-wrapper.jar这个包，也就是说你每执行一次程序，那么就会跑一次这样的程序，只是转发了这个参数，然后在程序里面执行这个命令，并打印出来。

基本上就是这样了。。。。