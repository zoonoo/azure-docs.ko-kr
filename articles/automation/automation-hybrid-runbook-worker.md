---
title: Azure Automation Hybrid Runbook Worker | Microsoft Docs
description: "이 문서에서는 로컬 데이터 센터의 컴퓨터에서 Runbook을 실행할 수 있도록 해주는 Azure 자동화의 기능인 Hybrid Runbook Worker를 설치하고 사용하는 방법에 대한 정보를 제공합니다."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/29/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9faa1c9d40c50b2ed70d5574f3fde79dad71c9e5
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="automate-resources-in-your-data-center-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker를 사용하여 데이터 센터의 리소스 자동화
Azure 자동화의 Runbook은 Azure 클라우드에서 실행되므로 로컬 데이터 센터의 리소스에 액세스할 수 없습니다.  Azure Automation의 Hybrid Runbook Worker 기능을 사용하면 데이터 센터에 있는 컴퓨터에서 Runbook을 실행하여 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure 자동화에서 저장되고 관리된 다음 하나 이상의 온-프레미스 컴퓨터에 전달됩니다.  

이 기능은 다음 이미지에 나와 있습니다.<br>  

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

데이터 센터에서 Hybrid Runbook Worker 역할을 할 컴퓨터를 하나 이상 지정하고 Azure 자동화에서 Runbook을 실행할 수 있습니다.  각 작업자에는 Microsoft Operations Management Suite 및 Azure 자동화 Runbook 환경에 연결된 Microsoft 관리 에이전트가 필요합니다.  Operations Management Suite는 관리 에이전트를 설치 및 유지 관리하고 작업자의 기능을 모니터링하는 데에만 사용됩니다.  Runbook 및 Runbook 실행 지침은 Azure 자동화에 의해 전달됩니다.

