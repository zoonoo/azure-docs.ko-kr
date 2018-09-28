---
title: Azure Firewall 로그 개요
description: 이 문서는 Azure Firewall 진단 로그의 개요입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6d8d0b2dc0f6baf48d2aacb9c7203937aef08d15
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957800"
---
# <a name="azure-firewall-logs"></a>Azure Firewall 로그

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그를 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), 저장소 및 Event Hubs로 보내 Log Analytics 또는 Excel 및 Power BI 같은 다른 도구에서 분석합니다.

## <a name="diagnostic-logs"></a>진단 로그

 다음 진단 로그는 Azure Firewall에 사용할 수 있습니다.

* **응용 프로그램 규칙 로그**

   각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 응용 프로그램 규칙 로그를 저장소 계정에 저장하고 Event 허브로 스트리밍하고/또는 Log Analytics로 보냅니다. 허용/거부된 연결에 대한 로그에서 구성된 응용 프로그램 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **네트워크 규칙 로그**

   각 Azure Firewall에 대해 사용하도록 설정하는 경우에만 네트워크 규칙 로그를 저장소 계정에 저장하고 Event 허브로 스트리밍하고/또는 Log Analytics로 보냅니다. 허용/거부된 연결에 대한 로그에서 구성된 네트워크 규칙 결과 중 하나와 일치하는 각 새 연결입니다. 데이터는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브** - 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Log Analytics** - 일반적으로 응용 프로그램을 실시간 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

## <a name="activity-logs"></a>활동 로그

   활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.

   [Azure 활동 로그](../azure-resource-manager/resource-group-audit.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Firewall 로그 및 메트릭을 모니터링하는 방법은 [자습서: Azure Firewall 로그 모니터링](tutorial-diagnostics.md)을 참조하세요.