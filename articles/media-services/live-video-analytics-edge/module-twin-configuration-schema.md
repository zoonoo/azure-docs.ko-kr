---
title: Live Video Analytics의 모듈 쌍 JSON 스키마 - Azure
description: 이 항목에서는 IoT Edge의 Azure Live Video Analytics 모듈 쌍 JSON 스키마에 대해 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: be80b5782d76f1ec490395013310058f1cb2fe79
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371174"
---
# <a name="module-twin-json-schema-in-live-video-analytics"></a>Live Video Analytics의 모듈 쌍 JSON 스키마

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

디바이스 쌍은 메타데이터, 구성, 조건 등 디바이스 상태 정보가 저장된 JSON 문서입니다. Azure IoT Hub는 IoT Hub에 연결하는 디바이스마다 하나의 디바이스 쌍을 유지합니다. 자세한 내용은 [IoT Hub의 모듈 쌍 이해 및 사용](../../iot-hub/iot-hub-devguide-module-twins.md)을 참조하세요.

이 항목에서는 IoT Edge의 Live Video Analytics 모듈 쌍 JSON 스키마에 대해 설명합니다.

> [!NOTE]
> Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. 자세한 내용은 [Azure Media Services API 액세스](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)를 참조하세요.

## <a name="module-twin-properties"></a>모듈 쌍 속성

IoT Edge의 Live Video Analytics는 다음 모듈 쌍 속성을 노출합니다. 

|속성 |필수 |동적 |Description |
|---|---|---|---|
|applicationDataDirectory |예 |예 |구성을 유지하기 위해 탑재된 볼륨의 경로입니다. |
|azureMediaServicesArmId |예 |예 |Media Services 계정의 고유한 Azure 리소스 관리 식별자입니다.|
|aadTenantId |예 |예 |고객 Azure AD 테넌트 ID입니다.|
|aadServicePrincipalAppId |예 |예 |고객이 만든 Azure AD AppId입니다.|
|aadServicePrincipalCertificate |예<sup>*</sup>  |yes |고객이 만든 Azure AD AppId 인증서입니다.|
|aadServicePrincipalPassword |예<sup>*</sup>  |yes |고객이 Azure AD AppId를 암호를 만들었습니다.|
|aadEndpoint |예 |예 |클라우드 특정 Azure AD 엔드포인트입니다. <br/>기본값: `https://login.microsoftonline.com` |
|aadResourceId |예 |예 |클라우드별 Azure AD 대상 그룹/리소스 ID입니다. <br/>기본값: `https://management.core.windows.net/` |
|armEndpoint |예 |예 |클라우드별 Azure 리소스 관리 엔드포인트입니다. <br/>기본값: `https://management.azure.com/` |
|diagnosticsLevel |예 |예 |이벤트의 세부 정보 표시 수준입니다. <br/>정보 &#x02758; 경고 &#x02758; 오류 &#x02758; 위험 &#x02758; 없음 |
|diagnosticsEventsOutputName |예 |예 |진단 이벤트에 대한 허브 출력입니다. <br/>(비어 있는 경우 진단이 게시되지 않았음을 의미함)|
|operationalEventsOutputName|예|예|작업 이벤트에 대한 허브 출력입니다.<br/>(비어 있는 경우 작업 이벤트가 게시되지 않았음을 의미함)
|logLevel|예|예|다음 중 하나 <br/>&#x000B7; 세부 정보<br/>&#x000B7; 정보(기본값)<br/>&#x000B7; 경고<br/>&#x000B7; 오류<br/>&#x000B7; 없음|
|logCategories|예|예|쉼표로 구분된 애플리케이션, MediaPipeline, 이벤트 목록입니다. <br/>기본값: 애플리케이션, 이벤트|
|debugLogsDirectory|예|예|디버그 로그 디렉터리입니다. 디렉터리가 있으면 로그를 생성하고, 없으면 디버그 로그를 사용하지 않도록 설정합니다.

<sup>*</sup>서비스 주체 인증서 또는 암호를 제공해야 합니다. 

모듈을 다시 시작하지 않고도 동적 속성을 업데이트할 수 있습니다. [Media Services API에 대한 액세스 권한 얻기](../latest/access-api-howto.md) 문서에 있는 지침에 따라 여러 속성의 값을 얻을 수 있습니다. 

선택적 진단 설정의 역할에 대한 자세한 내용은 [모니터링 및 로깅](monitoring-logging.md) 문서를 참조하세요.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>다음 단계

[직접 메서드](direct-methods.md)
