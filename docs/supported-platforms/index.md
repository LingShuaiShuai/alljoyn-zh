# 支持的平台

AllJoyn标准核心构建和运行在如下的平台上。由于每个平台的测试类型和深度不同,因此，请链接发布详细的测试信息。

## 15.09 发布

#### 平台

| 平台         | Core | 基础服务（规划中）|平台版本           | 工具链/ IDE           |
|--------------|:----:|:-----------------:|-------------------|-----------------------|
| Android      |  X   |         X         | JB, KK, LP        | Android SDK, NDK r10e |


#### 语言绑定

下表列出了哪些语言绑定都支持哪些平台上的核心和基础服务。

| 平台         | C++         | C    | Java        | Objective-C |
| -------------- | ------------- | ------ | ------------- | ------------- |
| Android      | Core        | Core | Core        |             |

15.09 注释:
* 请参见[核心15.09版本评审](https://wiki.allseenalliance.org/core/core_15.09_release_review) 细节测试在各种平台上的深度。
* Sun Java 7需要Java绑定。

---

## 15.04 发布

#### 平台

| 平台         | Core | 基础服务（规划中）| 平台版本          | 工具链/   IDE        |
|--------------|:----:|:-----------------:|-------------------|----------------------|
| Windows      |  X   |         X         | Windows 7 to 10   | VS 2012, VS 2013     |
| Android      |  X   |         X         | JB, KK, LP        | Android SDK, NDK r9d |
| iOS          |  X   |         X         | iOS 8.1           | XCode 6.1            |
| OS X         |  X   |                   | OS X 10.9         | XCode 6.1            |
| Linux Ubuntu |  X   |         X         | Ubuntu 14.04      |                      |
| Open WRT     |  X   |         X         | BB, CC            | <br>                 |


#### 语言绑定

下表列出了哪些语言绑定都支持哪些平台上的核心和基础服务

| 平台         | C++         | C    | Java        | Objective-C |
|--------------|-------------|------|-------------|-------------|
| Windows      | Core, Base  | Core | Core        |             |
| Android      | Core, Base  | Core | Core, Base  |             |
| iOS          | Core        |      |             | Core, Base  |
| OS X         | Core        |      |             |             |
| Linux Ubuntu | Core, Base  | Core | Core, Base  |             |
| Open WRT     | Core, Base  | Core |             | <br>        |

15.04 注释:
* 请参见[核心15.04版本评审](https://wiki.allseenalliance.org/core/core_15.04_release_review) 细节测试在各种平台上的深度。
* Sun Java 7需要Java绑定。

---
## 14.12 发布

#### 平台

| 平台         | Core | 基础服务      | 平台版本          | 工具链/IDE        |
|--------------|:----:|:-------------:|-------------------|----------------------|
| Windows      |  X   |      X        | Win7, Win8        | VS 2013              |
| Android      |  X   |      X        | ICS, JB, KK       | Android SDK, NDK r9d |
| iOS          |  X   |      X        | iOS 7, iOS 7.1    | XCode 6.1            |
| OS X         |  X   |               | OS X 10.9         | XCode 6.1            |
| Linux Ubuntu |  X   |      X        | Ubuntu 14.04      |                      |
| Open WRT     |  X   |      X        | BB, CC            | <br>                 |

#### 语言绑定

下表列出了哪些语言绑定都支持哪些平台上的核心和基础服务

| Platform     | C++         | C    | Java        | Objective-C |
|--------------|-------------|------|-------------|-------------|
| Windows      | Core, Base  | Core | Core        |             |
| Android      | Core, Base  | Core | Core, Base  |             |
| iOS          | Core        |      |             | Core, Base  |
| OS X         | Core        |      |             |             |
| Linux Ubuntu | Core, Base  | Core | Core, Base  |             |
| Open WRT     | Core, Base  | Core |             | <br>        |

14.12 注释:
* 请参见 [核心14.12版本评审](https://wiki.allseenalliance.org/core/core_14.12_release_review) and [基本服务14.12版本审核](https://wiki.allseenalliance.org/baseservices/base_services_14.12_release_review) 对细节的深度在各种平台上的测试。
* Sun Java 7需要Java绑定。

---
## 14.06 发布

#### 平台

| 平台         | Core | 基础服务      | 平台版本          | 工具链/IDE       |
|--------------|:----:|:-------------:|-------------------|---------------------|
| Windows      |  X   |               | Win7, Win8        | VS 2012             |
| Android      |  X   |      X        | GB, ICS, JB, KK   | Android SDK, NDK r9 |
| iOS          |  X   |      X        | iOS 7, iOS 7.1    | XCode 5.1           |
| OS X         |  X   |               | OS X 10.9         | XCode 5.1           |
| Linux Ubuntu |  X   |      X        | Ubuntu 12.04      |                     |
| Open WRT     |  X   |      X        | AA, BB            | <br>                |

#### 语言绑定

下表列出了哪些语言绑定都支持哪些平台上的核心和基础服务。

| 平台         | C++         | C    | C# Unity | Java        | Objective-C |
|--------------|-------------|------|----------|-------------|-------------|
| Windows      | Core        | Core | Core     | Core        |             |
| Android      | Core, Base  | Core | Core     | Core, Base  |             |
| iOS          | Core        |      |          |             | Core, Base  |
| OS X         | Core        |      |          |             |             |
| Linux Ubuntu | Core, Base  | Core |          | Core, Base  |             |
| Open WRT     | Core, Base  | Core |          |             | <br>        |

14.06 注释:
* 请参见[核心14.06版本评审](https://wiki.allseenalliance.org/core/core_14.06_release_review) and 基本服务14.06发布评论(https://wiki.allseenalliance.org/baseservices/base_services_14.06_release_review) 对细节在各种平台上的深度测试。
* Sun Java 7需要Java绑定。
* Unity 4.x 版本 需要 Unity 绑定.
