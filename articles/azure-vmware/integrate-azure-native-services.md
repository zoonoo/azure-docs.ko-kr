---
title: Azure 네이티브 서비스 통합 및 배포
description: Azure VMware Solution 워크로드를 모니터링하고 관리하기 위해 Microsoft Azure 네이티브 도구를 통합 및 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: e7af5617ee62f451f57daf806f70c7c433e40612
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758218"
---
# <a name="integrate-and-deploy-azure-native-services"></a>Azure 네이티브 서비스 통합 및 배포

Microsoft Azure 네이티브 서비스를 사용하면 하이브리드 환경(Azure, Azure VMware Solution, 온-프레미스)에서 VM(가상 머신)을 모니터링, 관리, 보호할 수 있습니다. 자세한 내용은 [VM에 지원되는 기능](../security-center/security-center-services.md)을 참조하세요.

Azure VMware Solution과 통합할 수 있는 Azure 네이티브 서비스는 다음과 같습니다.

- **Log Analytics 작업 영역:** 각 작업 영역에는 로그 데이터를 저장하기 위한 자체 데이터 리포지토리 및 구성이 있습니다. 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장하도록 구성됩니다. 새 VM 및 기존 VM에 대한 Azure Arc 사용 서버 VM 확장 지원을 사용하여 Log Analytics 에이전트를 쉽게 배포합니다. 
- **Azure Security Center:** 데이터 센터의 보안 상태를 강화하는 통합 인프라 보안 관리 시스템으로, 클라우드 또는 온-프레미스의 하이브리드 워크로드 전체에 Advanced Threat Protection을 제공합니다. 
- **Azure Sentinel** 은 클라우드 네이티브 SIEM(보안 정보 및 이벤트 관리) 솔루션입니다. 환경 전체에서 보안 분석, 경고 검색 및 자동화된 위협 응답을 제공합니다. Azure Sentinel은 Log Analytics 작업 영역의 상위에 위치하고 있습니다.
- **Azure Arc:** Azure 관리를 Azure VMware Solution, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯한 모든 인프라로 확장합니다. 
- **Azure 업데이트 관리:** 하이브리드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리합니다.
- **Azure Monitor:** 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석, 작업하기 위한 포괄적인 솔루션을 제공합니다. Azure Monitor는 배포가 필요하지 않습니다. 

이 문서에서는 Azure VMware Solution 프라이빗 클라우드에 Azure 네이티브 서비스를 통합합니다. 또한 도구를 사용하여 수명 주기 전체에서 VM을 관리하는 방법을 알아봅니다. 


## <a name="enable-azure-update-management"></a>Azure 업데이트 관리 사용

Azure Automation의 [Azure 업데이트 관리](../automation/update-management/overview.md)는 하이브리드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리합니다. 패치 준수를 모니터링하고 패치 편차 경고를 수정하기 위해 Azure Monitor로 전달합니다. 저장된 데이터를 사용하여 VM에 대한 업데이트 상태를 평가하기 위해 Azure 업데이트 관리를 Log Analytics 작업 영역에 연결해야 합니다.

1. [Azure Automation 계정을 만듭니다](../automation/automation-create-standalone-account.md). 

   >[!TIP]
   >[ARM(Azure Resource Manager) 템플릿을 사용하여 Automation 계정을 만들](../automation/quickstart-create-automation-account-template.md) 수 있습니다. ARM 템플릿을 사용하면 다른 배포 방법과 비교하여 단계가 줄어듭니다.

