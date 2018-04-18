---
title: Azure Automation 시작
description: 이 문서에서는 Azure Automation 서비스에 대한 개요를 제공합니다. Azure Marketplace에서 제품 등록을 준비하는 데 필요한 설계 및 구현 세부 정보를 검토합니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2fcbbc2532e5cb9963922b4987ba0c7080fdb170
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-azure-automation"></a>Azure Automation 시작

이 문서에서는 Azure Automation 배포와 관련된 핵심 개념을 소개합니다. Azure에서 Automation을 처음 사용하거나 System Center Orchestrator와 같은 자동화 워크플로 소프트웨어를 사용한 경험이 있으면, Automation을 준비하고 등록하는 방법을 알아볼 수 있습니다. 이 문서를 읽은 후에는 프로세스 자동화 요구 사항을 지원하는 Runbook을 개발할 준비가 됩니다. 


## <a name="automation-architecture-overview"></a>Automation 아키텍처 개요

![Azure Automation 개요](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation은 SaaS(Software as a Service) 응용 프로그램으로, Runbook을 사용하여 프로세스를 자동화할 수 있는 확장성과 안정성이 뛰어난 다중 테넌트 환경을 제공하는 소프트웨어입니다. Azure, 다른 클라우드 서비스 또는 온-프레미스 환경에서 DSC(Desired State Configuration)를 사용하여 Windows 및 Linux 시스템에 대한 구성 변경을 관리할 수 있습니다. Runbook, 자산 및 실행 계정을 포함하여 Automation 계정의 엔터티는 사용자 구독 및 다른 구독의 다른 Automation 계정으로부터 격리됩니다.  

Azure에서 실행하는 Runbook은 Automation 샌드박스에서 실행됩니다. 샌드박스는 Azure 플랫폼에서 PaaS(Platform as a Service) 가상 머신으로 호스팅됩니다. 

Automation 샌드박스는 모듈, 저장소, 메모리, 네트워크 통신 및 작업 스트림을 포함하여 Runbook을 실행하는 모든 측면에 대한 테넌트 격리를 제공합니다. 이 역할은 서비스에서 관리됩니다. 역할은 Azure 또는 Automation 계정에서 액세스하거나 관리할 수 없습니다.         

로컬 데이터 센터 또는 다른 클라우드 서비스에서 리소스의 배포 및 관리를 자동화하려면, Automation 계정을 만든 후에 [HRW(Hybrid Runbook Worker)](automation-hybrid-runbook-worker.md) 역할을 실행할 VM을 하나 이상 지정할 수 있습니다. 각 Hybrid Runbook Worker에는 Microsoft Management Agent를 설치하고 Automation 계정이 있어야 합니다. 이 에이전트는 Azure Log Analytics 작업 영역에 연결되어야 합니다. Log Analytics는 설치를 부트스트랩하고, Microsoft Management Agent를 유지 관리하며, Hybrid Runbook Worker 기능을 모니터링하는 데 사용됩니다. Azure Automation은 Runbook 배달 및 실행 지침을 수행합니다.

여러 Hybrid Runbook Worker를 배포할 수 있습니다. Hybrid Runbook Worker를 사용하여 Runbook 및 부하 분산 Runbook 작업에 고가용성을 제공합니다. 경우에 따라 특정 작업 또는 환경에 맞게 Runbook 작업을 수행할 수 있습니다. Hybrid Runbook Worker의 Microsoft Monitoring Agent는 443 TCP 포트를 통해 Automation 서비스와의 통신을 시작합니다. Hybrid Runbook Worker에는 인바운드 방화벽 요구 사항이 없습니다.  

Hybrid Runbook Worker에서 실행되는 Runbook을 통해 사용자 환경의 다른 컴퓨터 또는 서비스에 대한 관리 작업을 수행할 수 있습니다. 또한 이 시나리오에서는 Runbook에서 다른 포트에 액세스해야 할 수도 있습니다. IT 보안 정책을 통해 네트워크의 컴퓨터에서 인터넷에 연결할 수 없는 경우 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 검토합니다. OMS 게이트웨이는 Hybrid Runbook Worker에 대한 프록시로 작동합니다. 작업 상태를 수집하고 Automation 계정에서 구성 정보를 받습니다.

Hybrid Runbook Worker에서 실행되는 Runbook은 컴퓨터에 속한 로컬 시스템 계정의 컨텍스트에서 실행됩니다. 로컬 Windows 컴퓨터에서 관리 작업을 수행할 때 보안 컨텍스트를 사용하는 것이 좋습니다. Runbook에서 로컬 컴퓨터 외부에 있는 리소스에 대한 작업이 실행되도록 하려면 Automation 계정에 보안 자격 증명 자산을 정의해야 합니다. Runbook에서 보안 자격 증명 자산에 액세스하고 이러한 자산을 사용하여 외부 리소스를 인증할 수 있습니다. Runbook에 [자격 증명](automation-credentials.md), [인증서](automation-certificates.md) 및 [연결](automation-connections.md) 자산을 사용할 수 있습니다. 자격 증명을 지정하는 데 사용할 수 있는 cmdlet이 포함된 자산을 사용하여 인증합니다.

Azure Automation에 저장된 DSC 구성을 가상 머신에 적용할 수 있습니다. 다른 실제 및 가상 머신은 자동화 DSC 풀 서버에서 구성을 요청할 수 있습니다. 온-프레미스 실제 또는 가상 Windows 및 Linux 시스템의 구성을 관리하기 위해 자동화 DSC 풀 서버를 지원하는 인프라를 배포할 필요가 없습니다. 자동화 DSC를 사용하여 관리할 각 시스템에서 아웃바운드 인터넷 액세스만 필요합니다. 통신은 443 TCP 포트를 통해 Log Analytics 서비스에 발생합니다.   

## <a name="prerequisites"></a>필수 조건

### <a name="automation-dsc"></a>Automation DSC
자동화 DSC를 사용하여 다음 머신을 관리할 수 있습니다.

* Windows 또는 Linux를 실행하는 Azure 가상 머신(클래식)
* Windows 또는 Linux를 실행하는 Azure 가상 머신
* Windows 또는 Linux를 실행하는 AWS(Amazon Web Services) 가상 머신
* Azure나 AWS 이외의 클라우드 또는 온-프레미스에 있는 실제/가상 Windows 컴퓨터
* Azure나 AWS 이외의 클라우드 또는 온-프레미스에 있는 실제/가상 Linux 컴퓨터

Windows 머신의 경우 최신 버전의 WMF(Windows Management Framework) 5가 설치되어 있어야 합니다. Linux 머신의 경우 [Linux용 PowerShell DSC 에이전트](https://www.microsoft.com/en-us/download/details.aspx?id=49150)의 최신 버전이 설치되어 있어야 합니다. PowerShell DSC 에이전트는 WMF 5를 사용하여 Automation과 통신합니다. 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
하이브리드 Runbook 작업을 실행하도록 컴퓨터를 지정하는 경우 컴퓨터는 다음 필수 구성 요소를 충족해야 합니다.

* Windows Server 2012 이상
* Windows PowerShell 4.0 이상 - 안정성을 향상시키기 위해 Windows PowerShell 5.0을 사용하는 것이 좋습니다. Microsoft 다운로드 센터에서 [새 버전을 다운로드](https://www.microsoft.com/download/details.aspx?id=50395)할 수 있습니다.
* .NET Framework 4.6.2 이상
* 둘 이상의 코어
* 4GB 이상의 RAM

### <a name="permissions-required-to-create-an-automation-account"></a>Automation 계정을 만드는 데 필요한 권한
Automation 계정을 만들거나 업데이트하고 이 문서에서 설명하는 작업을 완료하는 데 필요한 권한은 다음과 같습니다.   
 
* Automation 계정을 만들려면 **Microsoft.Automation** 리소스에 대한 소유자 역할과 동일한 권한이 있는 Azure AD(Azure Active Directory) 사용자 계정을 역할에 추가해야 합니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.  
* Azure Portal의 **Azure Active Directory** > **관리** > **앱 등록**에서 **앱 등록**이 **예**로 설정된 경우, Azure AD 테넌트에서 관리자가 아닌 사용자는 [Active Directory 응용 프로그램을 등록](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)할 수 있습니다. **앱 등록 설정**이 **아니요**로 설정된 경우, 이 작업을 수행하는 사용자는 Azure AD의 전역 관리자여야 합니다. 

사용자는 구독의 전역 관리자/공동 관리자 역할에 추가되기 전에 해당 구독의 Active Directory 인스턴스에 속한 멤버가 아닌 경우 Active Directory에 게스트로 추가됩니다. 이 시나리오에서는 **Automation 계정 추가** 페이지에 "만들 수 있는 권한이 없습니다."라는 메시지가 표시됩니다. 

사용자가 전역 관리자/공동 관리자 역할에 먼저 추가된 경우, 구독의 Active Directory 인스턴스에서 제거한 다음 Active Directory의 전체 사용자 역할에 다시 추가할 수 있습니다.

사용자 역할을 확인하려면 다음을 수행합니다.
1. Azure Portal에서 **Azure Active Directory** 창으로 이동합니다.
2. **사용자 및 그룹**을 선택합니다.
3. **모든 사용자**를 선택합니다. 
4. 특정 사용자를 선택한 후에 **프로필**을 선택합니다. 사용자 프로필의 **사용자 유형** 특성 값은 **Guest**가 아니어야 합니다.

## <a name="authentication-planning"></a>인증 계획
Azure Automation에서는 Azure, 온-프레미스 및 다른 클라우드 서비스에 있는 리소스에 대한 작업을 자동화할 수 있습니다. Runbook에서 필요한 작업을 수행하려면 리소스에 안전하게 액세스할 수 있는 권한이 있어야 합니다. 구독 내에서 필요한 최소 권한만 있어야 합니다.  

### <a name="what-is-an-automation-account"></a>Automation 계정이란? 
Azure Automation에서 cmdlet을 사용하여 리소스에 대해 수행하는 모든 자동화 작업은 Azure AD 조직의 ID 자격 증명 기반 인증을 사용하여 Azure에 인증됩니다.  포털에 로그인하는 데 사용하는 계정과는 별도로 Automation 계정은 Azure 리소스를 구성하고 사용합니다. 

Automation 계정에 포함되는 리소스는 다음과 같습니다.

* **인증서** Runbook 또는 DSC 구성의 인증에 사용되는 인증서가 포함됩니다. 인증서를 추가할 수도 있습니다.
* **연결**. Runbook 또는 DSC 구성에서 외부 서비스 또는 응용 프로그램에 연결하는 데 필요한 인증 및 구성 정보가 포함됩니다.
* **자격 증명**. 사용자 이름 및 암호와 같은 보안 자격 증명이 있는 **PSCredential** 개체가 포함됩니다. 자격 증명은 Runbook 또는 DSC 구성에서 인증하는 데 필요합니다.
* **통합 모듈**. Automation 계정에 포함되는 PowerShell 모듈입니다. PowerShell 모듈을 사용하여 Runbook 및 DSC 구성에서 cmdlet을 실행합니다.
* **일정**. 반복되는 빈도를 포함하여 지정된 시간에 Runbook을 시작하거나 중지하는 일정이 포함됩니다.
* **변수**. Runbook 또는 DSC 구성에서 사용할 수 있는 값이 포함됩니다.
* **DSC 구성**. 운영 체제 기능 또는 설정을 구성하는 방법이나 Windows 또는 Linux 컴퓨터에 응용 프로그램을 설치하는 방법을 설명하는 PowerShell 스크립트입니다.  
* **Runbook**. Windows PowerShell을 기반으로 하는 Automation에서 자동화된 프로세스를 수행하는 일단의 작업입니다.    

각 Automation 계정에 대한 자동화 리소스는 단일 Azure 지역과 연결됩니다. 그러나 Automation 계정을 사용하여 구독의 모든 리소스를 관리할 수 있습니다. 데이터 및 리소스를 특정 지역으로 격리해야 하는 정책이 있는 경우 여러 지역에서 Automation 계정을 만듭니다.

Azure Portal에서 Automation 계정을 만들면 다음 두 개의 인증 엔터티가 자동으로 만들어집니다.

* **실행 계정**. 이 계정에서 수행하는 작업은 다음과 같습니다.
  - Azure AD에 서비스 사용자를 만듭니다.
  - 인증서를 만듭니다.
  - Runbook을 사용하여 Azure Resource Manager 리소스를 관리하는 참가자 RBAC(역할 기반 액세스 제어)를 할당합니다.
* **클래식 실행 계정** 이 계정은 관리 인증서를 업로드합니다. 인증서는 Runbook을 통해 클래식 리소스를 관리하는 데 사용됩니다.

RBAC는 Resource Manager와 함께 사용되어 Azure AD 사용자 계정 및 실행 계정에 허용되는 작업을 부여할 수 있습니다. 또한 RBAC를 사용하여 해당 서비스 사용자를 인증할 수도 있습니다. 자세한 내용 및 Automation 권한을 관리하기 위한 모델 개발에 대한 도움말은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md) 문서를 참조하세요.  

#### <a name="authentication-methods"></a>인증 방법
다음 표에서는 Azure Automation이 지원하는 각 환경에서 사용할 수 있는 인증 방법을 요약하고 있습니다.

| 방법 | Environment 
| --- | --- | 
| Azure 실행 또는 클래식 실행 계정 |Azure Resource Manager 및 Azure 클래식 배포 |  
| Azure AD 사용자 계정 |Azure Resource Manager 및 Azure 클래식 배포 |  
| Windows 인증 |로컬 데이터 센터 또는 클라우드 서비스 공급자(Hybrid Runbook Worker 역할 사용) |  
| AWS(Amazon Web Services) 자격 증명 |Amazon 웹 서비스 |  

다음 문서에서는 이러한 환경에 대한 인증을 구성하기 위한 개요 및 구현 단계를 제공합니다. 이러한 문서는 해당 환경에 전적으로 사용할 기존 또는 새 계정을 사용하는 방법에 대해 설명합니다. 
* [독립 실행형 Azure Automation 계정 만들기](automation-create-standalone-account.md)
* [Azure 클래식 배포 및 Resource Manager를 사용하여 Runbook 인증](automation-create-aduser-account.md)
* [Amazon Web Services를 사용하여 Runbook 인증](automation-config-aws-account.md)
* [Automation 실행 계정 업데이트](automation-create-runas-account.md)

Azure 실행 계정 및 클래식 실행 계정과 관련하여 [Automation 실행 계정 업데이트](automation-create-runas-account.md)에서는 기존 Automation 계정을 포털의 실행 계정으로 업데이트하는 방법에 대해 설명합니다. 또한 Automation 계정이 처음부터 실행 계정 또는 클래식 실행 계정으로 구성되지 않은 경우 PowerShell을 사용하는 방법에 대해서도 설명합니다. 엔터프라이즈 CA(인증 기관)에서 발급한 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만들 수 있습니다. 이 구성을 사용하여 계정을 만드는 방법을 알아보려면 [Automation 실행 계정 업데이트](automation-create-runas-account.md)를 검토하세요.     
 
## <a name="network-planning"></a>네트워크 계획
Hybrid Runbook Worker에서 Log Analytics에 연결하고 등록하려면 이 섹션에서 설명하는 포트 번호 및 URL에 대한 액세스 권한이 있어야 합니다. 이는 [Microsoft Monitoring Agent에서 Log Analytics에 연결하는 데 필요한 포트 및 URL](../log-analytics/log-analytics-windows-agent.md)에 추가됩니다. 

에이전트와 Log Analytics 서비스 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. 방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다.

Hybrid Runbook Worker 역할에서 Automation 역할과 통신하려면 다음 포트와 URL이 필요합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 전역 URL: *.azure-automation.net

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

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

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요. 

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 계산, SQL 및 저장소 범위가 포함되어 있습니다. 
>
>업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다. 
>
> 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하는 데 이 파일을 사용하고 있음에 유의해야 합니다. 
> 

## <a name="creating-an-automation-account"></a>Automation 계정 만들기

다음 표에서는 Azure Portal에 Automation 계정을 만드는 방법을 소개하며, 각 배포 환경 유형과 그 차이점에 대해 설명합니다.  

|방법 | 설명 |
|-------|-------------|
| Azure Marketplace에서 **Automation & Control** 선택 | Azure Marketplace 제품은 동일한 리소스 그룹과 지역에 연결된 Automation 계정 및 Log Analytics 작업 영역을 만듭니다. Log Analytics와 통합하면 이를 사용하여 시간이 지남에 따라 Runbook 작업 상태 및 작업 스트림을 모니터링하고 분석할 수 있는 이점이 있습니다. 또한 Log Analytics의 고급 기능을 사용하여 문제를 보고하거나 조사할 수 있습니다. 기본적으로 사용되는 **변경 내용 추적** 및 **업데이트 관리** 솔루션도 배포합니다. |
| Marketplace에서 **Automation** 선택 | Log Analytics 작업 영역에 연결되지 않은 새 또는 기존 리소스 그룹에 Automation 계정을 만듭니다. **Automation & Control** 제품에서 사용 가능한 솔루션은 포함되지 않습니다. Automation을 소개하는 기본 구성입니다. Runbook 및 DSC 구성을 작성하는 방법 및 서비스 기능을 사용하는 방법을 알아볼 수 있습니다. |
| **관리** 솔루션 선택 | [업데이트 관리](../operations-management-suite/oms-solution-update-management.md), [작업 시간 외 VM 시작/중지](automation-solution-vm-management.md) 또는 [변경 내용 추적](../log-analytics/log-analytics-change-tracking.md)을 포함하여 **관리** 솔루션을 선택하면, 솔루션에서 기존 Automation 계정 및 Log Analytics 작업 영역을 선택하라는 메시지를 표시합니다. 솔루션에서 구독에 솔루션을 배포하는 데 필요한 Automation 계정 및 Log Analytics 작업 영역을 만드는 옵션을 제공합니다. |

### <a name="create-an-automation-account-thats-integrated-with-log-analytics"></a>Log Analytics와 통합된 Automation 계정 만들기
Automation을 등록하려면 Marketplace에서 **Automation & Control** 제품을 선택하는 것이 좋습니다. 이 방법을 사용하면 Automation 계정이 만들어지고 Log Analytics 작업 영역과의 통합이 설정됩니다. 또한 제품과 함께 사용할 수 있는 관리 솔루션을 설치하는 옵션도 제공됩니다.  

[독립 실행형 Automation 계정 만들기](automation-create-standalone-account.md)에서는 **Automation & Control** 제품을 등록하여 Automation 계정 및 Log Analytics 작업 영역을 만드는 과정을 단계별로 설명합니다. 서비스를 테스트하거나 미리 보기 위해 독립 실행형 Automation 계정을 만드는 방법을 알아볼 수 있습니다.  

**Automation & Control** Marketplace 제품을 사용하여 Automation 계정 및 Log Analytics 작업 영역을 만들려면 다음을 수행합니다.

1. 구독 관리자 역할의 멤버 및 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.
2. **새로 만들기**를 선택합니다.<br><br> ![Azure Portal에서 새로 만들기 선택](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. **Automation**을 검색합니다. 검색 결과에서 **Automation & Control**을 선택합니다.<br><br> ![Azure Marketplace에서 Automation & Control 검색 및 선택](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)<br>   
4. 제품에 대한 설명을 검토하고 **만들기**를 선택합니다.  
5. **Automation & Control**에서 **OMS 작업 영역**을 선택합니다. **OMS 작업 영역**에서 Automation 계정이 있는 Azure 구독에 연결된 Log Analytics 작업 영역을 선택합니다. Log Analytics 작업 영역이 없으면 **새 작업 영역 만들기**를 선택합니다. **OMS 작업 영역**에서 다음을 수행합니다. 
  1. **OMS 작업 영역**에 대해 새 작업 영역의 이름을 입력합니다.
  2. **구독**에 대해 연결할 구독을 선택합니다. 기본 선택 항목이 사용하려는 구독이 아니면 드롭다운 목록에서 구독을 선택합니다.
  3. **리소스 그룹**에 대해 리소스 그룹을 만들거나 기존 리소스 그룹을 선택할 수 있습니다.  
  4. **위치**에 대해 지역을 선택합니다. 자세한 내용은 [Azure Automation을 사용할 수 있는 지역](https://azure.microsoft.com/regions/services/)을 참조하세요. 솔루션은 체험 계층 및 노드당(OMS) 계층이라는 두 계층으로 제공됩니다. 체험 계층에서는 매일 수집되는 데이터의 양, 보존 기간 및 Runbook 작업 런타임 시간(분)이 제한됩니다. 노드당(OMS) 계층에서는 매일 수집되는 데이터의 양이 제한되지 않습니다.  
  5. **Automation 계정**을 선택합니다.  Log Analytics 작업 영역을 새로 만드는 경우 새 Log Analytics 작업 영역과 연결되는 Automation 계정도 만들어야 합니다. Azure 구독, 리소스 그룹 및 지역을 포함합니다. 
    1. **Automation 계정 만들기**를 선택합니다.
    2. **Automation 계정**의 **이름** 필드에서 Automation 계정의 이름을 입력합니다.
    다른 모든 옵션은 선택한 Log Analytics 작업 영역을 기반으로 자동으로 채워집니다. 이러한 옵션은 수정할 수 없습니다. 
    3. Azure 실행 계정은 제품에 대한 기본 인증 방법입니다. **확인**을 선택하면 구성 옵션의 유효성이 검사되고 Automation 계정이 만들어집니다. 진행률을 추적하려면 메뉴에서 **알림**을 선택합니다. 
    4. 그렇지 않으면, 기존 Automation 실행 계정을 선택합니다. 선택한 계정은 다른 Log Analytics 작업 영역에 아직 연결할 수 없습니다. 이 경우 알림 메시지가 표시됩니다. 계정이 이미 Log Analytics 작업 영역에 연결되어 있으면 다른 Automation 실행 계정을 선택하거나 하나를 새로 만듭니다.
    5. 필요한 정보를 입력하거나 선택한 후 **만들기**를 선택합니다. 정보가 확인되고 Automation 계정 및 실행 계정이 만들어집니다. **OMS 작업 영역** 창으로 자동으로 돌아갑니다.  
6. **OMS 작업 영역** 창에서 필요한 정보를 입력하거나 선택한 후 **만들기**를 선택합니다.  정보가 확인되고 작업 영역이 만들어집니다. 진행률을 추적하려면 메뉴에서 **알림**을 선택합니다. **솔루션 추가** 창으로 돌아갑니다.  
7. **Automation & Control** 설정에서 미리 선택된 권장 솔루션을 설치할지 확인합니다. 기본 옵션을 변경하면 나중에 솔루션을 개별적으로 설치할 수 있습니다.  
8. Automation 및 Log Analytics 작업 영역 등록을 진행하려면 **만들기**를 선택합니다. 모든 설정의 유효성이 검사된 다음, Azure에서 구독에 제품을 배포하려고 합니다. 이 프로세스에는 몇 초 정도 걸릴 수 있습니다. 진행률을 추적하려면 메뉴에서 **알림**을 선택합니다. 

제품이 등록되면 다음 작업을 수행할 수 있습니다.
* Runbook 만들기를 시작합니다.
* 사용 하도록 설정한 관리 솔루션을 사용합니다.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) 역할을 배포합니다.
* [Log Analytics](https://docs.microsoft.com/azure/log-analytics) 작업을 시작하여 클라우드 또는 온-프레미스 환경에서 리소스에서 생성된 데이터를 수집합니다.   

## <a name="next-steps"></a>다음 단계
* 새 Automation 계정이 Azure 리소스에 대해 인증할 수 있는지 확인합니다. 자세한 내용은 [Azure Automation 실행 계정 인증 테스트](automation-verify-runas-authentication.md)를 참조하세요.
* 작성을 시작하기 전에 Runbook을 만드는 방법 및 관련 고려 사항을 알아봅니다. 자세한 내용은 [Automation Runbook 형식](automation-runbook-types.md)을 참조하세요.


