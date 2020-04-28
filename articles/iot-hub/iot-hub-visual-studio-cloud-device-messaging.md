---
title: VS 클라우드 탐색기를 사용 하 여 Azure IoT Hub 장치 메시징 관리
description: Visual Studio용 클라우드 탐색기를 사용하여 디바이스-클라우드 메시지를 모니터링하고 클라우드를 Azure IoT Hub의 디바이스 메시지로 보내는 방법을 알아봅니다.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74079486"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Visual Studio용 클라우드 탐색기를 사용하여 디바이스와 IoT Hub 간에 메시지 보내고 받기

![엔드투엔드 다이어그램](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)는 Azure 리소스를 보고, 해당 속성을 검사하고, Visual Studio 내에서 핵심 개발자 작업을 수행할 수 있는 유용한 Visual Studio 확장입니다. 이 문서에서는 클라우드 탐색기 사용 하 여 장치와 허브 간에 메시지를 보내고 받는 방법에 대해 집중적으로 설명 합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>학습 내용

이 문서에서는 Visual Studio 용 클라우드 탐색기를 사용 하 여 장치-클라우드 메시지를 모니터링 하 고 클라우드-장치 메시지를 보내는 방법에 대해 알아봅니다. 디바이스-클라우드 메시지는 디바이스에서 수집한 다음, IoT Hub로 보내는 센서 데이터일 수 있습니다. 클라우드-디바이스 메시지는 IoT Hub에서 디바이스로 보내는 명령일 수 있습니다. 예를 들어 디바이스에 연결된 LED를 깜박입니다.

## <a name="what-you-do"></a>수행할 작업

이 문서에서는 다음 작업을 수행 합니다.

- Visual Studio용 클라우드 탐색기를 사용하여 디바이스-클라우드 메시지를 모니터링합니다.

- Visual Studio용 클라우드 탐색기를 사용하여 클라우드-디바이스 메시지를 보냅니다.

## <a name="what-you-need"></a>필요한 항목

다음 필수 조건이 필요합니다.

- 활성화된 Azure 구독.

- 구독 중인 Azure IoT Hub

- Microsoft Visual Studio 2017 업데이트 9 이상 이 문서에서는 [Visual Studio 2019](https://www.visualstudio.com/vs/)을 사용 합니다.

- Azure 워크 로드에서 기본적으로 선택 되는 Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소입니다.

## <a name="update-cloud-explorer-to-latest-version"></a>최신 버전으로 클라우드 탐색기 업데이트

Visual Studio 2017 Visual Studio 설치 관리자의 클라우드 탐색기 구성 요소는 장치-클라우드 및 클라우드-장치 메시지의 모니터링만 지원 합니다. Visual Studio 2017을 사용 하려면 최신 [클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)를 다운로드 하 여 설치 합니다.

## <a name="sign-in-to-access-your-hub"></a>허브에 액세스 하려면 로그인 하세요.

허브에 액세스 하려면 다음 단계를 수행 합니다.

1. Visual Studio에서**클라우드 탐색기** **보기** > 를 선택 하 여 클라우드 탐색기를 엽니다.

1. 계정 관리 아이콘을 선택 하 여 구독을 표시 합니다.

    ![계정 관리 아이콘](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Azure에 로그인 한 경우 계정이 표시 됩니다. 처음으로 Azure에 로그인 하려면 **계정 추가**를 선택 합니다.

1. 사용 하려는 Azure 구독을 선택 하 고 **적용**을 선택 합니다.

1. 구독을 확장 한 다음 **IoT hub**를 확장 합니다.  각 허브에서 해당 허브에 대 한 장치를 볼 수 있습니다.

    ![디바이스 목록](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>디바이스-클라우드 메시지 모니터링

디바이스에서 IoT Hub로 보낸 메시지를 모니터링하려면 다음 단계를 수행합니다.

1. IoT Hub 또는 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 시작**을 선택합니다.

    ![D2C 메시지 모니터링 시작](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. 모니터링 되는 메시지는 **출력**아래에 나타납니다.

    ![D2C 메시지 모니터링 결과](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. 모니터링을 중지하려면 IoT Hub 또는 디바이스를 마우스 오른쪽 단추로 클릭하고 **D2C 메시지 모니터링 중지**를 선택합니다.

## <a name="send-cloud-to-device-messages"></a>클라우드-디바이스 메시지 보내기

IoT Hub에서 디바이스로 메시지를 보내려면 다음 단계를 수행합니다.

1. 디바이스를 마우스 오른쪽 단추로 클릭하고 **C2D 메시지 보내기**를 선택합니다.

1. 입력 상자에 메시지를 입력합니다.

    ![C2D 메시지 보내기](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    결과는 **출력**아래에 나타납니다.

    ![C2D 메시지 보내기 결과](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>다음 단계

IoT 디바이스와 Azure IoT Hub 간에 디바이스-클라우드 메시지를 모니터링하고 클라우드-디바이스 메시지를 보내는 방법을 알아보았습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]