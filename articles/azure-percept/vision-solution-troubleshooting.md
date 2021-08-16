---
title: Azure Percept Vision 및 비전 모듈 관련 문제 해결
description: 비전 AI 프로토타입 환경에서 발견되는 몇 가지 일반적인 문제를 해결하는 팁을 확인합니다.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 28ac7d0d7079d8ba8c9483e7da816430295941c9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112027860"
---
# <a name="vision-solution-troubleshooting"></a>비전 솔루션 문제 해결

이 문서에서는 Azure Percept Studio에서 코드 없는 비전 솔루션과 관련된 문제를 해결하는 방법을 설명합니다.

## <a name="delete-a-vision-project"></a>비전 프로젝트 만들기

1. [Custom Vision 프로젝트](https://www.customvision.ai/projects) 페이지로 이동합니다.

1. 삭제할 프로젝트를 마우스로 가리키고 휴지통 아이콘을 선택하여 프로젝트를 삭제합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="삭제 아이콘이 강조 표시된 Custom Vision 프로젝트 페이지 스크린샷":::

## <a name="check-which-modules-are-on-a-device"></a>디바이스에 있는 모듈 확인

1. [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)로 이동합니다.

1. **IoT Hub** 아이콘을 선택합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="IoT Hub 아이콘이 강조 표시된 Azure Portal 홈페이지 스크린샷" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. 대상 디바이스가 연결된 IoT 허브를 선택합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT 허브 목록 스크린샷":::

1. **IoT Edge** 를 선택한 다음, **디바이스 ID** 탭에서 해당 디바이스를 선택합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge 홈페이지 스크린샷":::

1. 디바이스 모듈이 **모듈** 탭의 목록에 표시됩니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="모듈 탭 내용이 표시된, 선택한 디바이스의 IoT Edge 페이지 스크린샷" lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>디바이스 삭제

1. [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)로 이동합니다.

1. **IoT Hub** 아이콘을 선택합니다.

1. 대상 디바이스가 연결된 IoT 허브를 선택합니다.

1. **IoT Edge** 를 선택한 다음, 대상 디바이스 ID 옆의 확인란을 선택합니다. **삭제** 를 선택하여 디바이스를 삭제합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="삭제 단추가 강조 표시된 IoT Edge 홈페이지 스크린샷":::

## <a name="eye-module-troubleshooting-tips"></a>Eye 모듈 문제 해결 팁

다음 문제 해결 팁은 비전 AI 프로토타입 환경에서 발견되는 몇 가지 일반적인 문제를 해결하는 데 도움이 됩니다.

### <a name="check-the-runtime-status-of-azureeyemodule"></a>azureeyemodule의 런타임 상태를 확인합니다.

**WebStreamModule** 에 문제가 있는 경우 비전 모델 추론을 처리하는 **azureeyemodule** 이 실행되고 있는지 확인합니다. 런타임 상태를 확인하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)로 이동한 다음, **모든 리소스** >  *\<your IoT hub>*  > **IoT Edge** >  *\<your device ID>* 로 이동합니다. 
1. **모듈** 탭을 선택하여 설치된 모든 모듈의 런타임 상태를 확인합니다.

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="디바이스 모듈 런타임 상태 화면 스크린샷" lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. **azureeyemodule** 의 런타임 상태가 **실행 중** 으로 표시되지 않는 경우 **모듈 설정** > **azureeyemodule** 을 선택합니다. 
1. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정하고 **업데이트** 를 선택합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="모듈 설정 구성 화면 스크린샷":::

### <a name="update-telemetryintervalneuralnetworkms"></a>TelemetryIntervalNeuralNetworkMs 업데이트

다음 개수 제한 오류가 발생하는 경우 azureeyemodule 모듈 쌍 설정에서 TelemetryIntervalNeuralNetworkMs 값을 업데이트해야 합니다.

|오류 메시지|
|------|
|IotHub ' xxxxxxxxx '의 총 메시지 수가 할당량을 초과했습니다. 허용되는 최대 메시지 수: '8,000', 현재 메시지 수: 'xxxx'. 다음 UTC 날까지 해당 허브 관련 송수신 작업이 차단됩니다. 할당량을 늘리려면 허브의 단위를 늘려 보세요.|

TelemetryIntervalNeuralNetworkMs는 신경망에서 메시지를 전송하는 빈도를 결정합니다. 메시지는 밀리초 단위로 전송됩니다. Azure 구독은 일일 메시지 수가 제한됩니다.

메시지 양은 구독 계층을 기반으로 합니다. 너무 많은 메시지를 전송하여 잠긴 경우에는 메시지 양을 늘립니다. 메시지 양이 12,000이면 12초마다 메시지 1개가 됩니다. 이 메시지 양은 하루에 7,200개의 메시지에 해당하므로 무료 구독의 메시지 한도인 8,000개 미만입니다.

TelemetryIntervalNeuralNetworkMs 값을 업데이트하려면 다음을 수행합니다.

1. [Azure Portal](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home)에 로그인하고 **모든 리소스** 를 엽니다.

1. **모든 리소스** 페이지에서 설치 중에 개발 키트에 프로비저닝된 IoT 허브의 이름을 선택합니다.

1. **IoT Hub** 페이지 왼쪽에 있는 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다. IoT Edge 디바이스 페이지에서 개발 키트의 디바이스 ID를 찾습니다. 개발 키트의 디바이스 ID를 선택하여 해당 IoT Edge 디바이스 페이지를 엽니다.

1. **모듈** 탭에서 **azureeyemodule** 을 선택합니다.

1. **azureeyemodule** 페이지에서 **모듈 ID 쌍** 을 엽니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="모듈 페이지 스크린샷" lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. **속성** 으로 스크롤합니다. **실행** 및 **로깅** 속성은 현재 활성화되지 않습니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="모듈 ID 쌍 속성 스크린샷" lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. **TelemetryIntervalNeuralNetworkMs** 값을 원하는 대로 업데이트하고 **저장** 아이콘을 선택합니다.

## <a name="view-device-rtsp-video-stream"></a>디바이스 RTSP 비디오 스트림 보기

[Azure Percept Studio](./how-to-view-video-stream.md) 또는 [VLC 미디어 플레이어](https://www.videolan.org/vlc/index.html)에서 디바이스의 RTSP 비디오 스트림을 확인합니다.

VLC 미디어 플레이어에서 RTSP 스트림을 열려면 **미디어** > **네트워크 스트림 열기** > **rtsp://[디바이스 IP 주소]:8554/result** 로 이동합니다.

## <a name="next-steps"></a>다음 단계

Azure Percept DK 인스턴스와 관련된 문제를 해결하는 방법에 대한 자세한 내용은 [일반적인 문제 해결 가이드](./troubleshoot-dev-kit.md)를 참조하세요.