---
title: "OMS의 업데이트 관리 솔루션 | Microsoft Docs"
description: "이 문서는 이 솔루션을 사용하여 Windows 및 Linux 컴퓨터에 대한 업데이트를 관리하는 방법을 이해할 수 있도록 제공됩니다."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: e3d605b12a1db2fca1048be15e7b365e5336f663
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="update-management-solution-in-oms"></a>OMS의 업데이트 관리 솔루션

![업데이트 관리 기호](./media/oms-solution-update-management/update-management-symbol.png)

OMS의 업데이트 관리 솔루션을 사용하면 Azure, 온-프레미스 환경 또는 다른 클라우드 공급자에 배포된 Windows 및 Linux 컴퓨터에 대한 운영 체제 보안 업데이트를 관리할 수 있습니다.  모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

## <a name="update-management-in-azure-automation"></a>Azure Automation의 업데이트 관리

[Azure Automation](../automation/automation-offering-get-started.md) 계정에서 직접 가상 컴퓨터에 업데이트 관리를 사용하도록 설정할 수 있습니다.
Automation 계정에서 가상 컴퓨터에 업데이트 관리를 사용하는 방법을 알아보려면 [여러 가상 컴퓨터에 대한 업데이트 관리](../automation/manage-update-multi.md)를 참조하세요.


## <a name="solution-overview"></a>솔루션 개요
OMS를 통해 관리되는 컴퓨터는 다음을 사용하여 평가 및 업데이트 배포를 수행합니다.

* Windows 또는 Linux용 OMS 에이전트
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 컴퓨터용 Microsoft Update 또는 Windows Server Update Services

다음 다이어그램은 솔루션이 업데이트를 평가하고 작업 영역의 모든 연결된 Windows Server 및 Linux 컴퓨터에 보안 업데이트를 적용하는 방식과 관련된 동작 및 데이터 흐름의 개념도를 보여 줍니다.    

#### <a name="windows-server"></a>Windows Server
![Windows Server 업데이트 관리 프로세스 흐름](media/oms-solution-update-management/update-mgmt-windows-updateworkflow.png)

#### <a name="linux"></a>Linux
![Linux 업데이트 관리 프로세스 흐름](media/oms-solution-update-management/update-mgmt-linux-updateworkflow.png)

컴퓨터가 업데이트 준수를 검색한 후 OMS 에이전트는 OMS에 대량으로 정보를 전달합니다. Windows 컴퓨터에서 준수 검사는 기본적으로 12시간마다 수행됩니다.  검사 일정 외에도 MMA(Microsoft Monitoring Agent)가 다시 시작되면 업데이트 설치 전 그리고 업데이트 설치 후 15분 내에 업데이트 준수 검사가 시작됩니다.  Linux 컴퓨터의 경우 기본적으로 3시간마다 준수 검사가 수행되며, MMA 에이전트가 다시 시작되면 15분 내에 준수 검사가 시작됩니다.  

그런 다음 준수 정보가 처리되어 솔루션에 포함된 대시보드 또는 사용자 정의 쿼리/미리 지정된 쿼리를 사용하여 검색할 수 있는 대시보드에 요약됩니다.  솔루션은 동기화하도록 구성된 소스를 기반으로 컴퓨터가 최신 상태를 유지하는 방식을 보고합니다.  Windows 컴퓨터가 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft Update와 마지막으로 동기화된 시기에 따라 그 결과는 Microsoft Update가 표시하는 것과 다를 수 있습니다.  로컬 리포지토리에 보고하도록 구성된 Linux 컴퓨터와 공용 리포지토리에 보고하도록 구성된 Linux 컴퓨터도 마찬가지입니다.   

예약 배포를 만들어서 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다.  *선택 사항*으로 분류된 업데이트는 Windows 컴퓨터의 배포 범위에 포함되지 않으며, 필수 업데이트만 포함됩니다.  예약 배포는 명시적으로 컴퓨터를 지정하거나 특정 컴퓨터 집합의 로그 검색을 기반으로 하는 [컴퓨터 그룹](../log-analytics/log-analytics-computer-groups.md)을 선택하여 해당 업데이트를 받을 대상 컴퓨터를 정의합니다.  또한 업데이트 설치가 허용되는 시간을 승인하고 지정하는 일정을 지정합니다.  Azure Automation의 runbook에서 업데이트가 설치됩니다.  이러한 runbook을 볼 수 없고 구성이 필요하지 않습니다.  업데이트 배포가 생성되면 업데이트 배포는 포함된 컴퓨터에 지정된 시간에 마스터 업데이트 runbook을 시작하는 일정을 만듭니다.  이 마스터 runbook은 필수 업데이트를 설치하는 각 에이전트에서 하위 runbook을 시작합니다.       

