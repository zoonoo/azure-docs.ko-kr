---
title: Azure VMware Solution VM의 수명 주기 관리
description: Microsoft Azure 네이티브 도구를 사용하여 Azure VMware Solution VM의 수명 주기에 대한 모든 측면을 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 2cb9964b68769b1e784cebf62b4d336b355c68fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572202"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware Solution VM의 수명 주기 관리

Microsoft Azure 네이티브 도구를 사용하여 Azure 환경에서 VM(가상 머신)을 모니터링하고 관리할 수 있습니다. 또한 Azure VMware Solution 및 온-프레미스 VM에서 VM을 모니터링하고 관리할 수 있습니다. 이 개요에서는 Azure에서 제공하는 통합 모니터링 아키텍처와 해당 네이티브 도구를 사용하여 수명 주기 동안 Azure VMware Solution VM을 관리하는 방법을 살펴봅니다.

## <a name="benefits"></a>이점

- 하이브리드 환경(Azure, Azure VMware Solution 및 온-프레미스)에서 VM을 관리하는 데 Azure 네이티브 서비스를 사용할 수 있습니다.
- Azure, Azure VMware Solution 및 온-프레미스 VM의 통합 모니터링 및 가시성.
- Azure Automation에서 Azure 업데이트 관리를 사용하여 Windows 및 Linux 머신의 운영 체제 업데이트를 관리할 수 있습니다. 
- Azure Security Center는 다음과 같은 고급 위협 방지 기능을 제공합니다.
    - 파일 무결성 모니터링
    - 파일리스 보안 경고
    - 운영 체제 패치 평가
    - 잘못된 보안 구성 평가
    - Endpoint Protection 평가 
- 새 VM 및 기존 VM에 대한 Azure Arc 사용 서버 VM 확장 지원을 사용하여 Log Analytics 에이전트를 쉽게 배포합니다. 
- Azure Monitor의 Log Analytics 작업 영역에서는 Log Analytics 에이전트 또는 확장을 사용하여 로그 및 성능 카운터를 수집할 수 있습니다. 데이터 및 로그를 단일 지점으로 수집하고 해당 데이터를 다른 Azure 네이티브 서비스에 제공합니다. 
- 추가적으로 얻을 수 있는 Azure Monitor의 이점은 다음과 같습니다. 
    - 원활한 모니터링 
    - 더 나은 인프라 가시성 
    - 인스턴트 알림 
    - 자동 해결 
    - 비용 효율성 

## <a name="integrated-azure-monitoring-architecture"></a>통합 Azure 모니터링 아키텍처

다음 다이어그램은 Azure VMware Solution VM에 대한 통합 모니터링 아키텍처를 보여 줍니다.

