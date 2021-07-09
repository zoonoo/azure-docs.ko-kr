---
title: Speech SDK 로깅 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK(C++, C#, Python, Objective-C, Java)에서 로깅을 사용하도록 설정하는 방법에 대해 알아봅니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 73e42ac1f076b67d31cbad0823ea63db40045c1e
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746036"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK에서 로깅 사용

파일에 로깅은 Speech SDK에 대한 선택적 기능입니다. 개발 중에 로깅은 Speech SDK의 핵심 구성 요소에서 추가 정보 및 진단을 제공합니다. 음성 구성 개체의 `Speech_LogFilename` 속성을 로그 파일의 위치와 이름으로 설정하여 사용하도록 설정할 수 있습니다. 로깅은 Speech SDK의 네이티브 라이브러리에서 정적 클래스에 의해 처리됩니다. Speech SDK 인식기 또는 신시사이저 인스턴스에 대한 로깅을 설정할 수 있습니다. 동일한 프로세스의 모든 인스턴스는 로그 항목을 동일한 로그 파일에 씁니다.

> [!NOTE]
> 로깅은 JavaScript를 제외하고 지원되는 모든 Speech SDK 프로그래밍 언어에서 Speech SDK 버전 1.4.0부터 사용할 수 있습니다.

## <a name="sample"></a>샘플

로그 파일 이름은 구성 개체에 지정됩니다. `SpeechConfig`를 예로 들어 `config`라는 인스턴스를 만들었다고 가정합니다.

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

config 개체에서 인식기를 만들 수 있습니다. 이렇게 하면 모든 인식기에서 로깅을 사용할 수 있습니다.

> [!NOTE]
> 구성 개체에서 `SpeechSynthesizer`를 만들면 로깅이 활성화되지 않습니다. 하지만 로깅을 사용하도록 설정하면 `SpeechSynthesizer`에서 진단도 받게 됩니다.

## <a name="create-a-log-file-on-different-platforms"></a>다른 플랫폼에서 로그 파일 만들기

Windows 또는 Linux의 경우 로그 파일은 사용자가 쓰기 권한이 있는 모든 경로에 있을 수 있습니다. 다른 운영 체제의 파일 시스템 위치에 대한 쓰기 권한은 기본적으로 제한될 수 있습니다.

### <a name="universal-windows-platform-uwp"></a>UWP(유니버설 Windows 플랫폼)

UWP 애플리케이션은 애플리케이션 데이터 위치(로컬, 로밍 또는 임시) 중 하나에 로그 파일을 배치해야 합니다. 로그 파일은 로컬 애플리케이션 폴더에 만들 수 있습니다.

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Unity UWP 애플리케이션 내에서 다음과 같이 애플리케이션 영구 데이터 경로 폴더를 사용하여 로그 파일을 만들 수 있습니다.

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
UWP 애플리케이션의 파일 액세스 권한에 대한 자세한 내용은 [파일 액세스 권한](/windows/uwp/files/file-access-permissions)을 참조하세요.

### <a name="android"></a>Android

로그 파일을 내부 스토리지, 외부 스토리지 또는 캐시 디렉터리에 저장할 수 있습니다. 내부 스토리지 또는 캐시 디렉터리에서 만든 파일은 애플리케이션 전용입니다. 외부 스토리지에 로그 파일을 만드는 것이 좋습니다.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

위의 코드는 애플리케이션별 디렉터리 루트의 외부 스토리지에 로그 파일을 저장합니다. 사용자는 파일 관리자를 통해 파일에 액세스할 수 있습니다(일반적으로 `Android/data/ApplicationName/logfile.txt`에서). 애플리케이션을 제거하면 파일이 삭제됩니다.

또한 매니페스트 파일에서 `WRITE_EXTERNAL_STORAGE` 권한을 요청해야 합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Unity Android 애플리케이션 내에서 다음과 같이 애플리케이션 영구 데이터 경로 폴더를 사용하여 로그 파일을 만들 수 있습니다.

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
또한 Android용 Unity 플레이어 설정에서 쓰기 권한을 "외부(SDCard)"로 설정해야 합니다. 로그는 AndroidStudio 디바이스 파일 탐색기와 같은 도구를 사용하여 얻을 수 있는 디렉터리에 기록됩니다. 정확한 디렉터리 경로는 Android 디바이스마다 다를 수 있으며 위치는 일반적으로 `sdcard/Android/data/your-app-packagename/files` 디렉터리입니다.

Android 애플리케이션용 데이터 및 파일 스토리지에 대한 자세한 내용은 [여기에서](https://developer.android.com/guide/topics/data/data-storage.html) 확인할 수 있습니다.

#### <a name="ios"></a>iOS

애플리케이션 샌드박스 내의 디렉터리만 액세스할 수 있습니다. 파일은 문서, 라이브러리 및 임시 디렉터리에서 만들 수 있습니다. 사용자가 문서 디렉터리에 있는 파일을 사용할 수 있습니다. 

iOS에서 Objective-C를 사용하는 경우 다음 코드 조각을 사용하여 애플리케이션 문서 디렉터리에 로그 파일을 만듭니다.

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

만든 파일에 액세스하려면 아래 속성을 애플리케이션의 `Info.plist` 속성 목록에 추가합니다.

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

iOS에서 Swift를 사용하는 경우 다음 코드 조각을 사용하여 로그를 사용하도록 설정하세요.
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

iOS 파일 시스템에 대한 자세한 내용은 [여기에서](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
