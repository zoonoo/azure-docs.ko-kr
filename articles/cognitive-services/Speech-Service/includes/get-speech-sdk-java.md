---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399988"
---
:::row:::
    :::column span="3":::
        안드로이드용 Java SDK는 필요한 라이브러리와 필요한 Android 권한을 포함하는 <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR(Android 라이브러리)으로 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>패키지됩니다. `https://csspeechstorage.blob.core.windows.net/maven/`의 Maven 리포지토리에서 패키지 `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`로 호스트됩니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Android Studio 프로젝트의 패키지를 사용하는 경우 다음과 같이 변경합니다.

1. 프로젝트 수준 *build.gradle* 파일에서 `repository` 다음을 섹션에 추가합니다.
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. 모듈 수준 *build.gradle* 파일에서 `dependencies` 다음을 섹션에 추가합니다.
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Java SDK는 [Speech Devices SDK](../speech-devices-sdk.md)에도 포함됩니다.

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">안드로이드 특정 자바 빠른 시작 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">자바 런타임 (JRE) 빠른 시작 소스 코드<span class="docon docon-navigate-external x-hidden-focus"></span></a>