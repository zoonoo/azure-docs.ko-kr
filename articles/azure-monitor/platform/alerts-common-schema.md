---
title: Azure 모니터 경고에 대한 일반적인 경고 스키마
description: 일반적인 경고 스키마, 이 스키마를 사용해야 하는 이유 및 이를 활성화하는 방법 이해
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249049"
---
# <a name="common-alert-schema"></a>일반 경고 스키마

이 문서에서는 일반적인 경고 스키마가 무엇인지, 스키마를 사용하는 이점과 이를 활성화하는 방법에 대해 설명합니다.

## <a name="what-is-the-common-alert-schema"></a>일반적인 경고 스키마는 무엇입니까?

일반적인 경고 스키마는 현재 Azure에서 경고 알림에 대한 소비 환경을 표준화합니다. 지금까지 Azure의 세 가지 경고 유형(메트릭, 로그 및 활동 로그)에는 자체 전자 메일 템플릿, webhook 스키마 등이 있었습니다. 이제 일반적인 경고 스키마를 사용하면 일관된 스키마를 통해 경고 알림을 받을 수 있습니다.

모든 경고 인스턴스는 **영향을 받은 리소스와** **경고의 원인을**설명하며 이러한 인스턴스는 다음 섹션의 공통 스키마에 설명되어 있습니다.
* **필수**요소 : 모든 경고 유형에서 **공통으로 표준화된 필드**집합으로, 경고가 **있는 리소스와** 추가 공통 경고 메타데이터(예: 심각도 또는 설명)를 설명합니다. 
* **경고 컨텍스트**: 경고 유형에 **따라**달라지는 필드가 있는 **경고의 원인을**설명하는 필드 집합입니다. 예를 들어 메트릭 경고에는 경고 컨텍스트의 메트릭 이름 및 메트릭 값과 같은 필드가 있는 반면 활동 로그 경고에는 경고를 생성한 이벤트에 대한 정보가 있습니다. 

고객이 듣는 일반적인 통합 시나리오에는 일부 피벗(예: 리소스 그룹)을 기반으로 경고 인스턴스를 관련 팀에 라우팅한 다음 책임 있는 팀이 작업을 시작합니다. 일반적인 경고 스키마를 사용하면 필수 필드를 활용하여 경고 유형 전반에 걸쳐 표준화된 라우팅 논리를 사용할 수 있으며, 컨텍스트 필드를 그대로 두고 관련 팀이 추가로 조사할 수 있습니다.

즉, 통합 횟수가 줄어들어 통합 프로세스를 _훨씬_ 더 간단한 작업으로 관리하고 유지 관리할 수 있습니다. 또한 향후 경고 페이로드 보강(예: 사용자 지정, 진단 보강 등)은 공통 스키마에서만 나타납니다.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>일반적인 경고 스키마는 어떤 향상된 기능을 제공하는가?

일반적인 경고 스키마는 주로 경고 알림에 표시됩니다. 표시되는 향상된 기능은 다음과 같습니다.

| 작업 | 개선 사항|
|:---|:---|
| sms | 모든 경고 유형에 대한 일관된 SMS 템플릿입니다. |
| Email | 일관되고 상세한 이메일 템플릿으로 문제를 한눈에 쉽게 진단할 수 있습니다. 포털의 경고 인스턴스에 대한 딥 링크와 영향을 받는 리소스에 대한 딥 링크는 수정 프로세스로 빠르게 이동할 수 있도록 합니다. |
| 웹후크/로직 앱/Azure 함수/자동화 런북 | 모든 경고 유형에 대한 일관된 JSON 구조로, 다양한 경고 유형에 대한 통합을 쉽게 구축할 수 있습니다. |

또한 새 스키마를 사용하면 곧 Azure 포털과 Azure 모바일 앱 모두에서 보다 풍부한 경고 소비 환경을 사용할 수 있습니다. 

[Webhooks/논리 앱/Azure 함수/자동화 Runbook에 대한 스키마 정의에 대해 자세히 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 다음 작업은 일반적인 경고 스키마인 ITSM 커넥터를 지원하지 않습니다.

## <a name="how-do-i-enable-the-common-alert-schema"></a>일반적인 경고 스키마를 사용하도록 설정하려면 어떻게 해야 합니까?

포털과 REST API를 통해 작업 그룹을 통해 공통 경고 스키마를 옵트인하거나 옵트아웃할 수 있습니다. 새 스키마로 전환하는 토글은 작업 수준에 있습니다. 예를 들어 이메일 작업과 웹후크 작업을 별도로 선택해야 합니다.

> [!NOTE]
> 1. 다음 경고 유형은 기본적으로 공통 스키마를 지원합니다(옵트인이 필요하지 않습니다).
>     * 스마트 감지 경고
> 1. 다음 경고 유형은 현재 일반적인 스키마를 지원하지 않습니다.
>     * [VM에 대한 Azure 모니터에서](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) 생성된 경고
>     * Azure 비용 [관리에서](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) 생성된 경고

### <a name="through-the-azure-portal"></a>Azure 포털을 통해

![일반적인 경고 스키마 옵트인](media/alerts-common-schema/portal-opt-in.png)

1. 작업 그룹의 기존 또는 새 작업을 엽니다. 
1. 그림과 같이 일반적인 경고 스키마를 활성화하려면 토글에 대해 '예'를 선택합니다.

### <a name="through-the-action-groups-rest-api"></a>작업 그룹 REST API를 통해

[작업 그룹 API를](https://docs.microsoft.com/rest/api/monitor/actiongroups) 사용하여 공통 경고 스키마를 옵트인할 수도 있습니다. REST API 호출을 [만들거나 업데이트하는](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) 동안 다음 작업(전자 메일/웹후크/논리 앱/Azure Function/Automation Runbook)에 대해 "useCommonAlertSchema" 플래그를 'true'(옵트인) 또는 'false'(옵트아웃)로 설정할 수 있습니다.

예를 들어 REST API 만들기 [또는 업데이트에](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) 대한 다음 요청 본문은 다음을 수행합니다.

* 전자 메일 작업 "John Doe의 전자 메일"에 대한 일반적인 경고 스키마 를 사용하도록 설정합니다.
* 전자 메일 작업 "Jane Smith의 전자 메일"에 대 한 일반적인 경고 스키마를 사용 하지 않도록 설정 합니다.
* 웹후크 작업 "샘플 웹후크"에 대한 공통 경고 스키마 사용

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>다음 단계

- [Webhooks/논리 앱/Azure 함수/자동화 실행록에 대한 일반적인 경고 스키마 정의입니다.](https://aka.ms/commonAlertSchemaDefinitions)
- [일반적인 경고 스키마를 활용하여 모든 경고를 처리하는 논리 앱을 만드는 방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