업데이트 배포에 지정된 날짜 및 시간에 대상 컴퓨터는 배포를 병렬로 실행합니다.  업데이트가 여전히 필수인지 확인하기 위한 검사가 수행된 수 업데이트가 설치됩니다.  WSUS 클라이언트 컴퓨터의 경우 업데이트가 WSUS에서 승인되지 않으면 업데이트 배포가 실패합니다.  적용된 업데이트의 결과는 OMS로 전달되어 대시보드에서 또는 검색 이벤트를 통해 처리 및 요약됩니다.     

## <a name="prerequisites"></a>필수 조건
* 이 솔루션은 Windows Server 2008 이상에 대한 업데이트 평가 및 Windows Server 2008 R2 SP1 이상에 대한 업데이트 배포 수행을 지원합니다.  Nano 서버는 지원되지 않습니다.

    > [!NOTE]
    > Windows Server 2008 R2 SP1에 업데이트를 배포하려면 .NET Framework 4.5 및 WMF 5.0 이상이 필요합니다.
    >  
* Windows 클라이언트 운영 체제는 지원되지 않습니다.  
* Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Microsoft Update에 대한 액세스 권한이 있어야 합니다.  

    > [!NOTE]
    > System Center Configuration Manager에서 동시에 Windows 에이전트를 관리할 수 없습니다.  
    >
* CentOS 6(x86/x64) 및 7(x64)  
* Red Hat Enterprise 6(x86/x64) 및 7(x64)  
* SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)  
* Ubuntu 12.04 LTS 및 최신 x86/x64   
    > [!NOTE]  
    > Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

* Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.  

    > [!NOTE]
    > 여러 OMS 작업 영역에 보고하도록 구성된 Linux용 OMS 에이전트는 이 솔루션에서 지원되지 않습니다.  
    >

Linux용 OMS 에이전트를 설치하고 최신 버전을 다운로드하는 방법에 대한 자세한 내용은 [Linux용 Operations Management Suite 에이전트](https://github.com/microsoft/oms-agent-for-linux)를 참조하세요.  Windows용 OMS 에이전트를 설치하는 방법은 [Windows용 Operations Management Suite 에이전트](../log-analytics/log-analytics-windows-agent.md)를 검토하세요.  

### <a name="permissions"></a>권한
업데이트 배포를 만들려면 Automation 계정과 Log Analytics 작업 영역에서 contributor 역할이 부여되어야 합니다.  

## <a name="solution-components"></a>솔루션 구성 요소
이 솔루션은 Automation 계정 및 직접 연결된 에이전트 또는 Operations Manager와 연결된 관리 그룹에 추가되는 다음 리소스로 구성됩니다.

### <a name="management-packs"></a>관리 팩
System Center Operations Manager 관리 그룹이 OMS 작업 영역에 연결된 경우 다음 관리 팩이 Operations Manager에 설치됩니다.  이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩과 관련하여 아무 것도 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../log-analytics/log-analytics-om-agents.md)을 참조하세요.

### <a name="hybrid-worker-groups"></a>Hybrid Worker 그룹
이 솔루션을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 OMS 작업 영역에 직접 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다.  솔루션에서 관리하는 각 Windows 컴퓨터는 Automation 계정의 Hybrid Runbook Worker 그룹 블레이드에 *Hostname FQDN_GUID* 명명 규칙에 따라 나열됩니다.  계정의 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없으며, 만약 지정하면 오류가 발생합니다. 이러한 그룹은 관리 솔루션을 지원하는 용도로만 사용할 수 있습니다.   

하지만 솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다.  이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.  

## <a name="configuration"></a>구성
다음 단계를 수행하여 업데이트 관리 솔루션을 OMS 작업 영역에 추가하고 에이전트가 보고하는지 확인합니다. 작업 영역에 이미 연결된 Windows 에이전트는 추가 구성 없이 자동으로 추가됩니다.

다음 메서드를 사용하여 솔루션을 배포할 수 있습니다.

* Automation 및 제어 기능 또는 업데이트 관리 솔루션을 선택하는 Azure Portal의 Azure Marketplace의 경우
* OMS 작업 영역의 OMS 솔루션 갤러리의 경우

같은 리소스 그룹 및 지역에서 Automation 계정과 OMS 작업 영역이 이미 연결된 경우 Automation 및 제어를 선택하면 현재 구성을 확인한 후 이 솔루션만 설치되고 두 서비스에서 구성됩니다.  Azure Marketplace에서 업데이트 관리 솔루션을 선택해도 동일한 동작이 제공됩니다.  구독에 두 서비스 중 어떤 것도 배포하지 않은 경우 **새 솔루션 만들기** 블레이드의 단계에 따라 다른 미리 선택된 권장 솔루션을 설치할 것을 확인합니다.  필요에 따라 솔루션 갤러리에서 [OMS 솔루션 추가](../log-analytics/log-analytics-add-solutions.md)에 설명된 단계에 따라 OMS 작업 영역에 업데이트 관리 솔루션을 추가할 수 있습니다.  

