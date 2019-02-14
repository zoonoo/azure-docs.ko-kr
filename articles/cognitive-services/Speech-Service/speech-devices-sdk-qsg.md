---
title: Speech Devices SDK 시작 - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK를 시작하기 위한 필수 구성 요소 및 지침입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 4c01cf93fe3bb66f9bce73acb3c2f100764d1f46
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872546"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Speech Devices SDK 시작

이 문서에서는 Speech Devices SDK를 사용하여 음성 지원 디바이스를 개발하기 위해 개발 PC와 Speech 디바이스 개발 키트를 구성하는 방법에 대해 설명합니다. 그런 다음, 샘플 애플리케이션을 빌드하여 디바이스에 배포합니다.

샘플 애플리케이션의 소스 코드는 Speech Devices SDK에 포함되어 있으며, [GitHub에서도 사용할 수 있습니다](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>필수 조건

Speech Devices SDK를 사용하여 개발을 시작하기 전에 필요한 정보와 소프트웨어를 수집합니다.

* [ROOBO에서 개발 키트](http://ddk.roobo.com/)를 가져옵니다. 이러한 키트는 선형 또는 원형 마이크 배열 구성으로 사용할 수 있습니다. 요구 사항에 맞는 올바른 구성을 선택합니다.

    |개발 키트 구성|스피커 위치|
    |-----------------------------|------------|
    |순환|디바이스로부터 임의 방향|
    |선형|디바이스 정면|

* [Speech 장치 SDK 다운로드 사이트](https://shares.datatransfer.microsoft.com/)에서 Android 샘플 앱이 포함된 최신 버전의 Speech 장치 SDK를 가져옵니다. .zip 파일을 로컬 폴더(예: C:\SDSDK)에 추출합니다.

* PC에 [Android Studio](https://developer.android.com/studio/) 및 [Vysor](http://vysor.io/download/)을 설치합니다.

* [Speech Service 구독 키](get-started.md)를 가져옵니다. 30일 평가판을 가져오거나 Azure 대시보드에서 키를 가져올 수 있습니다.

* Speech Service의 의도 인식 기능을 사용하려면 [LUIS(Language Understanding 서비스)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)를 구독하고 [구독 키](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)를 가져옵니다.

    [간단한 LUIS 모델을 만들거나](https://docs.microsoft.com/azure/cognitive-services/luis/) LUIS-example.json LUIS 모델 샘플을 사용할 수 있습니다. LUIS 모델 샘플은 [Speech Devices SDK 다운로드 사이트](https://shares.datatransfer.microsoft.com/)에서 사용할 수 있습니다. 모델의 JSON 파일을 [LUIS 포털](https://www.luis.ai/home)에 업로드하려면 **새 앱 가져오기**를 선택한 다음, JSON 파일을 선택합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정
    
1. 개발 키트에는 마이크로 USB 커넥터 2개가 있습니다. 왼쪽 커넥터는 개발 키트에 전원을 공급하기 위한 것이며 아래 이미지에서 전원으로 강조 표시됩니다. 오른쪽 커넥터는 개발 키트를 제어하기 위한 것이며 해당 이미지에 디버그로 표시됩니다.

    ![개발 키트 연결](media/speech-devices-sdk/qsg-1.png)
       
1. 마이크로 USB 케이블을 사용해 전원 포트를 PC 또는 전원 어댑에 연결하여 개발 키트에 전원을 공급합니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.

1. 두 번째 마이크로 USB 케이블을 사용하여 컴퓨터의 디버그 포트에 대한 개발 키트 연결을 제어합니다. 신뢰할 수 있는 통신을 확보하기 위해 반드시 고품질 케이블을 사용해야 합니다.

1. 개발 키트의 방향을 원형 또는 선형 구성으로 지정합니다.

    |개발 키트 구성|방향|
    |-----------------------------|------------|
    |순환|수직, 마이크가 천장을 향함|
    |선형|측면, 마이크가 사용자를 향함(다음 이미지에 표시)|

    ![선형 개발 키트 방향](media/speech-devices-sdk/qsg-2.png)

1. 인증서 및 절전 모드 해제 단어(키워드) 표 파일을 설치하고 사운드 디바이스의 사용 권한을 설정합니다. [명령 프롬프트] 창에서 다음 명령을 입력합니다.

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 이러한 명령은 Android Studio 설치의 일부인 Android Debug Bridge, `adb.exe`를 사용합니다. 이 도구는 C:\Users\[사용자 이름]\AppData\Local\Android\Sdk\platform-tools에 있습니다. 이 디렉터리를 경로에 추가하면 `adb`를 더 편리하게 호출할 수 있습니다. 그렇지 않으면, `adb`를 호출하는 모든 명령에 adb.exe 설치의 전체 경로를 지정해야 합니다.
    >
    > `no devices/emulators found` 오류가 표시되면 USB 케이블이 연결되었는지, 고품질 케이블인지 확인합니다. `adb devices`를 사용하여 컴퓨터가 디바이스 목록을 반환할 때 개발 키트와 통신할 수 있는지 확인할 수 있습니다.

    > [!TIP]
    > PC의 마이크와 스피커를 음소거하여 개발 키트의 마이크를 사용하고 있는지 확인하세요. 이렇게 하면 디바이스를 PC에서 오디오로 실수로 트리거하지 않습니다.

1.  컴퓨터에서 Vysor를 시작합니다.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  디바이스가 **디바이스 선택** 아래에 나열되어야 합니다. 디바이스 옆에 있는 **보기** 단추를 선택합니다.

1.  폴더 아이콘을 선택하여 무선 네트워크에 연결한 다음, **설정** > **WLAN**을 차례로 선택합니다.

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
    >
    > 스피커를 개발 키트에 연결하려면 오디오 라인 출력에 연결할 수 있습니다. 고품질 3.5mm 스피커를 선택해야 합니다.
    >
    > ![Vysor 오디오](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>샘플 애플리케이션 실행

ROOBO 테스트를 실행하고 개발 키트 설정의 유효성을 검사하려면 샘플 응용 프로그램을 빌드하고 설치합니다.

1.  Android Studio를 시작합니다.

1.  **기존 Android Studio 프로젝트 열기**를 선택합니다.

    ![Android Studio - 기존 프로젝트 열기](media/speech-devices-sdk/qsg-5.png)

1.  C:\SDSDK\Android-Sample-Release\example로 이동합니다. **확인**을 선택하여 예제 프로젝트를 엽니다.

1.  소스 코드에 음성 구독 키를 추가합니다. 의도 인식을 사용해 보려면 [Language Understanding 서비스](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 구독 키 및 응용 프로그램 ID를 추가합니다.

    키 및 애플리케이션 정보는 MainActivity.java 원본 파일의 다음 줄에 표시됩니다.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app ID]"
    ```

1. 기본 절전 모드 해제 단어(키워드)는 "Computer"입니다. "Machine" 또는 "Assistant"와 같이 제공되는 다른 절전 모드 해제 단어 중 하나를 시도할 수도 있습니다. 이러한 대체 절전 모드 해제 단어에 대한 리소스 파일은 Speech Devices SDK의 keyword 폴더에 있습니다. 예를 들어 C:\SDSDK\Android-Sample-Release\keyword\Computer에는 "Computer" 절전 모드 해제 단어에 사용되는 파일이 들어 있습니다.

    [사용자 지정 절전 모드 해제 단어를 만들](speech-devices-sdk-create-kws.md) 수도 있습니다.

    사용하려는 절전 모드 해제 단어를 설치하려면 다음을 수행합니다.

    * [명령 프롬프트] 창에서 다음 명령을 실행하여 디바이스의 data 폴더에 keyword 폴더를 만듭니다.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * kws.table, kws_k.fst 및 words_kw.txt 파일을 디바이스의 \data\keyword 폴더에 복사합니다. [명령 프롬프트 창]에서 다음 명령을 실행합니다. [사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md)를 만든 경우 웹에서 생성된 kws.table 파일은 kws.table, kws_k.fst 및 words_kw.txt 파일과 동일한 디렉터리에 있습니다. 사용자 지정 절전 모드 해제 단어의 경우 `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` 명령을 사용하여 kws.table 파일을 개발 키트에 푸시합니다.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * 샘플 애플리케이션에서 이러한 파일을 참조합니다. MainActivity.java에서 다음 줄을 찾습니다. 지정한 키워드가 사용 중인 키워드이고 해당 경로가 디바이스에 푸시한 `kws.table` 파일을 가리키는지 확인합니다.

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 사용자 고유의 코드에서 kws.table 파일을 사용하여 키워드 모델 인스턴스를 만들고 인식을 시작할 수 있습니다.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  마이크 배열 기하 도형 설정이 포함된 다음 줄을 업데이트합니다.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    다음 표에서는 사용 가능한 값을 설명하고 있습니다.

    |변수|의미|사용 가능한 값|
    |--------|-------|----------------|
    |`DeviceGeometry`|실제 마이크 구성|원형 개발 키트의 경우: `Circular6+1` |
    |||선형 개발 키트의 경우: `Linear4`|
    |`SelectedGeometry`|소프트웨어 마이크 구성|모든 마이크를 사용하는 원형 개발 키트의 경우: `Circular6+1`|
    |||네 개의 마이크를 사용하는 원형 개발 키트의 경우: `Circular3+1`|
    |||모든 마이크를 사용하는 선형 개발 키트의 경우: `Linear4`|
    |||두 개의 마이크를 사용하는 선형 개발 키트의 경우: `Linear2`|


1.  애플리케이션을 빌드하려면 **실행** 메뉴에서 **'앱' 실행**을 선택합니다. **배포 대상 선택** 대화 상자가 나타납니다.

1. 디바이스를 선택한 다음, **확인**을 선택하여 애플리케이션을 디바이스에 배포합니다.

    ![배포 대상 선택 대화 상자](media/speech-devices-sdk/qsg-7.png)

1.  Speech Devices SDK 예제 애플리케이션이 시작되고 다음 옵션이 표시됩니다.

    ![샘플 Speech Devices SDK 예제 애플리케이션 및 옵션](media/speech-devices-sdk/qsg-8.png)

1. 실험!

## <a name="troubleshooting"></a>문제 해결

### <a name="certificate-failures"></a>인증서 오류

Speech Service를 사용할 때 인증서 오류가 발생하면 디바이스의 날짜와 시간이 정확한지 확인합니다.

1. **설정**으로 이동합니다. **시스템** 아래에서 **날짜 및 시간**을 선택합니다.

    ![[설정] 아래에서 [날짜 및 시간] 선택](media/speech-devices-sdk/qsg-12.png)

1. **자동 날짜 및 시간** 옵션을 선택한 채로 둡니다. **표준 시간대 선택** 아래에서 현재 표준 시간대를 선택합니다.

    ![날짜 및 표준 시간대 선택](media/speech-devices-sdk/qsg-13.png)

    개발 키트의 시간이 PC의 시간과 일치하면 개발 키트가 인터넷에 연결됩니다.

    자세한 개발 정보는 [ROOBO 개발 가이드](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)를 참조하세요.

### <a name="audio"></a>오디오

ROOBO는 모든 오디오를 플래시 메모리에 캡처하는 도구를 제공합니다. 이는 오디오 문제를 해결하는 데 도움이 될 수 있습니다. 이 도구 버전이 각 개발 키트 구성에 제공됩니다. [ROOBO 사이트](http://ddk.roobo.com/)에서 장치를 선택한 다음, 페이지 아래쪽의 **ROOBO Tools** 링크를 선택합니다.
