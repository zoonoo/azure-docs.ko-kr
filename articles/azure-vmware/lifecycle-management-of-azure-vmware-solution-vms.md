---
title: Azure VMware 솔루션 Vm의 수명 주기 관리
description: Microsoft Azure native tools를 사용 하 여 Azure VMware 솔루션 Vm의 수명 주기에 대 한 모든 측면을 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 928a632a34dd31272c7c3bf92f6dc6dda97cb6cc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216252"
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
- 새 Vm에 대해 Azure ARC를 사용 하 여 Microsoft Monitoring Agent (MMA)를 쉽게 배포 합니다. 
- Azure Monitor의 Log Analytics 작업 영역에서는 MMA 또는 확장을 사용 하 여 로그 수집 및 성능 카운터를 수집할 수 있습니다. 데이터 및 로그를 단일 지점으로 수집 하 고 해당 데이터를 다른 Azure 네이티브 서비스에 제공 합니다. 
- Azure Monitor의 이점은 다음과 같습니다. 
    - 원활한 모니터링 
    - 더 나은 인프라 표시 유형 
    - 인스턴트 알림 
    - 자동 해결 
    - 비용 효율성 

## <a name="integrated-azure-monitoring-architecture"></a>통합 Azure 모니터링 아키텍처

다음 다이어그램은 Azure VMware 솔루션 Vm에 대 한 통합 모니터링 아키텍처를 보여 줍니다.

![통합 Azure 모니터링 아키텍처](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure native services 통합 및 배포

Azure **ARC** 는 azure 관리를 Azure VMware 솔루션, 온-프레미스 또는 기타 클라우드 플랫폼을 비롯 한 모든 인프라로 확장 합니다. Azure ARC는 azure VMware 솔루션 환경에서 AKS (Azure Kubernetes Service) 클러스터를 설치 하 여 배포할 수 있습니다. 자세한 내용은 [Azure Arc를 사용 하는 Kubernetes 클러스터 연결](../azure-arc/kubernetes/connect-cluster.md)을 참조 하세요.

Azure VMware 솔루션 Vm은 MMA (Log Analytics 에이전트 또는 OMS Linux 에이전트 라고도 함)를 통해 모니터링할 수 있습니다. MMA는 ARC VM 수명 주기 워크플로를 통해 Vm을 프로 비전 할 때 자동으로 설치 될 수 있습니다. VCenter의 템플릿에서 Vm을 배포할 때 MMA를 설치할 수도 있습니다. 다시, ARC 워크플로를 통해 프로 비전 된 Vm을 사용 합니다. 프로 비전 된 모든 Azure VMware 솔루션 Vm은 MMA를 설치 하 고 Azure Log Analytics 작업 영역에 로그를 보낼 수 있습니다. 자세한 내용은 [Log Analytics 에이전트 개요](../azure-monitor/platform/log-analytics-agent.md)를 참조 하세요.

**Log Analytics 작업 영역** 에서는 MMA 또는 확장을 사용 하 여 로그 수집 및 성능 카운터 컬렉션을 사용할 수 있습니다. Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)를 참조 하세요.
- Windows Vm을 log analytics 작업 영역에 추가 하려면 [windows 컴퓨터에 Log Analytics 에이전트 설치](../azure-monitor/platform/agent-windows.md)를 참조 하세요.
- Log analytics 작업 영역에 Linux Vm을 추가 하려면 [linux 컴퓨터에 Log Analytics 에이전트 설치](../azure-monitor/platform/agent-linux.md)를 참조 하세요.

Azure Automation의 **Azure 업데이트 관리** 는 하이브리드 환경에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리 합니다. 패치 준수를 모니터링 하 고 패치 편차 경고를 수정 하기 위해 Azure Monitor으로 전달 합니다. Azure 업데이트 관리는 저장 된 데이터를 사용 하 여 Vm에 대 한 업데이트 상태를 평가 하기 위해 Log Analytics 작업 영역에 연결 해야 합니다.
- Azure 업데이트 관리에 Log Analytics를 추가 하려면 먼저 [Azure Automation 계정을 만들어야](../automation/automation-create-standalone-account.md)합니다.
- Log Analytics 작업 영역을 automation 계정에 연결 하려면 [Log Analytics 작업 영역 및 automation 계정](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)을 참조 하세요.
- Vm에 대 한 Azure 업데이트 관리를 사용 하도록 설정 하려면 [Automation 계정에서 업데이트 관리 사용](../automation/update-management/enable-from-automation-account.md)을 참조 하세요.
- Azure 업데이트 관리에 Vm을 추가 하면 [vm에 업데이트를 배포 하 고 결과를 검토할](../automation/update-management/deploy-updates.md)수 있습니다. 

