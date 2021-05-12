---
title: Azure Monitor 에이전트 개요
description: 가상 머신의 게스트 운영 체제에서 모니터링 데이터를 수집하는 AMA(Azure Monitor 에이전트)의 개요입니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.custom: references_regions
ms.openlocfilehash: f1f1ea787406d900c8035c0462ef903b848d7e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608217"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor 에이전트 개요(미리 보기)
AMA(Azure Monitor 에이전트)는 가상 머신의 게스트 운영 체제에서 모니터링 데이터를 수집하여 이를 Azure Monitor에 전달합니다. 이 문서에서는 Azure Monitor 에이전트를 설치하는 방법과 데이터 수집을 구성하는 방법을 비롯하여 Azure Monitor 에이전트에 대한 개요를 제공합니다.

## <a name="relationship-to-other-agents"></a>다른 에이전트와의 관계
Azure Monitor 에이전트는 현재 Azure Monitor에서 사용하는 다음 에이전트를 대체하여 가상 머신에서 게스트 데이터를 수집합니다.

- [Log Analytics 에이전트](./log-analytics-agent.md) - 데이터를 Log Analytics 작업 영역으로 보내고 VM Insights 및 모니터링 솔루션을 지원합니다.
- [진단 확장](./diagnostics-extension-overview.md) - 데이터를 Azure Monitor 메트릭(Windows에만 해당), Azure Event Hubs 및 Azure Storage에 보냅니다.
- [Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md) - 데이터를 Azure Monitor 메트릭에 보냅니다(Linux에만 해당).

이 기능을 단일 에이전트로 통합하는 것 외에도 Azure Monitor 에이전트는 기존 에이전트에 비해 다음과 같은 혜택을 제공합니다.

- 모니터링의 범위입니다. 서로 다른 VM 집합으로부터 서로 다른 데이터 집합을 수집하는 구성을 중앙에서 수행합니다.  
- Linux 멀티 호밍: 데이터를 Linux VM에서 여러 작업 영역으로 보냅니다.
- Windows 이벤트 필터링: 수집되는 Windows 이벤트를 XPATH 쿼리를 사용하여 필터링합니다.
- 개선된 확장 관리: Azure Monitor 에이전트는 현재 Log Analytics 에이전트의 관리 팩 및 Linux 플러그인에 비해 투명성이 높고 제어가 용이한 새로운 확장 처리 방법을 사용합니다.

### <a name="changes-in-data-collection"></a>데이터 수집 변경
기존 에이전트에 사용하는 데이터 수집 정의 방법은 서로 간에 확연하게 다르며 각 방법에는 Azure Monitor 에이전트로 해결되는 문제점이 있습니다.

- Log Analytics 에이전트는 구성을 Log Analytics 작업 영역에서 가져옵니다. 이 에이전트는 중앙집중식으로 구성하기는 쉽지만 서로 다른 가상 머신별로 독립적인 정의를 지정하기에는 어렵습니다. 이 에이전트는 Log Analytics 작업 영역에만 데이터를 보낼 수 있습니다.

- 진단 확장에는 각 가상 머신에 대한 구성이 있습니다. 진단 확장은 서로 다른 가상 머신별로 독립적인 정의를 지정하기는 쉽지만 중앙집중식으로 관리하기에는 어렵습니다. 진단 확장은 Azure Monitor 메트릭, Azure Event Hubs 또는 Azure Storage에만 데이터를 보낼 수 있습니다. Linux 에이전트의 경우 Azure Monitor 메트릭에 데이터를 보내기 위해 오픈 소스 Telegraf 에이전트가 필요합니다.

Azure Monitor 에이전트는 [DCR(데이터 수집 규칙)](data-collection-rule-overview.md)을 사용하여 각 에이전트에서 수집할 데이터를 구성합니다. 데이터 수집 규칙을 사용하면 수집 설정을 대규모로 관리하면서도 계속해서 머신의 하위 집합에 대해 고유하고 범위가 지정된 구성을 사용할 수 있습니다. 데이터 수집 규칙은 작업 영역 및 가상 머신을 가리지 않으며 이는 데이터 수집 규칙을 머신 및 환경 전체에서 한 번만 정의하면 다시 사용할 수 있도록 해 줍니다. [Azure Monitor 에이전트를 위한 데이터 수집 구성(미리 보기)](data-collection-rule-azure-monitor-agent.md)을 참조하세요.