1. [Automation 계정에서 업데이트 관리를 사용하도록 설정합니다.](../automation/update-management/enable-from-automation-account.md)  그러면 Log Analytics 작업 영역이 Automation 계정에 연결됩니다. 또한 업데이트 관리에서 Azure VM과 비 Azure VM을 사용하도록 설정합니다.

   - 작업 영역이 있는 경우 **업데이트 관리** 를 선택합니다. 그런 다음 Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용** 을 선택합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

   - 새 Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. [CLI](../azure-monitor/logs/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 또는 [Azure Resource Manager 템플릿](../azure-monitor/logs/resource-manager-workspace.md)으로 작업 영역을 만들 수도 있습니다.
 
1. 업데이트 관리를 사용하도록 설정했으면 [VM에 업데이트를 배포하고 결과를 검토](../automation/update-management/deploy-updates.md)할 수 있습니다. 


## <a name="enable-azure-security-center"></a>Azure Security Center 활성화

Azure Security Center는 Azure VMware Solution 및 온-프레미스 VM(가상 머신) 전체에 Advanced Threat Protection을 제공합니다. Azure VMware Solution VM의 취약성을 평가하고 필요에 따라 경고를 발생시킵니다. 해당 보안 경고를 Azure Monitor에 전달하여 해결할 수 있습니다. 

Azure Security Center는 다음을 비롯한 다양한 기능을 제공합니다.
- 파일 무결성 모니터링
- 파일리스 공격 탐지
- 운영 체제 패치 평가 
- 잘못된 보안 구성 평가
- Endpoint Protection 평가

>[!NOTE]
>Azure Security Center는 배포가 필요하지 않은 미리 구성된 도구이지만 Azure Portal에서 사용하도록 설정해야 합니다. 

Azure Security Center를 사용하도록 설정하려면 [Azure Security Center와 Azure VMware Solution 통합](azure-security-integration.md)을 참조하세요.

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>VM을 Azure Arc 사용 서버에 온보딩

Azure Arc는 Azure 관리를 Azure VMware Solution, 온-프레미스를 비롯한 모든 인프라로 확장합니다.  [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하면 Azure ‘외부’에서 회사 네트워크 또는 다른 클라우드 공급자에 호스트되는 Windows와 Linux의 물리적 서버와 가상 머신을 관리할 수 있습니다.

여러 Windows 또는 Linux VM에 대해 Azure Arc 사용 서버를 사용하도록 설정하는 방법에 대한 자세한 내용은 [대규모로 Azure에 하이브리드 머신 연결](../azure-arc/servers/onboard-service-principal.md)을 참조하세요.

## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Arc 사용 Kubernetes를 사용하여 하이브리드 Kubernetes 클러스터 온보딩

[Azure Arc 사용 Kubernetes](../azure-arc/kubernetes/overview.md)를 사용하여 Azure VMware Solution 환경에서 호스트되는 Kubernetes 클러스터를 연결할 수 있습니다. 

자세한 내용은 [Azure Arc 사용 온보딩 서비스 주체 만들기](../azure-arc/kubernetes/create-onboarding-service-principal.md)를 참조하세요.

## <a name="deploy-the-log-analytics-agent"></a>Log Analytics 에이전트 배포

Log Analytics 에이전트를 통해 Azure VMware Solution VM을 모니터링할 수 있습니다. Log Analytics 작업 영역에 연결된 가상 머신은 설치된 소프트웨어, Microsoft 서비스, Windows 레지스트리와 파일, 모니터링되는 서버의 Linux 디먼에 대한 변경 내용의 데이터를 수집하기 위해 [Log Analytics 에이전트](../azure-monitor/agents/log-analytics-agent.md)를 사용합니다. 데이터를 사용할 수 있는 경우 에이전트는 처리를 위해 Azure Monitor 로그에 데이터를 보냅니다. Azure Monitor 로그는 수신한 데이터에 논리를 적용하고, 데이터를 기록하고, 분석에 사용할 수 있게 만듭니다.

[Azure Arc 사용 서버 VM 확장 지원](../azure-arc/servers/manage-vm-extensions.md)을 사용하여 Log Analytics 에이전트를 배포합니다.

## <a name="enable-azure-monitor"></a>Azure Monitor 사용

[Azure Monitor](../azure-monitor/overview.md)는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석, 작업하기 위한 포괄적인 솔루션을 제공합니다. Azure Monitor의 몇 가지 추가적인 이점은 다음과 같습니다. 

   - 원활한 모니터링 

   - 더 나은 인프라 가시성 

   - 인스턴트 알림 

   - 자동 해결 

   - 비용 효율성 

다양한 원본에서 데이터를 수집하여 모니터링하고 분석할 수 있습니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](../azure-monitor/agents/data-sources.md)을 참조하세요.  분석, 시각화, 경고에 대한 다양한 유형의 데이터를 수집할 수도 있습니다. 자세한 내용은 [Azure Monitor 데이터 플랫폼](../azure-monitor/data-platform.md)을 참조하세요. 

게스트 운영 체제 성능을 모니터링하고 Azure VMware Solution 또는 온-프레미스 VM에 대한 애플리케이션 종속성을 검색하고 매핑할 수 있습니다. 리소스의 많은 사용, 누락된 패치, 디스크 공간 부족, VM의 하트비트와 같은 사용자 환경 문제를 식별하는 경고 규칙을 만들 수도 있습니다. ITSM(IT 서비스 관리) 도구에 경고를 전송하여 검색된 이벤트에 대한 자동화된 응답을 설정할 수 있습니다. 메일을 통해 경고 검색 알림을 보낼 수도 있습니다.


1. [Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md)

1. [VM용 Azure Monitor 사용 개요](../azure-monitor/vm/vminsights-enable-overview.md)

1. [VM용 Azure Monitor에 대한 Log Analytics 작업 영역을 구성합니다.](../azure-monitor/vm/vminsights-configure-workspace.md)

1. 환경에서 문제를 식별하는 경고 규칙을 만듭니다.
   - [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md).
   - [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md).
   - 자동화된 작업 및 알림을 설정하는 [작업 규칙](../azure-monitor/alerts/alerts-action-rules.md).
   - [IT 서비스 관리 커넥터를 사용하여 ITSM 도구에 Azure 연결](../azure-monitor/alerts/itsmc-overview.md).