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

在打开文件之前，首先需要在文件系统中找到文件。系统框架提供许多用于获取已知目录引用的方法，例如`Library`目录及其内容。还可以通过从已知目录名称构建URL或基于字符串的路径手动指定位置。

当知道文件位置时，就可以开始规划最佳的访问方式。根据文件类型，可能有几个选项。对于已知的文件类型，通常使用内置的系统方法来读取或写入文件内容，并提供可以使用的对象。对于自定义文件类型，可能需要自己读取原始文件数据。

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

### 指定文件或目录的路径

指定文件或目录位置的首选是使用`NSURL`类。虽然`NSString`类具有许多与路径创建相关的方法，但URL提供了一种更强大的方法来定位文件和目录。对于也可以使用网络资源的App，URL也意味着可以使用一种类型的对象来管理位于本地文件系统或网络服务器上的项目。除了`NSURL`，还可以使用`CFURLRef`不透明类型来操作路径作为URL，该类与`NSURL`是桥接的，这意味着可以用代码互相转换使用。

对于大多数URL，可以通过使用适当的`NSURL`方法将目录和文件名连接在一起直到具有项目路径来构建URL。以这种方式构建的URL被称为基于路径的URL，因为它存储着遍历目录层次结构以查找项目所需的名称。(还可以通过将目录和文件名连接在一起构建基于字符串的路径，结果以与`NSURL`类使用格式略有不同的格式存储)。除了基于路径的URL，还可以创建文件引用URL，它使用唯一的ID来标识文件或目录的位置。

所有以下条目都是对用户`Documents`目录中调用的`MyFile.txt`文件的有效引用：

**基于路径的URL:** `file://localhost/Users/steve/Documents/MyFile.txt`

**文件引用URL:** `file:///.file/id=6571367.2773272/`

**基于字符串的路径:** `/Users/steve/Documents/MyFile.txt`

可以使用`NSURL`方法创建URL对象，并在需要时将其转换为文件引用URL。基于路径的URL更容易操作，更容易调试，并且通常被类首选，如`NSFileManager`类。文件引用URL的优点是，在应用程序运行时，它们比基于路径的URL更强壮。如果用户在Finder中移动文件，则引用该文件的任何基于路径的URL立即变为无效，并且必须更新到新路径。但是，只要文件移动到同一磁盘上的另一个位置，其唯一的ID不更改，任何文件引用URL仍然有效。尽管在应用程序运行时它们可以安全使用，但如果系统重新启动，则文件引用URL在应用程序启动之间存储和重新使用是不安全的，因为文件的ID可能会更改。如果要在应用程序启动之间持久存储文件的位置，请按“使用书签查找文件”中所述创建一个书签。

当然，您仍然可能需要使用字符串来引用文件。幸运的是，`NSURL`类提供了将基于路径的URL转换为`NSString`对象的方法。当向用户呈现该路径或调用接收字符串而不是URL的系统方法时，可能会使用基于字符串的路径。使用`NSURL`类的`absoluteString`方法来完成`NSURL`对象和`NSString`对象之间的转换。

因为`NSURL`和`NSString`描述文件或目录的唯一位置，可以在实际的文件或目录存在之前创建它们。两个类都不会尝试验证指定的文件或目录的实际存在。事实上，必须创建一个不存在的文件或目录的路径，然后才能在磁盘上创建它。

如果有一个`NSURL`对象引用磁盘上的实际文件或目录，并且要获取用户所在卷的可见名称，则可以使用`getResourceValue:forKey:error:`方法两步获取：

- 使用`NSURLVolumeURLKey`常量从资源URL获取卷URL。
- 使用`NSURLLocalizedNameKey`常量从卷URL获取用户可见的卷名称。

使用`NSURLLocalizedNameKey`常数而不是`NSURLNameKey`常数。使用`NSURLNameKey`返回文件系统中卷的名称，可能与用户可见名称不同。以下演示了当前用户主目录的这个过程。

