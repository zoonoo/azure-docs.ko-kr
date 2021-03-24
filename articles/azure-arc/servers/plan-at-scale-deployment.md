---
title: Azure Arc 사용 서버를 계획 하 고 배포 하는 방법
description: Azure Arc 사용 서버에 대해 많은 수의 컴퓨터를 사용 하도록 설정 하 여 Azure에서 필수 보안, 관리 및 모니터링 기능의 구성을 간소화 하는 방법을 알아봅니다.
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: 5aa7022dba943fa3de247404522408f4660e80e3
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023285"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Arc 사용 서버 계획 및 배포

모든 회사에서 IT 인프라 서비스 또는 비즈니스 응용 프로그램을 배포 하는 것은 쉽지 않습니다. 줄여 줍니다 예상치 및 계획 되지 않은 비용을 방지 하기 위해이를 철저히 계획 하 여 가능한 한 준비를 해야 합니다. 모든 규모에서 Azure Arc 사용 서버를 배포 하도록 계획 하려면 작업을 성공적으로 완료 하기 위해 충족 해야 하는 디자인 및 배포 기준을 포함 해야 합니다.

배포가 원활 하 게 진행 되도록 하려면 계획에서 다음을 명확 하 게 이해 해야 합니다.

* 역할 및 책임.
* 네트워크 및 시스템 요구 사항을 충족 하는지 확인 하기 위한 물리적 서버 또는 가상 컴퓨터의 인벤토리
* 성공적인 배포 및 진행 중인 관리를 가능 하 게 하는 데 필요한 기술 집합 및 교육입니다.
* 승인 기준 및 성공 여부를 추적 하는 방법을 설명 합니다.
* 배포를 자동화 하는 데 사용할 도구나 방법
* 지연, 중단 등을 방지 하기 위해 식별 된 위험 및 완화 계획
* 배포 하는 동안 중단을 방지 하는 방법입니다.
* 중요 한 문제가 발생 하는 경우의 에스컬레이션 경로는 무엇 인가요?

이 문서의 목적은 사용자 환경의 여러 프로덕션 물리적 서버 또는 가상 컴퓨터에서 Azure Arc 사용 서버를 성공적으로 배포할 준비가 되었는지 확인 하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

