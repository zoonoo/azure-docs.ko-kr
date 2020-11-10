---
title: 진단 로그 사용 및 쿼리
titleSuffix: Azure Digital Twins
description: 진단 설정으로 로깅을 사용 하도록 설정 하는 방법을 참조 하 고 즉시 볼 수 있도록 로그를 쿼리 합니다.
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 0d775ffa1ce063c01fc6762d77201e5a4caaad87
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411768"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins 문제 해결: 진단 로깅

Azure Digital Twins는 서비스 인스턴스에 대 한 로그를 수집 하 여 성능, 액세스 및 기타 데이터를 모니터링할 수 있습니다. 이러한 로그를 사용 하 여 Azure Digital Twins 인스턴스에서 발생 하는 상황을 파악 하 고, Azure 지원에 문의 하지 않고도 문제에 대 한 근본 원인 분석을 수행할 수 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com) 에서 [**진단 설정을 구성**](#turn-on-diagnostic-settings) 하 여 Azure Digital twins 인스턴스에서 로그 수집을 시작 하는 방법을 보여 줍니다. 로그를 저장할 위치를 지정할 수도 있습니다 (예: Log Analytics 또는 선택한 저장소 계정).

이 문서에는 Azure 디지털 쌍이 수집 하는 모든 [로그 범주](#log-categories) 및 [로그 스키마](#log-schemas) 의 목록도 포함 되어 있습니다.

로그를 설정한 후 [**로그를 쿼리하여**](#view-and-query-logs) 사용자 지정 정보를 신속 하 게 수집할 수도 있습니다.

## <a name="turn-on-diagnostic-settings"></a>진단 설정 켜기 

진단 설정을 켜서 Azure Digital Twins 인스턴스에서 로그 수집을 시작 합니다. 내보낸 로그를 저장 해야 하는 대상을 선택할 수도 있습니다. Azure Digital Twins 인스턴스에 대 한 진단 설정을 사용 하도록 설정 하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 메뉴에서 **진단 설정** 을 선택 하 고 **진단 설정을 추가** 합니다.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 뒤에 나오는 페이지에서 다음 값을 입력 합니다.
     * **진단 설정 이름** : 진단 설정에 이름을 지정 합니다.
     * **범주 세부 정보** : 모니터링할 작업을 선택 하 고 확인란을 선택 하 여 해당 작업에 대 한 진단을 사용 하도록 설정 합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        이러한 범주 및 해당 범주에 포함 된 정보에 대 한 자세한 내용은 아래 [*로그 범주*](#log-categories) 섹션을 참조 하세요.
     * **대상 세부 정보** : 로그를 보낼 위치를 선택 합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
        - Log Analytics에 보내기
        - 스토리지 계정에 보관
        - 이벤트 허브로 스트림

        대상 선택에 필요한 경우 추가 세부 정보를 입력 하 라는 메시지가 표시 될 수 있습니다.  
    
4. 새 설정을 저장합니다. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="사용자가 진단 설정 이름을 입력 한 진단 설정 페이지를 보여 주는 스크린샷 및 범주 세부 정보 및 대상 세부 정보에 대 한 몇 가지 확인란을 선택 했습니다. 저장 단추가 강조 표시 됩니다." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

새 설정은 약 10분 후에 적용됩니다. 그 후에는 해당 인스턴스에 대 한 **진단 설정** 페이지에서 로그가 구성 된 대상에 다시 표시 됩니다. 

진단 설정 및 설정 옵션에 대 한 자세한 내용은 [*진단 설정 만들기를 방문 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보낼*](../azure-monitor/platform/diagnostic-settings.md)수 있습니다.

## <a name="log-categories"></a>로그 범주

Azure Digital Twins가 수집 하는 로그의 범주에 대 한 자세한 내용은 다음과 같습니다.

| 로그 범주 | Description |
| --- | --- |
| ADTModelsOperation | 모델과 관련 된 모든 API 호출을 기록 합니다. |
| ADTQueryOperation | 쿼리와 관련 된 모든 API 호출을 기록 합니다. |
| ADTEventRoutesOperation | 이벤트 경로와 관련 된 모든 API 호출을 기록 하 고 Azure Digital Twins에서 Event Grid, Event Hubs 및 Service Bus와 같은 끝점 서비스로 이벤트를 송신 합니다. |
| ADTDigitalTwinsOperation | Azure Digital Twins와 관련 된 모든 API 호출을 기록 합니다. |

각 로그 범주는 쓰기, 읽기, 삭제 및 작업으로 구성 됩니다.  이러한 REST API 호출은 다음과 같이 호출 됩니다.

| 이벤트 유형 | REST API 작업 |
| --- | --- |
| 쓰기 | PUT 및 PATCH |
| 읽기 | GET |
| 삭제 | Delete |
| 작업 | POST |

다음은 각 범주에 기록 되는 작업 및 해당 [Azure Digital Twins REST API 호출](/rest/api/azure-digitaltwins/) 의 포괄적인 목록입니다. 

>[!NOTE]
> 각 로그 범주에는 여러 개의 작업/REST API 호출이 포함 됩니다. 아래 표에서 각 로그 범주는 다음 로그 범주가 나열 될 때까지 그 아래의 모든 작업/REST API 호출에 매핑됩니다. 

| 로그 범주 | 작업(Operation) | REST API 호출 및 기타 이벤트 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/모델/쓰기 | 디지털 쌍 모델 업데이트 API |
|  | DigitalTwins/모델/읽기 | ID 및 List Api를 통해 가져오는 디지털 쌍 모델 |
|  | DigitalTwins/모델/삭제 | 디지털 쌍 모델 삭제 API |
|  | DigitalTwins/모델/작업 | 디지털 쌍 모델 API 추가 |
| ADTQueryOperation | DigitalTwins/쿼리/동작 | 쿼리 Twins API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 이벤트 경로 API 추가 |
|  | DigitalTwins/eventroutes/읽기 | ID로 Get 이벤트 경로 및 목록 Api |
|  | DigitalTwins/eventroutes/삭제 | 이벤트 경로 Delete API |
|  | DigitalTwins/eventroutes/작업 | API 호출이 아닌 끝점 서비스에 이벤트를 게시 하는 동안 오류가 발생 했습니다. |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | Digital Twins 추가, 관계 추가, 업데이트, 업데이트 구성 요소 |
|  | DigitalTwins/DigitalTwins/읽기 | 디지털 쌍 ID로 가져오기, 구성 요소 가져오기, ID로 관계 가져오기, 들어오는 관계 나열, 관계 나열 |
|  | DigitalTwins/DigitalTwins/delete | 디지털 쌍 삭제, 관계 삭제 |
|  | DigitalTwins/DigitalTwins/작업 | 디지털 쌍 송신 구성 요소 원격 분석, 원격 분석 보내기 |

## <a name="log-schemas"></a>로그 스키마 

각 로그 범주에는 해당 범주의 이벤트를 보고 하는 방법을 정의 하는 스키마가 있습니다. 각 개별 로그 항목은 텍스트로 저장 되 고 JSON blob으로 서식이 지정 됩니다. 로그 및 예제 JSON 본문의 필드는 아래 각 로그 유형에 대해 제공 됩니다. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` 및은 `ADTQueryOperation` 일관 된 API 로그 스키마를 사용 하며, `ADTEventRoutesOperation` 별도의 스키마가 있습니다.

### <a name="api-log-schemas"></a>API 로그 스키마

이 로그 스키마는, 및에 대해 일치 `ADTDigitalTwinsOperation` `ADTModelsOperation` `ADTQueryOperation` 합니다. Azure Digital Twins 인스턴스에 대 한 API 호출과 관련 된 정보를 포함 합니다.

다음은 API 로그에 대 한 필드 및 속성 설명입니다.

| 필드 이름 | 데이터 형식 | Description |
|-----|------|-------------|
| `Time` | DateTime | 이 이벤트가 발생 한 날짜와 시간 (UTC)입니다. |
| `ResourceID` | String | 이벤트가 발생 한 리소스의 Azure Resource Manager 리소스 ID입니다. |
| `OperationName` | String  | 이벤트 중 수행 되는 동작의 유형입니다. |
| `OperationVersion` | String | 이벤트 중에 사용 된 API 버전 |
| `Category` | String | 내보내는 리소스의 형식입니다. |
| `ResultType` | String | 이벤트의 결과 |
| `ResultSignature` | String | 이벤트에 대 한 Http 상태 코드 |
| `ResultDescription` | String | 이벤트에 대 한 추가 정보 |
| `DurationMs` | String | 이벤트를 수행 하는 데 걸린 시간 (밀리초) |
| `CallerIpAddress` | String | 이벤트에 대 한 마스킹된 원본 IP 주소입니다. |
| `CorrelationId` | GUID | 고객이 이벤트에 대해 고유한 식별자를 제공 했습니다. |
| `Level` | String | 이벤트의 로깅 심각도입니다. |
| `Location` | String | 이벤트가 발생 한 지역입니다. |
| `RequestUri` | URI | 이벤트 중에 사용 된 끝점입니다. |

다음은 이러한 로그 유형에 대 한 예제 JSON 본문입니다.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>송신 로그 스키마

로그에 대 한 스키마입니다 `ADTEventRoutesOperation` . 여기에는 예외에 대 한 세부 정보와 Azure Digital Twins 인스턴스에 연결 된 송신 끝점에 대 한 API 작업이 포함 됩니다.

|필드 이름 | 데이터 형식 | Description |
|-----|------|-------------|
| `Time` | DateTime | 이 이벤트가 발생 한 날짜와 시간 (UTC)입니다. |
| `ResourceId` | String | 이벤트가 발생 한 리소스의 Azure Resource Manager 리소스 ID입니다. |
| `OperationName` | String  | 이벤트 중 수행 되는 동작의 유형입니다. |
| `Category` | String | 내보내는 리소스의 형식입니다. |
| `ResultDescription` | String | 이벤트에 대 한 추가 정보 |
| `Level` | String | 이벤트의 로깅 심각도입니다. |
| `Location` | String | 이벤트가 발생 한 지역입니다. |
| `EndpointName` | String | Azure Digital Twins에서 만든 송신 끝점의 이름 |

다음은 이러한 로그 유형에 대 한 예제 JSON 본문입니다.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>로그 보기 및 쿼리

이 문서의 앞부분에서 저장소 위치를 저장 하 고 지정 하는 로그 유형을 구성 했습니다.

이러한 로그에서 문제를 해결 하 고 통찰력을 생성 하기 위해 **사용자 지정 쿼리** 를 생성할 수 있습니다. 시작 하려면 서비스에서 제공 하는 몇 가지 예제 쿼리를 활용할 수도 있습니다. 이러한 쿼리는 고객이 해당 인스턴스에 대해 가질 수 있는 일반적인 질문을 해결 하는 데 사용 됩니다.

다음은 인스턴스에 대 한 로그를 쿼리 하는 방법입니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 메뉴에서 **로그** 를 선택 하 여 로그 쿼리 페이지를 엽니다. 페이지가 *쿼리* 라는 창으로 열립니다.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Azure Digital Twins 인스턴스에 대 한 로그 페이지를 보여 주는 스크린샷 DigitalTwin API 대기 시간 및 모델 API 대기 시간과 같은 다양 한 로그 옵션 후에 명명 된 미리 작성 된 쿼리를 보여 주는 쿼리 창에 중첩 됩니다." lightbox="media/troubleshoot-diagnostics/logs.png":::

    다음은 다양 한 로그에 대해 작성 된 미리 작성 된 예제 쿼리입니다. 쿼리 중 하나를 선택 하 여 쿼리 편집기에 로드 하 고 실행 하 여 인스턴스에 대 한 이러한 로그를 볼 수 있습니다.

    또한 사용자 지정 쿼리 코드를 작성 하거나 편집할 수 있는 쿼리 편집기 페이지로 바로 이동 하기 위해 아무것도 실행 하지 않고도 *쿼리* 창을 닫을 수 있습니다.

3. *쿼리* 창을 종료 한 후에는 주 쿼리 편집기 페이지가 표시 됩니다. 여기에서 예제 쿼리 텍스트를 보거나 편집 하거나 처음부터 직접 쿼리를 작성할 수 있습니다.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Azure Digital Twins 인스턴스에 대 한 로그 페이지를 보여 주는 스크린샷 쿼리 창이 사라지고 대신 다른 로그 목록, 편집 가능한 쿼리 코드를 표시 하는 편집 창 및 쿼리 기록을 보여 주는 창이 있습니다." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    왼쪽 창에서 
    - *테이블* 탭에는 쿼리에 사용할 수 있는 다양 한 Azure Digital twins [로그 범주가](#log-categories) 표시 됩니다. 
    - *쿼리* 탭에는 편집기에 로드할 수 있는 예제 쿼리가 포함 되어 있습니다.
    - *필터* 탭에서는 쿼리가 반환 하는 데이터의 필터링 된 뷰를 사용자 지정할 수 있습니다.

로그 쿼리와 이러한 쿼리를 작성 하는 방법에 대 한 자세한 내용은 Azure Monitor의 [*로그 쿼리 개요*](../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 진단을 구성 하는 방법에 대 한 자세한 내용은 [*Azure 리소스에서 로그 데이터 수집 및 사용*](../azure-monitor/platform/platform-logs-overview.md)을 참조 하세요.
* Azure Digital Twins 메트릭에 대 한 자세한 내용은 [*문제 해결: Azure Monitor 사용 하 여 메트릭 보기*](troubleshoot-metrics.md)를 참조 하세요.
* 메트릭에 대 한 경고를 사용 하도록 설정 하는 방법을 보려면 [*문제 해결: 경고 설정*](troubleshoot-alerts.md)을 참조 하세요.