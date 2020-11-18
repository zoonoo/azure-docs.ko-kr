---
title: 자습서 - Azure IoT Central(OpenVINO)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기
description: 이 자습서에서는 IoT Central에서 비디오 분석 애플리케이션을 만드는 방법을 보여줍니다. 이 애플리케이션을 만들어서 사용자 지정하고, 다른 Azure 서비스에 연결합니다. 이 자습서에서는 실시간 개체 검색을 위한 Intel OpenVINO&trade; 도구 키트를 사용합니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: af967c58cdeb2c750178141193a711a66af7477c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426744"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>자습서: Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기

솔루션 개발자로서 IoT Central *비디오 분석 - 개체 및 동작 감지* 애플리케이션 템플릿, Azure IoT Edge 디바이스, Azure Media Services 및 Intel의 하드웨어 최적화 OpenVINO&trade;를 사용하여 개체 및 동작 감지를 위한 비디오 분석 애플리케이션을 만드는 방법을 알아봅니다. 이 솔루션에서는 소매점을 사용하여 보안 카메라 모니터링이라는 일반적인 비즈니스 요구 사항을 충족하는 방법을 보여줍니다. 이 솔루션에서는 비디오 피드의 자동 개체 감지를 사용하여 관심 있는 이벤트를 신속하게 식별하고 찾습니다.

> [!TIP]
> 동작 감지 개체에 대해 OpenVINO&trade; 대신 YOLO v3를 사용하려면 [자습서: Azure IoT Central(YOLO v3)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기](tutorial-video-analytics-create-app-yolo-v3.md)를 참조하세요.

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>배포 매니페스트 편집

배포 매니페스트를 사용하여 IoT Edge 모듈을 배포합니다. IoT Central에서 매니페스트를 디바이스 템플릿으로 가져온 다음, IoT Central에서 모듈 배포를 관리하게 할 수 있습니다.

배포 매니페스트를 준비하는 방법은 다음과 같습니다.

1. 텍스트 편집기를 사용하여 *lva-configuration* 폴더에 저장한 *deployment.openvino.amd64.json* 파일을 엽니다.

1. 다음 코드 조각과 같이 `LvaEdgeGatewayModule` 설정을 찾아서 이미지 이름을 변경합니다.

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. `LvaEdgeGatewayModule` 섹션의 `env` 노드에 Media Services 계정 이름을 추가합니다. 앞에서 *scratchpad.txt* 파일에 Media Services 계정 이름을 기록해 두었습니다.

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. 이 템플릿은 IoT Central에서 이러한 속성을 공개하지 않으므로, 배포 매니페스트에 Media Services 구성 값을 추가해야 합니다. `lvaEdge` 모듈을 찾은 다음, 해당 자리 표시자를 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 값으로 바꿉니다.

    `azureMediaServicesArmId`는 앞에서 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 **리소스 ID** 입니다.

    앞에서 Media Services 계정의 서비스 주체를 만들 때 *scratchpad.txt* 파일에 `aadTenantId`, `aadServicePrincipalAppId` 및 `aadServicePrincipalSecret`을 기록해 두었습니다.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 변경 내용을 저장합니다.

이 자습서에서는 개체 및 동작 감지에 OpenVINO&trade; 모듈을 사용하도록 솔루션을 구성합니다. 다음 코드 조각은 모듈의 구성을 보여줍니다.

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>매니페스트 바꾸기

**LVA Edge 게이트웨이 v2** 페이지에서 **+ 매니페스트 바꾸기** 를 선택합니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="매니페스트 바꾸기":::

*lva-configuration* 폴더로 이동하고 이전에 편집한 *deployment.openvino.amd64.json* 매니페스트 파일을 선택합니다. **업로드** 를 선택합니다. 유효성 검사가 완료되면 **바꾸기** 를 선택합니다.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
