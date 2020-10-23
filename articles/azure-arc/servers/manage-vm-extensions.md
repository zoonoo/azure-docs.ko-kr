---
title: Azure Arc 사용 서버를 사용 하 여 VM 확장 관리
description: Azure Arc 사용 서버는 Azure가 아닌 Vm을 사용 하 여 배포 후 구성 및 자동화 작업을 제공 하는 가상 머신 확장의 배포를 관리할 수 있습니다.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460889"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Azure Arc 사용 서버를 사용 하 여 가상 머신 확장 관리

VM (가상 컴퓨터) 확장은 Azure Vm에서 배포 후 구성 및 자동화 작업을 제공 하는 작은 응용 프로그램입니다. 예를 들어 가상 컴퓨터에 소프트웨어 설치, 바이러스 백신 보호가 필요한 경우, 또는 가상 컴퓨터 내부에서 스크립트를 실행하려면 VM 확장을 사용하면 됩니다.

Azure Arc 사용 서버를 사용 하면 azure VM 확장을 비 Azure Windows 및 Linux Vm에 배포 하 여 해당 수명 주기를 통해 온-프레미스, edge 및 기타 클라우드 환경에서 하이브리드 컴퓨터를 관리 하는 기능을 간소화할 수 있습니다. 가상 컴퓨터 또는 Arc 사용 서버에서 관리 하는 서버에서 다음 방법을 사용 하 여 VM 확장을 관리할 수 있습니다.

- [Azure Portal](manage-vm-extensions-portal.md)
- [Azure CLI](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- Azure [리소스 관리자 템플릿](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>주요 이점

Azure Arc 사용 서버 VM 확장 지원은 다음과 같은 주요 이점을 제공 합니다.

- [Azure Automation 상태 구성을](../../automation/automation-dsc-overview.md) 사용 하 여 중앙에서 구성을 저장 하 고 DSC VM 확장을 통해 사용 하도록 설정 된 하이브리드 연결 컴퓨터의 원하는 상태를 유지 관리 합니다.

- Log Analytics 에이전트 VM 확장을 통해 사용 하도록 설정 된 [Azure Monitor의 로그](../../azure-monitor/platform/data-platform-logs.md) 를 분석 하기 위해 로그 데이터를 수집 합니다. 이는 다양 한 소스의 데이터에서 복잡 한 분석을 수행 하는 데 유용 합니다.

- [VM용 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)를 사용 하 여 Windows 및 Linux vm의 성능을 분석 하 고 다른 리소스 및 외부 프로세스에 대 한 프로세스 및 종속성을 모니터링 합니다. 이는 Log Analytics 에이전트와 종속성 에이전트 VM 확장을 모두 사용 하도록 설정 하 여 수행 됩니다.

- 사용자 지정 스크립트 확장을 사용 하 여 하이브리드 연결 컴퓨터에서 스크립트를 다운로드 하 여 실행 합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성 또는 관리 작업에 유용합니다.

## <a name="availability"></a>가용성

VM 확장 기능은 지원 되는 [지역](overview.md#supported-regions)목록 에서만 사용할 수 있습니다. 이러한 지역 중 하나에서 컴퓨터를 등록 했는지 확인 합니다.

## <a name="extensions"></a>확장

이 릴리스에서는 Windows 및 Linux 컴퓨터에서 다음 VM 확장을 지원 합니다.

|확장 |OS |게시자 |추가 정보 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC 확장](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 에이전트 |Windows |Microsoft.EnterpriseCloud.Monitoring |[Windows 용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft 종속성 에이전트 | Windows |Microsoft.Compute | [Windows 용 종속성 에이전트 가상 컴퓨터 확장](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure 확장명 |[Linux 사용자 지정 스크립트 확장 버전 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Linux 용 PowerShell DSC 확장](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 에이전트 |Linux |Microsoft.EnterpriseCloud.Monitoring |[Linux 용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft 종속성 에이전트 | Linux |Microsoft.Compute | [Linux 용 종속성 에이전트 가상 머신 확장](../../virtual-machines/extensions/agent-dependency-linux.md) |

Azure 연결 된 컴퓨터 에이전트 패키지 및 확장 에이전트 구성 요소에 대 한 자세한 내용은 [에이전트 개요](agent-overview.md#agent-component-details)를 참조 하세요.

## <a name="prerequisites"></a>사전 준비 사항

이 기능은 구독에 있는 다음 Azure 리소스 공급자에 따라 달라 집니다.

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

아직 등록 하지 않은 경우 [Azure 리소스 공급자 등록](agent-overview.md#register-azure-resource-providers)의 단계를 따릅니다.

Linux 용 Log Analytics 에이전트 VM 확장에는 대상 컴퓨터에 Python 2.x가 설치 되어 있어야 합니다.

### <a name="connected-machine-agent"></a>Connected Machine 에이전트

컴퓨터가 Azure 연결 된 컴퓨터 에이전트에 대해 [지원 되는 버전](agent-overview.md#supported-operating-systems) 의 Windows 및 Linux 운영 체제와 일치 하는지 확인 합니다.

Windows 및 Linux에서이 기능으로 지원 되는 연결 된 컴퓨터 에이전트의 최소 버전은 1.0 릴리스입니다.

필요한 에이전트 버전으로 컴퓨터를 업그레이드 하려면 [에이전트 업그레이드](manage-agent.md#upgrading-agent)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Portal](manage-vm-extensions-portal.md)또는 [Azure Resource Manager 템플릿에서](manage-vm-extensions-template.md) [Azure CLI](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md)을 사용 하 여 VM 확장을 배포, 관리 및 제거할 수 있습니다.
