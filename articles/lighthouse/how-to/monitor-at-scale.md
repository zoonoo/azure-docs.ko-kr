---
title: 위임된 리소스 규모 모니터링
description: 관리하려는 고객 테넌트 전체에서 확장 가능한 방식으로 Azure Monitor Logs를 효과적으로 사용하는 방법을 알아봅니다.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122897"
---
# <a name="monitor-delegated-resources-at-scale"></a>위임된 리소스 규모 모니터링

서비스 공급자는 Azure 위임 리소스 관리를 위해 여러 고객 테넌트를 온보딩했을 수 있습니다. [Azure Lighthouse](../overview.md)를 사용하여 서비스 공급자는 여러 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

이 항목에서는 관리하려는 고객 테넌트의 확장 가능한 방식으로 [Azure Monitor Logs를](../../azure-monitor/platform/data-platform-logs.md) 사용하는 방법을 보여 주며 있습니다.

## <a name="create-log-analytics-workspaces"></a>로그 분석 작업 영역 만들기

데이터를 수집하려면 로그 분석 작업 영역을 만들어야 합니다. 이러한 로그 분석 작업 영역은 Azure Monitor에서 수집한 데이터에 대한 고유한 환경입니다. 각 작업 영역에는 자체 데이터 리포지토리 및 구성이 있으며 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장하도록 구성됩니다.

고객 테넌에서 이러한 작업 영역을 직접 만드는 것이 좋습니다. 이렇게 하면 데이터가 내보내지 않고 테넌트에 남아 있습니다. 또한 Log Analytics에서 지원하는 리소스 또는 서비스를 중앙 집중식으로 모니터링할 수 있으므로 모니터링하는 데이터 유형에 대한 유연성을 높일 수 있습니다.

Azure [CLI를](../../azure-monitor/learn/quick-create-workspace-cli.md)사용하거나 Azure [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)을 사용하여 [Azure 포털을](../../azure-monitor/learn/quick-create-workspace.md)사용하여 로그 분석 작업 영역을 만들 수 있습니다.

## <a name="deploy-policies-that-log-data"></a>데이터를 기록하는 정책 배포

Log Analytics 작업 영역을 만든 후에는 진단 데이터가 각 테넌트의 적절한 작업 영역으로 전송되도록 고객 계층에 [Azure 정책을](../../governance/policy/index.yml) 배포할 수 있습니다. 배포하는 정확한 정책은 모니터링하려는 리소스 유형에 따라 다를 수 있습니다.

정책 만들기에 대한 자세한 내용은 [자습서: 정책 만들기 및 관리를 참조하여 규정 준수를 적용합니다.](../../governance/policy/tutorials/create-and-manage.md) 이 [커뮤니티 도구는](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator) 선택한 특정 리소스 유형을 모니터링하는 정책을 만드는 데 도움이 되는 스크립트를 제공합니다.

배포할 정책을 결정한 경우 [이를 대규모로 위임된 구독에 배포할](policy-at-scale.md)수 있습니다.

## <a name="analyze-the-gathered-data"></a>수집된 데이터 분석

정책을 배포하면 각 고객 테넌트에서 만든 Log Analytics 작업 영역에 데이터가 기록됩니다. 관리되는 모든 고객에 대한 통찰력을 얻으려면 [Azure Monitor 통합 문서와](../../azure-monitor/platform/workbooks-overview.md) 같은 도구를 사용하여 여러 데이터 원본에서 정보를 수집하고 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure [모니터에](../../azure-monitor/index.yml)대해 자세히 알아보기.
- Azure [모니터 로그에](../../azure-monitor/platform/data-platform-logs.md)대해 자세히 알아보기.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