* 컴퓨터에서 연결 된 컴퓨터 에이전트에 대해 [지원 되는 운영 체제](agent-overview.md#supported-operating-systems) 를 실행 합니다.
* 사용자의 컴퓨터는 직접 또는 프록시 서버를 통해 온-프레미스 네트워크 또는 다른 클라우드 환경에서 Azure의 리소스로 연결 됩니다.
* Arc 사용 서버 연결 된 컴퓨터 에이전트를 설치 하 고 구성 하려면 컴퓨터에 대 한 관리자 권한 (즉, 관리자 또는 루트) 권한이 있는 계정입니다.
* 머신을 등록하려면 **Azure Connected Machine Onboarding** 역할의 멤버여야 합니다.
* 컴퓨터를 읽고, 수정 하 고, 삭제 하려면 **Azure 연결 된 컴퓨터 리소스 관리자** 역할의 구성원입니다.

## <a name="pilot"></a>파일럿

모든 프로덕션 컴퓨터에를 배포 하기 전에이 배포 프로세스를 평가 하 여 먼저 환경에서 광범위 하 게 채택 합니다. 파일럿을 위해 회사에서 업무를 수행 하는 데 중요 하지 않은 컴퓨터의 대표적인 샘플링을 파악 합니다. 파일럿을 실행 하 고 영향을 평가할 수 있는 충분 한 시간을 허용 해야 합니다. 최소 30 일이 권장 됩니다.

파일럿의 범위 및 세부 정보를 설명 하는 공식 요금제를 설정 합니다. 다음은 시작 하기 위해 계획에 포함 해야 하는 항목의 샘플입니다.

* 목적-파일럿이 필요한 결정을 내리는 비즈니스 및 기술 드라이버에 대해 설명 합니다.
* 선택 기준-파일럿을 통해 표시 되는 솔루션의 측면을 선택 하는 데 사용 되는 조건을 지정 합니다.
* 범위-파일럿의 범위에 대해 설명 합니다. 여기에는 솔루션 구성 요소, 예상 되는 일정, 파일럿 기간, 대상으로 할 컴퓨터 수 등이 포함 되지만이에 국한 되지 않습니다.
* 성공 조건 및 메트릭-성공 수준을 결정 하는 데 사용 되는 파일럿의 성공 조건 및 특정 측정값을 정의 합니다.
* 교육 계획-파일럿 중에 Azure 및 it 서비스를 처음 접하는 시스템 엔지니어, 관리자 등 교육에 대 한 계획을 설명 합니다.
* 전환 계획-파일럿에서 프로덕션으로의 전환을 안내 하는 데 사용 되는 전략과 조건을 설명 합니다.
* 롤백-파일럿을 배포 전 상태로 롤백하는 절차에 대해 설명 합니다.
* 위험-파일럿을 수행 하 고 프로덕션 배포와 관련 하 여 식별 된 모든 위험을 나열 합니다.

## <a name="phase-1-build-a-foundation"></a>1 단계: 기반 빌드

이 단계에서 시스템 엔지니어 또는 관리자는 조직의 Azure 구독에서 핵심 기능을 사용 하도록 설정 하 여 Arc 사용 서버 및 기타 Azure 서비스에 대 한 관리를 위해 컴퓨터를 사용 하도록 설정 하기 전에 기반을 시작 합니다.

|작업 |세부 정보 |Duration |
|-----|-------|---------|
| [리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Arc 사용 서버만 포함 하 고 이러한 리소스에 대 한 중앙 집중식 관리 및 모니터링을 위한 전용 리소스 그룹입니다. | 1시간 |
| 컴퓨터를 구성 하는 데 도움이 되는 [태그](../../azure-resource-manager/management/tag-resources.md) 를 적용 합니다. | Arc 사용 서버를 관리 하는 복잡성을 줄이고 관리 결정을 간소화 하는 데 도움이 되는 IT에 맞춘 [태그 지정 전략](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) 을 평가 하 고 개발 합니다. | 1일 |
| [Azure Monitor 로그](../../azure-monitor/logs/data-platform-logs.md) 디자인 및 배포 | [디자인 및 배포 고려 사항을](../../azure-monitor/logs/design-logs-deployment.md) 평가 하 여 조직에서 기존를 사용 하거나 다른 Log Analytics 작업 영역을 구현 하 여 하이브리드 서버 및 컴퓨터에서 수집 된 로그 데이터를 저장 해야 하는지 확인 합니다. <sup>1</sup> | 1일 |
| [Azure Policy](../../governance/policy/overview.md) 거 버 넌 스 계획 개발 | Azure Policy를 사용 하 여 구독 또는 리소스 그룹 범위에서 하이브리드 서버 및 컴퓨터의 관리를 구현 하는 방법을 결정 합니다. | 1일 |
| RBAC ( [역할 기반 액세스 제어](../../role-based-access-control/overview.md) ) 구성 | 액세스 계획을 개발 하 여 Arc 사용 서버를 관리 하 고 다른 Azure 서비스 및 솔루션에서 데이터를 볼 수 있는 기능을 제어할 수 있는 사용자를 제어 합니다. | 1일 |
| Log Analytics 에이전트가 이미 설치 된 컴퓨터 식별 | [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) 에서 다음 로그 쿼리를 실행 하 여 기존 Log Analytics 에이전트 배포를 확장 관리 에이전트로 변환할 수 있도록 지원 합니다.<br> 하트비트 <br> TimeGenerated > 전에 &#124; (30d) <br> &#124; ResourceType = = "machines" 및 (ComputerEnvironment = = "비 Azure") <br> 컴퓨터, ResourceProvider, ResourceType, ComputerEnvironment로 요약 &#124; | 1시간 |

<sup>1</sup> Log Analytics 작업 영역 디자인을 평가 하는 과정에서 중요 한 고려 사항은 업데이트 관리 및 변경 내용 추적 및 인벤토리 기능을 지 원하는 Azure Automation와 Azure Security Center 및 Azure 센티널의 통합입니다. 조직에 이미 Automation 계정이 있고 Log Analytics 작업 영역에 연결 된 관리 기능을 사용 하도록 설정한 경우 관리 작업을 중앙 집중화 하 고 간소화할 수 있을 뿐만 아니라 기존 리소스를 사용 하 고 중복 계정, 작업 영역 등을 만들어 비용을 최소화할 수 있는지 여부를 평가 합니다.

## <a name="phase-2-deploy-arc-enabled-servers"></a>2 단계: Arc 사용 서버 배포

다음으로, Arc 사용 서버 연결 된 컴퓨터 에이전트를 준비 하 고 배포 하 여 1 단계에 추가 된 기반에 추가 합니다.

|작업 |세부 정보 |Duration |
|-----|-------|---------|
| 미리 정의 된 설치 스크립트 다운로드 | 자동화 된 배포 요구 사항을 지원 하기 위해 연결 된 컴퓨터 에이전트를 대규모로 배포 하기 위해 미리 정의 된 설치 스크립트를 검토 하 고 사용자 지정 합니다.<br><br> 샘플 확장 온 보 딩 리소스:<br><br> <ul><li> [규모에 맞게 기본 배포 스크립트](onboard-service-principal.md)</ul></li> <ul><li>[Windows Server Vm에 대 한 확장 VMware vSphere 온 보 딩](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[VMware vSphere Linux Vm에 대 한 대규모 온 보 딩](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Ansible를 사용 하 여 스케일 아웃 온 보 딩 AWS EC2 인스턴스](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[PowerShell 원격을 사용 하 여 규모에 맞게 배포](./onboard-powershell.md) (Windows에만 해당)</ul></li>| 요구 사항, 조직 프로세스 (예: 변경 및 Release Management) 및 사용 되는 자동화 방법에 따라 하나 이상의 일이 있습니다. |
| [서비스 주체 만들기](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Azure PowerShell 또는 포털을 사용 하 여 비 대화형으로 컴퓨터를 연결 하는 서비스 주체를 만듭니다.| 1시간 |
| 대상 서버 및 컴퓨터에 연결 된 컴퓨터 에이전트 배포 |자동화 도구를 사용 하 여 서버에 스크립트를 배포 하 고 Azure에 연결 합니다.| 릴리스 요금제에 따라 1 일 이상, 단계별 출시 후에 수행 됩니다. |

## <a name="phase-3-manage-and-operate"></a>3 단계: 관리 및 운영

3 단계에서는 관리자 또는 시스템 엔지니어가 해당 수명 주기 동안 연결 된 컴퓨터 에이전트와 컴퓨터를 관리 하 고 운영 하는 수동 작업을 자동화할 수 있습니다.

|작업 |세부 정보 |Duration |
|-----|-------|---------|
|Resource Health 경고 만들기 |서버에서 15 분 이상 Azure로 하트 비트 전송을 중지 하는 경우에는 오프 라인 상태 이거나, 네트워크 연결이 차단 되었거나, 에이전트가 실행 되 고 있지 않음을 의미할 수 있습니다. 이러한 인시던트를 응답 하 고 조사 하는 방법에 대 한 계획을 수립 하 고 [Resource Health 경고](../..//service-health/resource-health-alert-monitor-guide.md) 를 사용 하 여 시작 시 알림을 받을 수 있습니다.<br><br> 경고를 구성할 때 다음을 지정 합니다.<br> **리소스 종류**  =  **Azure Arc 사용 서버**<br> **현재 리소스 상태**  =  **사용할 수 없음**<br> **이전 리소스 상태**  =  **사용 가능** | 1시간 |
|Azure Advisor 경고 만들기 | 최상의 경험과 최신 보안 및 버그 수정을 위해 Azure Arc 사용 서버 에이전트를 최신 상태로 유지 하는 것이 좋습니다. 최신 에이전트는 [Azure Advisor 경고](../../advisor/advisor-alerts-portal.md)로 식별 됩니다.<br><br> 경고를 구성할 때 다음을 지정 합니다.<br> **권장 사항 유형**  =  **최신 버전의 Azure 연결 된 컴퓨터 에이전트로 업그레이드** | 1시간 |
|구독 또는 리소스 그룹 범위에 [Azure 정책 할당](../../governance/policy/assign-policy-portal.md) |**사용 VM용 Azure Monitor** [정책](../../azure-monitor/vm/vminsights-enable-policy.md) (및 요구 사항을 충족 하는 다른 사용자)을 구독 또는 리소스 그룹 범위에 할당 합니다. Azure Policy를 사용 하면 사용자 환경에서 VM용 Azure Monitor에 필요한 에이전트를 설치 하는 정책 정의를 할당할 수 있습니다.| 상황에 따라 다름 |
|[Arc 사용 서버에 대 한 업데이트 관리 사용](../../automation/update-management/enable-from-automation-account.md) |Azure Automation에서 업데이트 관리를 구성 하 여 Arc 사용 서버에 등록 된 Windows 및 Linux 가상 머신에 대 한 운영 체제 업데이트를 관리 합니다. | 15분 |

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* Azure Automation [상태 구성](../../automation/automation-dsc-overview.md) 및 지원 되는 기타 [azure VM 확장과](manage-vm-extensions.md)같은 다른 azure 서비스를 사용 하 여 배포를 간소화 하는 방법을 알아봅니다.