**Azure Security Center** 는 클라우드 및 온-프레미스에서 하이브리드 워크 로드에 대해 고급 위협 방지 기능을 제공 합니다. Azure VMware 솔루션 Vm의 취약성을 평가 하 고 필요에 따라 경고를 발생 시킵니다. 이러한 보안 경고를 Azure Monitor에 전달 하 여 해결할 수 있습니다.
- Azure Security Center는 배포가 필요 하지 않습니다. 자세한 내용은 [가상 컴퓨터에 대해 지원 되는 기능](../security-center/security-center-services.md)목록을 참조 하세요.
- Azure Security Center에 Azure VMware 솔루션 Vm 및 비 Azure Vm을 추가 하려면 [Windows 컴퓨터를 Azure Security Center](../security-center/quickstart-onboard-machines.md) 및 [온보드 Linux 컴퓨터에 Azure Security Center에](../security-center/quickstart-onboard-machines.md)등록을 참조 하세요.
- Vm을 추가한 후 Azure Security Center는 리소스의 보안 상태를 분석 하 여 잠재적인 취약성을 식별 합니다. 또한 개요 탭에서 권장 사항을 제공 합니다. 자세한 내용은 [Azure Security Center의 보안 권장 사항](../security-center/security-center-recommendations.md)을 참조 하세요.
- Azure Security Center에서 보안 정책을 정의할 수 있습니다. 보안 정책을 구성 하는 방법에 대 한 자세한 내용은 [보안 정책](../security-center/tutorial-security-policy.md)사용을 참조 하세요.

**Azure Monitor** 는 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동 하는 포괄적인 솔루션입니다. 배포가 필요 하지 않습니다.
- Azure Monitor를 사용 하면 다양 한 원본에서 데이터를 수집 하 여 모니터링 하 고 분석할 수 있습니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](../azure-monitor/platform/data-sources.md)을 참조 하세요.
- 분석, 시각화 및 경고에 대해 다양 한 유형의 데이터를 수집할 수도 있습니다. 자세한 내용은 [Azure Monitor data platform](../azure-monitor/platform/data-platform.md)을 참조 하십시오.
- Log Analytics 작업 영역을 사용 하 여 Azure Monitor를 구성 하려면 [VM용 Azure Monitor에 대 한 Log Analytics 작업 영역 구성](../azure-monitor/insights/vminsights-configure-workspace.md)을 참조 하세요.
- 리소스의 많은 사용, 누락 된 패치, 디스크 공간 부족, Vm의 하트 비트와 같이 사용자 환경의 문제를 식별 하는 경고 규칙을 만들 수 있습니다. ITSM (IT Service Management) 도구에 경고를 전송 하 여 검색 된 이벤트에 대 한 자동화 된 응답을 설정할 수도 있습니다. 전자 메일을 통해 경고 검색 알림을 보낼 수도 있습니다. 이러한 규칙을 만들려면 다음을 참조 하세요.
    - [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-metric.md)
    - [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-log.md)
    - 자동화 된 작업 및 알림을 설정 하는 [작업 규칙](../azure-monitor/platform/alerts-action-rules.md)
    - [IT 서비스 관리 커넥터를 사용 하 여 Azure를 ITSM 도구에 연결](../azure-monitor/platform/itsmc-overview.md)합니다.

**Azure PaaS (platform as a service)** 는 클라우드 기반 응용 프로그램을 제공할 수 있는 리소스를 포함 하는 클라우드의 개발 및 배포 환경입니다. 예를 들어 Azure SQL Database를 Azure VMware 솔루션 Vm과 통합할 수 있습니다. 그런 다음 SQL 경고를 Azure VMware 솔루션 VM 알림과 상관 관계를 지정할 수 있습니다. 예를 들어 응용 프로그램의 SQL Database 다리가 Azure PAAS 내에 있고 동일한 응용 프로그램의 웹 응용 프로그램 계층이 Azure VMware 솔루션 Vm에서 호스트 되는 경우를 가정해 보겠습니다. 그런 다음 데이터베이스 경고를 웹 응용 프로그램 알림과 상관 관계를 지정할 수 있습니다. Azure, Azure VMware 솔루션 및 온-프레미스 Vm의 단일 통합 가시성을 통해 문제 해결이 간소화 되었습니다.