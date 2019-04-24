---
title: Azure Monitor에서 경고 마이그레이션 도구를 어떻게 자발적 이해
description: 경고 마이그레이션 도구 작동 원리를 이해 하 고 문제를 실행 하는 경우 문제를 해결 합니다.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347603"
---
# <a name="understand-how-the-migration-tool-works"></a>마이그레이션 도구 작동 원리를 이해합니다

로 [발표](monitoring-classic-retirement.md), Azure Monitor에서 클래식 경고 2019 년 7 월에에서 사용 중지 됩니다. 마이그레이션 도구 마이그레이션을 자발적으로 트리거를 Azure portal에서 사용할 수 있으며 클래식 경고 규칙을 사용 하는 고객에 게 배포 됩니다.

이 문서에서는 안내 자발적 마이그레이션 도구 작동 원리입니다. 몇 가지 일반적인 문제에 대 한 업데이트 관리를 설명합니다.

## <a name="which-classic-alert-rules-can-be-migrated"></a>클래식 경고 규칙을 마이그레이션할 수 있습니까?

이 도구를 사용 하 여 거의 모든 클래식 경고 규칙을 마이그레이션될 수 있지만, 일부의 예외가 있습니다. 이 도구를 사용 하 여 다음 경고 규칙을 마이그레이션할 수 없습니다 (또는 2019 년 7 월의에서 자동 마이그레이션 중)

- 가상 머신 게스트 메트릭 (Windows 및 Linux)에서 클래식 경고 규칙입니다. [새로운 메트릭 경고에서 이러한 경고 규칙을 다시 만드는 방법에 지침을 참조 하세요.](#guest-metrics-on-virtual-machines)
- 클래식 저장소 메트릭에 대 한 클래식 경고 규칙입니다. [클래식 저장소 계정의 모니터링에서 지침을 참조 하세요.](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- 일부 저장소 계정 메트릭에서 클래식 경고 규칙입니다. [아래 세부 정보](#storage-account-metrics)

구독의 클래식 이러한 규칙에 나머지 규칙 마이그레이션되지만 이러한 규칙 수동으로 마이그레이션하는 데 필요 합니다. 자동 마이그레이션을 제공할 수 있는 이러한 기존 클래식 메트릭 경고 년 6 월 2020 새 경고로 전환 하려면 시간을 제공 하기 계속 됩니다. 그러나 새 클래식 경고 만들 수 있습니다 post 2019 년 6 월.

### <a name="guest-metrics-on-virtual-machines"></a>가상 머신에서 게스트 메트릭

게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수, 게스트 메트릭을 Azure Monitor 메트릭 사용자 지정 저장소를 전송할 수 있도록 해야 합니다. 진단 설정에서 Azure Monitor 싱크에 사용 하도록 설정 하려면 아래 지침을 따릅니다.

- [Windows Vm에 대 한 게스트 메트릭을 사용 하도록 설정](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux Vm 용 게스트 메트릭 사용](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

위의 단계가 완료 되 면 게스트 메트릭에 대 한 새로운 메트릭 경고를 만들 수 있습니다. 새로운 메트릭 경고를 다시 생성 한 후에 클래식 경고를 삭제할 수 있습니다.

### <a name="storage-account-metrics"></a>저장소 계정 메트릭

저장소 계정에서 모든 클래식 경고는 다음 메트릭에 대 한 이러한 경고를 제외 하 고 마이그레이션할 수 있습니다.

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

백분율 기준에 클래식 경고 규칙에 따라 마이그레이션하는 데 필요 합니다 [이전 및 새 storage 메트릭 간의 매핑을](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)합니다. 임계값을 사용할 수 있는 새 메트릭 항목을 절대 그대로 적절히 수정 해야 합니다.

AnonymousThrottlingError 및 SASThrottlingError 두 새 경고 만큼으로 분할 해야 하는 클래식 경고 규칙은 동일한 기능을 제공 하는 없는 결합 된 메트릭입니다. 임계값을 적절 하 게 적응 해야 합니다.

## <a name="roll-out-phases"></a>출시 단계

마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객에 게 단계별로 출시 됩니다. **구독 소유자** 구독 도구를 사용 하 여 마이그레이션할 준비가 되 면 이메일을 받게 됩니다.

> [!NOTE]
> 단계는 초기 단계에서 도구를 롤아웃하는 대부분의 구독 되지 않았는지 아직 마이그레이션할 준비가 표시 될 수 있습니다.

현재는 **하위 집합** 구독 하는 **만** 형식 마이그레이션 준비 완료 상태로 표시 된 다음 리소스에 대 한 클래식 경고 규칙이 있습니다. 이후 단계에서 자세한 리소스 종류에 대 한 지원이 추가 됩니다.

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

기본 제공 역할을 가진 모든 사용자의 **Monitoring Contributor** 구독 수준이 수준 마이그레이션을 트리거할 수 있게 됩니다. 다음 권한이 있는 사용자 지정 역할을 사용 하 여 사용자 마이그레이션을 트리거할 수도 있습니다.

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>일반적인 문제 및 재구성

나면 [마이그레이션 트리거](alerts-using-migration-tool.md), 마이그레이션 완료를 알리는에 제공 된 전자 메일 주소를 사용할지 또는 작업이 필요 하는 경우. 다음 섹션에서는 몇 가지 일반적인 문제 및 수정 하는 방법을 설명 합니다.

### <a name="validation-failed"></a>유효성 검사 실패

구독에서 클래식 경고 규칙에 몇 가지 최근 변경 내용으로 인해 구독을 마이그레이션할 수 없습니다. 이것이 일시적인 문제입니다. 마이그레이션 상태를 다시 전환 되 면 마이그레이션을 다시 시작할 수 있습니다 **마이그레이션에 대 한 준비** 몇 일 내에 있습니다.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>정책/범위 잠금 규칙을 마이그레이션에서 못하고 있습니다.

마이그레이션의 일부로, 새 메트릭 경고 및 새 작업 그룹 생기 며 클래식 경고 규칙 삭제 됩니다 (새 규칙이 생성 됩니다). 그러나 정책이 나 범위 잠금 리소스를 만들지 못하게 하지 못하고 있습니다. 정책 또는 범위 잠금에 따라 일부 또는 모든 규칙을 마이그레이션할 수 없습니다. 범위 잠금 정책을 일시적으로 제거 하 여이 문제를 해결 하 고 마이그레이션을 다시 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구를 사용 하는 방법](alerts-using-migration-tool.md)
- [마이그레이션 준비](alerts-prepare-migration.md)
