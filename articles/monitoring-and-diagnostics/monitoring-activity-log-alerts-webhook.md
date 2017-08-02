---
title: "Azure 서비스 알림에 대한 활동 로그 경고 수신 | Microsoft Docs"
description: "Azure 서비스에서 문제가 발생할 때 SMS, 전자 메일 또는 웹후크를 통해 알림을 받습니다."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c8339c08333aaa471b42f6468ca2a59ee4efacf9
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 활동 로그 경고에 대한 웹후크
작업 그룹 정의의 일부로 활동 로그 경고 알림을 받기 위한 웹후크 끝점을 구성할 수 있습니다. 웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 이러한 알림을 다른 시스템으로 라우팅할 수 있습니다. 이 문서는 Webhook에 대한 HTTP POST의 페이로드 형태를 보여 줍니다.

한편 Azure 활동 로그 경고를 만드는 방법에 대한 자세한 내용은 [이 페이지를 대신 참조하세요](monitoring-activity-log-alerts.md).

작업 그룹을 만드는 방법에 대한 자세한 내용은 [이 페이지를 대신 참조하세요](monitoring-action-groups.md).

## <a name="authenticating-the-webhook"></a>웹후크 인증
웹후크는 토큰 기반 인증을 사용하여 인증할 수 있습니다. 토큰 ID를 사용하여 webhook URI를 저장합니다. 예를 들면 `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`와 같습니다.

## <a name="payload-schema"></a>페이로드 스키마
POST 작업에 포함된 JSON 페이로드는 페이로드 data.context.activityLog.eventSource 필드에 따라 다릅니다.

###<a name="common"></a>일반
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>관리
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

서비스 상태 알림 활동 로그 경고의 특정 스키마에 대한 세부 정보는 [여기를 클릭](monitoring-service-notifications.md), 다른 모든 활동 로그 경고의 특정 스키마에 대한 세부 정보는 [여기를 클릭](monitoring-overview-activity-logs.md)

| 요소 이름 | 설명 |
| --- | --- |
| status |메트릭 경고에 사용됩니다. 활동 로그 경고에 대해서는 항상 "activated"로 설정합니다. |
| context |이벤트에 대한 컨텍스트입니다. |
| resourceProviderName |영향을 받는 리소스의 리소스 공급자입니다. |
| conditionType |항상 "Event"입니다. |
| name |경고 규칙의 이름입니다. |
| id |경고의 리소스 ID입니다. |
| 설명 |경고를 만드는 동안 설정되는 경고 설명 입니다. |
| subscriptionId |Azure 구독 ID입니다. |
| timestamp |요청을 처리하는 Azure 서비스에서 이벤트를 생성한 시간입니다. |
| resourceId |영향을 받는 리소스의 리소스 ID입니다. |
| resourceGroupName |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| properties |이벤트에 대한 세부 정보를 포함하는 `<Key, Value>` 쌍의 집합(즉, `Dictionary<String, String>`)입니다. |
| event |이벤트에 대한 메타데이터가 포함된 요소입니다. |
| 권한 부여 |이벤트의 RBAC 속성입니다. 이러한 속성에는 일반적으로 “action”, “role” 및 “scope”이 포함됩니다. |
| 카테고리 |이벤트의 범주. 지원되는 값으로 Administrative, Alert, Security, ServiceHealth, Recommendation이 있습니다. |
| caller |가용성에 기반한 작업, UPN 클레임 또는 SPN 클레임을 수행한 사용자의 메일 주소입니다. 특정 시스템 호출의 경우 null일 수 있습니다. |
| CorrelationId |일반적으로 문자열 형식의 GUID. correlationId가 있는 이벤트는 동일한 상위 작업에 속하며 일반적으로 correlationId를 공유합니다. |
| eventDescription |이벤트에 대한 정적 텍스트 설명입니다. |
| eventDataId |이벤트에 대한 고유 식별자입니다. |
| eventSource |이벤트를 생성한 Azure 서비스 또는 인프라의 이름입니다. |
| httpRequest |요청은 일반적으로 “clientRequestId”, “clientIpAddress” 및 HTTP “method”(예: PUT)를 포함합니다. |
| 최소 수준 |“Critical”, “Error”, “Warning”, “Informational” 및 “Verbose” 값 중 하나입니다. |
| operationId |일반적으로 이벤트 간에 공유되는 GUID로서 단일 작업에 해당합니다. |
| operationName |작업의 이름입니다. |
| properties |이벤트의 속성입니다. |
| status |문자열입니다. 작업의 상태입니다. 일반적인 값으로 "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved"가 포함됩니다. |
| subStatus |일반적으로 해당 REST 호출의 HTTP 상태 코드를 포함합니다. 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 일반적인 하위 상태 값으로 OK (HTTP Status Code: 200), Created (HTTP Status Code: 201), Accepted (HTTP Status Code: 202), No Content (HTTP Status Code: 204), Bad Request (HTTP Status Code: 400), Not Found (HTTP Status Code: 404), Conflict (HTTP Status Code: 409), Internal Server Error (HTTP Status Code: 500), Service Unavailable (HTTP Status Code: 503), Gateway Timeout (HTTP Status Code: 504)이 있습니다. |

## <a name="next-steps"></a>다음 단계
* [활동 로그에 대한 자세한 내용](monitoring-overview-activity-logs.md)
* [Azure 경고에 대한 Azure Automation 스크립트 실행 (Runbooks)](http://go.microsoft.com/fwlink/?LinkId=627081)
* [논리 앱을 사용하여 Azure 경고에서 Twilio 통해 SMS 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.
* [논리 앱을 사용하여 Azure 경고에서 Azure Queue에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). 이 예제는 메트릭 경고를 위한 것이지만 활동 로그 경고도 지원하도록 수정될 수 있습니다.

