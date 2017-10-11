---
title: "Azure 활동 로그 이벤트 스키마 | Microsoft 문서"
description: "활동 로그로 내보내는 데이터의 이벤트 스키마에 대해 설명합니다."
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.openlocfilehash: a4ceb822e0ec3e1c1dc31ece1db761834e795f6c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="azure-activity-log-event-schema"></a>Azure 활동 로그 이벤트 스키마
**Azure 활동 로그**는 Azure에서 발생한 모든 구독 수준 이벤트에 대한 정보를 제공하는 로그입니다. 이 문서에서는 데이터 범주별 이벤트 스키마에 대해 설명합니다.

## <a name="administrative"></a>관리
이 범주에는 Resource Manager를 통해 수행한 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드가 포함되어 있습니다. 이 범주에 표시되는 이벤트의 유형의 예로는 "가상 컴퓨터 만들기", "네트워크 보안 그룹 삭제" 등이 있습니다. 사용자나 응용 프로그램이 Resource Manager를 사용하여 취하는 모든 동작은 특정 리소스 종류에 대한 작업으로 모델링됩니다. 작업 유형이 쓰기, 삭제 또는 동작이면 해당 작업의 시작 및 성공이나 실패 레코드가 모두 관리 범주에 기록됩니다. 관리 범주에는 구독의 역할 기반 액세스 제어 변경 내용도 포함됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| authorization |이벤트의 RBAC 속성 Blob입니다. 일반적으로 "action", "role" 및 "scope" 속성이 포함됩니다. |
| caller |가용성을 기반으로 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. |
| channels |"Admin", "Operation" 값 중 하나여야 합니다. |
| claims |Resource Manager에서 이 작업을 수행하기 위해 사용자 또는 응용 프로그램을 인증하는 데 Active Directory에서 사용하는 JWT 토큰입니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 correlationId를 공유하는 이벤트입니다. |
| 설명 |이벤트의 정적 텍스트 설명입니다. |
| eventDataId |이벤트의 고유 식별자입니다. |
| httpRequest |Http 요청을 설명하는 Blob입니다. 일반적으로 "clientRequestId", "clientIpAddress" 및 "method"(PUT 등의 HTTP 메서드) 포함. |
| 최소 수준 |이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자 이름입니다. |
| resourceId |영향을 받는 리소스의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| status |작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드이지만 다음과 같이 하위 상태를 설명하는 다른 문자열을 포함할 수 있습니다. 예를 들어 이러한 일반적인 값은 다음과 같습니다. OK(HTTP 상태 코드: 200), Created(HTTP 상태 코드: 201), Accepted(HTTP 상태 코드: 202), No Content(HTTP 상태 코드: 204), Bad Request(HTTP 상태 코드: 400), Not Found(HTTP 상태 코드: 404), Conflict(HTTP 상태 코드: 409), Internal Server Error(HTTP 상태 코드: 500), Service Unavailable(HTTP 상태 코드:503), Gateway Timeout(HTTP 상태 코드: 504). |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

