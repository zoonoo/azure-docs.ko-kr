---
title: Azure monitor 경고에 대 한 일반적인 경고 스키마
description: 일반적인 경고 스키마 이해, 사용 해야 하는 이유 및 사용 방법
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249049"
---
# <a name="common-alert-schema"></a>일반 경고 스키마

이 문서에서는 일반적인 경고 스키마의 정의, 사용의 이점 및 사용 방법에 대해 설명 합니다.

## <a name="what-is-the-common-alert-schema"></a>일반적인 경고 스키마는 무엇 인가요?

일반적인 경고 스키마는 현재 Azure에서 경고 알림에 대 한 소비 환경을 표준화 합니다. 지금까지 Azure의 세 가지 경고 유형 (메트릭, 로그 및 활동 로그)에는 자체 전자 메일 템플릿, webhook 스키마 등이 있습니다. 공통 경고 스키마를 사용 하 여 이제 일관 된 스키마로 경고 알림을 받을 수 있습니다.

경고 인스턴스는 **영향을 받은 리소스** 및 **경고의 원인을**설명 하며, 이러한 인스턴스는 다음 섹션의 공통 스키마에 설명 되어 있습니다.
* **Essentials**: 모든 경고 유형에 공통 된 **표준화 된 필드**집합으로, 경고가 발생 하는 **리소스** 를 설명 하는 추가 공통 경고 메타 데이터 (예: 심각도 또는 설명)를 설명 합니다. 
* **경고 컨텍스트**: 경고 **유형에 따라**달라 지는 필드를 사용 하 여 **경고의 원인을**설명 하는 필드 집합입니다. 예를 들어 메트릭 경고는 경고 컨텍스트에 메트릭 이름 및 메트릭 값과 같은 필드를 포함 하는 반면, 활동 로그 경고에는 경고를 생성 한 이벤트에 대 한 정보가 포함 됩니다. 

고객 으로부터 수신 하는 일반적인 통합 시나리오는 팀이 작업을 시작 하기 시작 하는 일부 피벗 (예: 리소스 그룹)을 기반으로 하는 경고 인스턴스를 관련 팀에 라우팅하는 작업을 포함 합니다. 일반적인 경고 스키마를 사용 하면 중요 한 필드를 활용 하 여 경고 유형에 대해 표준화 된 라우팅 논리를 수행할 수 있습니다 .이를 통해 관련 팀이 더 자세히 조사할 수 있습니다.

이는 잠재적으로 더 간단한 통합을 포함 하 여 _훨씬_ 간단한 작업을 관리 하 고 유지 관리 하는 프로세스를 수행 하는 것을 의미 합니다. 또한 이후 경고 페이로드 강화 (예: 사용자 지정, 진단 보강 등)는 공통 스키마에만 노출 됩니다.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>일반적인 경고 스키마의 향상 된 기능은 무엇 인가요?

일반적인 경고 스키마는 주로 경고 알림에 생성 됩니다. 표시 되는 향상 된 기능은 다음과 같습니다.

| 작업 | 개선 사항|
|:---|:---|
| SMS | 모든 경고 유형에 대 한 일관 된 SMS 템플릿입니다. |
| 메일 | 일관 되 고 자세한 전자 메일 템플릿으로, 문제를 한눈에 쉽게 진단할 수 있습니다. 포함 된 딥-포털의 경고 인스턴스와 영향을 받는 리소스에 대 한 링크를 통해 신속 하 게 재구성 프로세스로 이동할 수 있습니다. |
| 웹 후크/논리 앱/a p i/Azure 함수/자동화 Runbook | 모든 경고 유형에 대해 일관 된 JSON 구조를 사용 하 여 다양 한 경고 유형에 서 통합을 쉽게 빌드할 수 있습니다. |

새 스키마를 사용 하 여 Azure Portal 및 Azure 모바일 앱에 대 한 보다 다양 한 경고 소비 환경을 제공할 수도 있습니다. 

[웹 후크/Logic Apps/Azure Functions/자동화 Runbook에 대 한 스키마 정의에 대해 자세히 알아보세요.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 다음 작업은 일반적인 경고 스키마를 지원 하지 않습니다. ITSM 커넥터.

## <a name="how-do-i-enable-the-common-alert-schema"></a>공통 경고 스키마를 사용 하도록 설정 어떻게 할까요?

포털 및 REST API를 통해 작업 그룹을 통해 일반적인 경고 스키마를 옵트인 또는 옵트아웃 (opt out) 할 수 있습니다. 새 스키마로 전환 하는 토글은 동작 수준에 있습니다. 예를 들어 전자 메일 작업 및 웹 후크 작업을 별도로 옵트인 (opt in) 해야 합니다.

> [!NOTE]
> 1. 다음 경고 유형은 기본적으로 공통 스키마를 지원 합니다 (옵트인 필요 없음).
>     * 스마트 감지 경고
> 1. 다음 경고 유형은 현재 공통 스키마를 지원 하지 않습니다.
>     * [VM용 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) 에서 생성 된 경고
>     * [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario) 에서 생성 된 경고

### <a name="through-the-azure-portal"></a>Azure Portal

![일반적인 경고 스키마 옵트인](media/alerts-common-schema/portal-opt-in.png)

1. 작업 그룹에서 기존 또는 새 작업을 엽니다. 
1. 표시 된 대로 일반 경고 스키마를 사용 하도록 설정 하려면 ' 예 '를 선택 합니다.

### <a name="through-the-action-groups-rest-api"></a>작업 그룹 REST API

[작업 그룹 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 를 사용 하 여 공통 경고 스키마를 옵트인 (opt in) 할 수도 있습니다. REST API를 [만들거나 업데이트](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) 하는 동안 "useCommonAlertSchema" 플래그를 ' t r u e '로 설정 하거나 (옵트인 (opt in)) 다음 작업 중 하나에 대해 (옵트아웃) (옵트아웃) 할 수 있습니다.

예를 들어 REST API [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) 에 대 한 다음 요청 본문은 다음을 수행 합니다.

* 전자 메일 작업 "John Doe의 메일"에 대해 일반적인 경고 스키마를 사용 하도록 설정 합니다.
* 전자 메일 작업 "Jane Smith의 전자 메일"에서 일반적인 경고 스키마를 사용 하지 않도록 설정 합니다.
* 웹 후크 작업 "샘플 웹 후크"에 대해 일반적인 경고 스키마를 사용 하도록 설정 합니다.

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

- [웹 후크/Logic Apps/Azure Functions/자동화 Runbook에 대 한 일반적인 경고 스키마 정의입니다.](https://aka.ms/commonAlertSchemaDefinitions)
- [일반적인 경고 스키마를 활용 하 여 모든 경고를 처리 하는 논리 앱을 만드는 방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



