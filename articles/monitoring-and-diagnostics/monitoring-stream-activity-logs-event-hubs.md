---
title: "Azure 활동 로그를 이벤트 허브로 스트림 | Microsoft Docs"
description: "Azure 활동 로그를 이벤트 허브로 스트림하는 방법에 대해 알아봅니다."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 51a7a274c0cdbec169154bd1100abb4534283cff
ms.openlocfilehash: ac478dc5c6691ef9f6c4829bd47a81912774d032


---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 이벤트 허브로 스트림
포털에서 기본 제공 "내보내기" 옵션을 사용하거나 Azure PowerShell Cmdlet 또는 Azure CLI를 통해 로그 프로필에서 Service Bus 규칙 ID를 사용하도록 설정하여 [**Azure 활동 로그**](monitoring-overview-activity-logs.md)를 거의 실시간으로 응용 프로그램에 스트림할 수 있습니다.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>활동 로그 및 이벤트 허브에서 수행할 수 있는 작업
활동 로그의 스트리밍 기능을 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

* **타사 로깅 및 원격 분석 시스템으로 스트림** – 시간이 지나면서 이벤트 허브 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드** – 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 이벤트 허브의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다. [글로벌 확장 원격 분석 플랫폼에 이벤트 허브 사용에 대해서는 여기 Dan Rosanova의 가이드를 참조하세요.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>활동 로그의 스트리밍 사용
프로그래밍 방식이나 포털을 통해 활동 로그의 스트리밍을 사용하도록 설정할 수 있습니다. 어떤 방법으로든 Service Bus 네임스페이스와 네임스페이스에 대한 공유 액세스 정책을 선택하면 처음으로 새로운 활동 로그 이벤트가 발생하면 이 네임스페이스에 Event Hub가 생성됩니다. Service Bus 네임스페이스가 없는 경우 먼저 만들어야 합니다. 이전에 활동 로그 이벤트를 이 Service Bus 네임스페이스로 스트리밍한 경우 이전에 만든 Event Hub를 다시 사용합니다. 공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. 현재, 이벤트 허브로 스트리밍하려면 **관리**, **보내기** 및 **수신** 권한이 필요합니다. 클래식 포털에서 서비스 버스 네임스페이스에 대한 "구성" 탭 아래에서 서비스 버스 네임스페이스 공유 액세스 정책을 만들거나 수정할 수 있습니다. 스트리밍을 포함할 활동 로그 로그 프로필을 업데이트하려면 변경하는 사용자에게 Service Bus 권한 부여 규칙에 대한 ListKey 권한이 있어야 합니다.

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 서비스 버스 또는 이벤트 허브 네임스페이스는 로그를 내보내는 구독과 동일한 구독을 가지고 있지 않아도 됩니다.

### <a name="via-azure-portal"></a>Azure 포털을 통해
1. 포털 왼쪽에 있는 메뉴를 사용하여 **활동 로그** 블레이드로 이동합니다.
   
    ![포털에서 활동 로그로 이동](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. 블레이드 맨 위에서 **내보내기** 단추를 클릭합니다.
   
    ![포털에서 내보내기 단추](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. 표시되는 블레이드에서 이벤트를 스트림할 지역, 이러한 이벤트를 스트리밍하기 위해 이벤트 허브를 생성할 서비스 버스 네임스페이스를 선택할 수 있습니다.
   
    ![활동 로그 내보내기 블레이드](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **저장** 을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.

### <a name="via-powershell-cmdlets"></a>PowerShell Cmdlet을 통해
로그 프로필이 이미 있는 경우 먼저 해당 프로필을 제거해야 합니다.

1. `Get-AzureRmLogProfile` 를 사용하여 로그 프로필이 있는지 확인합니다.
2. 있는 경우 `Remove-AzureRmLogProfile` 를 사용하여 제거합니다.
3. `Set-AzureRmLogProfile` 을 사용하여 프로필을 만듭니다.

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

서비스 버스 규칙 ID는 예를 들어, {service bus resource ID}/authorizationrules/{key name} 형식의 문자열입니다. 

### <a name="via-azure-cli"></a>Azure CLI를 통해
로그 프로필이 이미 있는 경우 먼저 해당 프로필을 제거해야 합니다.

1. `azure insights logprofile list` 를 사용하여 로그 프로필이 있는지 확인합니다.
2. 있는 경우 `azure insights logprofile delete` 를 사용하여 제거합니다.
3. `azure insights logprofile add` 을 사용하여 프로필을 만듭니다.

```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

서비스 버스 규칙 ID는 `{service bus resource ID}/authorizationrules/{key name}`형식의 문자열입니다.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>이벤트 허브에서 로그 데이터를 사용하려면 어떻게 하나요?
[여기에서 활동 로그에 대한 스키마를 사용할 수 있습니다](monitoring-overview-activity-logs.md). 각 이벤트는 "레코드"라는 JSON Blob 배열입니다.

## <a name="next-steps"></a>다음 단계
* [저장소 계정에 활동 로그 보관](monitoring-archive-activity-log.md)
* [Azure 활동 로그 개요 알아보기](monitoring-overview-activity-logs.md)
* [활동 로그 이벤트를 기반으로 경고 설정](insights-auditlog-to-webhook-email.md)




<!--HONumber=Dec16_HO2-->