## <a name="should-i-switch-to-azure-monitor-agent"></a>Azure Monitor 에이전트로 전환해야 하나요?
Azure Monitor 에이전트는 [Azure Monitor에 일반적으로 제공되는 에이전트](agents-overview.md)와 함께 사용할 수 있지만 Azure Monitor 에이전트 퍼블릭 미리 보기 기간 동안 VM을 현재 에이전트에서 전환하는 방안을 고려할 수 있습니다. 이러한 결정을 내릴 때는 다음 요소를 고려해야 합니다.

- **환경 요구 사항.** Azure Monitor 에이전트는 지원되는 운영 체제, 환경 및 네트워킹 요구 사항이 현재 에이전트보다 많이 제한되어 있습니다. 새 운영 체제 버전 및 네트워킹 요구 사항 유형과 같은 향후 환경 지원은 Azure Monitor 에이전트에서만 제공될 가능성이 큽니다. Azure Monitor 에이전트가 내 환경을 지원하는지를 평가해야 합니다. 지원하지 않는다면 현재 에이전트를 유지해야 합니다. Azure Monitor 에이전트가 현재 환경을 지원한다면 전환을 고려해야 합니다.
- **퍼블릭 미리 보기 위험 용인 정도.** Azure Monitor 에이전트는 현재 지원되는 시나리오에 대해 철저한 테스트를 거치긴 했지만 아직 퍼블릭 미리 보기 상태에 있습니다. 버전 업데이트 및 기능 개선이 자주 발생하고 버그를 유발할 수 있습니다. 내 VM의 에이전트에서 데이터 수집을 중지할 수 있는 버그의 위험을 평가해야 합니다. 데이터 수집의 간격으로 인해 서비스에 큰 영향이 미치지 않는다면 Azure Monitor 에이전트로의 전환을 진행하세요. 불안정성을 허용할 마음이 없는 경우 Azure Monitor 에이전트가 안정 상태에 도달할 때까지 일반적으로 사용 가능한 에이전트를 유지해야 합니다.
- **현재 기능 및 새 기능 요구 사항.** Azure Monitor 에이전트는 필터링, 범위 지정, 다중 호밍 등 몇 가지 새로운 기능을 제공하지만 사용자 지정 로그 수집 및 솔루션과의 통합과 같은 다른 기능에 대해서는 아직 현재 에이전트에 미치지 못합니다. Azure Monitor의 새 기능 대부분은 Azure Monitor 에이전트에서만 사용할 수 있으므로 시간이 지나면서 새 에이전트에서만 사용할 수 있는 기능이 늘어날 것입니다. Azure Monitor 에이전트에 나에게 필요한 기능이 있는지 및 새 에이전트에서 다른 중요한 기능을 사용하기 위해 일시적으로 사용하지 않아도 되는 기능이 있는지를 고려해야 합니다. 필요한 모든 핵심 기능이 Azure Monitor 에이전트에 있다면 전환을 고려하세요. 필요한 중요 기능이 현재 에이전트에 있다면 Azure Monitor 에이전트가 그 수준에 도달할 때까지 현재 에이전트를 유지하세요.
- **재작업에 대한 용인 정도.** 배포 스크립트 및 온보딩 템플릿과 같은 리소스로 새 환경을 설정 중인 경우 Azure Monitor 에이전트를 일반적으로 사용할 수 있게 되었을 때 이러한 리소스에 대한 재작업을 수행할 수 있을지 고려해야 합니다. 이러한 재작업에 드는 노력이 최소한에 그친다면 지금은 현재 에이전트를 유지하세요. 재작업에 많은 노력이 소요된다면 새 에이전트로 새 환경을 설정하는 것이 좋습니다. 2021년에는 Azure Monitor 에이전트는 일반적으로 사용할 수 있게 될 것으로 예상되며 Log Analytics 에이전트에 대한 사용 중단 날짜가 발표됩니다. 사용 중단이 시작되더라도 현재 에이전트에 대한 지원은 몇 년 동안 계속될 것입니다.



## <a name="current-limitations"></a>현재 제한 사항
Azure Monitor 에이전트의 퍼블릭 미리 보기 기간 중에는 다음과 같은 제한 사항이 적용됩니다.

- Azure Monitor 에이전트는 VM Insights 및 Azure Security Center와 같은 솔루션 및 인사이트를 지원하지 않습니다. 현재 지원되는 유일한 시나리오는 여러분이 구성하는 데이터 수집 규칙을 사용하여 데이터를 수집하는 것입니다. 
- 데이터 수집 규칙은 대상으로 사용되는 Log Analytics 작업 영역과 동일한 지역에 만들어야 합니다.
- Azure Virtual Machines, Microsoft Azure Virtual Machine Scale Sets 및 Azure Arc 사용 서버는 현재 지원됩니다. Azure Kubernetes Service 및 기타 컴퓨팅 리소스 종류는 지원되지 않습니다.
- 가상 머신에는 다음 HTTPS 엔드포인트에 대한 액세스 권한이 있어야 합니다.
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="supported-regions"></a>지원되는 지역
Azure Monitor 에이전트는 현재 다음 지역의 리소스를 지원합니다.

