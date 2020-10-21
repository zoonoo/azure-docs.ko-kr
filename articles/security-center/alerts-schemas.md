---
title: Azure Security Center 경고에 대 한 스키마
description: 이 문서에서는 Azure Security Center에서 보안 경고에 사용 하는 다양 한 스키마에 대해 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 082f246437cdd99b844d1ed8010d8dc846fc4d47
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341943"
---
# <a name="security-alerts-schemas"></a>보안 경고 스키마

구독에서 Azure Defender를 사용 하는 경우 리소스에 대 한 위협을 검색할 Security Center 때 보안 경고가 표시 됩니다.

Azure Security Center의 **위협 방지** 페이지에서 또는 다음과 같은 외부 도구를 통해 이러한 보안 경고를 볼 수 있습니다.

- [Azure 센티널](../sentinel/index.yml) -Microsoft의 클라우드 기본 siem. 센티널 커넥터는 Azure Security Center에서 경고를 가져오고 Azure 센티널의 [Log Analytics 작업 영역](../azure-monitor/learn/quick-create-workspace.md) 으로 보냅니다.
- 타사 SIEMs-Security Center의 [연속 내보내기](continuous-export.md) 도구를 사용 하 여 [Azure Event Hubs](../event-hubs/index.yml)에 데이터를 전송 합니다. 그런 다음 이벤트 허브 데이터를 타사 SIEM과 통합 합니다.
- [REST API](/rest/api/securitycenter/) -REST API를 사용 하 여 경고에 액세스 하는 경우 [온라인 경고 API 설명서](/rest/api/securitycenter/alerts)를 참조 하세요.

프로그래밍 방법을 사용 하 여 경고를 사용 하는 경우 적절 한 스키마를 사용 하 여 관련 필드를 찾아야 합니다. 또한 이벤트 허브로 내보내거나 일반 HTTP 커넥터를 사용 하 여 워크플로 자동화를 트리거하기를 시도 하는 경우 스키마를 사용 하 여 JSON 개체를 올바르게 구문 분석 합니다.

>[!IMPORTANT]
> 이러한 각 시나리오 마다 스키마가 약간 다르므로 아래에서 관련 탭을 선택 해야 합니다.


## <a name="the-schemas"></a>스키마 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[워크플로 자동화 및 이벤트 허브로 연속 내보내기](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Logic Apps, 이벤트 허브 및 타사 SIEMs로 전송 되는 경고에 대 한 샘플 JSON

아래에 전달 된 경고 이벤트의 스키마를 찾을 수 있습니다.

- Security Center의 워크플로 자동화에서 구성 된 Azure 논리 앱 인스턴스
- Security Center의 연속 내보내기 기능을 사용 하는 Azure 이벤트 허브

