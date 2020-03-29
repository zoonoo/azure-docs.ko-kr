---
title: Azure 보안 센터 경고에 대한 스키마
description: 이 문서에서는 보안 경고에 대해 Azure 보안 센터에서 사용하는 다양한 스키마에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062963"
---
# <a name="security-alerts-schemas"></a>보안 경고 스키마

Azure Security Center의 표준 계층 사용자는 보안 센터에서 리소스에 대한 위협을 감지하면 보안 경고를 받습니다.

Azure 보안 센터의 **위협 보호** 페이지 또는 다음과 같은 외부 도구를 통해 이러한 보안 경고를 볼 수 있습니다.

- [Azure 센티넬](https://docs.microsoft.com/azure/sentinel/) - 마이크로 소프트의 클라우드 네이티브 SIEM. Sentinel 커넥터는 Azure 보안 센터에서 경고를 받고 Azure Sentinel의 [로그 분석 작업 영역으로](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 보냅니다.
- 타사 SIEM - 보안 센터의 [지속적인 내보내기](continuous-export.md) 도구를 사용하여 [Azure 이벤트 허브로](https://docs.microsoft.com/azure/event-hubs/)데이터를 보냅니다. 그런 다음 이벤트 허브 데이터를 타사 SIEM과 통합합니다.
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) - REST API를 사용하여 경고에 액세스하는 경우 [온라인 경고 API 설명서를](https://docs.microsoft.com/rest/api/securitycenter/alerts)참조하십시오.

프로그래밍 방식으로 경고를 사용하는 경우 관련 필드를 찾으려면 올바른 스키마가 필요합니다. 또한 이벤트 허브로 내보내거나 일반 HTTP 커넥터를 사용하여 워크플로 자동화를 트리거하려는 경우 스키마를 사용하여 JSON 개체를 올바르게 구문 분석합니다.

>[!IMPORTANT]
> 스키마는 이러한 각 시나리오에 대해 약간 다르므로 아래관련 탭을 선택해야 합니다.


## <a name="the-schemas"></a>스키마 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[워크플로 자동화 및 이벤트 허브로의 지속적인 내보내기](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>로직 앱, 이벤트 허브 및 타사 SIEM으로 전송된 경고에 대한 샘플 JSON

아래에서 전달된 경고 이벤트의 스키마를 확인할 수 있습니다.

- 보안 센터의 워크플로 자동화에서 구성된 Azure Logic 앱 인스턴스
- 보안 센터의 지속적인 내보내기 기능을 사용하는 Azure 이벤트 허브

워크플로 자동화 기능에 대한 자세한 내용은 [경고 및 권장 사항에 대한 응답 자동화를](workflow-automation.md)참조하십시오.
지속적인 내보내기에 대한 자세한 내용은 [내보내기 경고 및 권장 사항을](continuous-export.md)참조하십시오.

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure 센티넬 및 로그 분석 작업 영역](#tab/schema-sentinel)

Sentinel 커넥터는 Azure 보안 센터에서 경고를 받고 Azure Sentinel의 로그 분석 작업 영역으로 보냅니다. 

보안 센터 경고를 사용하여 Sentinel 사례 또는 인시던트를 만들려면 아래에 표시된 경고에 대한 스키마가 필요합니다. 

Azure Sentinel에 대한 자세한 내용은 [설명서를](https://docs.microsoft.com/azure/sentinel/)참조하십시오.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 동작 로그](#tab/schema-activitylog)

Azure Security Center는 생성된 보안 경고를 Azure 활동 로그의 이벤트로 감사합니다.

다음과 같이 활성화 경고 이벤트를 검색하여 활동 로그에서 보안 경고 이벤트를 볼 수 있습니다.

[![활성화 경고 이벤트에 대한 활동 로그 검색](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Azure 활동 로그에 전송된 경고에 대한 샘플 JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

|필드|설명|
|----|----|
|**채널**|상수, "작업"|
|**Correlationid**|Azure 보안 센터 경고 ID|
|**설명**|경고에 대한 설명|
|**eventDataId**|상관 관계 ID 참조|
|**Eventname**|값 및 지역화된Value 하위 필드에경고 표시 이름이 포함됩니다.|
|**범주**|값 및 지역화가치 하위 필드는 일정합니다 - "보안"|
|**이벤트타임스탬프**|경고가 생성된 시UTC 타임스탬프|
|**id**|정규화된 경고 ID|
|**수준**|상수, "정보"|
|**작업ID**|상관 관계 ID 참조|
|**작업이름**|값 필드는 일정합니다 - "Microsoft.Security/위치/경고/활성화/작업", 지역화된 값은 "활성화 경고"(잠재적으로 사용자 로캘과 지역화될 수 있음)가 됩니다.|
|**리소스 그룹 이름**|리소스 그룹 이름이 포함됩니다.|
|**resourceProviderName**|값 및 지역화가치 하위 필드는 일정합니다 - "Microsoft.Security"|
|**Resourcetype**|값 및 지역화가치 하위 필드는 일정합니다 - "Microsoft.Security/위치/경고"|
|**Resourceid**|정규화된 Azure 리소스 ID|
|**상태**|값 및 지역화가치 하위 필드는 일정합니다 - "활성"|
|**하위 상태**|값 및 지역화가치 하위 필드가 비어 있습니다.|
|**submissionTimestamp**|활동 로그에 이벤트 제출의 UTC 타임스탬프|
|**구독ID**|손상된 리소스의 구독 ID|
|**속성**|경고와 관련된 추가 속성의 JSON 가방입니다. 이러한 경고는 한 경고에서 다른 경고로 변경될 수 있지만 다음 필드는 모든 경고에 표시됩니다.<br>- 심각도 : 공격의 심각도<br>- 손상된 엔터티: 손상된 리소스의 이름<br>- 수정 단계: 취해야 할 수정 단계의 배열<br>- 의도 : 경고의 킬 체인 의도. 가능한 의도는 의도 [테이블에 문서화되어 있습니다.](alerts-reference.md#intentions)|
|**relatedEvents**|상수 - 빈 배열|
|||





### <a name="ms-graph-api"></a>[MS 그래프 API](#tab/schema-graphapi)

마이크로소프트 그래프는 마이크로소프트 365의 데이터와 인텔리전스에 대한 관문이다. Office 365, Windows 10 및 엔터프라이즈 이동성 + 보안에서 엄청난 양의 데이터에 액세스하는 데 사용할 수 있는 통합 프로그래밍 기능 모델을 제공합니다. Microsoft Graph의 풍부한 데이터를 사용하여 수백만 명의 사용자와 상호 작용하는 조직 및 소비자를 위한 앱을 빌드합니다.

MS Graph로 전송된 보안 경고에 대한 스키마 및 JSON 표현은 [Microsoft 그래프 설명서에서](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)확인할 수 있습니다.

---


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Security Center의 위협 보호 도구가 보안 경고 정보를 보낼 때 사용하는 스키마에 대해 설명했습니다.

보안 센터 외부에서 보안 경고에 액세스하는 방법에 대한 자세한 내용은 다음 페이지를 참조하십시오.

- [Azure 센티넬](https://docs.microsoft.com/azure/sentinel/) - 마이크로소프트의 클라우드 네이티브 SIEM
- [Azure 이벤트 허브](https://docs.microsoft.com/azure/event-hubs/) - Microsoft의 완전히 관리되는 실시간 데이터 수집 서비스
- 보안 센터의 [지속적인 내보내기 기능](continuous-export.md)
- [로그 분석 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) - Azure Monitor는 데이터 및 구성 정보를 포함하는 컨테이너인 로그 분석 작업 영역에 로그 데이터를 저장합니다.
