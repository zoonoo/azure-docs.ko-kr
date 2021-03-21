---
title: Azure Percept 비전 및 비전 모듈 관련 문제 해결
description: 시각 AI 프로토타입 환경에서 발견 된 몇 가지 일반적인 문제에 대 한 문제 해결 팁을 확인 하세요.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663515"
---
# <a name="vision-solution-troubleshooting"></a>비전 솔루션 문제 해결

Azure Percept Studio에서 코드 없는 비전 솔루션 문제 해결에 대 한 자세한 내용은 다음 지침을 참조 하세요.

## <a name="delete-a-vision-project"></a>비전 프로젝트 삭제

1. https://www.customvision.ai/projects로 이동합니다.

1. 삭제할 프로젝트를 마우스로 가리키고 휴지통 아이콘을 클릭 하 여 프로젝트를 삭제 합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="삭제 아이콘이 강조 표시 된 Custom Vision의 프로젝트 페이지입니다.":::

## <a name="check-which-modules-are-on-a-device"></a>장치에 있는 모듈 확인

1. [Azure 포털](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)로 이동합니다.

1. **Iot Hub** 아이콘을 클릭 합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Iot Hub 아이콘이 강조 표시 된 Azure Portal 홈페이지" lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. 대상 장치가 연결 된 IoT Hub를 선택 합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="IoT Hub의 목록입니다.":::

1. **IoT Edge** 를 선택 하 고 장치 **ID** 탭에서 장치를 클릭 합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="홈페이지를 IoT Edge 합니다.":::

1. 장치 모듈은 **모듈** 탭에 나열 됩니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="모듈 탭 콘텐츠를 표시 하는 선택한 장치에 대 한 Iot Edge 페이지입니다." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>디바이스 삭제

1. [Azure 포털](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home)로 이동합니다.

1. **Iot Hub** 아이콘을 클릭 합니다.

1. 대상 장치가 연결 된 IoT Hub를 선택 합니다.

1. **IoT Edge** 를 선택 하 고 대상 장치 ID 옆의 확인란을 선택 합니다. 휴지통 아이콘을 클릭 하 여 장치를 삭제 합니다.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Iot Edge 홈페이지에 강조 표시 된 삭제 아이콘":::

## <a name="eye-module-troubleshooting-tips"></a>눈 모듈 문제 해결 팁

**Webstreammodule** 에 문제가 있는 경우 비전 모델 추론 **azureeyemodule** 가 실행 중인지 확인 합니다. 런타임 상태를 확인 하려면 [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) 로 이동 하 여 IoT Edge **모든 리소스** 로 이동  ->  **\<your IoT hub>**  ->    ->  **\<your device ID>** 합니다. **모듈** 탭을 클릭 하 여 설치 된 모든 모듈의 런타임 상태를 확인 합니다.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="장치 모듈 런타임 상태 화면입니다." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

**Azureeyemodule** 의 런타임 상태가 **실행 중** 으로 표시 되지 않으면 **모듈**  ->  **azureeyemodule** 설정을 클릭 합니다. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정 하 고 **업데이트** 를 클릭 합니다.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="모듈 설정 구성 화면입니다.":::

## <a name="view-device-rtsp-video-stream"></a>장치 RTSP 비디오 스트림 보기

[Azure Percept Studio](./how-to-view-video-stream.md) 또는 [vlc media PLAYER](https://www.videolan.org/vlc/index.html)에서 장치의 RTSP 비디오 스트림을 확인 합니다.

Vlc 미디어 플레이어에서 rtsp 스트림을 열려면 **media**  ->  **open network stream**  ->  **RTSP://[장치 IP 주소]/result** 로 이동 합니다.

## <a name="next-steps"></a>다음 단계

Azure Percept에 대 한 문제 해결에 대 한 자세한 내용은 [일반 문제 해결 가이드](./troubleshoot-dev-kit.md) 를 참조 하세요.