Workflow automation 기능에 대 한 자세한 내용은 [경고 및 권장 사항에 대 한 응답 자동화](workflow-automation.md)를 참조 하세요.
연속 내보내기에 대 한 자세한 내용은 [경고 및 권장 사항 내보내기](continuous-export.md)를 참조 하세요.

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure 센티널 및 Log Analytics 작업 영역](#tab/schema-sentinel)

센티널 커넥터는 Azure Security Center에서 경고를 가져오고 Azure 센티널의 Log Analytics 작업 영역으로 보냅니다. 

Security Center 경고를 사용 하 여 센티널 사례 또는 인시던트를 만들려면 아래에 표시 된 해당 경고에 대 한 스키마가 필요 합니다. 

Azure 센티널에 대 한 자세한 내용은 [설명서](../sentinel/index.yml)를 참조 하세요.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 활동 로그](#tab/schema-activitylog)

Azure Security Center는 생성 된 보안 경고를 Azure 활동 로그의 이벤트로 감사 합니다.

다음과 같이 경고 활성화 이벤트를 검색 하 여 활동 로그에서 보안 경고 이벤트를 볼 수 있습니다.

[![활동 로그에서 활성화 경고 이벤트를 검색 하는 중](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Azure 활동 로그로 전송 되는 경고에 대 한 샘플 JSON

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
|**채널이**|상수, "Operation"|
|**correlationId**|Azure Security Center 경고 ID|
|**description**|경고에 대한 설명|
|**eventDataId**|CorrelationId 참조|
|**eventName**|Value 및 localizedValue 하위 필드에는 경고 표시 이름이 포함 됩니다.|
|**category**|Value 및 localizedValue 하위 필드는 상수 "보안"입니다.|
|**eventTimestamp**|경고가 생성 된 시간에 대 한 UTC 타임 스탬프|
|**id**|정규화 된 경고 ID|
|**level**|상수, "정보"|
|**operationId**|CorrelationId 참조|
|**operationName**|값 필드는 상수-"Microsoft. 보안/위치/경고/활성화/동작" 이며, 지역화 된 값은 "경고 활성화" (잠재적으로 사용자 로캘과 함께 지역화 될 수 있음)입니다.|
|**resourceGroupName**|리소스 그룹 이름을 포함 합니다.|
|**resourceProviderName**|Value 및 localizedValue 하위 필드는 상수-"Microsoft. 보안"입니다.|
|**resourceType**|Value 및 localizedValue 하위 필드는 상수-"Microsoft. 보안/위치/경고"입니다.|
|**resourceId**|정규화 된 Azure 리소스 ID|
|**status**|Value 및 localizedValue 하위 필드는 상수 "Active"입니다.|
|**하위 상태**|Value 및 localizedValue 하위 필드는 비어 있습니다.|
|**submissionTimestamp**|활동 로그에 대 한 이벤트 전송의 UTC 타임 스탬프입니다.|
|**subscriptionId**|손상 된 리소스의 구독 ID입니다.|
|**properties**|경고와 관련 된 추가 속성의 JSON 모음입니다. 이는 경고 간에 변경 될 수 있지만 모든 경고에는 다음 필드가 표시 됩니다.<br>-심각도: 공격의 심각도<br>-compromisedEntity: 손상 된 리소스의 이름입니다.<br>-remediationSteps: 수행할 재구성 단계 배열<br>-의도: 경고의 kill 체인 의도입니다. 가능한 의도는 [의도 테이블](alerts-reference.md#intentions) 에 설명 되어 있습니다.|
|**relatedEvents**|상수-빈 배열|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph은 Microsoft 365의 데이터 및 인텔리전스에 대 한 게이트웨이입니다. Microsoft 365, Windows 10 및 Enterprise Mobility + Security에서 엄청난 양의 데이터에 액세스 하는 데 사용할 수 있는 통합 프로그래밍 모델을 제공 합니다. Microsoft Graph의 다양 한 데이터를 사용 하 여 수백만 명의 사용자와 상호 작용 하는 조직과 소비자를 위한 앱을 빌드할 수 있습니다.

MS Graph로 전송 되는 보안 경고에 대 한 스키마 및 JSON 표현은 [Microsoft Graph 설명서](/graph/api/resources/alert?preserve-view=true&view=graph-rest-1.0)에서 사용할 수 있습니다.

---


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 경고 정보를 보낼 때 Azure Security Center의 위협 방지 도구에서 사용 하는 스키마에 대해 설명 했습니다.

외부 Security Center에서 보안 경고에 액세스 하는 방법에 대 한 자세한 내용은 다음 페이지를 참조 하세요.

- [Azure 센티널](../sentinel/index.yml) -Microsoft의 클라우드-기본 siem
- [Azure Event Hubs](../event-hubs/index.yml) -Microsoft의 완전히 관리 되는 실시간 데이터 수집 서비스
- Security Center의 [연속 내보내기 기능](continuous-export.md)
- [Log Analytics 작업 영역](../azure-monitor/learn/quick-create-workspace.md) -데이터 및 구성 정보를 포함 하는 컨테이너 Log Analytics 작업 영역에 로그 데이터를 저장 Azure Monitor