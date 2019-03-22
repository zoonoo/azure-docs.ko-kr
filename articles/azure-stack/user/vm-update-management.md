---
title: VM 업데이트 및 Azure Stack을 사용 하 여 관리 | Microsoft Docs
description: Vm, 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 Azure Automation에 대 한 Azure Monitor를 사용 하 여 Windows 및 Azure Stack에 배포 된 Linux Vm을 관리 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316150"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM 업데이트 및 관리
Windows 및 Azure Stack을 사용 하 여 배포 된 Linux Vm을 관리 하려면 다음 Azure Automation 솔루션 기능을 사용할 수 있습니다.

- **[업데이트 관리](https://docs.microsoft.com/azure/automation/automation-update-management)** 합니다. 업데이트 관리 솔루션을 사용 하면 신속 하 게 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 평가 하 고 관리할 수 있습니다 이러한 Windows 및 Linux Vm에 대 한 필수 업데이트를 설치 하는 프로세스입니다.

- **[변경 내용 추적](https://docs.microsoft.com/azure/automation/automation-change-tracking)** 합니다. Linux 디먼 모니터링 되는 서버에 설치 된 소프트웨어, Windows 서비스, Windows 레지스트리 및 파일을 변경 처리를 위해 클라우드에서 Azure Monitor 서비스에 전송 됩니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 변경 내용 추적 대시보드의 정보를 사용하여 서버 인프라에서 수행한 변경 내용을 쉽게 확인할 수 있습니다.

- **[인벤토리](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** 합니다. 인벤토리 하는 Azure Stack virtual machine에 대 한 추적 설정 및 인벤토리 수집을 구성 하기 위한 브라우저 기반 사용자 인터페이스를 제공 합니다.

- **[Vm에 대 한 azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** 합니다. Vm에 대 한 azure Monitor는 Azure 및 Azure Stack 가상 컴퓨터 (VM)를 모니터링 하 고 대규모 가상 머신 확장 집합입니다. 성능과 Windows 및 Linux Vm의 상태를 분석 하 고 해당 프로세스 및 다른 리소스와 외부 프로세스에 대 한 종속성을 모니터링 합니다. 

> [!IMPORTANT]
> 이러한 솔루션은 Azure Vm을 관리 하는 데 사용 하는 것과 동일 합니다. Azure 및 Azure Stack Vm에서에서 관리 됩니다 동일한 인터페이스에서 동일한 방식으로 동일한 도구를 사용 하 여. Azure Stack Vm도 가격은 Azure Vm으로 동일한 Azure Stack을 사용 하 여 업데이트 관리, 변경 내용 추적, 인벤토리 및 Azure 가상 머신 모니터링 솔루션을 사용 하는 경우.

## <a name="prerequisites"></a>필수 조건
몇 가지 필수 구성 요소를 업데이트 하 고 Azure Stack Vm 관리에 이러한 기능을 사용 하기 전에 충족 되어야 합니다. 여기에 Azure portal 뿐만 아니라 Azure Stack 관리 포털에서 수행 해야 하는 단계 포함 됩니다.

### <a name="in-the-azure-portal"></a>Azure 포털에서 다음을 수행합니다.
Azure Stack Vm에 대 한 Vm, 인벤토리, 변경 내용 추적 및 업데이트 관리 Azure automation 기능에 대 한 Azure 모니터를 사용 하려면 먼저 Azure에서 이러한 솔루션을 사용 하도록 설정 해야 합니다.

> [!TIP]
> 이러한 기능을 Azure Vm에 대 한 사용에 이미 있는 경우 기존 LogAnalytics 작업 영역 자격 증명을 사용할 수 있습니다. LogAnalytics WorkspaceID 및 사용 하려는 기본 키를 이미 있는 경우 건너 뛰 세요 [절로](./vm-update-management.md#in-the-azure-stack-administration-portal)합니다. 이렇게 하지 않으면 새 LogAnalytics 작업 영역 및 automation 계정을 만들려면이 섹션에서 계속 합니다.

첫 번째 단계는 이러한 솔루션을 사용 하도록 설정 하는 것 [LogAnalytics 작업 영역 만들기](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) Azure 구독에 있습니다. Log Analytics 작업 영역에는 자체 데이터 리포지토리, 데이터 원본 및 솔루션을 사용 하 여 고유한 Azure Monitor 로그 환경입니다. 작업 영역을 만든 후에 WorkspaceID 및 키를 note 합니다. 이 정보를 보려면 작업 영역 블레이드로 이동를 클릭 **고급 설정**를 검토 합니다 **작업 영역 ID** 및 **기본 키** 값. 

다음을 수행 해야 [Automation 계정 만들기](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)합니다. Automation 계정에는 Azure Automation 리소스에 대 한 컨테이너입니다. 환경을 분리 하거나 추가 자동화 워크플로 및 리소스를 구성 하는 방법을 제공 합니다. Automation 계정이 만들어지면 필요는 인벤토리를 사용 하도록 변경 내용 추적, 하 관리 기능을 업데이트 합니다. 이렇게 하려면 각 기능을 사용 하도록 설정 하려면 다음이 단계를 수행 합니다.

1. Azure portal에서 사용 하려는 Automation 계정으로 이동 합니다.

2. 사용 하도록 설정 하려면 솔루션을 선택 (하거나 **인벤토리**를 **변경 내용 추적**, 또는 **업데이트 관리**).

3. 사용 된 **작업 영역을 선택 하는 중...**  드롭다운 목록에서 사용 하 여 Log Analytics 작업 영역을 선택 합니다.

4. 나머지 모든 정보가 올바른지 확인 한 다음 클릭 **사용** 솔루션을 사용 하도록 합니다.

5. 모든 세 가지 솔루션을 사용 하도록 설정 하려면 2-4 단계를 반복 합니다. 

   [![](media/vm-update-management/1-sm.PNG "Automation 계정 기능을 사용 하도록 설정")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>VM용 Azure Monitor 사용

VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 성능과 Windows 및 Linux Vm의 상태를 분석 하 고 해당 프로세스 및 다른 리소스와 외부 프로세스에 대 한 종속성을 모니터링 합니다.

VM용 Azure Monitor 솔루션에는 온-프레미스 또는 다른 클라우드 공급자에 호스트되는 VM의 성능 및 애플리케이션 종속성 모니터링에 대한 지원이 포함됩니다. 다음 세 가지 주요 기능이 심층적인 인사이트를 제공합니다.

1. Windows 및 Linux를 실행 하는 Azure Vm의 논리적 구성 요소: 미리 구성된 상태 기준에 따라 측정되고 평가 조건이 충족되면 경고합니다. 

2. 미리 정의 된 최신 성능 차트: 게스트 VM 운영 체제의 핵심 성능 메트릭을 표시합니다.

3. 종속성 맵: 다양한 리소스 그룹 및 구독에서 VM과 상호 연결된 구성 요소를 표시합니다.

Log Analytics 작업 영역을 만든 후 뿐만 아니라 Linux 및 Windows Vm에서 컬렉션에 대 한 작업 영역에서 성능 카운터를 사용 하도록 설정 하 고, 설치 하 고, 작업 영역에서은 ServiceMap 및 InfrastructureInsights 솔루션을 사용 하도록 설정 해야 합니다. 프로세스에 설명 되어는 [Vm에 대 한 Azure Monitor 배포](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms) 가이드입니다.

### <a name="in-the-azure-stack-administration-portal"></a>Azure Stack 관리 포털에서
Azure portal에서 Azure Automation 솔루션을 사용 하도록 설정한 후 다음 해야 클라우드 관리자로 Azure Stack 관리 포털에 로그인 하 고 다운로드 합니다 **Azure Monitor, 업데이트 및 구성 관리** 및 **Azure Monitor, 업데이트 및 Linux에 대 한 구성 관리** 확장 Azure Stack marketplace 항목. 

   ![Azure Monitor, 업데이트 및 구성 관리 확장 marketplace 항목](media/vm-update-management/2.PNG) 

을 Vm 맵 솔루션에 대 한 Azure Monitor를 사용 하려면 네트워킹 종속성에 대 한 통찰력 얻기도 다운로드 해야 합니다 **Azure Monitor 종속성 에이전트**:

   ![Azure Monitor 종속성 에이전트](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Azure Stack virtual machines에 대 한 업데이트 관리를 사용 합니다.
Azure Stack Vm에 대 한 업데이트 관리를 사용 하도록 설정 하려면 다음이 단계를 수행 합니다.

1. Azure Stack 사용자 포털에 로그인 합니다.

2. Azure Stack 사용자 포털에서 이러한 솔루션을 사용 하도록 설정 하려면 클릭 하려는 가상 머신의 확장 블레이드로 이동 **+ 추가**를 선택 합니다 **Azure 업데이트 및 구성 관리** 확장 하 고 **만들기**:

   [![](media/vm-update-management/3-sm.PNG "VM 확장 블레이드")](media/vm-update-management/3-lg.PNG#lightbox)

3. 이전에 만든 WorkspaceID 및 기본 키를 클릭 하 여 LogAnalytics 작업 영역을 사용 하 여 에이전트가 연결을 제공 **확인** 확장을 배포 합니다.

   [![](media/vm-update-management/4-sm.PNG "WorkspaceID 및 키를 제공합니다.")](media/vm-update-management/4-lg.PNG#lightbox) 

4. 에 설명 된 대로 합니다 [automation 업데이트 관리 설명서](https://docs.microsoft.com/azure/automation/automation-update-management)를 관리 하려는 각 VM에 대 한 업데이트 관리 솔루션을 사용 하도록 설정 해야 합니다. 작업 영역에 보고 하는 모든 Vm에 대 한 솔루션을 사용 하려면 **업데이트 관리**, 클릭 **컴퓨터를 관리할**를 선택한 후는 **사용 가능한 모든 향후 컴퓨터에서사용하도록설정** 옵션입니다.

   [![](media/vm-update-management/5-sm.PNG "WorkspaceID 및 키를 제공합니다.")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Azure Stack Vm 작업 영역에 해당 보고서에 대 한 각 솔루션을 사용 하도록 설정 하려면이 단계를 반복 합니다. 
  
Azure 업데이트 및 구성 관리 확장을 설정 하면 검색을 관리 되는 각 VM에 대해 하루에 두 번 수행 됩니다. API에는 상태가 변경 되었는지 여부를 확인 하려면 마지막 업데이트 시간에 대 한 쿼리를 15 분 마다 호출 됩니다. 상태가 변경되었으면 준수 검사가 시작됩니다.

Vm 검색 되는 업데이트 관리 솔루션에서 Azure Automation 계정에 나타납니다. 

   [![](media/vm-update-management/6-sm.PNG "WorkspaceID 및 키를 제공합니다.")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 관리되는 컴퓨터의 업데이트 데이터를 대시보드에 표시하는 데 30분에서 6시간이 걸릴 수 있습니다.

이제 Azure Vm과 함께 예약 된 업데이트 배포에서 Azure Stack Vm은 포함할 수 있습니다.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Azure Stack에서 실행 중인 Vm에 대 한 Azure Monitor를 사용 하도록 설정
VM에는 **Azure Monitor, 업데이트 및 구성 관리** 하며 **Azure 모니터 종속성 에이전트** 확장이 설치를 시작 데이터를 보고는 [Azure Monitor Vm에 대 한](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) 솔루션입니다. 

> [!TIP]
> 합니다 **Azure Monitor 종속성 에이전트** 확장 매개 변수가 필요 하지 않습니다. VM용 Azure Monitor 맵 Dependency Agent는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. 맵 데이터는 IT 보안 정책에서 네트워크의 컴퓨터가 인터넷에 연결하는 것을 허용하지 않는 경우 [OMS 게이트웨이](https://docs.microsoft.com/azure/azure-monitor/platform/gateway)를 통해 또는 직접 Log Analytics 에이전트에서 Azure Monitor 서비스로 항상 전송됩니다.

VM용 Azure Monitor는 가상 머신이 얼마나 잘 실행되고 있는지 확인하기 위한 여러 가지 KPI(핵심 성과 지표)를 대상으로 하는 성능 차트 집합을 포함하고 있습니다. 이러한 차트는 시간에 따른 리소스 사용률을 표시하므로 병목 상태 및 이상 현상을 식별하거나 각 머신을 나열하는 큐브 뷰로 전환하여 선택한 메트릭을 기반으로 리소스 사용률을 볼 수 있습니다. 성능을 다룰 때 고려해 야 할 다양 한 요소 있기는 Vm 모니터 핵심 운영 체제 성능 표시기에 대 한 Azure Monitor 프로세서, 메모리, 네트워크 어댑터 및 디스크 사용률과 관련이 있습니다. 성능은 상태 모니터링 기능을 보완하며 시스템 구성 요소 오류를 나타내는 문제를 공개하고, 효율성 향상을 위한 튜닝 및 최적화를 지원하고, 용량 계획을 지원하는 데 도움이 됩니다.

   ![Azure 모니터 성능 탭](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Azure Stack에서 실행 되는 Windows 및 Linux virtual machines에서 검색 된 응용 프로그램 구성 요소를 보기 관찰할 수 있습니다 Azure Monitor를 사용 하 여 두 가지 방법으로 Vm에 대 한 Azure Monitor에서 Vm의 그룹 또는 가상 머신에서 직접.
합니다 [Vm (미리 보기) 응용 프로그램 구성 요소를 이해 하는 지도 대 한 Azure Monitor를 사용 하 여](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) 문서를 맵 기능을 사용 하는 방법과 두 가지 관점의 환경을 이해할 수 있습니다.

   ![Azure 모니터 성능 탭](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 업데이트 관리 사용
Azure Stack Vm 수가 많은 경우 하는 경우 사용할 수 있습니다 [이 Azure Resource Manager 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) 쉽게 Vm에서 솔루션을 배포 하려면. 기존 Azure Stack VM에 Microsoft Monitoring Agent 확장을 배포 하 고 기존 Azure LogAnalytics 작업 영역에 추가 하는 템플릿입니다.
 
## <a name="next-steps"></a>다음 단계
[SQL Server VM 성능 최적화](azure-stack-sql-server-vm-considerations.md)