### <a name="confirm-oms-agents-and-operations-manager-management-group-connected-to-oms"></a>OMS 에이전트 및 Operations Manager 관리 그룹이 OMS에 연결되었는지 확인

몇 분 후 다음 로그 검색을 실행하여 직접 연결된 Linux용 및 Windows용 OMS 에이전트가 OMS와 통신하는지 확인할 수 있습니다.

* Linux - `Type=Heartbeat OSType=Linux | top 500000 | dedup SourceComputerId | Sort Computer | display Table`.  

* Windows - `Type=Heartbeat OSType=Windows | top 500000 | dedup SourceComputerId | Sort Computer | display Table`

Windows 컴퓨터에서 다음 사항을 검토하여 에이전트가 OMS에 연결되었는지 확인할 수 있습니다.

1.  제어판에서 Microsoft Monitoring Agent를 열면 **OMS(Azure Log Analytics)** 탭에서 에이전트가 **Microsoft Monitoring Agent가 Microsoft Operations Management Suite 서비스에 성공적으로 연결되었습니다.**와 같은 메시지를 표시합니다.   
2.  Windows 이벤트 로그를 열고, **응용 프로그램 및 서비스 Logs\Operations Manager**로 이동하여 원본 서비스 커넥터에서 이벤트 ID 3000 및 5002를 검색합니다.  이러한 이벤트는 컴퓨터가 OMS 작업 영역에 등록되었으며 구성을 수신하고 있음을 나타냅니다.  

에이전트가 OMS 서비스와 통신할 수 없고 방화벽 또는 프록시 서버를 통해 인터넷과 통신하도록 구성된 경우 [Windows 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-windows-agent.md) 또는 [Linux 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-agent-linux.md)을 검토하여 방화벽 또는 프록시 서버가 올바르게 구성되었는지 확인합니다.