## <a name="service-health"></a>서비스 상태
이 범주에는 Azure에서 발생한 모든 서비스 상태 관련 인시던트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "미국 동부의 SQL Azure가 가동 중지 상태입니다." 등이 있습니다. 서비스 상태 이벤트는 작업 필요, 복구 지원, 인시던트, 유지 관리, 정보 또는 보안의 5가지 중 하나이며 구독에 이벤트의 영향을 받는 리소스가 있는 경우에만 표시됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```

### <a name="property-descriptions"></a>속성 설명
요소 이름 | 설명
-------- | -----------
channels | “Admin”, “Operation” 값 중 하나입니다.
CorrelationId | 일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 이벤트는 일반적으로 동일한 correlationId를 공유합니다.
설명 | 이벤트의 설명입니다.
eventDataId | 이벤트의 고유 식별자입니다.
eventName | 이벤트의 제목입니다.
level | 이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose”
resourceProviderName | 영향을 받는 리소스의 리소스 공급자 이름입니다. 알 수 없는 경우 null로 설정됩니다.
resourceType| 영향을 받는 리소스의 리소스 형식입니다. 알 수 없는 경우 null로 설정됩니다.
subStatus | 서비스 상태 이벤트의 경우 대개 null입니다.
eventTimestamp | 로그 이벤트가 생성되어 활동 로그로 제출된 시간의 타임스탬프입니다.
submissionTimestamp |   활동 로그에서 이벤트를 사용할 수 있게 된 시간의 타임스탬프입니다.
subscriptionId | 이 이벤트가 로깅된 Azure 구독입니다.
status | 작업의 상태를 설명하는 문자열. 일반적인 값으로는 Active, Resolved 등이 있습니다.
operationName | 작업의 이름입니다. 보통 Microsoft.ServiceHealth/incident/action입니다.
카테고리 | "ServiceHealth"
resourceId | 영향을 받는 리소스의 리소스 ID(확인된 경우)입니다. 확인되지 않은 경우에는 구독 ID가 제공됩니다.
Properties.title | 이 통신에 대한 지역화된 제목입니다. 기본 언어는 영어입니다.
Properties.communication | HTML 태그와 통신에 대한 지역화된 세부 정보입니다. 기본값은 영어입니다.
Properties.incidentType | 가능한 값: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | 이 이벤트가 연결된 인시던트를 식별합니다. 인시던트와 관련된 이벤트를 상호 연결할 때 사용합니다.
Properties.impactedServices | 인시던트에 의해 영향을 받는 서비스 및 지역을 설명하는 이스케이프된 JSON Blob입니다. 각각 ServiceName과 ImpactedRegions 목록을 포함하는 서비스 목록으로, 각 ImpactedRegions에는 RegionName이 포함됩니다.
Properties.defaultLanguageTitle | 통신은 영어로 이루어집니다.
Properties.defaultLanguageContent | 영어로 통신은 html 태그 또는 일반 텍스트로 수행됩니다.
Properties.stage | AssistedRecovery, ActionRequired, Information, Incident, Security에 대해 가능한 값: Active, Resolved. Maintenance에 대해 가능한 값: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | 이 이벤트가 연결된 통신입니다.

## <a name="alert"></a>경고
이 범주에는 모든 Azure 경고 활성화의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "지난 5분 동안 myVM의 CPU 사용률이 80%를 초과했습니다." 등이 있습니다. 다수의 Azure 시스템에서 경고 개념이 사용됩니다. 일종의 규칙을 정의하여 조건이 해당 규칙과 일치하면 알림을 수신할 수 있습니다. 지원되는 Azure 경고 유형이 '활성화'되거나 알림 생성을 위한 조건이 충족될 때마다 활성화 레코드도 이 활동 로그 범주로 푸시됩니다.

### <a name="sample-event"></a>샘플 이벤트

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| caller | Always Microsoft.Insights/alertRules |
| channels | 항상 "Admin, Operation"입니다. |
| claims | 경고 엔진의 SPN(서비스 사용자 이름) 또는 리소스 종류가 포함된 JSON Blob입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| 설명 |경고 이벤트의 정적 텍스트 설명입니다. |
| eventDataId |경고 이벤트의 고유 식별자입니다. |
| level |이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” |
| resourceGroupName |메트릭 경고의 경우 영향을 받는 리소스의 리소스 그룹 이름입니다. 기타 경고 유형의 경우에는 경고 자체가 포함된 리소스 그룹의 이름입니다. |
| resourceProviderName |메트릭 경고의 경우 영향을 받는 리소스의 리소스 공급자 이름입니다. 기타 경고 유형의 경우에는 경고 자체의 리소스 공급자 이름입니다. |
| resourceId | 메트릭 경고의 경우 영향을 받는 리소스의 리소스 ID 이름입니다. 기타 경고 유형의 경우에는 경고 리소스 자체의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| status |작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | 경고의 경우 대개 null입니다. |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

### <a name="properties-field-per-alert-type"></a>경고 유형별 속성 필드
속성 필드에는 경고 이벤트의 원본에 따라 다른 값이 포함됩니다. 일반적으로 사용되는 두 경고 이벤트 공급자는 활동 로그 경고와 메트릭 경고입니다.

#### <a name="properties-for-activity-log-alerts"></a>활동 로그 경고의 속성
| 요소 이름 | 설명 |
| --- | --- |
| properties.subscriptionId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 구독 ID입니다. |
| properties.eventDataId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 이벤트 데이터 ID입니다. |
| properties.resourceGroup | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 리소스 그룹입니다. |
| properties.resourceId | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 리소스 ID입니다. |
| properties.eventTimestamp | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 이벤트 타임스탬프입니다. |
| properties.operationName | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 작업 이름입니다. |
| properties.status | 이 활동 로그 경고 규칙을 활성화한 활동 로그 이벤트의 상태입니다.|

#### <a name="properties-for-metric-alerts"></a>메트릭 경고의 속성
| 요소 이름 | 설명 |
| --- | --- |
| properties.RuleUri | 메트릭 경고 규칙 자체의 리소스 ID입니다. |
| properties.RuleName | 메트릭 경고 규칙의 이름입니다. |
| properties.RuleDescription | 경고 규칙에 정의된 메트릭 경고 규칙의 설명입니다. |
| properties.Threshold | 메트릭 경고 규칙의 평가에 사용되는 임계값입니다. |
| properties.WindowSizeInMinutes | 메트릭 경고 규칙의 평가에 사용되는 창 크기입니다. |
| properties.Aggregation | 메트릭 경고 규칙에 정의된 집계 유형입니다. |
| properties.Operator | 메트릭 경고 규칙의 평가에 사용되는 조건부 연산자입니다. |
| properties.MetricName | 메트릭 경고 규칙의 평가에 사용되는 메트릭의 이름입니다. |
| properties.MetricUnit | 메트릭 경고 규칙의 평가에 사용되는 메트릭의 메트릭 단위입니다. |

## <a name="autoscale"></a>Autoscale
이 범주에는 구독에서 정의한 자동 크기 조정 설정을 기준으로 하는 자동 크기 조정 엔진 작업 관련 이벤트의 레코드가 포함됩니다. 이 범주에 표시되는 이벤트 유형의 예로는 "자동 크기 조정 강화 동작이 실패했습니다." 등이 있습니다 자동 크기 조정을 사용하면 자동 크기 조정 설정을 사용하여 시간 및/또는 로드(메트릭) 데이터를 기준으로 지원되는 리소스 종류의 인스턴스 수를 자동으로 규모 확장하거나 규모 감축할 수 있습니다. 강화 또는 규모 축소 조건이 충족되면 시작 이벤트와 성공 또는 실패 이벤트가 이 범주에 기록됩니다.

### <a name="sample-event"></a>샘플 이벤트
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>속성 설명
| 요소 이름 | 설명 |
| --- | --- |
| caller | 항상 Microsoft.Insights/autoscaleSettings입니다. |
| channels | 항상 "Admin, Operation"입니다. |
| claims | 자동 크기 조정 엔진의 SPN(서비스 사용자 이름) 또는 리소스 종류가 포함된 JSON Blob입니다. |
| CorrelationId | 문자열 형식의 GUID입니다. |
| 설명 |자동 크기 조정 이벤트의 정적 텍스트 설명입니다. |
| eventDataId |자동 크기 조정 이벤트의 고유 식별자입니다. |
| level |이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” |
| resourceGroupName |자동 크기 조정 설정의 리소스 그룹 이름입니다. |
| resourceProviderName |자동 크기 조정 설정의 리소스 공급자 이름입니다. |
| resourceId |자동 크기 조정 설정의 리소스 ID입니다. |
| operationId |단일 작업에 해당하는 이벤트 간에 공유되는 GUID입니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트에 대한 세부 정보를 설명하는 `<Key, Value>` 쌍의 집합(즉, 사전)입니다. |
| properties.Description | 자동 크기 조정 엔진이 수행 중이었던 작업의 자세한 설명입니다. |
| properties.ResourceName | 영향을 받는 리소스(크기 조정 동작을 수행 중이었던 리소스)의 리소스 ID입니다. |
| properties.OldInstancesCount | 자동 크기 조정 동작이 적용되기 전의 인스턴스 수입니다. |
| properties.NewInstancesCount | 자동 크기 조정 동작이 적용된 후의 인스턴스 수입니다. |
| properties.LastScaleActionTime | 자동 크기 조정이 수행된 시간의 타임스탬프입니다. |
| status |작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | 자동 크기 조정의 경우 대개 null입니다. |
| eventTimestamp |이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다. |
| submissionTimestamp |이벤트를 쿼리할 수 있게 되는 타임스탬프입니다. |
| subscriptionId |Azure 구독 ID입니다. |

## <a name="next-steps"></a>다음 단계
* [활동 로그(이전의 감사 로그)에 대해 자세히 알아보기](monitoring-overview-activity-logs.md)
* [Azure 활동 로그를 Event Hubs로 스트림](monitoring-stream-activity-logs-event-hubs.md)