Hybrid Runbook Worker를 지원하기 위한 인바운드 방화벽 요구 사항은 없습니다. 로컬 컴퓨터의 에이전트는 클라우드에서 Azure 자동화와의 모든 통신을 시작합니다. Runbook이 시작되면 Azure 자동화에서 에이전트에게 전달되는 지침을 만듭니다. 그런 다음 에이전트는 Runbook을 실행하기 전에 Runbook 및 모든 매개 변수를 끌어옵니다.  또한 Azure Automation에서 Runbook에 사용되는 [자산](http://msdn.microsoft.com/library/dn939988.aspx)을 검색합니다.

> [!NOTE]
> DSC(필요한 상태 구성)로 Hybrid Runbook Worker 역할을 지원하는 서버의 구성을 관리하려면 이들을 DSC 노드로 추가해야 합니다.  DSC를 통한 관리를 위한 온보드에 대한 정보는 [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.           
><br>
>[관리 솔루션 업데이트](../operations-management-suite/oms-solution-update-management.md)를 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 OMS 작업 영역에 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다.  그러나 Automation 계정에서 이미 정의한 모든 Hybrid Worker 그룹에 등록되지 않았습니다.  솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 한 Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 컴퓨터를 추가할 수 있습니다.  이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.  

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker 그룹
각 Hybrid Runbook Worker는 에이전트를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 구성원입니다.  그룹은 단일 에이전트를 포함할 수 있지만 고가용성을 위해 그룹에 여러 에이전트를 설치할 수 있습니다.

Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다.  그룹의 구성원에 따라 요청을 처리할 작업자가 결정됩니다.  특정 작업자를 지정할 수 없습니다.

## <a name="hybrid-runbook-worker-requirements"></a>Hybrid Runbook Worker 요구 사항
Hybrid Runbook 작업을 실행하려면 온-프레미스 컴퓨터를 적어도 하나 이상 지정합니다.  이 컴퓨터에는 다음 항목이 있어야 합니다.

* Windows Server 2012 이상
* Windows PowerShell 4.0 이상  안정성 개선을 위해 컴퓨터에 Windows PowerShell 5.0을 설치하는 것이 좋습니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=50395)에서 최신 버전을 다운로드할 수 있습니다.
* 최소 2개 코어 및 4GB RAM

Hybrid Worker에 대한 다음 권장 사항을 고려하십시오.

* 고가용성을 위해 각 그룹에 여러 Hybrid Worker를 지정합니다.  
* Hybrid Worker는 서비스 관리 자동화 또는 System Center Orchestrator Runbook 서버와 공존할 수 있습니다.
* 작업이 완료되면 작업 데이터가 Azure 자동화로 다시 전송되므로 자동화 계정 지역이나 그와 가까운 위치에 있는 컴퓨터를 사용하는 것이 좋습니다.

### <a name="configure-proxy-and-firewall-settings"></a>프록시 및 방화벽 설정 구성
온-프레미스 Hybrid Runbook Worker를 사용하여 Microsoft OMS(Operations Management Suite) 서비스에 연결하고 등록하려면 아래 설명된 포트 번호 및 URL에 대한 액세스 권한이 있어야 합니다.  [Microsoft Monitoring Agent에 필요한 포트 및 URL](../log-analytics/log-analytics-windows-agents.md#network) 외에도 OMS에 연결합니다. 에이전트와 OMS 서비스 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인해야 합니다. 방화벽을 사용하여 인터넷에 대한 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다.

아래 정보는 Hybrid Runbook Worker에서 Automation과 통신하는 데 필요한 포트 및 URL을 나열합니다.

* 포트: 아웃바운드 인터넷 액세스에는 TCP 443만 필요
* 글로벌 URL: *.azure-automation.net

특정 지역에 대해 정의된 자동화 계정이 있고 해당 지역의 데이터 센터와 통신을 제한하려는 경우 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 미국 정부 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us |

이름 대신 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) xml 파일을 다운로드하여 검토하세요. 

> [!NOTE]
> 이 파일에는 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위(Compute, SQL 및 Storage 범위 포함)가 포함되어 있습니다. 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영하는 업데이트 파일이 매주 게시됩니다. 파일에 표시되는 새 범위는 적어도 1주일 동안 데이터 센터에서 사용되지 않습니다. Azure에서 실행되는 서비스를 정확하게 식별할 수 있도록 매주 새로운 xml 파일을 다운로드하고 사이트에서 필요한 변경 작업을 수행하세요. ExpressRoute 사용자는 매월 첫 번째 주에 Azure 공간의 BGP 광고를 업데이트하는 데 이 파일이 사용되고 있음을 알 수 있습니다. 
> 

## <a name="installing-hybrid-runbook-worker"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치 및 구성하려면 2가지 방법을 사용할 수 있습니다.  권장되는 방법은 Automation Runbook을 사용하여 Windows 컴퓨터를 구성하는 데 필요한 프로세스를 완전히 자동화하는 것입니다.  두 번째 방법은 단계별 절차에 따라 역할을 수동으로 설치하고 구성하는 것입니다.  

### <a name="automated-deployment"></a>자동화된 배포

다음 단계에 따라 Hybrid Worker 역할의 설치와 구성을 자동화합니다.  

1. Hybrid Runbook Worker 역할을 실행하는 컴퓨터에서 직접 또는 사용자 환경의 다른 컴퓨터에서 [PowerShell 갤러리](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript)의 *새로 OnPremiseHybridWorker.ps1* 스크립트를 다운로드한 후 작업자에 복사합니다.  

    *New-OnPremiseHybridWorker.ps1* 스크립트에는 실행 중 다음 매개 변수가 필요합니다.

  * *AutomationAccountName*(필수) - 자동화 계정의 이름  
  * *ResourceGroupName*(필수) - Automation 계정과 연결된 리소스 그룹의 이름  
  * *HybridGroupName*(필수) - 이 시나리오를 지원하는 Runbook에 대한 대상으로 지정할 Hybrid Runbook Worker 그룹의 이름 
  *  *SubscriptionID*(필수) - Automation 계정이 있는 Azure 구독 ID
  *  *WorkspaceName*(선택) - OMS 작업 영역 이름  OMS 작업 영역이 없는 경우 스크립트에서 하나를 만들어 구성합니다.  

     > [!NOTE]
     > 현재 OMS와 통합되도록 지원되는 Automation 영역은 **오스트레일리아 남동부**, **미국 동부 2**, **동남 아시아** 및 **서유럽**뿐입니다.  이러한 영역 중 하나에 Automation 계정에 없는 경우 스크립트에서 OMS 작업 영역을 만들지만 연결할 수 없다고 경고합니다.
     > 
2. 컴퓨터에서 관리자 모드의 **시작** 화면에서 **Windows PowerShell**을 시작합니다.  
3. PowerShell 명령줄 셸에서 다운로드한 스크립트가 포함된 폴더로 이동한 후 *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* 및 *-WorkspaceName* 매개 변수 값을 변경하여 실행합니다.

     > [!NOTE] 
     > 스크립트를 실행한 후에 Azure 인증을 묻는 메시지가 나타납니다.  구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 **로그인해야** 합니다.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. **NuGet**을 설치할지 묻는 메시지가 표시되고 Azure 자격 증명으로 인증을 받도록 요구됩니다.<br><br> ![New-OnPremiseHybridWorker 스크립트 실행](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. 스크립트가 완료되면 Hybrid Worker 그룹 블레이드에 새 그룹 및 멤버 수가 표시되고, 기존 그룹이 있으면 해당 멤버 수가 증가됩니다.  **Hybrid Worker 그룹** 블레이드의 목록에서 그룹을 선택하고 **Hybrid Worker** 타일을 선택합니다.  **Hybrid Worker** 블레이드에서 나열된 그룹의 각 멤버를 확인합니다.  

### <a name="manual-deployment"></a>수동 배포 
자동화 환경에 대해 처음 두 단계를 한 번 수행한 후 각 Worker 컴퓨터에 대해 나머지 단계를 반복합니다.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Operations Management Suite 작업 영역 만들기
Operations Management Suite 작업 영역이 아직 없는 경우 [작업 영역 관리](../log-analytics/log-analytics-manage-access.md)의 지침에 따라 작업 영역을 만듭니다. 이미 있는 경우에는 기존 작업 영역을 사용할 수 있습니다.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Operations Management Suite 작업 영역에 자동화 솔루션 추가
솔루션은 Operations Management Suite에 기능을 추가합니다.  자동화 솔루션은 Hybrid Runbook Worker에 대한 지원을 포함하여 Azure 자동화를 위한 기능을 추가합니다.  작업 영역에 솔루션을 추가할 때 다음 단계에서 설치할 에이전트 컴퓨터로 Worker 구성 요소를 자동으로 푸시다운합니다.

[솔루션 갤러리를 사용하여 솔루션을 추가하려면](../log-analytics/log-analytics-add-solutions.md) 의 지침에 따라 Operations Management Suite 작업 영역에 **자동화** 솔루션을 추가합니다.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Microsoft Monitoring Agent 설치
Microsoft 모니터링 에이전트는 컴퓨터를 Operations Management Suite에 연결합니다.  온-프레미스 컴퓨터에 에이전트를 설치하고 작업 영역에 연결하면 Hybrid Runbook Worker에 필요한 구성 요소가 자동으로 다운로드됩니다.

[Log Analytics에 Windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agents.md)의 지침에 따라 온-프레미스 컴퓨터에 에이전트를 설치합니다.  이 과정을 여러 컴퓨터에 반복하여 사용자의 환경에 여러 작업자를 추가합니다.

에이전트가 Operations Management Suite에 연결되면 Operations Management Suite **설정** 창의 **Connected Sources**(연결된 원본) 탭에 나열됩니다.  에이전트에서 자동화 솔루션 다운로드를 완료했는지 확인하려면 C:\Program Files\Microsoft Monitoring Agent\Agent에 **AzureAutomationFiles** 폴더가 있는지 확인합니다.  Hybrid Runbook Worker의 버전을 확인하려면 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\으로 이동하고 \\*version* 하위 폴더를 적어두세요.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Runbook 환경을 설치하고 Azure 자동화에 연결
에이전트를 Operations Management Suite에 추가하면 Automation 솔루션이 **Add-HybridRunbookWorker** cmdlet을 포함하는 **HybridRegistration** PowerShell 모듈을 푸시다운합니다.  이 cmdlet을 사용하여 컴퓨터에 Runbook 환경을 설치하고 Azure 자동화에 등록합니다.

관리자 모드에서 PowerShell 세션을 열고 다음 명령을 실행하여 모듈을 가져옵니다.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

그런 다음 아래 구문을 사용하여 **Add-HybridRunbookWorker** cmdlet을 실행합니다.

    Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Azure 포털의 **키 관리** 블레이드에서 이 cmdlet에 필요한 정보를 가져올 수 있습니다.  Automation 계정의 **설정** 블레이드에서 **키** 옵션을 선택하여 이 블레이드를 엽니다.

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **Name**은 Hybrid Runbook Worker 그룹의 이름입니다. 이 그룹이 자동화 계정에 이미 있으면 현재 컴퓨터가 추가되고,  그렇지 않으면 이 그룹이 추가됩니다.
* **끝점**은 **키 관리** 블레이드의 **URL** 필드입니다.
* **Token**은 **키 관리** 블레이드의 **기본 액세스 키**입니다.  

설치에 대해 자세한 정보를 받으려면 **Add-HybridRunbookWorker**와 함께 **-Verbose** 스위치를 사용합니다.

#### <a name="5-install-powershell-modules"></a>5. PowerShell 모듈 설치
Runbook은 Azure 자동화 환경에 설치된 모듈에 정의된 활동 및 cmdlet을 사용할 수 있습니다.  이러한 모듈은 온-프레미스 컴퓨터에 자동으로 배포되지 않으므로 수동으로 설치해야 합니다.  단, 기본적으로 설치되어 Azure Automation의 모든 Azure 서비스 및 활동에 사용되는 cmdlet에 대한 액세스를 제공하는 Azure 모듈은 예외입니다.

Hybrid Runbook Worker 기능의 주 목적은 로컬 리소스를 관리하는 것이므로 이러한 리소스를 지원하는 모듈을 설치해야 할 수 있습니다.  Windows PowerShell 모듈 설치에 대한 자세한 내용은 [모듈 설치](http://msdn.microsoft.com/library/dd878350.aspx) 를 참조하세요.  설치된 모듈은 Hybrid Worker가 자동으로 가져올 수 있도록 PSModulePath 환경 변수가 참조하는 위치에 있어야 합니다.  추가 정보는 [PSModulePath 설치 경로 수정](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)을 참조하세요. 

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker 제거 
그룹에서 하나 이상의 Hybrid Runbook Worker를 제거하거나 요구 사항에 따라 그룹을 제거할 수 있습니다.  온-프레미스 컴퓨터에서 Hybrid Runbook Worker를 제거하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.  
2. **설정** 블레이드에서 **키**를 선택하고 **URL** 및 **기본 액세스 키** 필드의 값을 확인합니다.  이 정보는 다음 단계에서 필요합니다.
3. 관리자 모드에서 PowerShell 세션을 열고 `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>` 명령을 실행합니다.  제거 과정에 대한 자세한 로그를 보려면 **-Verbose** 스위치를 사용합니다.

> [!NOTE]
> 컴퓨터의 Microsoft Monitoring Agent가 아닌 Hybrid Runbook Worker 역할의 기능 및 구성만을 제거합니다.  

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker 그룹 제거
그룹을 제거하려면 먼저 앞서 보았던 절차를 사용하여 그룹의 구성원인 모든 컴퓨터에서 Hybrid Runbook Worker를 제거한 후 다음 단계를 수행하여 그룹을 제거해야 합니다.  

1. Azure 포털에서 자동화 계정을 엽니다.
2. **Hybrid Worker 그룹** 타일을 선택하고 **Hybrid Worker 그룹** 블레이드에서 삭제하려는 그룹을 선택합니다.  특정 그룹을 선택한 후 **Hybrid Worker 그룹** 속성 블레이드가 표시됩니다.<br> ![Hybrid Runbook Worker 그룹 블레이드](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. 선택한 그룹에 대한 속성 블레이드에서 **삭제**를 클릭합니다.  이 작업을 확인하라는 메시지가 표시되고 계속하려면 **예**를 선택합니다.<br> ![그룹 삭제 확인 대화 상자](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> 이 프로세스를 완료하려면 몇 초 정도가 소요되며 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.  

## <a name="starting-runbooks-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 시작
[Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md) 에 Runbook을 시작하는 여러 가지 방법이 설명되어 있습니다.  Hybrid Runbook Worker는 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있는 **RunOn** 옵션을 추가합니다.  그룹을 지정하면 해당 그룹의 작업자가 Runbook을 검색하고 실행합니다.  이 옵션을 지정하지 않으면 평소처럼 Azure 자동화에서 Runbook이 실행됩니다.

Azure Portal에서 Runbook을 시작하면 **Azure** 또는 **Hybrid Worker**를 선택할 수 있는 **실행 대상** 옵션이 표시됩니다.  **Hybrid Worker**를 선택한 경우 드롭다운에서 그룹을 선택할 수 있습니다.

**RunOn** 매개 변수 사용.  Windows PowerShell에서 다음 명령을 사용하여 MyHybridGroup이라는 Hybrid Runbook Worker 그룹에서 Test-Runbook이라는 Runbook을 시작할 수 있습니다.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> **RunOn** 매개 변수는 Microsoft Azure PowerShell 버전 0.9.1에서 **Start-AzureAutomationRunbook** cmdlet에 추가되었습니다.  이전 버전을 설치한 경우 [최신 버전을 다운로드](https://azure.microsoft.com/downloads/)해야 합니다.  Windows PowerShell에서 Runbook을 시작할 워크스테이션에만 이 버전을 설치하면 됩니다.  작업자 컴퓨터에서 Runbook을 시작하려는 경우가 아니라면 해당 컴퓨터에 설치할 필요는 없습니다.  현재 Hybrid Runbook Worker에서 다른 Runbook을 통해 Runbook을 시작할 수는 없습니다. 이렇게 하려면 최신 버전의 Azure Powershell을 자동화 계정에 설치해야 합니다.  최신 버전은 Azure Automation에서 자동으로 업데이트되며 곧 작업자에 자동으로 푸시될 예정입니다.
>
>

## <a name="runbook-permissions"></a>Runbook 사용 권한
Hybrid Runbook Worker에서 실행하는 Runbook은 Azure 외부의 리소스에 액세스하기 때문에 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다.  Runbook은 로컬 리소스에 고유한 인증을 지정하거나 실행 계정을 지정하여 모든 Runbook에 대한 사용자 컨텍스트를 제공할 수 있습니다.

### <a name="runbook-authentication"></a>Runbook 인증
기본적으로 Runbook은 온-프레미스 컴퓨터에서 로컬 시스템 계정의 컨텍스트로 실행되므로 액세스할 리소스에 대한 자체 인증을 제공해야 합니다.  

여러 리소스를 인증할 수 있도록 자격 증명을 지정할 수 있는 cmdlet과 함께 Runbook의 [자격 증명](http://msdn.microsoft.com/library/dn940015.aspx) 및 [인증서](http://msdn.microsoft.com/library/dn940013.aspx) 자산을 사용할 수 있습니다.  다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다.  이 예제에서는 자격 증명 자산에서 자격 증명을 검색하고 변수 자산에서 컴퓨터 이름을 검색한 다음 Restart-Computer cmdlet에서 이러한 값을 사용합니다.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

[PSCredential 일반 매개 변수](http://technet.microsoft.com/library/jj129719.aspx)에 지정된 자격 증명으로 다른 컴퓨터에서 코드 블록을 실행할 수 있는 [InlineScript](automation-powershell-workflow.md#inlinescript)를 활용할 수도 있습니다.

### <a name="runas-account"></a>실행 계정
Runbook이 로컬 리소스에 고유한 인증을 제공하는 대신 Hybrid worker 그룹에 **실행** 계정을 지정할 수 있습니다.  로컬 리소스에 액세스 권한이 있는 [자격 증명 자산](automation-credentials.md)을 지정하고 그룹의 Hybrid Runbook Worker에서 실행될 때 모든 Runbook이 이러한 자격 증명으로 실행됩니다.  

자격 증명에 대한 사용자 이름은 다음 서식 중 하나여야 합니다.

* domain\username
* username@domain
* 사용자 이름(온-프레미스 컴퓨터에 로컬인 계정용)

Hybrid Worker 그룹에 실행 계정을 지정하려면 다음 절차를 사용합니다.

1. 로컬 리소스에 대한 액세스로 [자격 증명 자산](automation-credentials.md)을 만듭니다.
2. Azure 포털에서 자동화 계정을 엽니다.
3. **Hybrid Worker 그룹** 타일을 선택한 다음 그룹을 선택합니다.
4. **모든 설정** 및 **Hybrid Worker 그룹 설정**을 차례로 선택합니다.
5. **다음 계정으로 실행**을 **기본**에서 **사용자 지정**으로 변경합니다.
6. 자격 증명을 선택하고 **저장**을 클릭합니다.

### <a name="automation-run-as-account"></a>Automation 실행 계정
Azure에서 리소스를 배포하는 자동화된 빌드 프로세스의 일부로, 배포 시퀀스의 작업 또는 단계 집합을 지원하기 위해 온-프레미스 시스템을 조사해야 할 수 있습니다.  실행 계정을 사용하여 Azure에 대해 인증을 지원하려면 실행 계정 인증서를 설치해야 합니다.  

다음 PowerShell Runbook인 *Export-RunAsCertificateToHybridWorker*는 Azure Automation 계정에서 실행 인증서를 내보내고 다운로드하며 동일한 계정에 연결된 Hybrid Worker의 로컬 컴퓨터 인증서 저장소로 가져옵니다.  이 단계가 완료되면 작업자는 실행 계정을 사용하여 Azure를 성공적으로 인증할 수 있는지 확인합니다.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

*Export-RunAsCertificateToHybridWorker* Runbook을 `.ps1` 확장명의 컴퓨터에 저장합니다.  Runbook을 Automation 계정으로 가져오고 편집하여 `$Password` 변수 값을 사용자 고유의 암호로 변경합니다.  실행 계정을 사용하여 Runbook을 실행 및 인증하는 Hybrid Worker 그룹을 대상으로 Runbook을 게시한 후 실행합니다.  이 작업 스트림은 인증서를 로컬 컴퓨터 저장소로 가져오려는 시도를 보고하는데, 구독에 정의된 Automation 계정 수에 따라 그리고 인증이 성공했는지 여부에 따라 여러 줄로 표시합니다.  

## <a name="creating-runbooks-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker용 Runbook 만들기
Azure 자동화에서 실행되는 Runbook과 Hybrid Runbook Worker에서 실행되는 Runbook은 구조상 차이가 없습니다. 그러나 각각에서 사용하는 Hybrid Runbook Worker용 Runbook은 일반적으로 데이터 센터의 로컬 리소스를 관리하는 반면, Azure Automation의 Runbook은 일반적으로 Azure 클라우드의 리소스를 관리하기 때문에 각각에서 사용하는 Runbook은 서로 크게 다를 수 있습니다.

Azure 자동화에서 Hybrid Runbook Worker용 Runbook을 편집할 수 있지만 편집기에서 Runbook을 테스트하려는 경우 어려움이 있을 수 있습니다.  로컬 리소스에 액세스하는 PowerShell 모듈을 Azure 자동화 환경에 설치하지 못할 수 있으며, 이 경우 테스트에 실패합니다.  필요한 모듈을 설치한 경우에는 Runbook이 실행되지만 완전한 테스트에 필요한 로컬 리소스에 액세스할 수 없습니다.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 문제 해결
[Runbook 출력 및 메시지](automation-runbook-output-and-messages.md) 는 클라우드에서 실행되는 Runbook 작업처럼 Hybrid Worker에서 Azure 자동화로 전송됩니다.  Verbose 및 Progress 스트림을 다른 Runbook과 같은 방식으로 사용할 수도 있습니다.  

로그는 각 Hybrid Worker의 로컬에 저장되며 위치는 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes입니다.

Runbook이 정상적으로 완료되지 않고 작업 요약이 **일시 중단**상태를 표시하는 경우 [Hybrid Runbook Worker: Runbook 작업이 일시 중단 상태로 종료됨](automation-troubleshooting-hrw-runbook-terminates-suspended.md)문제 해결 문서를 검토합니다.   

## <a name="relationship-to-service-management-automation"></a>Service Management Automation과의 관계
[SMA(Service Management Automation)](https://technet.microsoft.com/library/dn469260.aspx)는 Azure 자동화에서 지원하는 것과 동일한 Runbook을 로컬 데이터 센터에서 실행할 수 있도록 해줍니다. SMA는 Windows Azure 팩이 SMA 관리를 위한 그래픽 인터페이스를 포함하므로 일반적으로 Windows Azure 팩과 함께 배포됩니다. Azure Automation과 달리 SMA는 API를 호스팅하는 웹 서버, Runbook 및 SMA 구성을 포함하는 데이터베이스 및 Runbook 작업을 실행하는 Runbook Worker를 포함하는 로컬 설치가 필요합니다. Azure 자동화는 클라우드에서 이러한 서비스를 제공하기 때문에 로컬 환경에서 Hybrid Runbook Worker를 유지 관리하기만 하면 됩니다.

기존 SMA 사용자는 [Hybrid Runbook Worker용 Runbook 만들기](#creating-runbooks-for-hybrid-runbook-worker)에 설명된 대로 리소스에 대한 자체 인증을 수행할 경우 Runbook을 Azure 자동화로 이동하여 변경 없이 Hybrid Runbook Worker에서 사용할 수 있습니다.  SMA의 Runbook은 Runbook에 대한 인증을 제공할 수 있는 작업자 서버에서 서비스 계정의 컨텍스트로 실행됩니다.

다음 조건을 사용하여 Azure 자동화 Hybrid Runbook Worker와 Service Management Automation 중 어떤 것이 요구 사항에 보다 적합한지 결정할 수 있습니다.

* SMA는 그래픽 관리 인터페이스가 필요한 경우 Windows Azure 팩에 연결된 해당 기본 구성 요소의 로컬 설치가 필요합니다. 많은 로컬 리소스는 로컬 Runbook Worker에 에이전트를 설치하기만 하면 되는 Azure Automation보다 높은 유지 관리 비용이 필요합니다. 에이전트가 Operations Management Suite에 의해 관리되므로 유지 관리 비용이 절감됩니다.
* Azure Automation은 해당 Runbook을 클라우드에 저장하여 온-프레미스 Hybrid Runbook Worker에 전달합니다. 보안 정책에서 이 동작을 허용하지 않는 경우에는 SMA를 사용해야 합니다.
* SMA는 System Center에 포함되므로 System Center 2012 R2 라이선스가 필요합니다. Azure Automation은 계층화된 구독 모델을 기반으로 합니다.
* Azure Automation에는 SMA에서 사용할 수 없는 그래픽 Runbook 등의 고급 기능이 있습니다.

## <a name="next-steps"></a>다음 단계
* Runbook을 시작하는 데 사용할 수 있는 여러 가지 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.  
* 텍스트 편집기를 사용하여 Azure 자동화에서 PowerShell 및 PowerShell 워크플로 Runbook을 작업하기 위한 여러 절차를 알아보려면 [Azure 자동화에서 Runbook 편집](automation-edit-textual-runbook.md)

