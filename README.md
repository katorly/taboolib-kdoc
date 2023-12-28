# 常见疑问 & 构建指南
> [!Warning]
> 本 README 写于 2023-04-05，部分内容可能不准确或已经过期。  

## 可能的疑问
### 为什么要提供文档？直接在IDEA里面F12不行么？
Taboolib 的作者自从 6.0 版本以来，就没有再在[原本用于提供 Javadoc 的地方](https://jd.tabooproject.org)提供开发文档了。  
可以，但我习惯翻阅网页版文档。  
### 什么是Dokka？
[Dokka](https://github.com/Kotlin/dokka) 是 Kotlin 文档生成工具，使用它，我们可以方便快捷地为 Kotlin 项目（Java 也可以）生成类似于 Javadoc 和其它格式的文档。  
### 为什么不把全部子项目都放入文档的侧栏导航中，而是另写一个索引页面？
在 Taboolib 项目中使用 Dokka 进行 `dokkaHtmlMultiModule` 任务构建 Kotlin 文档时，会出现某些子项目的文档未生成的情况，又不能单独添加未生成的子项目文档，因此只能额外写一个索引页面。  
这也是下面 构建指南 中不使用 `dokkaHtmlMultiModule` 而选择在每个子项目中执行 `dokkaHtml` 的原因。  
### 文档不是最新版本，为什么不更新/如何更新？
Taboolib 的小版本变动不大，而由于项目过大，生成一次全套的 Kotlin 文档需要耗费大量时间（依赖库就有几个G）。如果使用 Github Actions 进行构建，任务会因为没有 Gradle 缓存而编译超过 6 小时，最终被 Github 强行停止（附使用的工作流 [`generate-docs.yml`](/generate-docs.yml) 文件）。因此，当 Taboolib 出现大版本更新或大幅度变动时，本仓库会对相关 Kotlin 文档进行更新。如果您需要最新版本，可以参照下面的 构建指南 自行构建（并**欢迎您提交 Pull Request 为所有人更新文档**）。  
### 文档侧栏一片空白？搜索用不了？
正常情况下，本文档使用 Github Pages 托管，每个子项目文档都会有侧栏导航，搜索也都可用。但如果出现这种问题，那么很遗憾，您只能自行下载文档到本地并用 Intellij IDEA 作为网页服务打开（不能使用静态网页打开），这是 Dokka 的“特性”。  
### 文档侧栏中的图标分别是什么意思？
它们代表类型，如果不知道是什么意思的话可以在[这里](/icons)看到大部分图标及其含义。  
### 文档没看明白，xxx是什么意思/怎么用？
本仓库仅提供 Taboolib 的 Kotlin 文档服务，但您可以点击文档索引页面右上角的导航进入到 Taboolib 的官方文档中学习。若仍有疑问，请[联系原作者](https://github.com/TabooLib)。  
### xxx已经在 Taboolib 中过期/被移除，为什么还有它的文档？
为了方便还在使用 Taboolib 框架的过期/被移除版本的开发者进行查阅，我们保留了原来的文档。这些文档可能在将来某一次更新中被移除，但至少现在不会。  
### 我制作了 Taboolib 的扩展xxx，如何添加我的文档进来？
非常抱歉，本仓库仅针对 Taboolib 中有效的子项目（即 Taboolib 总项目在 `settings.gradle.kts` 中定义了的子项目）提供 Kotlin 文档，若您编写了相关的扩展，可以在您的仓库中自行提供扩展文档，这样更方便管理哟！（如果不会生成 Kotlin 文档，下面有简短的教程）  
### Taboolib 是什么？为什么要用 Taboolib？xxx不香么？
爬。  


## 构建 Dokka 文档
### 第1步
克隆 [Taboolib](https://github.com/TabooLib/taboolib) 的主分支至本地，并使用 IDE 打开。推荐使用 [Intellij IDEA](https://www.jetbrains.com/idea)。  
强烈建议不要将 Gradle Home 定位在系统盘，因为 Taboolib 的依赖库有几个G。  

对于大陆用户，如果下载依赖太慢，可以考虑使用阿里云镜像
```kotlin
maven("https://maven.aliyun.com/repository/public/")
```
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
在 `subprojects` 中：
```kotlin
apply(plugin = "org.jetbrains.dokka")
```
在文件末尾：
```kotlin
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
如果这一步没看懂，这里有一份修改过的 Taboolib 的 [`build.gradle.kts`](/build.gradle.kts) 文件供参考。  
### 第3步
如果电脑性能顶不住，在每个有效子项目下执行 `dokkaHtml`，不要用`dokkaHtmlMultiModule`。例如：在 `module子项目` 的 `module-effect子项目` 中执行：`./gradlew :module:module-effect dokkaHtml`。  

如果你比较勇，那么直接执行`./gradlew dokkaHtmlMultiModule`。如果要收敛点，则可以选择在每个有效项目分类下执行`dokkaHtmlMultiModule`，例如在`module分类`中执行：`./gradlew :module:dokkaHtmlMultiModule`。  

### 第4步
将每个项目的 `/build/dokka/html` 目录下的文件集中，并像本仓库一样依据 `module`, `platform`, `expansion` 等进行分类。  