```objective-c
NSURL *url = [NSURL fileURLWithPath:NSHomeDirectory()];
NSURL *volumeURL = nil;
NSString *volumeName = nil;
if ([url getResourceValue:&volumeURL forKey:NSURLVolumeURLKey error:nil]) {
    NSLog(@"卷URL为: %@", volumeURL);
    if ([volumeURL getResourceValue:&volumeName forKey:NSURLLocalizedNameKey error:nil]) {
        NSLog(@"卷名称为: %@", volumeName);
    }
}
```

## 在文件系统中查找项目

在可以访问文件或目录之前，需要知道其位置。找到文件和目录有以下几种方法：

- 自己找文件。
- 请用户指定一个位置。
- 在沙盒和非沙盒应用程序中的标准系统目录中查找文件。
- 使用书签。

iOS的文件系统对应该放置文件的位置制定了具体的指导方针，因此App创建或使用的大部分项目应存储在众所周知的位置。系统提供用于定位这些众所周知位置的接口，App可以使用这些接口来定位项目并构建特定文件的路径。

### 在App Bundle中查找

需要将资源文件定位在其bundle目录(或其他已知bundle内)中的应用程序必须使用`NSBundle`对象执行此操作。Bundles通过特定方式组织这些文件，消除App记住单个文件位置的需要。使用`NSBundle`类的方法来根据需要定位应用程序的资源。这种技术的优点在于，可以通过重新整理bundle的内容，而无需重写代码来访问它。Bundles还可以利用当前用户的语言设置来查找资源文件的适当本地化版本。

以下代码显示了如何检索位于应用程序主Bundle中名为`MyImage.png`图像的URL对象。此代码仅确定文件的位置，它不会打开文件。可以将返回的URL传递给`NSImage`类的方法，从磁盘加载使用它。

```objective-c
NSURL *url = [[NSBundle mainBundle] URLForResource:@"MyImage" withExtension:@"png"];
```

### 在标准目录中查找

当需要在其中一个标准目录中找到文件时，请首先使用系统框架找到目录，然后使用生成的URL构建文件的路径。`Foundation`框架包括几个用于定位标准系统目录的选项。通过使用这些方法，无论应用程序是否被沙盒化，路径都将是正确的：

- `NSFileManager`类的`URLsForDirectory:inDomains:`方法返回打包了目录位置的`NSURL`对象。要搜索的目录是一个`NSSearchPathDirectory`常量。这些常量为用户的主目录以及大多数标准目录提供URL。
- `NSSearchPathForDirectoriesInDomains`函数的功能类似于`URLsForDirectory:inDomains:`方法，但返回目录的位置是一个基于字符串的路径。使用`URLsForDirectory:inDomains:`方法替代。
- `NSHomeDirectory`函数返回用户或应用程序的主目录路径。(返回的主目录取决于平台以及应用程序是否在沙盒中)。当应用程序被沙盒化时，主目录指向应用程序的沙盒，否则它指向文件系统上的用户主目录。如果将文件构建到用户主目录的子目录中，请考虑使用`URLsForDirectory:inDomains:`方法替代。

可以使用从上述方法中接收的URL或基于路径的字符串来构建包含所需文件位置的新对象。`NSURL`和`NSString`类都提供了与路径相关的方法用于添加和删除路径组件并对路径进行一般性更改。下面显示了一个搜索标准`Application Support`目录并为包含应用程序数据文件的目录创建一个新URL的示例。

```objective-c
- (NSURL *)applicationDataDirectory {
    NSFileManager *sharedFM = [NSFileManager defaultManager];
    NSArray *possibleURLs = [sharedFM URLsForDirectory:NSApplicationSupportDirectory
                                             inDomains:NSUserDomainMask];
    NSURL *appSupportDir = nil;
    NSURL *appDirectory = nil;
    
    if ([possibleURLs count] >= 1) {
        // Use the first directory (if multiple are returned)
        appSupportDir = [possibleURLs objectAtIndex:0];
    }
    
    // If a valid app support directory exists, add the
    // app's bundle ID to it to specify the final directory.
    if (appSupportDir) {
        NSString *appBundleID = [[NSBundle mainBundle] bundleIdentifier];
        appDirectory = [appSupportDir URLByAppendingPathComponent:appBundleID];
    }
    
    return appDirectory;
}
```

