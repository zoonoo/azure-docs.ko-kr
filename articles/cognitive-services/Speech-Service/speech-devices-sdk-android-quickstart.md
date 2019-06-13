---
title: '빠른 시작: Android-음성 서비스에서 음성 장치 SDK를 실행 합니다.'
titleSuffix: Azure Cognitive Services
description: 필수 구성 요소 및 Android 음성 장치 SDK를 사용 하 여 시작 하는 것에 대 한 지침입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: bd0160073898a5a83af2e7b2bba2dba007c04513
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425672"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>빠른 시작: Android에서 음성 장치 SDK 샘플 앱 실행

이 빠른 시작에서는 Android 용 음성 장치 SDK를 사용 하 여 음성 지원 제품을 작성 하거나로 사용 하는 방법을 알아봅니다를 [대화 기록](conversation-transcription-service.md) 장치입니다.

이 가이드에서는 [Azure Cognitive Services](get-started.md) 음성 서비스 리소스를 사용 하 여 계정. 계정이 없는 경우 [평가판](https://azure.microsoft.com/try/cognitive-services/)을 사용하여 구독 키를 가져올 수 있습니다.

샘플 애플리케이션의 소스 코드는 Speech Devices SDK에 포함되어 있으며, [GitHub에서도 사용할 수 있습니다](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>필수 조건

음성 장치 SDK를 사용 하 여 시작 하기 전에 해야 합니다.

* 제공 된 지침에 따라 프로그램 [개발 키트](get-speech-devices-sdk.md) 장치에서 전원 합니다.

* 최신 버전을 다운로드 합니다 [음성 장치 SDK](https://aka.ms/sdsdk-download), 작업 디렉터리에.zip을 추출 하 고 합니다.
   > [!NOTE]
   > 이 빠른 시작 앱 C:\SDSDK\Android-Sample-Release에 추출 되는 가정 및 Android 샘플 앱을 포함 하는 Android-샘플-Release.zip 파일

* 가져오려는 [음성 서비스에 대 한 Azure 구독 키](get-started.md)

* 대화 기록 사용 하려는 경우 사용 해야는 [순환 마이크 장치](get-speech-devices-sdk.md) 및 현재 서비스는 "EN-US" 및에 "ZH-CN" 지역, "centralus" 및 "eastasia"에서 사용할 수만 있습니다. 음성 키 대화 기록 사용 하려면 해당 지역 중 하나에 있어야 합니다.

* 음성 서비스를 사용 하 여 사용자 표현에서 인 텐트 (또는 작업)를 식별 하려는 경우는 [Service LUIS (Language Understanding)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 구독 합니다. LUIS 및 의도 인식 하는 방법에 대 한 자세한 내용은 참조 하세요 [LUIS 사용 하 여 음성 의도 인식 C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)합니다.

    [간단한 LUIS 모델을 만들거나](https://docs.microsoft.com/azure/cognitive-services/luis/) LUIS-example.json LUIS 모델 샘플을 사용할 수 있습니다. LUIS 모델 샘플은 [Speech Devices SDK 다운로드 사이트](https://aka.ms/sdsdk-luis)에서 사용할 수 있습니다. 모델의 JSON 파일을 [LUIS 포털](https://www.luis.ai/home)에 업로드하려면 **새 앱 가져오기**를 선택한 다음, JSON 파일을 선택합니다.

* PC에 [Android Studio](https://developer.android.com/studio/) 및 [Vysor](https://vysor.io/download/)을 설치합니다.

## <a name="set-up-the-device"></a>장치 설정

1. 컴퓨터에서 Vysor를 시작합니다.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. 디바이스가 **디바이스 선택** 아래에 나열되어야 합니다. 디바이스 옆에 있는 **보기** 단추를 선택합니다.

1. 폴더 아이콘을 선택하여 무선 네트워크에 연결한 다음, **설정** > **WLAN**을 차례로 선택합니다.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > 회사에 디바이스를 Wi-Fi 시스템에 연결하는 정책이 있는 경우 MAC 주소를 가져오고 회사의 Wi-Fi에 연결하는 방법에 대해 IT 부서에 문의해야 합니다.
    >
    > 개발 키트의 MAC 주소를 찾으려면 개발 키트의 바탕 화면에서 파일 폴더 아이콘을 선택합니다.
    >
    >  ![Vysor 파일 폴더](media/speech-devices-sdk/qsg-10.png)
    >
    > **설정**을 선택합니다. "mac 주소"를 검색한 다음, **Mac 주소** > **고급 WLAN**을 차례로 선택합니다. 대화 상자의 아래쪽 근처에 표시되는 MAC 주소를 적어 둡니다.
    >
    > ![Vysor MAC 주소](media/speech-devices-sdk/qsg-11.png)
    >
    > 일부 회사는 디바이스를 Wi-Fi 시스템에 연결할 수 있는 시간을 제한할 수 있습니다. 개발 키트의 Wi-Fi 시스템에 대한 등록을 특정 기간(일) 후로 연장해야 할 수도 있습니다.

## <a name="run-the-sample-application"></a>샘플 애플리케이션 실행

개발 키트 설정 유효성 검사를 빌드하고 샘플 응용 프로그램을 설치 합니다.

1. Android Studio를 시작합니다.

1. **기존 Android Studio 프로젝트 열기**를 선택합니다.

   ![Android Studio - 기존 프로젝트 열기](media/speech-devices-sdk/qsg-5.png)

1. C:\SDSDK\Android-Sample-Release\example로 이동합니다. **확인**을 선택하여 예제 프로젝트를 엽니다.

1. 소스 코드에 음성 구독 키를 추가 합니다. 의도 인식을 사용해 보려면 [Language Understanding 서비스](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 구독 키 및 애플리케이션 ID를 추가합니다.

   LUIS와 음성에 대 한 정보 MainActivity.java를 살펴봅니다.

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    대화 기록을 사용 하는 경우 음성 키 및 지역 정보 conversation.java에도 필요 합니다.

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. 기본 절전 모드 해제 단어(키워드)는 "Computer"입니다. "Machine" 또는 "Assistant"와 같이 제공되는 다른 절전 모드 해제 단어 중 하나를 시도할 수도 있습니다. 이러한 대체 절전 모드 해제 단어에 대한 리소스 파일은 Speech Devices SDK의 keyword 폴더에 있습니다. 예를 들어 C:\SDSDK\Android-Sample-Release\keyword\Computer에는 "Computer" 절전 모드 해제 단어에 사용되는 파일이 들어 있습니다.

   > [!TIP]
   > [사용자 지정 절전 모드 해제 단어를 만들](speech-devices-sdk-create-kws.md) 수도 있습니다.

    새로운 절전 모드 해제 단어를 사용 하려면에서 다음 두 줄을 업데이트 `MainActivity.java`, 절전 모드 해제 package 앱에 복사 합니다. 예를 들어 절전 모드 해제 word 패키지 kws에서 절전 모드 해제 word 'Machine' 사용 하 여-machine.zip:

   * 절전 모드 해제 package "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" 폴더로 복사 합니다.
   * 업데이트 된 `MainActivity.java` 키워드와 패키지 이름:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 마이크 배열 기하 도형 설정이 포함된 다음 줄을 업데이트합니다.

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   이 표에서 지원 되는 값을 보여 줍니다.

   |변수|의미|사용 가능한 값|
   |--------|-------|----------------|
   |`DeviceGeometry`|실제 마이크 구성|원형 개발 키트의 경우: `Circular6+1` |
   |||선형 개발 키트의 경우: `Linear4`|
   |`SelectedGeometry`|소프트웨어 마이크 구성|모든 마이크를 사용하는 원형 개발 키트의 경우: `Circular6+1`|
   |||네 개의 마이크를 사용하는 원형 개발 키트의 경우: `Circular3+1`|
   |||모든 마이크를 사용하는 선형 개발 키트의 경우: `Linear4`|
   |||두 개의 마이크를 사용하는 선형 개발 키트의 경우: `Linear2`|

1. 애플리케이션을 빌드하려면 **실행** 메뉴에서 **'앱' 실행**을 선택합니다. **배포 대상 선택** 대화 상자가 나타납니다.

1. 디바이스를 선택한 다음, **확인**을 선택하여 애플리케이션을 디바이스에 배포합니다.

    ![배포 대상 선택 대화 상자](media/speech-devices-sdk/qsg-7.png)

1. Speech Devices SDK 예제 애플리케이션이 시작되고 다음 옵션이 표시됩니다.

   ![샘플 Speech Devices SDK 예제 애플리케이션 및 옵션](media/speech-devices-sdk/qsg-8.png)

1. 새 대화 기록 데모를 보십시오. 세션 시작 '을 사용 하 여 복사를 시작 합니다. 기본적으로 모든 사용자가 게스트입니다. 그러나 참가자의 음성 서명이 있는 경우 이러한 넣을 수 있습니다 파일로 `/video/participants.properties` 장치의 합니다. 음성 시그니처를 생성 하려면 살펴 [촬영할 대화 (SDK)](how-to-use-conversation-transcription-service.md)합니다.

   ![데모 대화 기록 응용 프로그램](media/speech-devices-sdk/qsg-15.png)

1. 실험!

## <a name="troubleshooting"></a>문제 해결

   음성 장치에 연결할 수 없으면입니다. 명령 프롬프트 창에서 다음 명령을 입력 합니다. 장치 목록이 반환 됩니다.

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > 이 명령은 Android Debug Bridge `adb.exe`, Android Studio 설치의 일부인 합니다. 이 도구는 C:\Users\[사용자 이름]\AppData\Local\Android\Sdk\platform-tools에 있습니다. 이 디렉터리를 경로에 추가하면 `adb`를 더 편리하게 호출할 수 있습니다. 그렇지 않으면, `adb`를 호출하는 모든 명령에 adb.exe 설치의 전체 경로를 지정해야 합니다.
   >
   > 오류가 표시 되 면 `no devices/emulators found` USB 케이블을 연결 되었는지 확인 하 고 고품질 케이블을 사용 합니다.
   >

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [릴리스 정보 검토](devices-sdk-release-notes.md)
