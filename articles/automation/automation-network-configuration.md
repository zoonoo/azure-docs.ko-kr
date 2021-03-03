---
title: 네트워크 구성 세부 정보 Azure Automation
description: 이 문서에서는 Azure Automation 상태 구성, Azure Automation Hybrid Runbook Worker, 업데이트 관리, 변경 내용 추적 및 인벤토리에 필요한 네트워크 정보의 세부 정보를 제공 합니다.
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708886"
---
# <a name="azure-automation-network-configuration-details"></a>네트워크 구성 세부 정보 Azure Automation

이 페이지에서는 [Hybrid Runbook Worker 및 상태 구성과](#hybrid-runbook-worker-and-state-configuration) [업데이트 관리 및 변경 내용 추적 및 인벤토리에](#update-management-and-change-tracking-and-inventory)필요한 네트워킹 세부 정보를 제공 합니다.

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker 및 상태 구성

Hybrid Runbook Worker에는 다음 포트 및 Url이 필요 하며, [자동화 상태 구성은](automation-dsc-overview.md) Azure Automation와 통신 합니다.

* 포트: 아웃 바운드 인터넷 액세스에는 443만 필요
* 전역 URL: `*.azure-automation.net`
* US Gov 버지니아의 글로벌 URL: `*.azure-automation.us`
* 에이전트 서비스: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대 한 네트워크 계획

시스템 또는 사용자 Hybrid Runbook Worker Azure Automation에 연결 하 고 등록 하려면이 섹션에서 설명 하는 포트 번호 및 Url에 대 한 액세스 권한이 있어야 합니다. 또한 작업자는 Log Analytics 에이전트가 Azure Monitor Log Analytics 작업 영역에 연결 하 [는 데 필요한 포트 및 url](../azure-monitor/agents/agent-windows.md) 에 액세스할 수 있어야 합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터와 Hybrid Runbook Worker의 통신을 제한할 수 있습니다. [Azure Automation에서 사용 하는 dns 레코드](how-to/automation-region-dns-records.md) 를 검토 하 여 필요한 dns 레코드를 확인 합니다.

### <a name="configuration-of-private-networks-for-state-configuration"></a>상태 구성에 대 한 개인 네트워크 구성

노드가 개인 네트워크에 있는 경우 위에 정의 된 포트 및 Url이 필요 합니다. 이러한 리소스는 관리 노드에 대한 네트워크 연결을 제공하고 DSC가 Azure Automation과 통신할 수 있도록 합니다.

[WaitFor * 리소스](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)와 같이 노드 간에 통신하는 DSC 리소스를 사용하는 경우에는 노드 간 트래픽만 허용해야 합니다. 이러한 네트워크 요구 사항을 이해하려면 각 DSC 리소스에 대한 문서를 참조하세요.

TLS 1.2에 대 한 클라이언트 요구 사항을 이해 하려면 [Azure Automation에 대 한 tls 1.2 적용](automation-managing-data.md#tls-12-enforcement-for-azure-automation)을 참조 하세요.

## <a name="update-management-and-change-tracking-and-inventory"></a>업데이트 관리 및 변경 내용 추적 및 인벤토리

이 테이블의 주소는 업데이트 관리 및 변경 내용 추적 및 인벤토리에 모두 필요 합니다. 표 다음에 나오는 단락은 둘 다에도 적용 됩니다.

이러한 주소에 대 한 통신은 **포트 443** 를 사용 합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Automation 서비스 및 Log Analytics 작업 영역에 대 한 트래픽을 허용 하도록 네트워크 그룹 보안 규칙을 만들거나 Azure 방화벽을 구성 하는 경우 **GuestAndHybridManagement** 및 **azuremonitor** [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags) 를 사용 합니다. 네트워크 보안 규칙의 지속적인 관리를 간소화 합니다. Azure Vm에서 자동화 서비스에 안전 하 고 개인적으로 연결 하려면 [Azure 개인 링크 사용](./how-to/private-link-security.md)을 검토 하세요. 온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 가져오려면 [다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Automation 업데이트 관리 개요](update-management\overview.md)에 대해 알아봅니다.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에 대해 알아봅니다.
* [변경 내용 추적 및 인벤토리에](change-tracking\overview.md)대해 알아보세요.
* [Automation 상태 구성](automation-dsc-overview.md)에 대해 알아봅니다.