### 使用书签查找文件

如果想要永久保存文件的位置，使用`NSURL`的书签功能。书签是不透明的数据结构，封装在一个描述文件位置的`NSData`对象中。尽管路径和文件引用URL在应用程序启动之间可能会很脆弱，但是通常也可以使用书签来重新创建文件的URL，即使在文件被移动或重命名的情况下也是如此。

要为现有URL创建一个书签，使用`NSURL`的`bookmarkDataWithOptions:includingResourceValuesForKeys:relativeToURL:error:`方法。指定`NSURLBookmarkCreationSuitableForBookmarkFile`选项创建一个适合保存到磁盘的`NSData`对象。以下是一个使用此方法创建书签数据对象的简单示例实现。

```objective-c
- (NSData *)bookmarkForURL:(NSURL *)url {
    NSError *theError = nil;
    NSData *bookmark = [url bookmarkDataWithOptions:NSURLBookmarkCreationSuitableForBookmarkFile
                     includingResourceValuesForKeys:nil
                                      relativeToURL:nil
                                              error:&theError];
    if (theError || (bookmark == nil)) {
        // Handle any errors.
        return nil;
    }
    return bookmark;
}
```

如果使用`NSURL`的`writeBookmarkData:toURL:options:error:`方法将永久书签数据写入磁盘，系统在磁盘上创建的是一个别名文件。别名类似于符号链接，但实现方式不同。通常，当用户想要创建到系统其他位置的文件链接时，用户会从Finder创建别名。

要将书签数据对象转换回URL，使用`NSURL`的`URLByResolvingBookmarkData:options:relativeToURL:bookmarkDataIsStale:error:`方法。以下显示了将书签转换为URL的过程。

```objective-c
- (NSURL *)urlForBookmark:(NSData *)bookmark {
    BOOL bookmarkIsStale = NO;
    NSError *theError = nil;
    NSURL *bookmarkURL = [NSURL URLByResolvingBookmarkData:bookmark
                                                   options:NSURLBookmarkResolutionWithoutUI
                                             relativeToURL:nil
                                       bookmarkDataIsStale:&bookmarkIsStale
                                                     error:&theError];
    
    if (bookmarkIsStale || (theError != nil)) {
        // Handle any errors
        return nil;
    }
    return bookmarkURL;
}
```

## 枚举目录的内容

要发现给定目录的内容需要枚举该目录。Cocoa支持一次枚举一个目录一个文件或一次所有。无论选择哪个，谨慎枚举目录。每次枚举都可以触及大量的文件和子目录，这很快将变得代价昂贵。

### 一次枚举一个目录一个文件

当要搜索特定文件并在找到该文件时停止枚举，建议一次枚举一个目录一个文件。逐个文件枚举使用`NSDirectoryEnumerator`类，该类定义了检索项目的方法。因为`NSDirectoryEnumerator`本身是一个抽象类，所以不能直接创建它的实例。相反，可以使用`enumeratorAtURL:includingPropertiesForKeys:options:errorHandler:`或`NSFileManager`对象的`enumeratorAtPath:`方法来获取用于枚举的类的具体实例。

枚举器对象返回枚举目录中的所有文件和目录的路径。因为枚举是递归和跨设备边界的，所返回的文件和目录的数量可能超过起始目录中的内容。可以通过调用枚举器对象的`skipDescendents`方法来跳过不感兴趣目录的内容。枚举器对象不会解析符号链接或尝试遍历指向目录的符号链接。

以下代码显示了如何使用该 `enumeratorAtURL:includingPropertiesForKeys:options:errorHandler:`方法列出给定目录的所有用户可见子目录，注意它们是目录还是文件包。该`keys`数组告诉枚举器对象为每个项目预取和缓存信息。通过仅接触一次磁盘可提高获取此信息的效率。options参数指定枚举不应列出的文件包和隐藏文件的内容。错误处理程序是返回布尔值的block块对象。如果block返回`YES`，则该在错误后继续列举， 如果返回`NO`，枚举停止。

