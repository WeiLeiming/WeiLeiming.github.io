---
title: 关于iOS文件系统
date: 2017-05-22 11:42:27
categories: iOS
tags:
	- iOS
	- Translate
typora-copy-images-to: ipic
---

# 文件系统基础

iOS中的文件系统处理数据文件的持久存储，应用程序和与操作系统本身相关的文件，文件系统是所有进程使用的基本资源之一。iOS中的文件系统主要格式是HFS Plus，为了使系统简单，iOS设备的用户不能直接访问文件系统，应用程序也应遵循此惯例。

## iOS标准目录

为了安全起见，iOS应用程序与文件系统的交互仅限于应用程序沙盒目录中的目录。在安装新应用程序期间，安装程序会在沙盒目录中为应用程序创建多个容器目录。bundle容器目录包含应用程序的bundle，而数据容器目录保存应用程序和用户的数据。数据容器目录进一步分为多个子目录，该应用程序可用于对其数据进行排序和整理。应用程序还可以在运行时请求访问其他容器目录，例如iCloud容器。

<!-- more -->

这些容器目录构成了应用程序对文件系统的主视图。以下是App的沙盒目录，来自苹果官方文档。

<img src="https://ws1.sinaimg.cn/large/006tKfTcly1ffu9f2migcj30la0n6jrr.jpg" style="zoom:50%" />

通常禁止App在其容器目录之外访问或创建文件。这个规则的一个例外是当一个App使用公共系统接口来访问诸如用户的联系人或音乐之类的东西。在这些情况下，使用系统框架帮助App来处理读取或修改相应数据存储所需的任何相关文件操作。

| *目录*            | *描述*                                     |
| :-------------- | :--------------------------------------- |
| AppName.app     | 这是App的bundle。此目录包含App及其所有资源。该目录访问权限为只读，为防止篡改，bundle目录在安装时签名，写入此目录会更改签名并阻止应用启动。该目录的内容不会被iTunes或iCloud备份。但是，从App Store购买的任何App，iTunes会执行初始同步。 |
| Documents/      | 使用此目录来存储用户生成的内容。该目录的内容可以通过文件共享提供给用户，因此，该目录只能包含希望向用户公开的文件。此目录的内容由iTunes和iCloud备份。 |
| Documents/Inbox | 使用此目录访问App被外部实体打开的文件。具体来说，邮件程序将与App相关联的电子邮件附件放在此目录。文档交互控制器也可以在其中放置文件。App可以读取和删除此目录中的文件，但无法创建新文件或写入现有文件。如果用户尝试编辑此目录中的文件，则在进行任何更改之前，App必须静静地将其移除目录。该目录的内容由iTunes和iCloud备份。 |
| Library/        | 这是一个存放除了用户数据文件之外文件的顶层目录。通常将文件放在几个标准子目录之一中。iOS应用程序通常使用 `Application Support`和 `Caches`子目录，但是可以创建自定义子目录。使用`Library`子目录存储不想向用户公开的文件，App不应将这些目录用于用户数据文件。Library目录(除子目录`Caches`外)的内容由iTunes和iCloud备份。 |
| tmp/            | 使用此目录存放不需要在App启动之间保留的临时文件。App应该在不需要时从该目录中删除文件，但是，当App未运行时系统可能会清除此目录。该目录不会被iTunes或iCloud备份。 |

## 文件存放位置选择

为了防止iOS设备上的同步和备份花费较长时间，要选择文件放置的位置。存储了大文件的App可能会减慢备份到iTunes或iCloud的过程。这些应用程序还会消耗大量用户的可用存储空间，这可能会使用户删除应用程序或禁用该应用备份数据到iCloud。考虑到这一点，应按照下列准则存储应用程序数据：

- 将用户数据放入`Documents/`。用户数据通常包含想要向用户公开的文件—可能希望用户创建，导入，删除或编辑的所有文件。对于绘图应用程序，用户数据包括用户可能创建的任何图形文件。对于文本编辑器，包括文本文件。视频和音频应用程序甚至可以包括用户下载以便稍后观看或收听的文件。
- 将App创建的支持文件放在`Library/Application support/`目录中。通常，此目录包括App用于运行但应保持隐藏的用户的文件。该目录还可以包括数据文件，配置文件，模板和从应用程序包中加载的资源的修改版本。
- 在`Documents`和`Application Support/`中的文件是默认备份。可以使用`-[NSURL setResourceValue:forKey:error:]`方法传入`NSURLIsExcludedFromBackupKey`键值调用备份中的文件。任何可以重新创建或下载的文件必须从备份中排除。这对于大型媒体文件尤为重要。如果App下载视频或音频文件，确保它们未包含在备份中。
- 将临时数据放在`tmp/`目录中。临时数据包括任何不需要长时间保留的数据。在完成这些文件时删除这些文件，以便它们不会继续占用用户设备上的空间。当应用程序未运行时，系统会定期清除这些文件。因此，应用程序终止后，无法依赖这些文件。
- 将数据缓存文件放在`Library/Caches/`目录中。缓存数据可用于需要比临时数据存储更持久的数据，但不能与支持文件一样长。一般来说，应用程序不需要缓存数据就能正常运行，但是可以使用缓存数据提高性能。高速缓存数据的示例包括(但不限于)数据库高速缓存文件和暂时的可下载内容。系统可能会删除`Caches/`目录以释放磁盘空间，因此App必须能够根据需要重新创建或下载这些文件。

