---
title: Azure IoT Hub를 사용 하 여 음성 도우미 응용 프로그램 구성
description: Azure IoT Hub를 사용 하 여 음성 도우미 응용 프로그램 구성
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: b22ef4ee0a8b5978bb2ec1c02fadf368815f3014
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095785"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Azure IoT Hub를 사용 하 여 음성 도우미 응용 프로그램 구성

이 문서에서는 IoT Hub를 사용 하 여 음성 도우미 응용 프로그램을 구성 하는 방법을 설명 합니다. 데모 템플릿을 사용 하 여 음성 도우미를 만드는 과정을 안내 하는 단계별 자습서는 [Azure Percept Studio 및 Azure Percept Audio를 사용 하 여 코드 없는 음성 도우미 빌드](./tutorial-no-code-speech.md)를 참조 하세요.

## <a name="update-your-voice-assistant-configuration"></a>음성 도우미 구성 업데이트

1. [Azure Portal](https://portal.azure.com) 를 열고 검색 창에 **IoT Hub** 을 입력 합니다. 아이콘을 클릭 하 여 IoT Hub 페이지를 엽니다.

1. IoT Hub 페이지에서 장치가 프로 비전 된 IoT Hub를 선택 합니다.

1. 왼쪽 탐색 메뉴의 **장치 자동 관리** 에서 **IoT Edge** 를 선택 하 여 IoT Hub에 연결 된 모든 장치를 확인 합니다.

1. 음성 도우미 응용 프로그램이 배포 된 장치를 선택 합니다.

1. **모듈 설정** 을 클릭 합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="모듈 집합이 강조 표시 된 장치 페이지의 스크린샷":::

1. **Container Registry 자격 증명** 섹션에 다음 항목이 있는지 확인 합니다. 필요한 경우 자격 증명을 추가 합니다.

    |Name|주소|사용자 이름|암호|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. **IoT Edge 모듈** 섹션에서 **azureearspeechclientmodule** 를 선택 합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="장치의 모든 IoT Edge 모듈 목록을 보여 주는 스크린샷":::

1. **모듈 설정** 탭을 클릭 합니다. 다음 구성을 확인 합니다.

    이미지 URI|다시 시작 정책|원하는 상태
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule:preload-devkit|always|실행 중

    설정이 일치 하지 않으면 편집 하 고 **업데이트** 를 클릭 합니다.

1. **환경 변수** 탭을 클릭 합니다. 환경 변수가 정의 되어 있지 않은지 확인 합니다.

1. **모듈 쌍 설정** 탭을 클릭 합니다. **SpeechConfigs** 섹션을 다음과 같이 업데이트 합니다.

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > 위에서 사용한 키워드는 공개적으로 사용할 수 있는 기본 키워드입니다. 사용자 고유의를 사용 하려는 경우 생성 된 테이블 파일을 blob 저장소에 업로드 하 여 사용자 지정 키워드를 추가할 수 있습니다. Blob 저장소는 익명 컨테이너 액세스 또는 익명 blob 액세스를 사용 하 여 구성 해야 합니다.

## <a name="how-to-find-out-appid-key-and-region"></a>AppId, 키 및 지역을 확인 하는 방법

**AppID**, **키** 및 **지역을** 찾으려면 [Speech Studio](https://speech.microsoft.com/)로 이동 합니다.

1. 로그인 하 고 적절 한 음성 리소스를 선택 합니다.
1. **Speech Studio** 홈 페이지의 **음성 도우미** 에서 **사용자 지정 명령** 을 클릭 합니다.
1. 대상 프로젝트를 선택 합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Speech Studio의 프로젝트 페이지 스크린샷":::

1. 왼쪽 메뉴 패널에서 **설정** 을 클릭 합니다.
1. **AppID** 와 **키** 는 **일반** 설정 탭에 있습니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="음성 프로젝트 일반 설정의 스크린샷":::

1. **영역** 을 찾으려면 설정 내에서 **LUIS 리소스** 탭을 엽니다. **제작 리소스** 선택은 지역 정보를 포함 합니다.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Speech project LUIS 리소스의 스크린샷":::

1. **SpeechConfigs** 정보를 입력 한 후 **업데이트** 를 클릭 합니다.

1. **모듈 설정** 페이지의 위쪽에 있는 **경로** 탭을 클릭 합니다. 다음 값을 가진 경로가 있는지 확인 합니다.

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    경로가 없으면 경로를 추가 합니다.

1. **리뷰 + 만들기** 를 클릭합니다.

1. **만들기** 를 클릭합니다.


## <a name="next-steps"></a>다음 단계

음성 도우미 구성을 업데이트 한 후에는 Azure Percept Studio의 데모로 돌아와서 응용 프로그램과 상호 작용 합니다.