```objective-c
NSURL *directoryURL = <#An NSURL object that contains a reference to a directory#>;

NSArray *keys = [NSArray arrayWithObjects:
                 NSURLIsDirectoryKey, NSURLIsPackageKey, NSURLLocalizedNameKey, nil];

NSDirectoryEnumerator *enumerator = [[NSFileManager defaultManager]
                                     enumeratorAtURL:directoryURL
                                     includingPropertiesForKeys:keys
                                     options:(NSDirectoryEnumerationSkipsPackageDescendants |
                                              NSDirectoryEnumerationSkipsHiddenFiles)
                                     errorHandler:^(NSURL *url, NSError *error) {
                                         // Handle the error.
                                         // Return YES if the enumeration should continue after the error.
                                         return <#YES or NO#>;
                                     }];

for (NSURL *url in enumerator) {
    // Error checking is omitted for clarity.
    NSNumber *isDirectory = nil;
    [url getResourceValue:&isDirectory forKey:NSURLIsDirectoryKey error:NULL];
    
    if ([isDirectory boolValue]) {
        NSString *localizedName = nil;
        [url getResourceValue:&localizedName forKey:NSURLLocalizedNameKey error:NULL];
        
        NSNumber *isPackage = nil;
        [url getResourceValue:&isPackage forKey:NSURLIsPackageKey error:NULL];
        
        if ([isPackage boolValue]) {
            NSLog(@"Package at %@", localizedName);
        }
        else {
            NSLog(@"Directory at %@", localizedName);
        }
    }
}
```

通过使用`NSDirectoryEnumerator`声明的其他方法来确定枚举期间文件的属性(父目录和当前文件或目录)，并控制递归到子目录中。以下代码枚举了一个目录的内容，并列出了最近24小时内修改过的文件。然而，如果它遇到RTFD文件包，它会跳过对它们的递归。

```objective-c
NSString *directoryPath = <#Get a path to a directory#>;
NSDirectoryEnumerator *directoryEnumerator = [[NSFileManager defaultManager] enumeratorAtPath:directoryPath];
NSDate *yesterday = [NSDate dateWithTimeIntervalSinceNow:(-60 * 60 * 24)];
for (NSString *path in directoryEnumerator) {
    if ([[path pathExtension] isEqualToString:@"rtfd"]) {
        // Don't enumerate this directory.
        [directoryEnumerator skipDescendents];
    } else {
        NSDictionary *attributes = [directoryEnumerator fileAttributes];
        NSDate *lastModificationDate = [attributes objectForKey:NSFileModificationDate];
        if ([yesterday earlierDate:lastModificationDate] == yesterday) {
            NSLog(@"%@ was modified within the last 24 hours", path);
        }
    }
}
```

### 在单个批处理中获取目录的内容

如果想要查看目录中的每一项，可以在方便的情况下检索项目的快照并对其进行迭代。在批处理操作中检索目录的内容不是枚举目录的最有效方法，因为文件管理器必须每次都遍历整个目录内容。但是，如果计划查看所有项目，那么检索项目是一种简单的方式。

使用`NSFileManager`有以下两种选择方式对目录进行批枚举：

- 执行浅枚举，使用`contentsOfDirectoryAtURL:includingPropertiesForKeys:options:error:`或`contentsOfDirectoryAtPath:error:`方法。
- 执行深枚举并仅返回子目录，使用`subpathsOfDirectoryAtPath:error:`方法。

以下代码是一个使用`contentsOfDirectoryAtURL:includingPropertiesForKeys:options:error:`方法枚举目录内容的示例。使用URL的好处之一是还可以有效地检索每项的其他信息。此示例检索目录中每项的本地化名称，创建日期和类型信息，并将该信息存储在相应的`NSURL`对象中。当该方法返回时，可以继续迭代`array`变量中的项目，并使用它们进行所需操作。

```objective-c
NSURL *url = <#A URL for a directory#>;
NSError *error = nil;
NSArray *properties = [NSArray arrayWithObjects: NSURLLocalizedNameKey,
                       NSURLCreationDateKey, NSURLLocalizedTypeDescriptionKey, nil];
NSArray *array = [[NSFileManager defaultManager]
                  contentsOfDirectoryAtURL:url
                  includingPropertiesForKeys:properties
                  options:(NSDirectoryEnumerationSkipsHiddenFiles)
                  error:&error];
if (array == nil) {
    // Handle the error
}
```