- 동아시아
- 동남아시아
- 오스트레일리아 중부
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 캐나다 중부
- 북유럽
- 서유럽
- 프랑스 중부
- 독일 중서부
- 인도 중부
- 일본 동부
- 한국 중부
- 남아프리카 북부
- 스위스 북부
- 영국 남부
- 영국 서부
- 미국 중부
- 미국 동부
- 미국 동부 2
- 미국 중북부
- 미국 중남부
- 미국 서부
- 미국 서부 2
- 미국 중서부

## <a name="coexistence-with-other-agents"></a>다른 에이전트와 함께 사용 가능
Azure Monitor 에이전트는 기존 에이전트와 함께 사용할 수 있으므로 평가 또는 마이그레이션 중에도 기존 기능을 계속 사용할 수 있습니다. 이는 퍼블릭 미리 보기에서 기존 솔루션에 대한 지원이 제한되는 점을 감안할 때 특히 중요합니다. 하지만 중복 데이터를 수집하는 경우 쿼리 결과가 왜곡되고 데이터 수집 및 보존에 대한 추가 요금이 발생할 수 있으므로 주의해야 합니다.

예를 들어 VM insights는 Log Analytics 에이전트를 사용하여 성능 데이터를 Log Analytics 작업 영역으로 보냅니다. 사용자가 에이전트에서 Windows 이벤트 및 Syslog 이벤트를 수집하도록 작업 영역을 구성했을 수도 있습니다. Azure Monitor 에이전트를 설치하고 동일한 이벤트 및 성능 데이터에 대한 데이터 수집 규칙을 만드는 경우 중복 데이터가 생성되는 결과가 발생합니다.


## <a name="costs"></a>비용
Azure Monitor 에이전트에 대한 비용은 없지만 데이터 수집에 대해서는 요금이 발생할 수 있습니다. Log Analytics 데이터 수집과 보존 및 고객 메트릭에 대한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="data-sources-and-destinations"></a>데이터 원본 및 대상
다음 표에는 현재 Azure Monitor 에이전트로 데이터 수집 규칙을 사용하여 수집할 수 있는 데이터 형식과 해당 데이터를 보낼 수 있는 위치가 나열되어 있습니다. Azure Monitor 에이전트를 사용하여 다른 종류의 데이터를 수집하는 인사이트, 솔루션 및 기타 솔루션의 목록은 [Azure Monitor에서 모니터링하는 항목](../monitor-reference.md)을 참조하세요.


Azure Monitor 에이전트는 Azure Monitor 메트릭 또는 Azure Monitor 로그를 지원하는 Log Analytics 작업 영역에 데이터를 보냅니다.

| 데이터 원본 | 대상 | Description |
|:---|:---|:---|
| 성능        | Azure Monitor 메트릭<br>Log Analytics 작업 영역 | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값입니다. |
| Windows 이벤트 로그 | Log Analytics 작업 영역 | Windows 이벤트 로깅 시스템으로 전송되는 정보입니다. |
| Syslog             | Log Analytics 작업 영역 | Linux 이벤트 로깅 시스템으로 전송되는 정보입니다. |


## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 Azure Monitor 에이전트에서 지원하는 Windows 및 Linux 운영 체제 버전의 목록은 [지원되는 운영 체제](agents-overview.md#supported-operating-systems)를 참조하세요.



## <a name="security"></a>보안
Azure Monitor 에이전트에는 키가 필요하지 않지만 대신 [시스템이 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)가 필요합니다. 에이전트를 배포하기 전에 각 가상 머신에 시스템이 할당한 관리 ID를 사용하도록 설정해야 합니다.

## <a name="networking"></a>네트워킹
Azure Monitor 에이전트는 Azure 서비스 태그(AzureMonitor 및 AzureResourceManager 태그가 모두 필요함)를 지원하지만 Azure Monitor Private Link 범위 또는 직접 프록시에서는 아직 작동하지 않습니다.


## <a name="next-steps"></a>다음 단계

- Windows 및 Linux 가상 머신에 [Azure Monitor 에이전트를 설치](azure-monitor-agent-install.md)합니다.
- 에이전트에서 데이터를 수집하고 Azure Monitor로 보내기 위한 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md).