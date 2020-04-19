---
title: 서버용 Azure Arc(미리 보기) 개요
description: 서버용 Azure Arc를 사용하여 마치 Azure 리소스인 것처럼 Azure 외부에 호스팅되는 머신을 관리하는 방법에 대해 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, 업데이트 관리, 변경 내용 추적, 인벤토리, Runbook, Python, 그래픽, 하이브리드
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5fa39028f1041a063bab295adabf8145a8b46ae4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308780"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>서버용 Azure Arc(미리 보기)란?

서버용 Azure Arc(미리 보기)를 사용하면 회사 네트워크 또는 다른 클라우드 공급자의 Azure 외부에 호스팅되는 Windows 및 Linux 머신을 네이티브 Azure 가상 머신을 관리하는 방법과 비슷하게 관리할 수 있습니다. 하이브리드 머신은 Azure에 연결되면 연결된 머신이 되어 Azure에서 리소스로 취급됩니다. 연결된 머신마다 리소스 ID가 있고 구독 내에서 리소스 그룹의 일부로 관리되며, Azure Policy 및 태그 적용과 같은 표준 Azure 구성의 이점을 활용할 수 있습니다.

Azure 외부에 호스팅되는 하이브리드 머신에 이 환경을 제공하려면 Azure에 연결하려는 각 머신에 Azure Connected Machine 에이전트를 설치해야 합니다. 이 에이전트는 다른 기능을 제공하지 않으며, Azure [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)를 대체하지 않습니다. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.

>[!NOTE]
>이 미리 보기 릴리스는 평가 목적으로 제공되며 중요한 프로덕션 머신 관리에는 사용하지 않는 것이 좋습니다.
>

## <a name="supported-scenarios"></a>지원되는 시나리오

서버용 Azure Arc(미리 보기)는 연결된 머신에서 다음과 같은 시나리오를 지원합니다.

