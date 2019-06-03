---
title: Azure 진단 로그 개요
description: Azure 진단 로그에서 Azure Monitor 및 사용 하 여 Azure 리소스 내에서 발생 하는 이벤트를 알아야 하는 방법을 알아봅니다.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244872"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Azure 진단 로그 개요

**진단 로그** Azure 리소스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다. Azure Monitor에서는 두 가지 진단 로그 유형을 제공합니다.

* **테넌트 로그** - Azure Active Directory 로그 등, Azure 구독 밖에 존재하는 테넌트 수준 서비스로부터 오는 로그입니다.
* **리소스 로그** - 네트워크 보안 그룹, 저장소 계정 등, Azure 구독 내에 리소스를 배포하는 Azure 서비스로부터 오는 로그입니다.

    ![리소스 진단 로그와 다른 로그 유형 비교](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

이러한 로그의 내용은 Azure 서비스와 리소스 유형에 따라 달라집니다. 예를 들어 네트워크 보안 그룹 규칙 카운터와 Key Vault 감사는 두 가지 진단 로그 유형입니다.

이러한 로그에서 다른 합니다 [활동 로그](activity-logs-overview.md)합니다. 활동 로그는 Resource Manager를 사용 하 여, 예를 들어, 가상 컴퓨터 만들기 또는 논리 앱 삭제 구독의 리소스에서 수행 된 작업에 대 한 정보를 제공 합니다. 활동 로그는 구독 수준 로그. 리소스 수준 진단 로그는 Key Vault에서 비밀 가져오기 등과 같이 리소스 자체에서 수행된 작업에 대한 정보를 제공합니다.

이 로그도 게스트 OS 수준 진단 로그와 다릅니다. 게스트 OS 진단 로그는 가상 머신이나 다른 지원되는 리소스 유형 안에서 실행되는 에이전트가 수집합니다. 리소스 수준 진단 로그는 에이전트가 필요하지 않으며 Azure 플랫폼 자체에서 리소스 특정 데이터를 수집하고, 게스트 OS 수준 진단 로그는 가상 머신에서 실행되는 운영 체제 및 애플리케이션에서 데이터를 수집합니다.

일부 서비스는 여기서 설명하는 진단 로그를 지원하지 않습니다. [이 문서에는 진단 로그를 지원하는 서비스를 나열하는 섹션이 있습니다.](./../../azure-monitor/platform/diagnostic-logs-schema.md)

## <a name="what-you-can-do-with-diagnostic-logs"></a>진단 로그로 수행할 수 있는 작업
진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

![진단 로그의 논리적 배치](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* 감사 또는 수동 검사를 위해 [**Storage 계정**](../../azure-monitor/platform/archive-diagnostic-logs.md)에 저장합니다. **리소스 진단 설정**을 사용하여 보존 기간(일)을 지정할 수 있습니다.
* [하도록 Stream **Event Hubs** ](diagnostic-logs-stream-event-hubs.md) 타사 서비스 또는 Power BI와 같은 사용자 지정 분석 솔루션에서 수집 합니다.
* 데이터를 스토리지에 먼저 쓰지 않아도 Azure Monitor에 데이터가 즉시 기록되는 [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md)로 분석하세요.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

로그를 내보내는 것과 동일한 구독에 위치하지 않는 스토리지 계정 또는 Event Hubs 네임스페이스를 사용할 수 있습니다. 설정을 구성하는 사용자에게는 두 구독에 대한 적절한 RBAC 액세스가 있어야 합니다.

> [!NOTE]
>  현재는 보안 가상 네트워크 뒤에 있는 스토리지 계정에 네트워크 흐름 로그를 보관할 수 없습니다.

## <a name="diagnostic-settings"></a>진단 설정

리소스 진단 로그는 리소스 진단 설정을 사용하여 구성됩니다. 테넌트 진단 로그는 테넌트 진단 설정을 사용하여 구성됩니다. 서비스 제어에 대한 **진단 설정**:

* 진단 로그 및 메트릭을 보내는 위치(Storage 계정, Event Hubs 및/또는 Azure Monitor).
* 전송되는 로그 범주 및 메트릭 데이터의 전송 여부.
* 각 로그 범주를 스토리지 계정에 보존해야 하는 기간.
    - 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값 1에서 365 사이의 일 수 있습니다.
    - 보존 정책이 설정되었지만 스토리지 계정에 로그를 저장할 수 없는 경우(예: Event Hubs 또는 Log Analytics 옵션만 선택한 경우) 보존 정책은 적용되지 않습니다.
    - 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다. 삭제 프로세스는 자정(UTC)에 시작되지만, 스토리지 계정에서 로그가 삭제될 때까지 최대 24시간이 걸릴 수 있습니다.

이러한 설정은 사용 하거나 Azure PowerShell 및 CLI 명령 사용 하 여 포털에서 진단 설정에서 구성 합니다 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/)합니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>진단 로그에 대해 지원되는 서비스, 범주 및 스키마

지원되는 서비스 및 해당 서비스에서 사용되는 로그 범주 및 스키마에 대한 전체 목록은 [이 문서를 참조](../../azure-monitor/platform/diagnostic-logs-schema.md)하세요.

## <a name="next-steps"></a>다음 단계

* [**Event Hubs**로 리소스 진단 로그 스트림](diagnostic-logs-stream-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/)
* [Azure Storage에서 Azure Monitor를 사용하여 로그 분석](collect-azure-metrics-logs.md)
