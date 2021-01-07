---
title: 음성 장치 SDK Roobo 스마트 오디오 개발자 키트 v1-Speech service
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK, Roobo Smart Audio Dev Kit v1을 시작 하기 위한 필수 구성 요소 및 지침입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015293"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>장치: Roobo 스마트 오디오 개발자 키트

이 문서에서는 Roobo Smart Audio Dev Kit에 대 한 장치 관련 정보를 제공 합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정

1. 개발 키트에는 마이크로 USB 커넥터 2개가 있습니다. 왼쪽 커넥터는 개발 키트에 전원을 공급하기 위한 것이며 아래 이미지에서 전원으로 강조 표시됩니다. 오른쪽 커넥터는 개발 키트를 제어하기 위한 것이며 해당 이미지에 디버그로 표시됩니다.

    ![개발 키트 연결](media/speech-devices-sdk/qsg-1.png)

1. 마이크로 USB 케이블을 사용해 전원 포트를 PC 또는 전원 어댑에 연결하여 개발 키트에 전원을 공급합니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.

1. 개발 키트를 제어 하려면 두 번째 마이크로 USB 케이블을 사용 하 여 디버그 포트를 컴퓨터에 연결 합니다. 신뢰할 수 있는 통신을 확보하기 위해 반드시 고품질 케이블을 사용해야 합니다.

1. 개발 키트의 방향을 원형 또는 선형 구성으로 지정합니다.

    |개발 키트 구성|방향|
    |-----------------------------|------------|
    |순환|수직, 마이크가 천장을 향함|
    |선형|측면, 마이크가 사용자를 향함(다음 이미지에 표시)|

    ![선형 개발 키트 방향](media/speech-devices-sdk/qsg-2.png)

1. 인증서를 설치 하 고 사운드 장치의 사용 권한을 설정 합니다. [명령 프롬프트] 창에서 다음 명령을 입력합니다.

   ```powershell
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
    > 오류가 표시 되 면 `no devices/emulators found` USB 케이블이 연결 되어 있고 고품질 케이블 인지 확인 합니다. `adb devices`를 사용하여 컴퓨터가 디바이스 목록을 반환할 때 개발 키트와 통신할 수 있는지 확인할 수 있습니다.
    >
    > [!TIP]
    > PC의 마이크와 스피커를 음소거하여 개발 키트의 마이크를 사용하고 있는지 확인하세요. 이렇게 하면 디바이스를 PC에서 오디오로 실수로 트리거하지 않습니다.

1. Dev kit에 스피커를 연결 하려는 경우 오디오 라인에 스피커를 연결할 수 있습니다. 3.5 mm 아날로그 플러그를 사용 하는 우수한 품질의 스피커를 선택 해야 합니다.

    ![Vysor 오디오](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>개발 정보

개발에 대 한 자세한 내용은 [Roobo 개발 가이드](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)를 참조 하세요.

## <a name="audio"></a>오디오

Roobo는 플래시 메모리에 모든 오디오를 캡처하는 도구를 제공 합니다. 이는 오디오 문제를 해결하는 데 도움이 될 수 있습니다. 이 도구 버전이 각 개발 키트 구성에 제공됩니다. [Roobo 사이트](http://ddk.roobo.com/)에서 장치를 선택 하 고 페이지 아래쪽의 **Roobo 도구** 링크를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Android 샘플 앱 실행](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)