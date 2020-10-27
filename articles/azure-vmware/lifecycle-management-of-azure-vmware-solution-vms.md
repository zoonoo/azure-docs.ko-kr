---
title: Azure VMware 솔루션 Vm의 수명 주기 관리
description: Microsoft Azure native tools를 사용 하 여 Azure VMware 솔루션 Vm의 수명 주기에 대 한 모든 측면을 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 5280d362c1e7b1bf33579d051c4cc11adb1b7e59
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545765"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware 솔루션 Vm의 수명 주기 관리

Microsoft Azure native tools를 사용 하 여 Azure 환경에서 Vm (가상 머신)을 모니터링 하 고 관리할 수 있습니다. 또한 Azure VMware 솔루션 및 온-프레미스 Vm에서 Vm을 모니터링 하 고 관리할 수 있습니다. 이 개요에서는 Azure에서 제공 하는 통합 모니터링 아키텍처와 해당 네이티브 도구를 사용 하 여 수명 주기 동안 Azure VMware 솔루션 Vm을 관리 하는 방법을 살펴봅니다.

## <a name="benefits"></a>이점

- Azure native services는 하이브리드 환경 (Azure, Azure VMware 솔루션 및 온-프레미스)에서 Vm을 관리 하는 데 사용할 수 있습니다.
- Azure, Azure VMware 솔루션 및 온-프레미스 Vm에 대 한 통합 모니터링 및 가시성.
- Azure Automation에서 Azure 업데이트 관리를 사용 하 여 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리할 수 있습니다. 
- Azure Security Center는 다음과 같은 고급 위협 방지 기능을 제공 합니다.
    - 파일 무결성 모니터링
    - 파일리스 보안 경고
    - 운영 체제 패치 평가
    - 잘못된 보안 구성 평가
    - Endpoint Protection 평가 
- 새 Vm 및 기존 Vm에 대 한 Azure Arc 사용 서버 VM 확장 지원을 사용 하 여 Log Analytics 에이전트를 쉽게 배포 합니다. 
- Azure Monitor의 Log Analytics 작업 영역에서는 Log Analytics 에이전트 또는 확장을 사용 하 여 로그 수집 및 성능 카운터를 수집할 수 있습니다. 데이터 및 로그를 단일 지점으로 수집 하 고 해당 데이터를 다른 Azure 네이티브 서비스에 제공 합니다. 
- Azure Monitor의 이점은 다음과 같습니다. 
    - 원활한 모니터링 
    - 더 나은 인프라 표시 유형 
    - 인스턴트 알림 
    - 자동 해결 
    - 비용 효율성 

## <a name="integrated-azure-monitoring-architecture"></a>통합 Azure 모니터링 아키텍처

다음 다이어그램은 Azure VMware 솔루션 Vm에 대 한 통합 모니터링 아키텍처를 보여 줍니다.

