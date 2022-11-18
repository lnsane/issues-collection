# SonarQube 扫描项目

## SonarQube
[SonarQube 服务端下载地址](https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.7.1.62043.zip)

[Sonar-Scanner-Cli 服务端下载地址](https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-windows.zip)

## Java 项目

```shell
mvn clean verify sonar:sonar \
  -Dsonar.projectKey=sdsda \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=sqp_5c356368f06719c6dde47d8f6883acf97a481a5d```
```

## Vue 项目
vue 项目要用Sonar-Scanner-Cli去做

```shell
sonar-scanner.bat -D"sonar.projectKey=sdsda" -D"sonar.sources=." -D"sonar.host.url=http://localhost:9000" -D"sonar.login=sqp_5c356368f06719c6dde47d8f6883acf97a481a5d"
```

- sonar-scanner.bat 这里要注意的是必须是对应项目下执行的才行

- sonar.sources 要扫描对应的项目