# 访问文件和目录

在打开文件之前，首先需要在文件系统中找到文件。系统框架提供许多用于获取已知目录引用的例程，例如`Library`目录及其内容。还可以通过从已知目录名称构建URL或基于字符串的路径手动指定位置。

当知道文件位置时，就可以开始规划最佳的访问方式。根据文件类型，可能有几个选项。对于已知的文件类型，通常使用内置的系统例程来读取或写入文件内容，并提供可以使用的对象。对于自定义文件类型，可能需要自己读取原始文件数据。

## 选择正确的方式访问文件

虽然可以打开任何文件并将其内容读取为字节流，但这样做并不总是正确的选择。iOS提供内置的支持，可以轻松打开许多类型的标准文件格式（如文本文件，图像，声音和属性列表）。对于这些标准文件格式，请使用更高级别的选项来读取和写入文件内容。

以下列出系统支持的常用文件类型。

| *文件类型*  | *例子*                                     | *描述*                                     |
| ------- | ---------------------------------------- | ---------------------------------------- |
| 资源文件    | Nib文件 <br>图像文件<br>声音文件<br>字符串文件<br>本地化资源 | App使用资源文件来存储独立于使用它的代码的数据。资源文件通常用于存储可本地化的内容，如字符串和图像。从资源文件读取数据的过程取决于资源的类型。[Resource Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/LoadingResources/Introduction/Introduction.html#//apple_ref/doc/uid/10000051i) |
| 文本文件    | 纯文本文件<br>UTF-8格式文件<br>UTF-16格式文件         | 文本文件是ASCII和Unicode字符的非结构化序列。通常将文本文件的内容加载到NSString对象中，但也可以读取和写入文本文件作为原始字符流。[String Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Strings/introStrings.html#//apple_ref/doc/uid/10000035i) |
| 结构化数据文件 | XML文件<br>属性列表文件<br>偏好文件                  | 结构化数据文件通常由使用一组特殊字符排列的字符串数据组成。[Event-Driven XML Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/XMLParsing/XMLParsing.html#//apple_ref/doc/uid/10000186i) |
| 归档文件    | 使用键控的归档对象创建的二进制文件                        | 归档是用于存储应用程序运行时对象持久版本的文件格式。归档对象是将对象的状态编码为可以一次写入磁盘的字节流。解档反转过程，使用字节流重新创建对象并将其恢复到之前的状态。归档通常是为文档或其他数据文件实施自定义二进制文件格式的便捷方式。[Archives and Serializations Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i) |
| 文件包     | 自定义文档格式                                  | 一个文件包是一个包含任意数量自定义数据文件的目录，但是它呈现给用户的就像它是单个文件一样。应用程序可以使用文件包来实现包含多个不同文件或不同类型文件混合的复杂文件格式。例如，如果文件格式包含二进制数据文件和一个或多个图像、视频或音频文件，则可以使用文件包。[Using FileWrappers as File Containers](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileWrappers/FileWrappers.html#//apple_ref/doc/uid/TP40010672-CH13-SW2) |
| Bundles | 应用<br>插件<br>框架                           | Bundles提供了一个结构化的环境用于存储代码和该代码使用的资源。大多数情况下，不能使用Bundle本身，而是使用其中的内容。但是可以根据需要找到Bundle并获取相关信息。[Bundle Programming Guide](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFBundles/Introduction/Introduction.html#//apple_ref/doc/uid/10000123i) |
| 代码文件    | 二进制代码资源<br>动态共享库                         | 使用插件和共享库的应用程序需要能够加载该项目的关联代码才能使用其功能。[Code Loading Programming Topics](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/LoadingCode/LoadingCode.html#//apple_ref/doc/uid/10000052i) |

在标准文件格式不足的情况下，可以随时创建自己的自定义文件格式。读取和写入自定义文件的内容时，可以将数据作为字节流读取和写入，并将这些字节应用于应用程序的文件相关数据结构。可以完全控制如何读取和写入字节以及如何管理与文件相关的数据结构。

## 指定文件或目录的路径

指定文件或目录位置的首选是使用`NSURL`类。

# 参考资料

[File System Programming Guide](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010672)

