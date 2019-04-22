---
title: Speech SDK 로깅-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK에서 로깅을 사용 하도록 설정 합니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012504"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Speech SDK에서 로깅을 사용 하도록 설정

파일에 로깅은 Speech SDK에 대 한 선택적 기능입니다. 개발 중 로깅은 Speeck SDK의 핵심 구성 요소에서 진단 및 추가 정보를 제공합니다. 속성을 설정 하 여 활성화할 수 있습니다 `Speech_LogFilename` 음성 구성 개체를 로그 파일의 이름과 위치에 있습니다. 로깅과 인식기를 해당 구성에서 만들어지면 전역적으로 활성화할 수 있습니다. 나중에 비활성화할 수 없습니다. 로깅 세션을 실행 하는 동안 로그 파일의 이름을 변경할 수 없습니다.

> [!NOTE]
> 로깅은 프로그래밍 언어, JavaScript 제외 하 고 모든 지원 되는 음성 SDK에서 사용할 수 있습니다.

## <a name="sample"></a>샘플

로그 파일 이름 구성 개체에 지정 됩니다. 수행 된 `SpeechConfig` 호출을 예로 들어 인스턴스 만들었다고 가정 하 고 `config`:

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

구성 개체에서를 인식기를 만들 수 있습니다. 이렇게 하면 모든 인식기에 대 한 로깅을 활성화 됩니다.

> [!NOTE]
> 만드는 경우는 `SpeechSynthesizer` 구성 개체에서 사용 되지 것입니다 로깅. 도에서 진단 로깅을 통해 설정 된 경우 받습니다는 `SpeechSynthesizer`합니다.

## <a name="create-a-log-file-on-different-platforms"></a>다른 플랫폼에서 로그 파일 만들기

Windows 또는 Linux 로그 파일은 사용자에 대 한 쓰기 권한을 경로에서 수 있습니다. 기타 운영 체제에서 파일 시스템 위치에 대 한 쓰기 권한이 제한 하거나 기본적으로 제한할 수 있습니다.

### <a name="universal-windows-platform-uwp"></a>UWP(유니버설 Windows 플랫폼)

UWP 응용 프로그램 응용 프로그램 데이터 위치 (로컬, 로밍, 또는 임시) 중 하나에서 로그 파일 위치를 해야 합니다. 로컬 응용 프로그램 폴더에 로그 파일을 만들 수 있습니다.

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

더 파일 액세스에 대 한 UWP 응용 프로그램에 대 한 권한이 제품은 [여기](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)합니다.

### <a name="android"></a>Android

내부 저장소, 외부 저장소 또는 캐시 디렉터리에 로그 파일을 저장할 수 있습니다. 내부 저장소에서 파일이 생성 또는 캐시 디렉터리는 응용 프로그램에 전용입니다. 외부 저장소에서 로그 파일을 만들도록 하는 것이 좋습니다.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

위의 코드는 응용 프로그램별 디렉터리의 루트에서 외부 저장소에 로그 파일을 저장 합니다. 사용자 파일 관리자를 사용 하 여 파일에 액세스할 수 (일반적으로 `Android/data/ApplicationName/logfile.txt`). 파일을 응용 프로그램을 제거할 때 삭제 됩니다.

요청 해야 `WRITE_EXTERNAL_STORAGE` 매니페스트 파일의 권한:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

보다 데이터 및 파일에 대 한 Android 응용 프로그램에 대 한 사용 가능한 저장 공간이 [여기](https://developer.android.com/guide/topics/data/data-storage.html)합니다.

#### <a name="ios"></a>iOS

응용 프로그램 샌드박스 내에서 디렉터리에만 액세스할 수 있습니다. 문서, 라이브러리 및 임시 디렉터리에 파일을 만들 수 있습니다. 문서 디렉터리의에서 파일 사용할 수는 사용자에 게 합니다. 다음 코드 조각은 응용 프로그램 문서 디렉터리에서 로그 파일의 생성을 보여 줍니다.

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

생성된 된 파일에 액세스 하려면 추가 된 속성을 아래는 `Info.plist` 응용 프로그램의 속성 목록:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

더 iOS에 대 한 파일 시스템을 사용할 수 [여기](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)

