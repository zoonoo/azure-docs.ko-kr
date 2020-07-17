---
title: 모듈 쌍 JSON 스키마-Azure
description: 이 항목에서는 IoT Edge에서 라이브 비디오 분석의 모듈 쌍 JSON 스키마에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a342c59b35c7ebb4b6021163da76bdd3e0d449c3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84266817"
---
# <a name="module-twin-json-schema"></a>모듈 쌍 JSON 스키마

장치 쌍은 메타 데이터, 구성 및 조건을 포함 하 여 장치 상태 정보를 저장 하는 JSON 문서입니다. Azure IoT Hub는 IoT Hub에 연결하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다. 자세한 설명은 [IoT Hub에서 모듈 쌍 이해 및 사용](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) 을 참조 하세요.

이 항목에서는 IoT Edge에서 라이브 비디오 분석의 모듈 쌍 JSON 스키마에 대해 설명 합니다.

> [!NOTE]
> Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. 자세한 내용은 [AZURE MEDIA SERVICES API 액세스](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api)를 참조 하세요.

## <a name="module-twin-properties"></a>모듈 쌍 속성

IoT Edge의 Live Video Analytics는 다음 모듈 쌍 속성을 노출 합니다. 

|속성 |필수 |동적 |설명 |
|---|---|---|---|
|applicationDataDirectory |예 |아니요 |구성을 유지 하기 위한 탑재 된 볼륨의 경로입니다. |
|azureMediaServicesArmId |예 |아니요 |Media Services 계정의 고유한 Azure 리소스 관리 식별자입니다.|
|aadTenantId |예 |아니요 |고객 Azure AD 테 넌 트 ID입니다.|
|aadServicePrincipalAppId |예 |예 |고객이 Azure AD AppId를 만들었습니다.|
|aadServicePrincipalCertificate |예로<sup>*</sup>  |예 |고객이 Azure AD AppId 인증서를 만들었습니다.|
|aadServicePrincipalPassword |예로<sup>*</sup>  |예 |고객이 Azure AD AppId 암호를 만들었습니다.|
|aadEndpoint |아니요 |아니요 |클라우드 별 Azure AD 끝점. <br/>기본값: `https://login.microsoftonline.com` |
|aadResourceId |아니요 |아니요 |클라우드 별 Azure AD 사용자/리소스 ID <br/>기본값: `https://management.core.windows.net/` |
|armEndpoint |아니요 |아니요 |클라우드 별 Azure 리소스 관리 끝점입니다. <br/>기본값: `https://management.azure.com/` |
|diagnosticsLevel |예 |예 |이벤트의 자세한 정도: <br/>정보 & # x02758; 경고 & # x02758; 오류 & # x02758; 중요 & # x02758; 없음을 |
|diagnosticsEventsOutputName |예 |예 |진단 이벤트에 대 한 허브 출력입니다. <br/>(Empty는 진단이 게시 되지 않았음을 의미 함)|
|operationalEventsOutputName|예|예|작업 이벤트에 대 한 허브 출력입니다.<br/>(Empty는 운영 이벤트가 게시 되지 않음을 의미 함)
|logLevel|예|예|다음 중 하나 <br/>& # x000B7; 구문<br/>& # x000B7; 정보 (기본값)<br/>& # x000B7; 내용의<br/>& # x000B7; 메시지가<br/>& # x000B7; 없음을|
|logCategories|예|예|응용 프로그램, MediaPipeline, 이벤트를 쉼표로 구분한 목록입니다. <br/>기본값: 응용 프로그램, 이벤트|
|debugLogsDirectory|예|예|디버그 로그의 디렉터리입니다. 존재 로그가 생성 된 경우에는 없는 경우 디버그 로그를 사용할 수 없습니다.

<sup>*</sup>서비스 사용자 인증서 또는 암호를 제공 해야 합니다. 

모듈을 다시 시작 하지 않고도 동적 속성을 업데이트할 수 있습니다. [MEDIA SERVICES API에 대 한 액세스 권한](../latest/access-api-cli-how-to.md) 문서에 있는 지침에 따라 이러한 속성 중 일부에 대 한 값을 가져올 수 있습니다. 

선택적 진단 설정의 역할에 대 한 자세한 내용은 [모니터링 및 로깅](monitoring-logging.md) 문서를 참조 하세요.

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