![통합 Azure 모니터링 아키텍처](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>시작하기 전에

Azure를 처음 접하는 경우 앞에서 언급한 서비스를 잘 모른다면 다음 문서를 검토하세요.

- [Automation 계정 인증 개요](../automation/automation-security-overview.md)
- [Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md) 및 [Azure Monitor](../azure-monitor/overview.md)
- Azure Security Center에 대한 [계획](../security-center/security-center-planning-and-operations-guide.md) 및 [지원 플랫폼](../security-center/security-center-os-coverage.md)
- [VM용 Azure Monitor 사용 개요](../azure-monitor/vm/vminsights-enable-overview.md)
- [Azure Arc 사용 서버란 무엇인가요?](../azure-arc/servers/overview.md) 및 [Azure Arc 사용 Kubernetes란 무엇인가요?](../azure-arc/kubernetes/overview.md)
- [업데이트 관리 개요](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure 네이티브 서비스 통합 및 배포

### <a name="enable-azure-update-management"></a>Azure 업데이트 관리 사용

Azure Automation의 Azure 업데이트 관리는 하이브리드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리합니다. 패치 준수를 모니터링하고 패치 편차 경고를 수정하기 위해 Azure Monitor로 전달합니다. 저장된 데이터를 사용하여 VM에 대한 업데이트 상태를 평가하기 위해 Azure 업데이트 관리를 Log Analytics 작업 영역에 연결해야 합니다.

1.  Azure 업데이트 관리에 Log Analytics를 추가하려면 먼저 [Azure Automation 계정을 만들어야](../automation/automation-create-standalone-account.md) 합니다. 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](../automation/quickstart-create-automation-account-template.md)를 참조하세요.

2. **Log Analytics 작업 영역** 을 사용하면 Log Analytics 에이전트 또는 확장을 사용하여 로그 및 성능 카운터를 수집할 수 있습니다. Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. 원하는 경우 [CLI](../azure-monitor/logs/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md) 또는 [Azure Resource Manager 템플릿](../azure-monitor/logs/resource-manager-workspace.md)을 통해 작업 영역을 만들 수도 있습니다.

3. VM의 Azure 업데이트 관리를 사용하도록 설정하려면 [Automation 계정에서 업데이트 관리 사용](../automation/update-management/enable-from-automation-account.md)을 참조하세요. 이 프로세스에서는 Log Analytics 작업 영역을 자동화 계정에 연결합니다. 
 
4. Azure 업데이트 관리에 VM을 추가하면 [VM에 업데이트를 배포하고 결과를 검토](../automation/update-management/deploy-updates.md)할 수 있습니다. 

### <a name="enable-azure-security-center"></a>Azure Security Center 활성화

Azure Security Center는 클라우드 및 온-프레미스에서 하이브리드 워크로드에 대한 고급 위협 방지 기능을 제공합니다. Azure VMware Solution VM의 취약성을 평가하고 필요에 따라 경고를 발생시킵니다. 해당 보안 경고를 Azure Monitor에 전달하여 해결할 수 있습니다.

Azure Security Center는 배포가 필요하지 않습니다. 자세한 내용은 [가상 머신에 지원되는 기능](../security-center/security-center-services.md) 목록을 참조하세요.

1. Security Center에 Azure VMware Solution VM 및 비 Azure VM을 추가하려면 [빠른 시작: Azure Security Center 설정](../security-center/security-center-get-started.md)을 참조하세요. 

2. Azure VMware Solution VM 또는 비 Azure 환경의 VM을 추가한 후 Security Center에서 Azure Defender를 사용하도록 설정합니다. Security Center는 잠재적인 보안 문제에 대해 VM을 평가합니다. 또한 개요 탭에서 권장 사항을 제공합니다. 자세한 내용은 [Azure Security Center의 보안 권장 사항](../security-center/security-center-recommendations.md)을 참조하세요.

3. Azure Security Center에서 보안 정책을 정의할 수 있습니다. 보안 정책 구성에 대한 자세한 내용은 [보안 정책을 사용하여 작업](../security-center/tutorial-security-policy.md)을 참조하세요.

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>VM을 Azure Arc 사용 서버에 온보딩

Azure Arc는 Azure 관리를 Azure VMware Solution, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯한 모든 인프라로 확장합니다.

- 여러 Windows 또는 Linux VM에 대해 Azure Arc 사용 서버를 사용하도록 설정하는 방법에 대한 자세한 내용은 [대규모로 Azure에 하이브리드 머신 연결](../azure-arc/servers/onboard-service-principal.md)을 참조하세요.

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Arc 사용 Kubernetes를 사용하여 하이브리드 Kubernetes 클러스터 온보딩

Azure Arc 사용 Kubernetes를 사용하여 Azure VMware Solution 환경에서 호스트되는 Kubernetes 클러스터를 연결할 수 있습니다. 

- 자세한 내용은 [Azure Arc 사용 온보딩 서비스 주체 만들기](../azure-arc/kubernetes/create-onboarding-service-principal.md)를 참조하세요.

### <a name="deploy-the-log-analytics-agent"></a>Log Analytics 에이전트 배포

Azure VMware Solution VM은 Log Analytics 에이전트(MMA(Microsoft Monitoring Agent) 또는 OMS Linux 에이전트라고도 함)를 통해 모니터링할 수 있습니다. Azure Automation 업데이트 관리를 사용하도록 설정하는 동안 Log Analytics 작업 영역을 이미 만들었습니다.

- [Azure Arc 사용 서버 VM 확장 지원](../azure-arc/servers/manage-vm-extensions.md)을 사용하여 Log Analytics 에이전트를 배포합니다.

### <a name="enable-azure-monitor"></a>Azure Monitor 사용

Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석의 수집, 분석 및 작업에 대한 포괄적인 솔루션을 제공합니다. Azure Monitor는 배포가 필요하지 않습니다. Azure Monitor를 사용하여 게스트 운영 체제 성능을 모니터링하고 Azure VMware Solution 또는 온-프레미스 VM에 대한 애플리케이션 종속성을 검색하고 매핑할 수 있습니다.

- Azure Monitor를 사용하면 다양한 원본에서 데이터를 수집하여 모니터링하고 분석할 수 있습니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](../azure-monitor/agents/data-sources.md)을 참조하세요.

- 분석, 시각화 및 경고에 대한 다양한 유형의 데이터를 수집합니다. 자세한 내용은 [Azure Monitor 데이터 플랫폼](../azure-monitor/data-platform.md)을 참조하세요.

- Log Analytics 작업 영역을 사용하여 Azure Monitor를 구성하려면 [VM용 Azure Monitor에 대한 Log Analytics 작업 영역 구성](../azure-monitor/vm/vminsights-configure-workspace.md)을 참조하세요.

- 리소스의 많은 사용, 누락된 패치, 디스크 공간 부족, VM의 하트비트와 같은 사용자 환경 문제를 식별하는 경고 규칙을 만들 수 있습니다. ITSM(IT 서비스 관리) 도구에 경고를 전송하여 검색된 이벤트에 대한 자동화된 응답을 설정할 수도 있습니다. 메일을 통해 경고 검색 알림을 보낼 수도 있습니다. 해당 규칙을 만들려면 다음을 참조하세요.
    - [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-metric.md).
    - [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md).
    - 자동화된 작업 및 알림을 설정하는 [작업 규칙](../azure-monitor/alerts/alerts-action-rules.md).
    - [IT 서비스 관리 커넥터를 사용하여 ITSM 도구에 Azure 연결](../azure-monitor/alerts/itsmc-overview.md).