## 确定存储App特定文件的位置

`Library`目录是应用程序创建和管理文件的指定存储库。App是沙盒目录，这些目录可能在不同位置。结果是始终使用`NSFileManager`的`URLsForDirectory:inDomains:`方法与`NSUserDomainMask`作为域来定位要存储这些数据的特定目录。

- 使用`Application Support`目录常量 `NSApplicationSupportDirectory`，将*<bundle_ID>*添加上：
  - 应用程序为用户创建和管理的资源和数据文件。使用此目录来存储应用程序状态信息，计算或下载的数据，甚至用户创建的数据。
  - 自动保存文件。
- 使用`Caches`目录常量`NSCachesDirectory`，添加*<bundle_ID>*目录缓存数据文件或任何文件，应用程序可以轻松重新创建。
- 使用`NSUserDefaults`类读取和写入偏好。该类自动将首选项写入适当的位置。
- 使用`NSFileManager`方法`URLsForDirectory:inDomains:`获取存储临时文件的目录。临时文件是打算立即使用某些正在进行操作的文件，但是稍后打算丢弃。一旦完成，删除临时文件。

如果在三天后不删除临时文件，系统可能会删除它们，无论是否使用它们。

# 管理文件和目录

涉及文件和目录的一些最基本操作是创建它们并将它们移动到文件系统。这些操作是应用程序如何构建其执行任务所需的文件系统结构。对于大多数操作，`NSFileManager`类应提供创建和操作文件所需的功能。在极少数情况下，不需要直接使用BSD级别的功能。

## 以编程方式创建新文件和目录

创建文件和目录是文件管理的基本部分。通常，创建自定义目录来组织代码创建的文件。例如，可以在应用程序`Application Support`目录中创建一些自定义目录，以存储应用程序管理的任何私有数据文件。有很多方法来创建文件。

### 创建目录

当要创建自定义目录时，可以使用以下`NSFileManager`方法。一个进程可以在任何具有这样做权限的地方创建目录，它始终包括当前主目录，并且还可以包括其他文件系统位置。可以通过构建一个路径来指定要创建的目录，并将`NSURL`或`NSString`对象传递给以下方法之一：

- `createDirectoryAtURL:withIntermediateDirectories:attributes:error:` 
- `createDirectoryAtPath:withIntermediateDirectories:attributes:error:`

以下代码显示了如何在`~/Library/Application Support`中为应用程序文件创建自定义目录。如果该目录不存在，则该方法将创建目录，并将目录的路径返回给调用代码。因为这种方法每次都触及文件系统，所以避免重复调用此方法来检索URL。可以调用它一次，然后缓存返回的URL。

```objective-c
- (NSURL *)applicationDirectory {
    NSString *bundleID = [[NSBundle mainBundle] bundleIdentifier];
    NSFileManager *fm = [NSFileManager defaultManager];
    NSURL *dirPath = nil;
    
    // Find the application support directory in the home directory.
    NSArray *appSupportDir = [fm URLsForDirectory:NSApplicationSupportDirectory
                                        inDomains:NSUserDomainMask];
    if ([appSupportDir count] > 0) {
        // Append the bundle ID to the URL for the
        // Application Support directory
        dirPath = [[appSupportDir objectAtIndex:0] URLByAppendingPathComponent:bundleID];
        // If the directory does not exist, this method creates it.
        // This method is only available in macOS 10.7 and iOS 5.0 or later.
        NSError *theError = nil;
        if (![fm createDirectoryAtURL:dirPath withIntermediateDirectories:YES
                           attributes:nil error:&theError]) {
            // Handle the error.
            return nil;
        }
    }
    return dirPath;
}
```

### 创建新文件

创建文件有两个部分：为文件系统中的文件创建一个记录，并用内容填充该文件。用于创建文件的所有高级接口同时执行两个任务，通常用`NSData`或`NSString`对象的内容填充文件，然后关闭该文件。还可以使用较低级别的函数来创建一个空文件，并获得一个文件描述符，然后可以使用该文件描述符来填充数据。可以用来创建文件的一些例程有：

