---
title: Speech Devices SDK 시작
description: Speech Devices SDK를 시작하기 위한 필수 구성 요소 및 지침입니다.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 463a015b7c01dafc5b30de56b95fa0510ffb98e4
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42424372"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Speech Devices SDK 시작

이 문서에서는 Speech Devices SDK를 사용하여 음성 지원 장치를 개발하기 위해 개발 PC 및 음성 장치 개발 키트를 구성하는 방법을 설명합니다. 그런 다음, 샘플 응용 프로그램을 빌드하고 장치에 배포합니다. 

샘플 응용 프로그램의 소스 코드는 Speech Devices SDK에 포함되어 있으며 고 [GitHub에서도 사용할 수 있습니다](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>필수 조건

Speech Devices SDK로 개발을 시작하기 전에 필요한 정보와 소프트웨어를 수집합니다.

* [Roobo에서](http://ddk.roobo.com/) 개발 키트를 구합니다. 이러한 키트는 선형 또는 순환 마이크 배열 구성으로 사용할 수 있습니다. 따라서 요구에 맞는 적절한 구성을 선택합니다.

    |개발 키트 구성|스피커 위치|
    |-----------------------------|------------|
    |순환|장치로부터 임의 방향|
    |선형|장치 정면|

* Speech Devices SDK의 [다운로드 사이트](https://shares.datatransfer.microsoft.com/)에서 Android 샘플 앱을 포함하는 최신 버전의 Speech Devices SDK를 다운로드합니다. 로컬 폴더(예: `C:\SDSDK`)에 ZIP 파일 압축을 풉니다.

* PC에 [Android Studio](https://developer.android.com/studio/) 및 [Vysor](http://vysor.io/download/)을 설치합니다.

* Speech Service [구독 키](get-started.md)를 구합니다. 30일 평가판을 구하거나 Azure 대시보드에서 키를 가져올 수 있습니다.

* Speech Service의 의도 인식 기능을 사용하려는 경우 [LUIS(언어 이해 서비스)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)를 구독하고 [구독 키](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription)를 가져옵니다. 

    [간단한 LUIS 모델을 만들거나](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/), Speech Devices SDK [다운로드 사이트](https://shares.datatransfer.microsoft.com/)에서 사용할 수 있는 샘플 LUIS 모델 `LUIS-example.json`을 사용할 수 있습니다. **새 앱 가져오기**를 클릭하고 JSON 파일을 선택하여 모델의 JSON 파일을 [LUIS 포털](https://www.luis.ai/home)에 업로드합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정

1. PC 또는 전원 어댑터에 연결된 미니 USB 케이블을 사용하여 개발 키트의 전원을 켭니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.

1. 두 번째 미니 USB 케이블을 사용하여 개발 키트를 컴퓨터에 연결합니다.

    ![개발 키트 연결](media/speech-devices-sdk/qsg-1.png)

1. 개발 키트 방향을 적절히 조정합니다.

    |개발 키트 구성|방향|
    |-----------------------------|------------|
    |순환|수직, 마이크가 천장을 향함|
    |선형|측면, 마이크가 사용자를 향함(아래 표시)|

    ![선형 개발 키트 방향](media/speech-devices-sdk/qsg-2.png)

1. 인증서 및 절전 모드 해제 단어(키워드) 표 파일을 설치하고 사운드 장치의 사용 권한을 설정합니다. 명령 창에서 다음 명령을 입력합니다.

    > [!NOTE]
    > 이러한 명령은 Android Studio 설치의 일부인 Android Debug Bridge, `adb.exe`를 사용합니다. 이 도구는 `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`에 있습니다. 경로에 이 디렉터리를 추가하여 좀 더 편리하게 `adb`를 호출하도록 할 수 있습니다. 그렇지 않으면, `adb`를 호출하는 모든 명령에 `adb.exe` 설치의 전체 경로를 지정해야 합니다.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > PC의 마이크 및 스피커를 음소거합니다. 이렇게 하면 개발 키트 마이크를 사용하게 되며, PC의 오디오가 장치를 트리거하는 경우를 방지할 수 있습니다.
    
1.  컴퓨터에서 Vysor를 시작합니다.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  사용자 장치가 "장치 선택" 아래에 표시되어야 합니다. 옆에 있는 **보기** 단추를 클릭합니다. 
 
1.  폴더 아이콘, **설정**, **WLAN**을 차례로 클릭하여 무선 네트워크에 연결합니다.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
 > [!NOTE]
 > Wi-Fi 시스템에 장치를 연결하는 것과 관련된 정책이 회사에 있는 경우 Mac 주소를 얻고 Wi-Fi 시스템에 연결하는 방법에 대해 IT 부서에 문의해야 합니다. 개발 키트의 Mac 주소를 찾으려면 개발 키트의 바탕 화면에 있는 파일 폴더 아이콘을 클릭한 다음, **설정**에서 "Mac 주소"를 검색하고, **Mac 주소**를 클릭하여 **고급 WLAN**에 들어갑니다. 아래쪽에 있는 Mac 주소를 적어 두세요. 또한 일부 회사에는 Wi-Fi 시스템에 장치를 연결하는 데 허용되는 시간 제한이 있을 수 있습니다. 개발 키트의 Wi-Fi 시스템에 대한 등록을 특정 기간(일) 후로 연장해야 할 수도 있습니다.  
 
 
   ![Vysor 파일 폴더](media/speech-devices-sdk/qsg-10.png)
   
   ![Vysor Mac 주소](media/speech-devices-sdk/qsg-11.png)
   
   
 > 개발 키트에 스피커를 연결하려면 오디오 라인 출력에 연결할 수 있습니다. 고품질 3.5mm 스피커도 선택해야 합니다.
 
   ![Vysor 오디오](media/speech-devices-sdk/qsg-14.png)
 
## <a name="run-a-sample-application"></a>샘플 응용 프로그램 실행

Roobo 테스트를 실행하고 개발 키트 설치가 유효한지 확인하려면 샘플 응용 프로그램을 빌드한 후 설치합니다.

1.  Android Studio를 시작합니다.

1.  기존 Android Studio 프로젝트를 열도록 선택합니다.

    ![Android Studio - 기존 프로젝트 열기](media/speech-devices-sdk/qsg-5.png)
 
1.  `C:\SDSDK\Android-Sample-Release\example`로 이동한 후 **확인**을 클릭하여 예제 프로젝트를 엽니다.
 
1.  소스 코드에 음성 구독 키를 추가합니다. 의도 인식을 시험해 보려면 [Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 구독 키 및 응용 프로그램 ID를 추가합니다. 

    키 및 응용 프로그램 정보가 소스 파일 `MainActivity.java`의 다음 줄에 표시됩니다.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. 기본 절전 모드 해제 단어(키워드)는 "컴퓨터"입니다.  원할 경우 제공된 절전 모드 해제 단어 "머신" 및 "도우미" 중 하나를 시도해볼 수 있습니다. 이러한 대체 단어에 대한 리소스 파일은 Speech Devices SDK의 "keyword" 폴더에서 찾을 수 있습니다. 예를 들어, `C:\SDSDK\Android-Sample-Release\keyword\Computer`에는 "컴퓨터"에 사용되는 파일이 포함되어 있습니다.

    [사용자 지정 절전 모드 해제 단어를 만들](speech-devices-sdk-create-kws.md) 수도 있습니다.

    원하는 절전 모드 해제 단어를 설치하려면
 
    * 명령 창에서 다음 명령을 실행하여 장치의 \data\ 폴더에 `keyword` 폴더를 만듭니다.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * 파일 `kws.table`, `kws_g.fst`, `kws_k.fst` 및 `words_kw.txt`를 장치의 \data\keyword\ 폴더에 복사합니다. 명령 창에서 다음 명령을 실행합니다. [사용자 지정 절전 모드 해제 단어](speech-devices-sdk-create-kws.md)를 만든 경우 웹에서 생성된 kws.table 파일은 `kws.table`, `kws_g.fst`, `kws_k.fst` 및 `words_kw.txt` 파일과 동일한 디렉터리에 있습니다. 대신 adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword 명령을 사용하여 kws.table 파일을 개발 키트로 푸시합니다.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * 샘플 응용 프로그램에서 이러한 파일을 참조합니다. `MainActivity.java`에서 다음 줄을 찾습니다. 지정한 키워드가 사용 중인 키워드인지와 해당 경로가 장치에 푸시한 `kws.table` 파일을 가리키는지 확인합니다.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 자체 코드에서 `kws.table` 파일을 사용하여 키워드 모델 인스턴스를 만들고 다음과 같이 인식을 시작할 수 있습니다.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  마이크 배열 기하 도형 설정을 포함하는 다음 줄을 업데이트합니다.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |변수|의미|사용 가능한 값|
    |--------|-------|----------------|
    |`DeviceGeometry`|실제 마이크 구성|`Circular6+1`: 순환 개발 키트|
    ||| `Linear4`: 선형 개발 키트|
    |`SelectedGeometry`|소프트웨어 마이크 구성|`Circular6+1`: 모든 마이크를 사용하는 순환 개발 키트|
    |||`Circular3+1`: 4개의 마이크를 사용하는 순환 개발 키트|
    |||`Linear4`: 모든 마이크를 사용하는 선형 개발 키트|
    |||`Linear2`: 2개의 마이크를 사용하는 선형 개발 키트|


1.  실행 메뉴에서 **'앱' 실행**을 선택하여 응용 프로그램을 빌드합니다. 배포 대상 선택 대화 상자가 나타납니다. 장치를 선택하고 **확인**을 클릭하여 장치에 응용 프로그램을 배포합니다.

    ![배포 대상 선택](media/speech-devices-sdk/qsg-7.png)
 
1.  Speech Devices SDK 예제 응용 프로그램이 시작되고 여기에 표시된 옵션이 표시됩니다.

    ![샘플 음성 장치 응용 프로그램](media/speech-devices-sdk/qsg-8.png)

1. 사용해 보세요.

## <a name="troubleshooting"></a>문제 해결

Speech Service를 사용할 때 인증서 오류가 발생하면 장치의 날짜와 시간이 올바른지 확인합니다. **설정**으로 이동하고, [시스템] 아래에서 **날짜 및 시간**을 클릭하고, **표준 시간대 선택**을 현재 표준 시간대로 설정합니다. **자동 날짜 및 시간**은 켜기(ON)로 유지합니다. 개발 키트의 시간이 PC의 시간과 일치하면 개발 키트가 인터넷에 연결되어 있음을 알 수 있습니다. 

 ![Vysor 파일 폴더](media/speech-devices-sdk/qsg-12.png)
 
 ![Vysor 파일 폴더](media/speech-devices-sdk/qsg-13.png)

추가 개발 정보를 보려면 Roobo [개발 가이드](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)를 참조하세요.

Roobo는 모든 오디오를 플래시 메모리로 캡처하는 도구를 제공합니다. 이 도구는 오디오 문제 해결에 도움이 될 수 있습니다. 이 도구 버전이 각 개발 키트 구성에 제공됩니다. [Roobo 사이트](http://ddk.roobo.com/)에서 장치를 선택하고 페이지 맨 아래에 있는 **ROOBO Tools**(ROOBO 도구) 링크를 클릭합니다.
