---
title: Azure Migrate의 종속성 시각화
description: 에서 서버 평가 서비스의 평가 계산에 대 한 개요를 제공 Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: fd069ed98fa34fd6f281c98a061925f96c7bb2cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269706"
---
# <a name="dependency-visualization"></a>종속성 시각화

이 문서에서는 Azure Migrate: 서버 평가에서 종속성 시각화에 대해 설명 합니다.

## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 

- Azure Migrate: 서버 평가에서 그룹으로 컴퓨터를 수집한 다음 그룹을 평가 합니다. 종속성 시각화를 사용 하면 평가를 위한 신뢰도를 높이기 위해 컴퓨터를 보다 정확 하 게 그룹화 할 수 있습니다.
- 종속성 시각화를 사용 하면 함께 마이그레이션해야 하는 컴퓨터를 식별할 수 있습니다. 컴퓨터가 사용 중인지 여부 또는 마이그레이션 대신 서비스를 해제할 수 있는지 여부를 식별할 수 있습니다.
- 종속성을 시각화 하면 아무것도 유지 되지 않으며 마이그레이션 중에 예기치 않은 중단이 발생 하지 않습니다.
- 시각화는 컴퓨터가 Azure로 마이그레이션하려는 앱 배포의 일부 인지 확실 하지 않은 경우 특히 유용 합니다.


> [!NOTE]
> Azure Government에서 종속성 시각화를 사용할 수 없습니다.

## <a name="agent-basedagentless-visualization"></a>에이전트 기반/에이전트 없는 시각화

종속성 시각화를 배포 하기 위한 두 가지 옵션이 있습니다.

- **에이전트 기반**: 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 온-프레미스 컴퓨터에 에이전트를 설치 해야 합니다.
- **에이전트**없음:이 옵션을 사용 하면 교차 확인 하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. 이 옵션은 현재 미리 보기 상태 이며 VMware Vm에 대해서만 사용할 수 있습니다.


## <a name="agent-based-visualization"></a>에이전트 기반 시각화

**요구 사항** | **세부 정보** | **자세히 알아보기**
--- | --- | ---
**배포 전** | Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다.<br/><br/>  온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다. | 처음으로 프로젝트를 만드는 [방법에 대해 알아봅니다](create-manage-projects.md) .<br/><br/> 기존 프로젝트에 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/><br/> [Hyper-v](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버를 평가 하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
**필요한 에이전트** | 분석 하려는 각 컴퓨터에서 다음 에이전트를 설치 합니다.<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/><br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)입니다.<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다. | [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아보세요.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 의 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용 합니다.<br/><br/> 새 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 합니다. Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 유럽 서부 지역에 상주해 야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결 된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 지정 됩니다.
**원가** | 서비스 맵 솔루션은 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 하는 날부터 처음 180 일 동안 요금이 발생 하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 Log Analytics에 대 한 [표준 요금이 부과](https://azure.microsoft.com/pricing/details/log-analytics/) 됩니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 사용이 가능 하지 않으며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 청구 됩니다.<br/><br/>일반 공급을 Azure Migrate 하기 전에 만든 프로젝트가 있는 경우 (GA-28 2 월 2018) 추가 서비스 맵 요금이 발생 했을 수 있습니다. 180 일 후에만 지불 되도록 하려면 GA 이전의 기존 작업 영역이 아직 청구 가능한 않으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공 하는 ID 및 키를 사용 합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결 된 Azure Migrate 프로젝트를 삭제 하면 작업 영역이 자동으로 삭제 되지 않습니다. [수동으로 삭제](../azure-monitor/platform/manage-access.md)합니다.<br/><br/> Azure Migrate 프로젝트를 삭제 하지 않으면 Azure Migrate으로 만든 작업 영역을 삭제 하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.

## <a name="agentless-visualization"></a>에이전트 없는 시각화


**요구 사항** | **세부 정보** | **자세히 알아보기**
--- | --- | ---
**배포 전** | Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다.<br/><br/>  온-프레미스 VMWare 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다. | 처음으로 프로젝트를 만드는 [방법에 대해 알아봅니다](create-manage-projects.md) .<br/><br/> 기존 프로젝트에 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/><br/> [VMware](how-to-set-up-appliance-vmware.md) vm의 평가를 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
**필요한 에이전트** | 분석 하려는 컴퓨터에 에이전트가 필요 하지 않습니다.
**지원되는 운영 체제** | 에이전트 없는 시각화에 대해 지원 되는 [운영 체제](migrate-support-matrix-vmware.md#agentless-dependency-visualization) 를 검토 합니다.
**VM** | **Vmware 도구**: vmware 도구를 설치 하 고 분석 하려는 vm에 실행 해야 합니다.<br/><br/> **계정**: Azure Migrate 어플라이언스에서 분석을 위해 vm에 액세스 하는 데 사용할 수 있는 사용자 계정을 추가 해야 합니다.<br/><br/> **Windows vm**: 사용자 계정은 컴퓨터의 로컬 또는 도메인 관리자 여야 합니다.<br/><br/> **Linux vm**: 계정에 루트 권한이 필요 합니다. 또는 사용자 계정에/bin/netstat 및/bin/ls 파일에 대 한 두 가지 기능 (CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE이 필요 합니다. | Azure Migrate 어플라이언스 [에 대해 알아봅니다](migrate-appliance.md) .
**VMware** | **vCenter**: 어플라이언스에는 읽기 전용 액세스 권한이 있는 VCenter Server 계정과 게스트 작업을 Virtual Machines >는 데 사용할 수 있는 권한이 필요 합니다.<br/><br/> **ESXi 호스트**: 분석 하려는 vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.
**수집된 데이터** |  에이전트 없는 종속성 분석은 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다. 종속성 검색을 사용 하도록 설정 하면 어플라이언스는 게스트 Vm에서 5 분 마다 TCP 연결 데이터를 수집 합니다. 이 데이터는 vSphere Api를 사용 하 여 vCenter Server 통해 게스트 Vm에서 수집 됩니다. 수집 된 데이터는 종속성 정보를 추론 하 고 6 시간 마다 Azure Migrate 전송 하기 위해 어플라이언스에서 처리 됩니다. 각 컴퓨터에서 수집 되는 데이터는 다음과 같습니다. <br/> -활성 연결이 있는 프로세스의 이름입니다.<br/> -활성 연결을 사용 하 여 프로세스를 실행 하는 응용 프로그램의 이름입니다.<br/> -활성 연결의 대상 포트입니다.


## <a name="next-steps"></a>다음 단계
- [종속성 시각화 설정](how-to-create-group-machine-dependencies.md)
- VMware Vm에 대 한 [에이전트 없는 종속성 시각화를 사용해 보세요](how-to-create-group-machine-dependencies-agentless.md) .
- 종속성 시각화에 대 한 [일반적인 질문](common-questions-discovery-assessment.md#what-is-dependency-visualization) 을 검토 합니다.