- Azure 가상 머신의 정책 할당과 동일한 환경을 사용하여 [Azure Policy 게스트 구성](../../governance/policy/concepts/guest-configuration.md)을 할당합니다.
- Log Analytics 에이전트를 통해 수집되어 머신이 등록된 Log Analytics 작업 영역에 저장된 로그 데이터는 이제 리소스 ID 같은 머신 관련 속성을 포함하며, 이러한 속성은 [리소스-컨텍스트](../../azure-monitor/platform/design-logs-deployment.md#access-mode) 로그 액세스를 지원하는 데 사용할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

서버용 Azure Arc(미리 보기)를 사용할 경우 특정 영역만 지원됩니다.

- WestUS2
- WestEurope
- WestAsia

대부분의 경우 설치 스크립트를 만들 때 선택한 위치는 머신의 위치와 지리적으로 가장 가까운 Azure 지역이어야 합니다. 미사용 데이터는 사용자가 지정한 지역을 포함하는 Azure 지리 내에 저장되며, 데이터 상주 요구 사항이 있는 경우 선택한 지역에 영향을 줄 수도 있습니다. 머신이 연결된 Azure 지역이 작동 중단의 영향을 받는 경우 연결된 머신에는 영향을 주지 않지만 Azure를 사용하는 관리 작업은 완료하지 못할 수 있습니다. 지역 가동 중단 시 복원력을 위해 지리적으로 중복된 서비스를 제공하는 여러 위치가 있는 경우 각 위치의 머신을 다른 Azure 지역에 연결하는 것이 가장 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="supported-operating-systems"></a>지원되는 운영 체제

Azure Connected Machine 에이전트를 공식적으로 지원하는 Windows 및 Linux 운영 체제 버전은 다음과 같습니다. 

- Windows Server 2012 R2 이상(Windows Server Core 포함)
- Ubuntu 16.04 및 18.04
- CentOS Linux 7
- SLES(SUSE Linux Enterprise Server) 15
- Red Hat Enterprise Linux(RHEL) 7
- Amazon Linux 2

>[!NOTE]
>이 Windows용 Connected Machine 에이전트의 미리 보기 릴리스는 영어를 사용하도록 구성된 Windows Server만 지원합니다.
>

### <a name="required-permissions"></a>필요한 사용 권한

- 머신을 등록하려면 **Azure Connected Machine Onboarding** 역할의 멤버여야 합니다.

- 머신을 읽고, 수정하고, 다시 등록하고, 삭제하려면 **Azure Connected Machine Resource Administrator** 역할의 멤버여야 합니다. 

### <a name="azure-subscription-and-service-limits"></a>Azure 구독 및 서비스 한도

서버용 Azure Arc(미리 보기)를 사용하여 머신을 구성하기 전에, Azure Resource Manager [구독 한도](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 및 [리소스 그룹 한도](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)를 검토하여 연결할 머신 수를 계획합니다.

## <a name="tls-12-protocol"></a>TLS 1.2 프로토콜

Azure로 전송되는 데이터의 보안을 보장하려면 TLS(전송 계층 보안) 1.2를 사용하도록 머신을 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**. 

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다. | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows Server 2012 R2 이상 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면|

### <a name="networking-configuration"></a>네트워킹 구성

Linux 및 Windows용 Connected Machine 에이전트는 TCP 포트 443을 통해 안전하게 Azure Arc로 아웃바운드 통신을 수행합니다. 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우 아래의 요구 사항을 검토하여 필요한 네트워크 구성을 파악하세요.

방화벽 또는 프록시 서버가 아웃바운드 연결을 제한하는 경우 아래에 나열된 URL이 차단되지 않았는지 확인합니다. 에이전트가 서비스와 통신하는 데 필요한 IP 범위 또는 도메인 이름만 허용하는 경우 다음 서비스 태그와 URL에 대한 액세스도 허용해야 합니다.

서비스 태그:

- AzureActiveDirectory
- AzureTrafficManager

URL:

| 에이전트 리소스 | Description |
|---------|---------|
|management.azure.com|Azure 리소스 관리자|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|게스트 구성|
|*-agentservice-prod-1.azure-automation.net|게스트 구성|
|*.his.hybridcompute.azure-automation.net|하이브리드 ID 서비스|

각 서비스 태그/지역의 IP 주소 목록은 JSON 파일 - [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. Microsoft는 각 Azure 서비스 및 여기에 사용되는 IP 범위를 포함하는 주간 업데이트를 게시합니다. 자세한 내용은 [서비스 태그](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)를 검토하세요.

대부분의 서비스에는 현재 서비스 태그 등록이 없기 때문에 서비스 태그 IP 주소 범위 정보 외에도 앞에서 나온 표의 URL이 필요합니다. 따라서 IP 주소는 변경될 수 있습니다. 방화벽 구성에 IP 주소 범위가 필요한 경우 모든 Azure 서비스에 대한 액세스를 허용하기 위해 **AzureCloud** 서비스 태그를 사용해야 합니다. 이러한 URL의 보안 모니터링 또는 검사를 해제하지 말고, 다른 인터넷 트래픽처럼 허용합니다.

### <a name="register-azure-resource-providers"></a>Azure 리소스 공급자 등록

서버용 Azure Arc(미리 보기)는 이 서비스를 사용하기 위해 구독의 다음 Azure 리소스 공급자를 사용합니다.

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

리소스 공급자가 등록되어 있지 않으면 다음 명령을 사용하여 등록할 수 있습니다.

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

[Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계에 따라 Azure Portal에서 리소스 공급자를 등록할 수도 있습니다.

## <a name="connected-machine-agent"></a>Connected Machine 에이전트

Windows 및 Linux용 Azure Connected Machine 에이전트 패키지는 아래에 나열된 위치에서 다운로드할 수 있습니다.

- Microsoft 다운로드 센터에서 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드합니다.
- Linux 에이전트 패키지는 선호하는 배포 패키지 형식(.RPM 또는 .DEB)을 사용하여 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)를 통해 배포됩니다.

>[!NOTE]
>이 미리 보기 기간에는 Ubuntu 16.04 또는 18.04에 적합한 한 가지 패키지만 릴리스되었습니다.

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 자세한 내용은 [여기](manage-agent.md)를 참조하세요.

### <a name="agent-status"></a>에이전트 상태

Connected Machine 에이전트는 5분마다 정기적인 하트비트 메시지를 서비스에 보냅니다. 서비스가 머신에서 이러한 하트비트 메시지 수신을 중지하면 해당 머신은 오프라인으로 간주되며 15~30분 내에 포털에서 상태가 자동으로 **연결 끊김**으로 변경됩니다. Connected Machine 에이전트로부터 후속 하트비트 메시지를 받으면 상태가 자동으로 **연결됨**으로 변경됩니다.

## <a name="install-and-configure-agent"></a>에이전트 설치 및 구성

하이브리드 환경의 머신을 Azure에 직접 연결할 때 요구 사항에 따라 다양한 방법을 사용할 수 있습니다. 다음 표를 통해 조직에 가장 적합한 방법을 결정할 수 있습니다.

| 방법 | Description |
|--------|-------------|
| 대화형 | [Azure Portal에서 머신 연결](onboard-portal.md)의 단계에 따라 머신 한 대 또는 약간의 머신에 에이전트를 수동으로 설치합니다.<br> Azure Portal에서 스크립트를 생성하고 머신에서 실행하여 에이전트의 설치 및 구성 단계를 자동화할 수 있습니다.|
| 대규모 | [서비스 주체를 사용하여 머신 연결](onboard-service-principal.md)의 지침에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 방법은 비 대화형으로 머신을 연결하는 서비스 주체를 만듭니다.|
| 대규모 | [Windows PowerShell DSC 사용](onboard-dsc.md) 방법에 따라 여러 머신의 에이전트를 설치하고 구성합니다.<br> 이 메서드는 서비스 주체를 사용하여 머신을 비 대화형으로 PowerShell DSC와 연결합니다. |

## <a name="next-steps"></a>다음 단계

- 서버용 Azure Arc(미리 보기)를 평가하려면 [Azure Portal에서 Azure에 하이브리드 머신 연결](onboard-portal.md) 문서를 따릅니다. 
