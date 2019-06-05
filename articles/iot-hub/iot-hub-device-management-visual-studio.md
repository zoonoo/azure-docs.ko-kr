---
title: Visual studio용 클라우드 탐색기를 사용하여 Azure IoT 디바이스 관리 | Microsoft Docs
description: Direct 메서드와 Twin의 desired 속성 관리 옵션을 제공하는 Visual Studio용 클라우드 탐색기를 사용하여 Azure IoT Hub 디바이스를 관리합니다.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399559"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>Azure IoT Hub 디바이스 관리를 위해 Visual Studio용 클라우드 탐색기 사용

![엔드투엔드 다이어그램](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)는 Azure 리소스를 보고, 해당 속성을 검사하고, Visual Studio 내에서 핵심 개발자 작업을 수행할 수 있는 유용한 Visual Studio 확장입니다. 다양한 작업을 수행하는 데 사용할 수 있는 관리 옵션이 제공됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 관리 옵션          | Task                    |
|----------------------------|--------------------------------|
| 직접 메서드             | 메시지 보내기 시작 또는 중지, 디바이스 다시 부팅 등의 디바이스 작업을 수행합니다.                                        |
| 디바이스 쌍 읽기           | 디바이스의 보고된 상태를 가져옵니다. 예를 들어 디바이스에서 지금 LED가 깜박이고 있다고 보고합니다.                                    |
| 디바이스 쌍 업데이트         | 디바이스를 특정 상태(예: LED를 녹색으로 설정 또는 원격 분석 전송 간격을 30 분으로 설정)로 지정합니다.         |
| 클라우드-장치 메시지   | 디바이스에 알림을 보냅니다. 예를 들어 "오늘 비가 올 가능성이 매우 높습니다. 반드시 우산을 챙기세요."              |

이러한 옵션을 사용하는 방법에 대한 차이점과 지침에 대한 자세한 내용은 [디바이스-클라우드 통신 지침](iot-hub-devguide-d2c-guidance.md) 및 [클라우드-디바이스 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

디바이스 쌍은 디바이스의 상태 정보(메타데이터, 상태 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 여기에 연결하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다. 디바이스 쌍에 대한 자세한 내용은 [디바이스 쌍 시작](iot-hub-node-node-twin-getstarted.md)을 참조하세요.

## <a name="what-you-learn"></a>학습 내용

개발 컴퓨터에서 다양 한 관리 옵션을 사용 하 여 Visual studio 클라우드 탐색기를 사용 하는 방법에 알아봅니다.

## <a name="what-you-do"></a>수행할 작업

다양한 관리 옵션을 사용하여 Visual Studio용 클라우드 탐색기를 실행합니다.

## <a name="what-you-need"></a>필요한 항목

- 활성 Azure 구독
- 구독에서 Azure IoT Hub
- Microsoft Visual Studio 2017 업데이트 8 이상
- Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소(Azure 워크로드를 사용하여 기본적으로 선택됨)

## <a name="update-cloud-explorer-to-latest-version"></a>최신 버전으로 클라우드 탐색기 업데이트

Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소는 디바이스-클라우드 및 클라우드-디바이스 메시지 모니터링만을 지원합니다. 관리 옵션에 액세스하려면 최신 [클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)를 다운로드하고 설치해야 합니다.

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

1. Visual Studio **클라우드 탐색기** 창에서 계정 관리 아이콘을 클릭합니다. **보기** > **클라우드 탐색기** 메뉴에서 클라우드 탐색기 창을 열 수 있습니다.

    ![계정 관리 클릭](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. 클라우드 탐색기에서 **계정 관리**를 클릭합니다.
1. 새 창에서 **계정 추가...** 를 클릭하여 처음으로 Azure에 로그인합니다.
1. 로그인한 후 Azure 구독 목록이 나타납니다. 보려는 Azure 구독을 선택하고 **적용**을 클릭합니다.
1. **구독** > **IoT Hub** > **IoT Hub**를 확장하면 IoT Hub 노드 아래에 디바이스 목록이 표시됩니다. 하나의 디바이스를 마우스 오른쪽 단추로 클릭하여 관리 옵션에 액세스합니다.

    ![관리 옵션](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>직접 메서드

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **디바이스 직접 메서드 호출**을 선택합니다.
1. 입력 상자에 메서드 이름 및 페이로드를 입력합니다.
1. **IoT Hub** 출력 창에 결과가 표시됩니다.

## <a name="read-device-twin"></a>디바이스 쌍 읽기

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **디바이스 쌍 편집**을 선택합니다.
1. **azure-iot-device-twin.json** 파일은 디바이스 쌍의 콘텐츠를 사용해 열립니다.

## <a name="update-device-twin"></a>디바이스 쌍 업데이트

1. **azure-iot-device-twin.json** 파일에 대한 **태그** 또는 **properties.desired** 필드를 일부 편집합니다.
1. **Ctrl+S** 키를 눌러 디바이스 쌍을 업데이트합니다.
1. **IoT Hub** 출력 창에 결과가 표시됩니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-장치 메시지 보내기

IoT Hub에서 디바이스로 메시지를 보내려면 다음 단계를 수행합니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **C2D 메시지 보내기**를 선택합니다.
1. 입력 상자에 메시지를 입력합니다.
1. **IoT Hub** 출력 창에 결과가 표시됩니다.

## <a name="next-steps"></a>다음 단계

다양한 관리 옵션을 사용하여 Visual Studio용 클라우드 탐색기를 사용하는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]