---
title: 음성 SDK 로깅 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK(C++, C#, 파이썬, 목표-C, 자바)에서 로깅을 활성화하는 방법에 대해 알아봅니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805793"
---
# <a name="enable-logging-in-the-speech-sdk"></a>음성 SDK에서 로그인 사용

파일에 로깅하는 것은 음성 SDK의 선택적 기능입니다. 개발 로깅 중에 Speech SDK의 핵심 구성 요소에서 추가 정보 및 진단을 제공합니다. 음성 구성 개체의 속성을 `Speech_LogFilename` 로그 파일의 위치와 이름으로 설정하여 활성화할 수 있습니다. 로깅은 해당 구성에서 인식기가 만들어지고 나중에 비활성화할 수 없는 경우 전역적으로 활성화됩니다. 실행 중인 로깅 세션 중에는 로그 파일의 이름을 변경할 수 없습니다.

> [!NOTE]
> 로깅은 자바 스크립트를 제외하고, 모든 지원되는 음성 SDK 프로그래밍 언어에서 음성 SDK 버전 1.4.0 부터 사용할 수 있습니다.

## <a name="sample"></a>예제

로그 파일 이름은 구성 개체에 지정됩니다. 예제로 `SpeechConfig` 수행 하 고 라는 `config`인스턴스를 만든 가정:

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

구성 개체에서 인식자를 만들 수 있습니다. 이렇게 하면 모든 인식기에 대해 로깅할 수 있습니다.

> [!NOTE]
> 구성 개체에서 `SpeechSynthesizer` a를 만들면 로깅을 사용할 수 없습니다. 하지만 로깅을 사용하도록 설정하면 `SpeechSynthesizer`에서 진단도 받게 됩니다.

## <a name="create-a-log-file-on-different-platforms"></a>다른 플랫폼에서 로그 파일 만들기

Windows 또는 Linux의 경우 로그 파일은 사용자가 쓰기 권한이 있는 경로에 있을 수 있습니다. 다른 운영 체제의 파일 시스템 위치에 대한 쓰기 권한은 기본적으로 제한되거나 제한될 수 있습니다.

### <a name="universal-windows-platform-uwp"></a>UWP(유니버설 Windows 플랫폼)

UWP 응용 프로그램은 응용 프로그램 데이터 위치 중 하나(로컬, 로밍 또는 임시) 중 하나에 로그 파일을 배치해야 합니다. 로컬 응용 프로그램 폴더에서 로그 파일을 만들 수 있습니다.

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

UWP 응용 프로그램에 대한 파일 액세스 권한에 대한 자세한 내용은 [여기에서](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)확인할 수 있습니다.

### <a name="android"></a>Android

로그 파일을 내부 저장소, 외부 저장소 또는 캐시 디렉터리에 저장할 수 있습니다. 내부 저장소 또는 캐시 디렉터리에서 만든 파일은 응용 프로그램에 비공개입니다. 외부 저장소에 로그 파일을 만드는 것이 좋습니다.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

위의 코드는 로그 파일을 응용 프로그램 별 디렉터리 루트의 외부 저장소에 저장합니다. 사용자는 파일 관리자(일반적으로)를 통해 파일에 `Android/data/ApplicationName/logfile.txt`액세스할 수 있습니다. 응용 프로그램이 제거되면 파일이 삭제됩니다.

또한 매니페스트 `WRITE_EXTERNAL_STORAGE` 파일에서 권한을 요청해야 합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

안드로이드 응용 프로그램에 대한 데이터 및 파일 저장에 대한 자세한 내용은 [여기에서](https://developer.android.com/guide/topics/data/data-storage.html)찾을 수 있습니다.

#### <a name="ios"></a>iOS

응용 프로그램 샌드박스 내부의 디렉터리만 액세스할 수 있습니다. 파일은 문서, 라이브러리 및 임시 디렉터리에서 만들 수 있습니다. 문서 디렉토리의 파일을 사용자가 사용할 수 있습니다. 다음 코드 조각은 응용 프로그램 문서 디렉터리에서 로그 파일 만들기를 보여 주며 다음과 같은 코드 조각입니다.

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

생성된 파일에 액세스하려면 응용 프로그램의 `Info.plist` 속성 목록에 아래 속성을 추가합니다.

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

iOS 파일 시스템에 대한 자세한 내용은 [여기에서](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)
