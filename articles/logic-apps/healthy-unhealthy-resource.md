---
title: Azure Security Center에서 논리 앱을 모니터링 하기 위한 로깅 설정
description: 진단 로깅을 설정 하 여 Azure Security Center에서 Logic Apps 리소스의 상태를 모니터링 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9c22e7cbc71c2b29c4b3e50319c6b6d256856bb3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855678"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Azure Security Center에서 논리 앱을 모니터링 하기 위한 로깅 설정

[Microsoft Azure Security Center](../security-center/security-center-introduction.md)에서 Logic Apps 리소스를 모니터링 하 [는 경우 논리 앱이 기본 정책을 따라 수행 되는지 여부를 검토할](#view-logic-apps-health-status)수 있습니다. 로깅을 사용 하도록 설정 하 고 로그의 대상을 올바르게 설정 하 고 나면 Azure에서 Logic Apps 리소스의 상태를 표시 합니다. 이 문서에서는 진단 로깅을 구성 하 고 모든 논리 앱이 정상 리소스 인지 확인 하는 방법을 설명 합니다.

> [!TIP]
> Logic Apps 서비스에 대 한 현재 상태를 확인 하려면 [Azure 상태 페이지](https://status.azure.com/)를 검토 합니다 .이 페이지에는 사용 가능한 각 지역의 다른 제품 및 서비스에 대 한 상태가 나열 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 구독이 없는 경우 시작 하기 전에 [무료 Azure 계정을 만듭니다](https://azure.microsoft.com/free/) .
* [진단 로깅을 사용](#enable-diagnostic-logging)하는 기존 논리 앱
* 논리 앱에 대 한 로깅을 사용 하도록 설정 하는 데 필요한 Log Analytics 작업 영역입니다. 작업 영역이 없으면 먼저 [작업 영역을 만듭니다](/learn/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용

논리 앱에 대 한 리소스 상태를 보려면 먼저 [진단 로깅을 설정](monitor-logic-apps-log-analytics.md)해야 합니다. 이미 Log Analytics 작업 영역이 있는 경우 논리 앱을 만들 때 또는 기존 논리 앱에서 로깅을 사용 하도록 설정할 수 있습니다.

> [!TIP]
> 기본 권장 사항은 Logic Apps에 진단 로그를 사용 하도록 설정 하는 것입니다. 그러나 논리 앱에 대해이 설정을 제어 합니다. 논리 앱에 대 한 진단 로그를 사용 하도록 설정 하는 경우 정보를 사용 하 여 보안 문제를 분석할 수 있습니다.

### <a name="check-diagnostic-logging-setting"></a>진단 로깅 설정 확인

논리 앱에서 진단 로깅을 사용 하도록 설정 했는지 여부를 확실 하 게 알 수 없는 경우 Security Center 체크 인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서를 입력 하 고 **Security Center** 를 선택 합니다.
1. Security Center 대시보드 메뉴의 **일반** 에서 **권장 사항** 을 선택 합니다.
1. 보안 제안 표에서 **감사 사용 및 로깅 사용** &gt; **Logic Apps에서 진단 로그** 로깅을 선택 하 여 보안 제어 테이블에서 사용 하도록 설정 해야 합니다.
1. 권장 사항 페이지에서 **수정 단계** 섹션을 확장 하 고 옵션을 검토 합니다. **빠른 수정** 을 선택 하 여 Logic Apps 진단을 사용 하도록 설정할 수 있습니다. 단추를 클릭 하거나 수동 수정 지침을 따릅니다.

## <a name="view-logic-apps-health-status"></a>논리 앱의 상태 보기

[진단 로깅을 사용 하도록 설정](#enable-diagnostic-logging)하면 Security Center에서 논리 앱의 상태를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 창에서를 입력 하 고 **Security Center** 를 선택 합니다.
1. Security Center 대시보드 메뉴의 **일반** 에서 **인벤토리** 를 선택 합니다.
1. 인벤토리 페이지에서 자산 목록을 필터링 하 여 Logic Apps 리소스만 표시 합니다. 페이지 메뉴에서 **리소스 종류** &gt; **논리 앱** 을 선택 합니다.

   **비정상 리소스** 카운터는 Security Center 비정상으로 간주 되는 논리 앱의 수를 표시 합니다.
1.  논리 앱 리소스 목록에서 **권장 사항** 열을 검토 합니다. 특정 논리 앱에 대 한 상태 세부 정보를 검토 하려면 리소스 이름을 선택 하거나 줄임표 단추 (**...**) &gt; 를 선택 합니다. **리소스를 봅니다**.
1.  잠재적 리소스 상태 문제를 해결 하려면 논리 앱에 대해 나열 된 단계를 따르세요.

진단 로깅을 이미 사용 하는 경우 로그의 대상에 문제가 있을 수 있습니다. [다른 진단 로깅 대상의 문제를 해결 하는 방법을](#fix-diagnostic-logging-for-logic-apps)검토 합니다.

## <a name="fix-diagnostic-logging-for-logic-apps"></a>논리 앱에 대 한 진단 로깅 수정

[논리 앱이 Security Center에서 비정상으로 표시 되는](#view-logic-apps-health-status)경우 Azure Portal 또는 Azure CLI를 통해 코드 보기에서 논리 앱을 엽니다. 그런 다음 진단 로그에 대 한 대상 구성 ( [azure Log Analytics](#log-analytics-and-event-hubs-destinations), [azure Event Hubs](#log-analytics-and-event-hubs-destinations)또는 [Azure Storage 계정을](#storage-account-destination)확인 합니다.

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics 및 Event Hubs 대상

Log Analytics 또는 Event Hubs를 Logic Apps 진단 로그의 대상으로 사용 하는 경우 다음 설정을 확인 합니다. 

1. 진단 로그를 사용 하도록 설정 했는지 확인 하려면 진단 설정 `logs.enabled` 필드가로 설정 되었는지 확인 `true` 합니다. 
1. 대신 저장소 계정을 대상으로 설정 하지 않았는지 확인 하려면 `storageAccountId` 필드가로 설정 되었는지 확인 `false` 합니다.

예를 들어 다음과 같습니다.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>저장소 계정 대상

저장소 계정을 Logic Apps 진단 로그의 대상으로 사용 하는 경우 다음 설정을 확인 합니다.

1. 진단 로그를 사용 하도록 설정 했는지 확인 하려면 진단 설정 `logs.enabled` 필드가로 설정 되었는지 확인 `true` 합니다.
1. 진단 로그에 대 한 보존 정책을 사용 하도록 설정 했는지 확인 하려면 `retentionPolicy.enabled` 필드가로 설정 되었는지 확인 `true` 합니다.
1. 보존 시간을 0-365 일로 설정 했는지 확인 하려면 `retentionPolicy.days` 필드가 0과 365 사이의 숫자로 설정 되어 있는지 확인 합니다.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```
