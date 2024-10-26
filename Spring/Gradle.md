# Gradle

[Gradle下载](https://gradle.org/releases/)

- 和IDEA集成中的Gradle版本保持一致
- 配置环境变量
- IDEA设置(repository和gradle_home)

- 配置gradle下载镜像

init.gradle(${gradle_home}/init.d)

```groovy
allprojects {
    repositories {
        mavenLocal()
        maven {
            name = "Alibaba"
            url = 'https://maven.aliyun.com/repository/public'
            allowInsecureProtocol = true
        }
        maven {
            name = "Bstek"
            url = 'https://nexus.bsdn.org/content/groups/public/'
            allowInsecureProtocol = true
        }
        mavenCentral()
    }

    buildscript {
        repositories {
            maven {
                name = "Alibaba"
                url = 'https://maven.aliyun.com/repository/public'
                allowInsecureProtocol = true
            }
            maven {
                name = "Bstek"
                url = 'https://nexus.bsdn.org/content/groups/public/'
                allowInsecureProtocol = true
            }
            maven {
                name = "M2"
                url = 'https://plugins.gradle.org/m2/'
                allowInsecureProtocol = true
            }
        }
    }
}
```

