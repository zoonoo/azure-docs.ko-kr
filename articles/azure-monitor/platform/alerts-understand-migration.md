---
title: Azure Monitor 경고에 대 한 자발적인 마이그레이션 도구 작동 방식을 이해합니다
description: 경고 마이그레이션 도구 작동 원리를 이해 하 고 문제를 해결 합니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015599"
---
# <a name="understand-how-the-migration-tool-works"></a>마이그레이션 도구 작동 원리를 이해합니다

로 [발표](monitoring-classic-retirement.md), 2019 년 9 월에에서 Azure Monitor에서 클래식 경고 사용이 중지 되는 (7 월 2019 원래 되었습니다). 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고 마이그레이션 자체 트리거 하려는 고객에 게 Azure portal에서 사용할 수 있습니다.

이 문서는 자발적 마이그레이션 도구 작동 원리를 설명 합니다. 또한 몇 가지 일반적인 문제에 대 한 해결책을 설명합니다.

> [!NOTE]
> 클래식 경고 마이그레이션에 대 한 사용 중지 날짜 마이그레이션 도구의 롤아웃을에서 지연으로 인해 되었습니다 [2019 년 8 월 31 일에 확장](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) 2019 년 6 월 30 일의 처음 발표 된 날짜에서.

## <a name="which-classic-alert-rules-can-be-migrated"></a>클래식 경고 규칙을 마이그레이션할 수 있습니까?

이 도구는 거의 모든 클래식 경고 규칙을 마이그레이션할 수도 있지만 일부의 예외가 있습니다. 이 도구를 사용 하 여 (또는 2019 년 9 월의에서 자동 마이그레이션 중) 다음 경고 규칙을 마이그레이션할 수 없습니다.

- 가상 컴퓨터 게스트 메트릭 (Windows 및 Linux)에서 클래식 경고 규칙입니다. 참조 된 [새로운 메트릭 경고에서 이러한 경고 규칙을 다시 만들기에 대 한 지침](#guest-metrics-on-virtual-machines) 이 문서의 뒷부분에 나오는.
- 클래식 저장소 메트릭에 대 한 클래식 경고 규칙입니다. 참조 된 [클래식 저장소 계정을 모니터링 하는 것에 대 한 지침](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)합니다.
- 일부 저장소 계정 메트릭에서 클래식 경고 규칙입니다. 참조 [세부 정보](#storage-account-metrics) 이 문서의 뒷부분에 나오는.

구독의 클래식 이러한 규칙에 있는 경우에 수동으로 마이그레이션해야 합니다. 자동 마이그레이션을 제공할 수 있으므로 이러한 유형의 모든 기존, 클래식 메트릭 경고 2020 년 6 월 까지는 작동 하도록 계속 됩니다. 이 확장을 통해 새 경고에 이동 시간을 제공 합니다. 그러나 2019 년 8 월 이후 새 클래식 경고를 만들 수 있습니다.

### <a name="guest-metrics-on-virtual-machines"></a>가상 머신에서 게스트 메트릭

게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수 있습니다, 전에 게스트 메트릭은 Azure Monitor 메트릭 사용자 지정 저장소에 전송 되어야 합니다. 진단 설정에서 Azure Monitor 싱크에 사용 하도록 설정 하려면 이러한 지침을 따릅니다.

- [Windows Vm에 대 한 게스트 메트릭을 사용 하도록 설정](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux Vm 용 게스트 메트릭 사용](collect-custom-metrics-linux-telegraf.md)

다음이 단계를 완료 게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수 있습니다. 및 새로운 메트릭 경고를 만든 후 클래식 경고를 삭제할 수 있습니다.

### <a name="storage-account-metrics"></a>저장소 계정 메트릭

이러한 메트릭에 대 한 경고를 제외 하 고 저장소 계정에서 모든 클래식 경고를 마이그레이션할 수 있습니다.

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

백분율 기준에 대 한 규칙을 마이그레이션해야 하는 클래식 경고에 따라 [이전 및 새 storage 메트릭 간의 매핑을](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)합니다. 임계값을 사용할 수 있는 새 메트릭 항목을 절대 이므로 적절 하 게 수정 해야 합니다.

동일한 기능을 제공 하는 결합 된 메트릭이 없습니다 있기 때문에 대해 AnonymousThrottlingError에 SASThrottlingError 클래식 경고 규칙 두 개의 새 경고도 분할 해야 합니다. 임계값을 적절 하 게 적응 해야 합니다.

## <a name="rollout-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객에 게 단계별로 출시 됩니다. 구독 소유자는 구독 도구를 사용 하 여 마이그레이션할 준비가 되 면 전자 메일을 받게 됩니다.

> [!NOTE]
> 이 도구는 단계에서 롤아웃 되는 대부분의 구독 되지 않았는지 아직 초기 단계에서 마이그레이션할 준비가 볼 수 있습니다.

현재 구독의 하위 집합은 마이그레이션에 대 한 준비로 표시 됩니다. 하위 집합 리소스 종류에만 클래식 경고 규칙에 있는 해당 구독을 포함 합니다. 이후 단계에서 자세한 리소스 종류에 대 한 지원이 추가 됩니다.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>마이그레이션을 트리거할 수는?

Monitoring Contributor의 기본 제공 역할은 구독 수준에 있는 사용자가 마이그레이션을 트리거할 수 있습니다. 다음 권한이 있는 사용자 지정 역할이 있는 사용자 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>일반적인 문제 및 해결책

한 후 [마이그레이션 트리거](alerts-using-migration-tool.md), 마이그레이션이 완료 되었는지 또는 모든 작업에서 필요한 경우 사용자에 게 알려 주기 위해 제공 된 주소로 전자 메일을 받게 됩니다. 이 섹션에서는 몇 가지 일반적인 문제 및 처리 하는 방법을 설명 합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독에서 클래식 경고 규칙에 몇 가지 최근 변경 내용으로 인해 구독을 마이그레이션할 수 없습니다. 이 문제는 일시적입니다. 마이그레이션 상태를 다시 이동한 후 마이그레이션을 다시 시작할 수 있습니다 **마이그레이션에 대 한 준비** 몇 일 내에 있습니다.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>정책 또는 범위 잠금 규칙을 마이그레이션에서 못하고 있습니다.

마이그레이션의 일부로, 새 메트릭 경고 및 새 작업 그룹에 만들어지고 클래식 경고 규칙 삭제 됩니다. 그러나 정책이 나 범위 잠금 리소스를 만들지 못하게 하지 못하고 있습니다. 정책 또는 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. 범위 잠금이 또는 정책을 일시적으로 제거 하 고 마이그레이션을 다시 트리거이 문제를 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구를 사용 하는 방법](alerts-using-migration-tool.md)
- [마이그레이션을 위한 준비](alerts-prepare-migration.md)