> [!NOTE]
> Linux 시스템이 프록시 또는 OMS Gateway와 통신하도록 구성되고 이 솔루션을 온보딩하는 경우 다음 명령을 수행하여 파일에 omiuser 그룹 읽기 권한을 부여하도록 *proxy.conf* 사용 권한을 업데이트하세요.  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`


평가가 수행된 후 새로 추가된 Linux 에이전트의 상태가 **업데이트됨**으로 표시됩니다.  이 프로세스는 최대 6시간까지 걸릴 수 있습니다.

Operations Manager 관리 그룹이 OMS와 통신하는지 확인하려면 [OMS와 Operations Manager 통합 유효성 검사](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms)를 참조하세요.

## <a name="data-collection"></a>데이터 수집
### <a name="supported-agents"></a>지원되는 에이전트
다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |솔루션은 Windows 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 필수 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |이 솔루션은 Linux 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필수 업데이트 설치를 시작합니다. |
| Operations Manager 관리 그룹 |예 |솔루션은 연결된 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집합니다.<br>Operations Manager 에이전트에서 Log Analytics로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 OMS 리포지토리로 전달됩니다. |
| Azure Storage 계정 |아니요 |Azure Storage는 시스템 업데이트에 대한 정보를 포함하지 않습니다. |

### <a name="collection-frequency"></a>수집 빈도
관리되는 Windows 컴퓨터 각각의 경우 검색은 하루에 두 번 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하여 상태가 변경되었는지, 상태가 변경되었다면 준수 검사가 시작되었는지 확인합니다.  관리되는 Linux 컴퓨터 각각의 경우 검색은 세 시간마다 수행됩니다.

관리되는 컴퓨터의 업데이트 데이터가 대시보드에 표시될 때까지 30분에서 6시간이 걸릴 수 있습니다.   

## <a name="using-the-solution"></a>솔루션 사용
업데이트 관리 솔루션을 OMS 작업 영역에 추가할 때 OMS 대시보드에 **업데이트 관리** 타일이 추가됩니다. 이 타일은 현재 환경의 컴퓨터 수 및 그래픽 표현과 업데이트 준수를 표시합니다.<br><br>
![업데이트 관리 요약 타일](media/oms-solution-update-management/update-management-summary-tile.png)  


## <a name="viewing-update-assessments"></a>업데이트 평가 보기
**업데이트 관리** 타일을 클릭하여 **업데이트 관리** 대시보드를 엽니다.<br><br> ![업데이트 관리 요약 대시보드](./media/oms-solution-update-management/update-management-dashboard.png)<br>

이 대시보드는 업데이트 상태를 운영 체제 및 업데이트 분류(중요, 보안 또는 기타(예: 정의 업데이트))에 따라 구체적으로 분류하여 보여 줍니다. 이 대시보드의 각 타일에 표시되는 결과는 배포에 대해 승인된 업데이트만 반영하며, 컴퓨터 동기화 원본을 기반으로 합니다.   **업데이트 배포** 타일을 선택하면 일정, 현재 실행 중인 배포, 완료된 배포를 확인하거나 새 배포를 예약할 수 있는 업데이트 배포 페이지로 리디렉션됩니다.  

특정 타일을 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있으며, 특정 범주 및 미리 정의된 기준을 쿼리하려면 **일반적인 업데이트 쿼리** 열에 제공되는 목록에서 하나를 선택합니다.    

## <a name="installing-updates"></a>업데이트 설치
작업 영역의 모든 Linux 및 Windows 컴퓨터에 대한 업데이트 평가가 완료된 후에는 *업데이트 배포*를 만들어서 필수 업데이트를 설치할 수 있습니다.  업데이트 배포는 하나 이상의 컴퓨터에 필수 업데이트를 예약하여 설치합니다.  배포 범위에 포함되어야 하는 컴퓨터 또는 컴퓨터 그룹 외에도 배포 날짜 및 시간을 지정합니다.  컴퓨터 그룹에 대한 자세한 내용은 [Log Analytics의 컴퓨터 그룹](../log-analytics/log-analytics-computer-groups.md)을 참조하세요.  업데이트 배포에 컴퓨터 그룹을 포함하는 경우 일정 생성 시 딱 한 번만 그룹 구성원을 평가합니다.  이후의 그룹 변경 내용은 반영되지 않습니다.  이 문제를 해결하려면 예약된 업데이트 배포를 삭제하고 다시 만들면 됩니다.

> [!NOTE]
> Azure Marketplace에서 배포된 VM은 기본적으로 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다.  이 동작은 작업 영역에 이 솔루션 또는 Windows VM을 추가한 후에도 변경되지 않습니다.  이 솔루션에서 업데이트를 능동적으로 관리하지 않는 경우 이 동작(업데이트 자동 적용)이 적용됩니다.  

Azure Marketplace에서 사용할 수 있는 주문형 RHEL(Red Hat Enterprise Linux) 이미지에서 만든 가상 컴퓨터는 Azure에 배포된 [RHUI(Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)에 액세스하도록 등록됩니다.  다른 모든 Linux 배포판은 지원되는 방법에 따라 배포판 온라인 파일 리포지토리에서 업데이트되어야 합니다.  

### <a name="viewing-update-deployments"></a>업데이트 배포 보기
**업데이트 배포** 타일을 클릭하여 기존 업데이트 배포의 목록을 볼 수 있습니다.  **예약됨**, **실행 중** 및 **완료됨**와 같은 상태별로 그룹화합니다.<br><br> ![업데이트 배포 일정 페이지](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

각 업데이트 배포에 표시된 속성을 다음 테이블에서 설명합니다.

| 속성 | 설명 |
| --- | --- |
| 이름 |업데이트 배포 이름입니다. |
| 일정 |일정의 형식입니다.  사용 가능한 옵션은 *한 번*, *매주 되풀이* 또는 *월별 되풀이*입니다. |
| 시작 시간 |업데이트 배포를 시작하도록 예약한 날짜 및 시간입니다. |
| 기간 |업데이트 배포를 실행할 수 있는 분 수입니다.  이 기간 내에서 모든 업데이트가 설치되지 않은 경우 나머지 업데이트는 다음 업데이트 배포까지 기다려야 합니다. |
| 서버 |업데이트 배포에 영향을 받는 컴퓨터의 수입니다.  |
| 가동 상태 |업데이트 배포의 현재 상태입니다.<br><br>가능한 값은 다음과 같습니다.<br>- 시작 안함<br>- 실행 중<br>- 완료됨 |

완료된 업데이트 배포를 선택하여 다음 테이블의 열이 포함된 세부 정보 화면을 확인합니다.  업데이트 배포가 아직 시작되지 않은 경우 이러한 열이 채워지지 않습니다.<br><br> ![업데이트 배포 결과의 개요](./media/oms-solution-update-management/update-management-deploymentresults-dashboard.png)

| 열 | 설명 |
| --- | --- |
| **컴퓨터 보기** | |
| Windows 컴퓨터 |업데이트 배포의 Windows 컴퓨터 수를 상태별로 나열합니다.  상태를 클릭하여 업데이트 배포에 대한 해당 상태와 함께 모든 업데이트 레코드를 반환하는 로그 검색을 실행합니다. |
| Linux 컴퓨터 |업데이트 배포의 Linux 컴퓨터 수를 상태별로 나열합니다.  상태를 클릭하여 업데이트 배포에 대한 해당 상태와 함께 모든 업데이트 레코드를 반환하는 로그 검색을 실행합니다. |
| 컴퓨터 설치 상태 |업데이트 배포에 포함된 컴퓨터 및 업데이트가 제대로 설치된 비율을 나열합니다. 항목 중 하나를 클릭하여 모든 누락된 중요 업데이트를 반환하는 로그 검색을 실행합니다. |
| **업데이트 보기** | |
| Windows 업데이트 |업데이트 배포에 포함된 Windows 업데이트 및 설치 상태를 각 업데이트별로 나열합니다.  특정 업데이트에 대한 모든 업데이트 레코드를 반환하는 로그 검색을 실행하려면 업데이트를 선택하고, 배포에 대한 모든 업데이트 레코드를 반환하는 로그 검색을 실행하려면 상태를 클릭합니다. |
| Linux 업데이트 |업데이트 배포에 포함된 Linux 업데이트 및 설치 상태를 각 업데이트별로 나열합니다.  특정 업데이트에 대한 모든 업데이트 레코드를 반환하는 로그 검색을 실행하려면 업데이트를 선택하고, 배포에 대한 모든 업데이트 레코드를 반환하는 로그 검색을 실행하려면 상태를 클릭합니다. |

### <a name="creating-an-update-deployment"></a>업데이트 배포 만들기
**새 업데이트 배포** 페이지를 열려면 화면 맨 위에 있는 **추가** 단추를 클릭하여 새 업데이트 배포를 만듭니다.  다음 테이블에서 속성의 값을 제공해야 합니다.

| 속성 | 설명 |
| --- | --- |
| 이름 |업데이트 배포를 식별하는 고유 이름입니다. |
| 표준 시간대 |시작 시간에 사용할 표준 시간대입니다. |
| 일정 형식 | 일정의 형식입니다.  사용 가능한 옵션은 *한 번*, *매주 되풀이* 또는 *월별 되풀이*입니다.  
| 시작 시간 |업데이트 배포를 시작할 날짜 및 시간입니다. **참고:** 즉시 배포해야 하는 경우 배포를 실행할 수 있는 가장 빠른 시간은 현재 시간으로부터 30분입니다. |
| 기간 |업데이트 배포를 실행할 수 있는 분 수입니다.  이 기간 내에서 모든 업데이트가 설치되지 않은 경우 나머지 업데이트는 다음 업데이트 배포까지 기다려야 합니다. |
| 컴퓨터 |업데이트 배포에 포함하고 대상으로 지정할 컴퓨터 또는 컴퓨터 그룹의 이름입니다.  드롭다운 목록에서 하나 이상의 항목을 선택합니다. |

<br><br> ![새 업데이트 배포 페이지](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>시간 범위
기본적으로 업데이트 관리 솔루션에서 분석된 데이터의 범위는 최근 1일 이내에 생성된 모든 연결된 관리 그룹에서 시작됩니다.

데이터의 시간 범위를 변경하려면 대시보드 위쪽에서 **데이터 기반**을 선택합니다. 지난 7일, 1일 또는 6시간 내에 생성되거나 업데이트된 레코드를 선택할 수 있습니다. 또는 **사용자 지정**을 선택하고 사용자 지정 날짜 범위를 지정할 수 있습니다.

## <a name="log-analytics-records"></a>Log Analytics 레코드
업데이트 관리 솔루션은 OMS 리포지토리에 두 가지 유형의 레코드를 만듭니다.

### <a name="update-records"></a>업데이트 레코드
**업데이트**라는 형식의 레코드가 각 컴퓨터에 설치되었거나 필요한 각 업데이트에 만들어집니다. 업데이트 레코드는 다음 테이블의 속성을 가집니다.

| 속성 | 설명 |
| --- | --- |
| 형식 |*업데이트* |
| SourceSystem |업데이트의 설치를 승인하는 원본입니다.<br>가능한 값은 다음과 같습니다.<br>- Microsoft 업데이트<br>- Windows 업데이트<br>- SCCM<br>- Linux 서버(패키지 관리자에서 가져옴) |
| 승인됨 |설치에 대한 업데이트를 승인했는지 여부를 명시합니다.<br> Linux 서버의 경우 패치가 OMS에서 관리되지 않기 때문에 현재 선택 사항입니다. |
| Windows용 분류 |업데이트의 분류입니다.<br>가능한 값은 다음과 같습니다.<br>- 응용 프로그램<br>- 중요 업데이트<br>- 정의 업데이트<br>- 기능 팩<br>- 보안 업데이트<br>- 서비스 팩<br>- 업데이트 롤업<br>- 업데이트 |
| Linux용 분류 |업데이트의 분류입니다.<br>가능한 값은 다음과 같습니다.<br>- 중요 업데이트<br>- 보안 업데이트<br>- 기타 업데이트 |
| 컴퓨터 |컴퓨터의 이름입니다. |
| InstallTimeAvailable |동일한 업데이트를 설치하는 다른 에이전트에서 설치 시간을 사용할 수 있는지 여부를 명시합니다. |
| InstallTimePredictionSeconds |동일한 업데이트를 설치하는 다른 에이전트를 기반으로 예상된 설치 시간(초)입니다. |
| KBID |업데이트에 대해 설명하는 KB 문서의 ID입니다. |
| ManagementGroupName |SCOM 에이전트의 경우 관리 그룹의 이름.  다른 에이전트의 경우 AOI-<workspace ID>입니다. |
| MSRCBulletinID |업데이트에 대해 설명하는 Microsoft 보안 게시판의 ID입니다. |
| MSRCSeverity |Microsoft 보안 게시판의 심각도입니다.<br>가능한 값은 다음과 같습니다.<br>- 중요<br>- 중요<br>- 보통 |
| 옵션 |업데이트가 선택 사항인지 여부를 명시합니다. |
| 제품 |업데이트가 되는 제품의 이름입니다.  **보기**를 클릭하여 브라우저에서 문서를 엽니다. |
| PackageSeverity |Linux 배포판 공급 업체에서 보고한 대로 이 업데이트에서 수정된 취약점의 심각도입니다. |
| PublishDate |업데이트가 설치된 날짜 및 시간입니다. |
| RebootBehavior |업데이트가 강제로 다시 부팅하는지 여부를 명시합니다.<br>가능한 값은 다음과 같습니다.<br>- canrequestreboot<br>- neverreboots |
| RevisionNumber |업데이트의 수정 버전 번호입니다. |
| SourceComputerId |컴퓨터를 고유하게 식별하는 GUID입니다. |
| TimeGenerated |레코드가 마지막 업데이트된 날짜 및 시간입니다. |
| 제목 |업데이트의 제목입니다. |
| UpdateID |업데이트를 고유하게 식별하는 GUID입니다. |
| UpdateState |이 컴퓨터에 업데이트가 설치되어 있는지 여부를 명시합니다.<br>가능한 값은 다음과 같습니다.<br>- 설치됨 - 이 컴퓨터에 업데이트가 설치되어 있습니다.<br>- 필요함 - 업데이트가 설치되어 있지 않으며 이 컴퓨터에 필요합니다. |

**업데이트**의 형식을 포함한 레코드를 반환하는 로그 검색을 수행하는 경우 검색에 의해 반환되는 업데이트를 요약하는 일련의 타일을 표시하는 **업데이트** 보기를 선택할 수 있습니다. **누락 및 적용된 업데이트** 및 **필수 및 선택적 업데이트** 타일에 있는 항목을 클릭하여 해당 업데이트 집합에 대한 보기의 범위를 지정할 수 있습니다. **목록** 또는 **테이블** 보기를 선택하여 개별 레코드를 반환합니다.<br>

![레코드 형식 업데이트로 로그 검색 업데이트 보기](./media/oms-solution-update-management/update-la-view-updates.png)  

**테이블** 보기에서 모든 레코드의 **KBID**를 클릭하여 KB 문서를 포함한 브라우저를 열 수 있습니다. 이를 통해 특정 업데이트의 세부 정보를 신속하게 읽을 수 있습니다.<br>

![타일 레코드 형식 업데이트로 로그 검색 테이블 보기](./media/oms-solution-update-management/update-la-view-table.png)

**목록** 보기에서 KBID 옆에 있는 **보기** 링크를 클릭하여 KB 문서를 엽니다.<br>

![타일 레코드 형식 업데이트로 로그 검색 목록 보기](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>UpdateSummary 레코드
형식이 **UpdateSummary**인 레코드가 각 Windows 에이전트 컴퓨터에 만들어집니다. 이 레코드는 업데이트를 위해 컴퓨터를 검색할 때마다 업데이트됩니다. **UpdateSummary** 레코드는 다음 테이블의 속성을 가집니다.

| 속성 | 설명 |
| --- | --- |
| 형식 |UpdateSummary |
| SourceSystem |OpsManager |
| 컴퓨터 |컴퓨터의 이름입니다. |
| CriticalUpdatesMissing |컴퓨터에 누락된 중요 업데이트 수입니다. |
| ManagementGroupName |SCOM 에이전트의 경우 관리 그룹의 이름. 다른 에이전트의 경우 AOI-<workspace ID>입니다. |
| NETRuntimeVersion |컴퓨터에 설치된 .NET 런타임의 버전입니다. |
| OldestMissingSecurityUpdateBucket |이 컴퓨터에서 가장 오랫동안 누락된 보안 업데이트가 게시된 이후 시간을 분류하는 버킷입니다.<br>가능한 값은 다음과 같습니다.<br>- 이전<br>- 180일 전<br>- 150일 전<br>- 120일 전<br>- 90일 전<br>- 60일 전<br>- 30일 전<br>- 최근 |
| OldestMissingSecurityUpdateInDays |이 컴퓨터에서 가장 오랫동안 누락된 보안 업데이트가 게시된 이후 일수입니다. |
| OsVersion |컴퓨터에 설치된 운영 체제의 버전입니다. |
| OtherUpdatesMissing |컴퓨터에 누락된 기타 업데이트 수입니다. |
| SecurityUpdatesMissing |컴퓨터에 누락된 보안 업데이트 수입니다. |
| SourceComputerId |컴퓨터를 고유하게 식별하는 GUID입니다. |
| TimeGenerated |레코드가 마지막 업데이트된 날짜 및 시간입니다. |
| TotalUpdatesMissing |컴퓨터에 누락된 업데이트 총수입니다. |
| WindowsUpdateAgentVersion |컴퓨터에 설치된 Windows Update 에이전트의 버전 번호입니다. |
| WindowsUpdateSetting |컴퓨터가 중요한 업데이트를 설치하는 방법에 대한 설정입니다.<br>가능한 값은 다음과 같습니다.<br>- 사용 안 함<br>- 설치하기 전에 알림<br>- 예약된 설치 |
| WSUSServer |컴퓨터에서 사용하도록 구성된 경우 WSUS 서버의 URL입니다. |

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 테이블은 이 솔루션에 의해 수집된 업데이트 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
| --- | --- |
| Type:Update OSType!=Linux UpdateState=Needed Optional=false Approved!=false &#124; measure count() by Computer |업데이트가 필요한 Windows 기반 서버 컴퓨터 |
| Type:Update OSType=Linux UpdateState!="Not needed" &#124; measure count() by Computer |업데이트가 필요한 Linux 서버 | 
| Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |업데이트가 누락된 모든 컴퓨터 |
| Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |특정 컴퓨터의 누락된 업데이트(값을 사용자 고유의 컴퓨터 이름으로 대체)|
| Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |중요 또는 보안 업데이트가 누락된 모든 컴퓨터 | 
| Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |업데이트가 수동으로 적용된 컴퓨터에 필요한 중요 또는 보안 업데이트 |
| Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |중요 또는 보안 필수 업데이트가 누락된 컴퓨터의 오류 이벤트 |
| Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |업데이트 롤업이 누락된 모든 컴퓨터 | 
| Type=Update UpdateState=Needed Optional=false &#124; Distinct Title |모든 컴퓨터에 누락된 업데이트 구분 | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Title, UpdateRunName |업데이트 실행에 실패한 업데이트가 있는 Windows 기반 서버 컴퓨터 | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Product, UpdateRunName |업데이트 실행에 실패한 업데이트가 있는 Linux 서버 | 
| Type=UpdateSummary &#124; measure count() by WSUSServer |WSUS 컴퓨터 멤버 자격 | 
| Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |자동 업데이트 구성 | 
| Type=UpdateSummary WindowsUpdateSetting=Manual |자동 업데이트를 사용하지 않는 컴퓨터 | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |패키지 업데이트를 사용할 수 있는 모든 Linux 컴퓨터 목록 | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |중요 또는 보안 취약점을 해결하는 패키지 업데이트를 사용할 수 있는 모든 Linux 컴퓨터 목록 | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" |사용 가능한 업데이트가 있는 모든 패키지 목록 | 
| Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |중요 또는 보안 취약점을 해결하는 업데이트를 사용할 수 있는 모든 패키지 목록 | 
| Type:UpdateRunProgress &#124; measure Count() by UpdateRunName |컴퓨터를 수정한 업데이트 배포 나열 | 
| Type:UpdateRunProgress UpdateRunName="DeploymentName" &#124; measure Count() by Computer |이 업데이트 실행에서 업데이트된 컴퓨터(값을 사용자의 업데이트 배포 이름으로 대체) | 
| Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |사용할 수 있는 업데이트가 있는 모든 "Ubuntu" 컴퓨터 목록 |

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager와 상호 작용

PC, 서버 및 모바일 장치를 관리하기 위해 System Center Configuration Manager에 투자하는 고객들은 SUM(소프트웨어 업데이트 관리) 주기의 일환으로 이 강력하고 완성도 있는 소프트웨어 업데이트 관리를 신뢰합니다.

Sytem Center Configuration Manager와 OMS 업데이트 관리 솔루션을 통합하는 방법을 알아보려면 [OMS 업데이트 관리와 System Center Configuration Manager 통합](../automation/oms-solution-updatemgmt-sccmintegration.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 업데이트 관리 솔루션과 관련된 문제 해결에 도움이 되는 정보를 제공합니다.

### <a name="how-do-i-troubleshoot-onboarding-issues"></a>온보딩 문제를 해결하려면 어떻게 할까요?
솔루션 또는 가상 컴퓨터를 온보딩하는 동안 문제가 발생할 경우 이벤트 ID가 4502인 **응용 프로그램 및 서비스 로그\운영 관리자** 이벤트 로그와 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**를 포함하고 있는 이벤트 메시지를 확인합니다.  다음 표에는 특정 오류 메시지 및 각각의 해결 방법이 설명되어 있습니다.  

| Message | 이유 | 해결 방법 |   
|----------|----------|----------|  
| 패치 관리용 컴퓨터를 등록할 수 없습니다.<br>예외와 함께 등록이 실패했습니다.<br>System.InvalidOperationException: {"메시지":"컴퓨터가 이미<br>다른 계정에 등록되었습니다. "} | 컴퓨터가 이미 업데이트 관리를 위한 다른 작업 영역에 등록되었습니다. | [하이브리드 Runbook 그룹을 삭제](../automation/automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)하여 오래된 아티팩트를 정리합니다.|  
| 패치 관리용 컴퓨터를 등록할 수 없습니다.<br>예외와 함께 등록이 실패했습니다.<br>System.Net.Http.HttpRequestException: 요청을 보내는 동안 오류가 발생했습니다. ---><br>System.Net.WebException: 기본 연결이<br>닫혔습니다. 받는 동안<br>예기치 않은 오류가 발생했습니다. ---> System.ComponentModel.Win32Exception:<br>클라이언트와 서버의 공통 알고리즘이 없기 때문에<br>서로 통신할 수 없습니다. | 프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](../automation/automation-offering-get-started.md#network-planning)|  
| 패치 관리용 컴퓨터를 등록할 수 없습니다.<br>예외와 함께 등록이 실패했습니다.<br>Newtonsoft.Json.JsonReaderException: 양의 무한대 값을 구문 분석하는 도중에 오류가 발생했습니다. | 프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](../automation/automation-offering-get-started.md#network-planning)| 
| <wsid>.oms.opinsights.azure.com 서비스에서 제공하는 인증서가<br>Microsoft 서비스에 사용된 인증서 기관에서<br>발급한 것이 아닙니다. 네트워크<br>관리자에 연락하여<br>TLS/SSL 통신을 가로채는 프록시를 실행 중인지 확인합니다. |프록시/게이트웨이/방화벽이 통신을 차단합니다. | [네트워크 요구 사항을 검토합니다.](../automation/automation-offering-get-started.md#network-planning)|  
| 패치 관리용 컴퓨터를 등록할 수 없습니다.<br>예외와 함께 등록이 실패했습니다.<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>자체 서명된 인증서를 만들지 못했습니다. ---><br>System.UnauthorizedAccessException: 액세스가 거부되었습니다. | 자체 서명된 인증서 생성 오류 | 시스템 계정에<br>다음 폴더에 대한 읽기 권한이 있는지 확인합니다.<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

### <a name="how-do-i-troubleshoot-update-deployments"></a>업데이트 배포를 문제를 해결하려면 어떻게 해야 하나요?
이 솔루션을 지원하는 OMS 작업 영역과 연결된 Automation 계정의 작업 블레이드에서 예약된 업데이트 배포에 포함된 업데이트의 배포를 담당하는 Runbook의 결과를 살펴볼 수 있습니다.  **Patch-MicrosoftOMSComputer** Runbook은 특정 관리형 컴퓨터를 대상으로 하는 자식 Runbook이며, 자세한 정보 스트림이 해당 배포에 대한 자세한 정보를 제공하는지 검토합니다.  출력에는 적용되는 필수 업데이트, 다운로드 상태, 설치 상태 및 추가 세부 정보가 표시됩니다.<br><br> ![배포 작업 상태 업데이트](media/oms-solution-update-management/update-la-patchrunbook-outputstream.png)<br>

자세한 내용은 [Automation Runbook 출력 및 메시지](../automation/automation-runbook-output-and-messages.md)를 참조하세요.   

## <a name="next-steps"></a>다음 단계
* [Log Analytics](../log-analytics/log-analytics-log-searches.md)의 로그 검색을 사용하여 자세한 업데이트 데이터 보기
* 관리되는 컴퓨터에 대한 업데이트 준수를 표시하는 [고유한 대시보드 만들기](../log-analytics/log-analytics-dashboards.md)
* 중요 업데이트가 컴퓨터에서 누락된 것으로 검색되거나 컴퓨터가 자동 업데이트를 사용하지 않도록 설정한 경우 [경고 만들기](../log-analytics/log-analytics-alerts.md)  