![통합 Azure 모니터링 아키텍처](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>시작하기 전에

Azure를 처음 접하는 경우 앞에서 언급 한 서비스를 잘 모르는 경우 다음 문서를 검토 하세요.

- [Automation 계정 인증 개요](../automation/automation-security-overview.md)
- [Azure Monitor 로그 배포](../azure-monitor/platform/design-logs-deployment.md) 및 [Azure Monitor](../azure-monitor/overview.md) 디자인
- Azure Security Center에 대 한 [계획](../security-center/security-center-planning-and-operations-guide.md) 및 [지원 플랫폼](../security-center/security-center-os-coverage.md)
- [VM용 Azure Monitor 사용 개요](../azure-monitor/insights/vminsights-enable-overview.md)
- [Azure Arc 사용 서버 란 무엇 인가요?](../azure-arc/servers/overview.md) [Azure Arc 사용 Kubernetes 무엇 인가요?](../azure-arc/kubernetes/overview.md)
- [업데이트 관리 개요](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure native services 통합 및 배포

### <a name="enable-azure-update-management"></a>Azure 업데이트 관리 사용

Azure Automation의 Azure 업데이트 관리는 하이브리드 환경에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리 합니다. 패치 준수를 모니터링 하 고 패치 편차 경고를 수정 하기 위해 Azure Monitor으로 전달 합니다. Azure 업데이트 관리는 저장 된 데이터를 사용 하 여 Vm에 대 한 업데이트 상태를 평가 하기 위해 Log Analytics 작업 영역에 연결 해야 합니다.

1.  Azure 업데이트 관리에 Log Analytics를 추가 하려면 먼저 [Azure Automation 계정을 만들어야](../automation/automation-create-standalone-account.md)합니다. 템플릿을 사용하여 계정을 만들려면 [Azure Resource Manager 템플릿을 사용하여 Automation 계정 만들기](../automation/quickstart-create-automation-account-template.md)를 참조하세요.

2. **Log Analytics 작업 영역** 을 사용 하면 Log Analytics 에이전트 또는 확장을 사용 하 여 로그 수집 및 성능 카운터를 수집할 수 있습니다. Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요. 원하는 경우 [CLI](../azure-monitor/learn/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)또는 [Azure Resource Manager 템플릿을](../azure-monitor/samples/resource-manager-workspace.md)통해 작업 영역을 만들 수도 있습니다.

3. Vm에 대 한 Azure 업데이트 관리를 사용 하도록 설정 하려면 [Automation 계정에서 업데이트 관리 사용](../automation/update-management/update-mgmt-enable-automation-account.md)을 참조 하세요. 이 프로세스에서는 Log Analytics 작업 영역을 automation 계정에 연결 합니다. 
 
4. Azure 업데이트 관리에 Vm을 추가 하면 [vm에 업데이트를 배포 하 고 결과를 검토할](../automation/update-management/deploy-updates.md)수 있습니다. 

### <a name="enable-azure-security-center"></a>Azure Security Center 활성화

Azure Security Center는 클라우드 및 온-프레미스에서 하이브리드 워크 로드에 대해 고급 위협 방지 기능을 제공 합니다. Azure VMware 솔루션 Vm의 취약성을 평가 하 고 필요에 따라 경고를 발생 시킵니다. 이러한 보안 경고를 Azure Monitor에 전달 하 여 해결할 수 있습니다.

Azure Security Center는 배포가 필요 하지 않습니다. 자세한 내용은 [가상 컴퓨터에 대해 지원 되는 기능](../security-center/security-center-services.md)목록을 참조 하세요.

1. Security Center에 Azure VMware 솔루션 Vm 및 비 Azure Vm을 추가 하려면 빠른 시작 [: Azure Security Center 설정](../security-center/security-center-get-started.md)을 참조 하세요. 

2. Azure가 아닌 환경에서 azure VMware 솔루션 Vm 또는 Vm을 추가한 후 Security Center에서 Azure Defender를 사용 하도록 설정 합니다. Security Center는 Vm에서 잠재적인 보안 문제를 평가 합니다. 또한 개요 탭에서 권장 사항을 제공 합니다. 자세한 내용은 [Azure Security Center의 보안 권장 사항](../security-center/security-center-recommendations.md)을 참조 하세요.

3. Azure Security Center에서 보안 정책을 정의할 수 있습니다. 보안 정책을 구성 하는 방법에 대 한 자세한 내용은 [보안 정책](../security-center/tutorial-security-policy.md)사용을 참조 하세요.

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Azure Arc 사용 서버에 Vm 등록

Azure Arc는 azure 관리를 Azure VMware 솔루션, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯 한 모든 인프라로 확장 합니다.

- 여러 Windows 또는 Linux Vm에 대 한 Azure Arc 사용 서버를 사용 하려면 [하이브리드 컴퓨터를 azure에 연결](../azure-arc/servers/onboard-service-principal.md) 을 참조 하세요.

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Arc enabled Kubernetes를 사용 하 여 하이브리드 Kubernetes 클러스터 등록

Azure Arc enabled Kubernetes를 사용 하 여 Azure VMware 솔루션 환경에서 호스트 되는 Kubernetes 클러스터를 연결할 수 있습니다. 

- [Azure Arc 사용 온 보 딩 서비스 주체 만들기](../azure-arc/kubernetes/create-onboarding-service-principal.md)를 참조 하세요.

### <a name="deploy-the-log-analytics-agent"></a>Log Analytics 에이전트 배포

Azure VMware 솔루션 Vm은 Log Analytics 에이전트 (MMA (Microsoft Monitoring Agent) 또는 OMS Linux 에이전트 라고도 함)를 통해 모니터링할 수 있습니다. Azure Automation 업데이트 관리를 사용 하도록 설정 하는 동안 Log Analytics 작업 영역을 이미 만들었습니다.

- [Azure Arc 사용 서버 VM 확장 지원을](../azure-arc/servers/manage-vm-extensions.md)사용 하 여 Log Analytics 에이전트를 배포 합니다.

### <a name="enable-azure-monitor"></a>Azure Monitor 사용

Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동 하는 포괄적인 솔루션입니다. 배포가 필요 하지 않습니다. Azure Monitor를 사용 하 여 게스트 운영 체제 성능을 모니터링 하 고 Azure VMware 솔루션 또는 온-프레미스 Vm에 대 한 응용 프로그램 종속성을 검색 하 고 매핑할 수 있습니다.

- Azure Monitor를 사용 하면 다양 한 원본에서 데이터를 수집 하 여 모니터링 하 고 분석할 수 있습니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](../azure-monitor/platform/data-sources.md)을 참조 하세요.

- 분석, 시각화 및 경고에 대 한 다양 한 유형의 데이터를 수집 합니다. 자세한 내용은 [Azure Monitor data platform](../azure-monitor/platform/data-platform.md)을 참조 하십시오.

- Log Analytics 작업 영역을 사용 하 여 Azure Monitor를 구성 하려면 [VM용 Azure Monitor에 대 한 Log Analytics 작업 영역 구성](../azure-monitor/insights/vminsights-configure-workspace.md)을 참조 하세요.

- 리소스의 많은 사용, 누락 된 패치, 디스크 공간 부족, Vm의 하트 비트와 같이 사용자 환경의 문제를 식별 하는 경고 규칙을 만들 수 있습니다. ITSM (IT Service Management) 도구에 경고를 전송 하 여 검색 된 이벤트에 대 한 자동화 된 응답을 설정할 수도 있습니다. 전자 메일을 통해 경고 검색 알림을 보낼 수도 있습니다. 이러한 규칙을 만들려면 다음을 참조 하세요.
    - [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-metric.md)
    - [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-log.md)
    - 자동화 된 작업 및 알림을 설정 하는 [작업 규칙](../azure-monitor/platform/alerts-action-rules.md)
    - [IT 서비스 관리 커넥터를 사용 하 여 Azure를 ITSM 도구에 연결](../azure-monitor/platform/itsmc-overview.md)합니다.
