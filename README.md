# 常见疑问 & 构建指南
> **Warning**
> 本 README 写于 2023-04-05，有些内容可能不准确或已经过期。

## 可能的疑问
### 为什么要提供文档？直接在IDEA里面F12不行么？
Taboolib 的作者自从 6.0 版本以来，就没有再在[原本用于提供 Javadoc 的地方](https://jd.tabooproject.org)提供开发文档。  
可以，但我习惯翻阅网页版文档。  
### 什么是Dokka？
[Dokka](https://github.com/Kotlin/dokka) 是 Kotlin 文档生成工具，使用它，我们可以为 Kotlin 项目（Java 也可以）方便快捷地生成类似于 Javadoc 和其它格式的文档。  
### 为什么不把全部子项目都放入文档的侧栏导航中，而是另写一个索引页面？
在 Taboolib 项目中使用 Dokka 进行 `dokkaHtmlMultiModule` 任务构建 Kotlin 文档时，无法生成最后汇总的那个 Kotlin 文档页面（报错，显示没有仓库），因此只能额外写一个索引页面。  
这也是下面 构建指南 中不使用 `dokkaHtmlMultiModule` 而选择在每个子项目中执行 `dokkaHtml` 的原因。  
### 文档不是最新版本，为什么不更新/如何更新？
Taboolib 的小版本变动不大，而由于项目过大，生成一次全套的 Kotlin 文档需要耗费大量时间（依赖库就有几个G）。因此，当 Taboolib 出现大版本更新或大幅度变动时，本仓库会对相关 Kotlin 文档进行更新。如果您需要最新版本，可以参照下面的 构建指南 自行构建（并欢迎您提交 Pull Request 为所有人更新文档）。  
### 文档侧栏一片空白？
正常情况下，本文档使用 Github Pages 托管，每个子项目文档不会出现没有侧栏导航的问题。如果出现这种问题，那么很遗憾，您只能自行下载文档到本地并用 Intellij IDEA 作为网页服务打开（不能使用静态网页打开），这是 Dokka 的“特性”。  
### 文档没看明白，xxx是什么意思/怎么用？
本仓库仅提供 Taboolib 的 Kotlin 文档服务，但您可以点击文档索引页面右上角的导航进入到 Taboolib 的官方文档中学习。若仍有疑问，请[联系原作者](https://github.com/TabooLib)。

## 构建 Dokka 文档
### 第1步
克隆 [Taboolib](https://github.com/TabooLib/taboolib) 的主分支至本地，并使用 IDE 打开。推荐使用 [Intellij IDEA](https://www.jetbrains.com/idea)。  
建议不要将 Gradle 定位在系统盘，因为 Taboolib 的依赖库有几个G。  
### 第2步
在项目根目录里的 `build.gradle.kts` 文件中添加以下内容：
```kotlin
import org.jetbrains.dokka.base.DokkaBase
import org.jetbrains.dokka.gradle.DokkaTask
import org.jetbrains.dokka.base.DokkaBaseConfiguration
```
在 `plugins` 中：
```kotlin
id("org.jetbrains.dokka") version "1.8.10"
```
在 `subprojects` 中：`
```kotlin
apply(plugin = "org.jetbrains.dokka")

buildscript {
    dependencies {
        classpath("org.jetbrains.dokka:dokka-base:1.8.10")
    }
}

tasks.withType<DokkaTask>().configureEach {
    pluginConfiguration<DokkaBase, DokkaBaseConfiguration> {
        separateInheritedMembers = false
        mergeImplicitExpectActualDeclarations = false
    }
}
```
如果这一步没看懂，这里有一份 [`build.gradle.kts`](/build.gradle.kts) 供参考。  
### 第3步
在每个有效子项目下执行 `dokkaHtml`，不要用`dokkaHtmlMultiModule`。  

例如：在 `module子项目` 的 `module-effect子项目` 中执行：`./gradlew :module:module-effect dokkaHtml`。  

### 第4步
将每个项目的 `/build/dokka/html` 目录下的文件集中，并依据 `module`, `platform`, `expansion` 进行分类。  