- `createFileAtPath:contents:attributes:`（`NSFileManager`）
- `writeToURL:atomically:`（`NSData`）
- `writeToURL:atomically:`（`NSString`）
- `writeToURL:atomically:encoding:error:`（`NSString`）
- `writeToURL:atomically:` （各种集合类定义了用于编写属性列表的方法。）
- `open`功能与`O_CREAT`选项创建一个新的空文件

创建的任何文件都会继承与当前用户和进程关联的权限。当一次写入新文件的内容时，系统例程通常在将内容写入磁盘后关闭文件。如果例程返回文件描述符，则可以使用该描述符继续从文件读取和写入。

## 复制移动文件和目录

要复制文件系统周围的项目，`NSFileManager`类提供`copyItemAtURL:toURL:error:`和`copyItemAtPath:toPath:error:`方法。移动文件使用`moveItemAtURL:toURL:error:`或`moveItemAtPath:toPath:error:`方法。

以上方法一次移动或复制单个文件或目录。移动或复制目录时，目录及其所有内容将受到影响。移动和复制操作的语义与Finder中的相同。在同一卷上移动操作不会导致创建新版本的项目。卷之间的移动操作的行为与复制操作相同。当移动或复制项目时，当前进程必须具有读取项目的权限，并将其移动或复制到新位置。

移动和复制操作可能需要很长时间才能完成，并且`NSFileManager`类同步执行这些操作。因此，建议在并发调度队列中执行任何此类操作，而不是在应用程序的主线程上执行。以下代码显示了一个例子，通过异步创建虚构应用程序私有数据的备份。（为了示例的目的，私有数据位于`~/Library/Application Support/<bundleID>/Data`目录中，其中*bundleID*是应用程序的实际bundle标识符。）如果首次尝试复制目录失败，则此方法将检查是否存在先前的备份并将其删除。然后继续尝试，如果第二次失败，则中止。

```objective-c
- (void)backupMyApplicationData {
    // Get the application's main data directory
    NSArray *theDirs = [[NSFileManager defaultManager] URLsForDirectory:NSApplicationSupportDirectory
                                                              inDomains:NSUserDomainMask];
    if ([theDirs count] > 0) {
        // Build a path to ~/Library/Application Support/<bundle_ID>/Data
        // where <bundleID> is the actual bundle ID of the application.
        NSURL *appSupportDir = (NSURL *)[theDirs objectAtIndex:0];
        NSString *appBundleID = [[NSBundle mainBundle] bundleIdentifier];
        NSURL *appDataDir = [[appSupportDir URLByAppendingPathComponent:appBundleID]
                             URLByAppendingPathComponent:@"Data"];
        // Copy the data to ~/Library/Application Support/<bundle_ID>/Data.backup
        NSURL *backupDir = [appDataDir URLByAppendingPathExtension:@"backup"];
        // Perform the copy asynchronously.
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            // It's good habit to alloc/init the file manager for move/copy operations,
            // just in case you decide to add a delegate later.
            NSFileManager *theFM = [[NSFileManager alloc] init];
            NSError *anError;
            // Just try to copy the directory.
            if (![theFM copyItemAtURL:appDataDir toURL:backupDir error:&anError]) {
                // If an error occurs, it's probably because a previous backup directory
                // already exists.  Delete the old directory and try again.
                if ([theFM removeItemAtURL:backupDir error:&anError]) {
                    // If the operation failed again, abort for real.
                    if (![theFM copyItemAtURL:appDataDir toURL:backupDir error:&anError]) {
                        // Report the error....
                    }
                }
            }
        });
    }
}
```

## 删除文件和目录

要删除文件和目录，使用以下`NSFileManager`类的方法：

- `removeItemAtURL:error:`
- `removeItemAtPath:error:`

当使用这些方法删除文件时，需意识到是永久删除文件系统中的文件。这些方法不会将文件移动到稍后可以将其恢复的垃圾桶。

# 参考资料

[File System Programming Guide](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010672)

