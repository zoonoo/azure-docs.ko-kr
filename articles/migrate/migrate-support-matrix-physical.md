---
title: Azure 마이그레이션의 물리적 서버 평가 지원
description: Azure 마이그레이션 서버 평가를 통해 물리적 서버 평가 지원에 대해 알아보기
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: ae76a6b570ec58e71a8a1728a2a601728030f58c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538157"
---
# <a name="support-matrix-for-physical-server-assessment"></a>물리적 서버 평가를 위한 지원 매트릭스 

이 문서에서는 [Azure 마이그레이션:서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 Azure로 마이그레이션할 물리적 서버를 평가할 때 필수 구성 프로그램 및 지원 요구 사항을 요약합니다. 실제 서버를 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스를](migrate-support-matrix-physical-migration.md)검토합니다.


물리적 서버를 평가하려면 Azure 마이그레이션 프로젝트를 만들고 프로젝트에 서버 평가 도구를 추가합니다. 도구가 추가된 후 Azure [마이그레이션 어플라이언스를](migrate-appliance.md)배포합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색하고 컴퓨터 메타데이터 및 성능 데이터를 Azure로 보냅니다. 검색이 완료되면 검색된 컴퓨터를 그룹으로 수집하고 그룹에 대한 평가를 실행합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 한도** | 단일 Azure 마이그레이션 프로젝트에서 최대 35,000개의 물리적 서버를 검색하고 평가할 수 [있습니다.](migrate-support-matrix.md#azure-migrate-projects)
**프로젝트 한도** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트에는 물리적 서버 외에도 VMware VM 및 Hyper-VM이 포함될 수 있으며 각 서버에 대한 평가 한도까지 포함됩니다.
**검색** | Azure 마이그레이션 어플라이언스는 최대 250개의 물리적 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35,000대의 컴퓨터를 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요.](concepts-assessment-calculation.md)

## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **물리적 서버 배포**       | 물리적 서버는 독립 실행형이거나 클러스터에 배포될 수 있습니다. |
| **사용 권한**           | **윈도우:** 검색하려는 모든 Windows 서버에서 로컬 또는 도메인 사용자 계정이 필요합니다. 사용자 계정은 원격 데스크톱 사용자, 성능 모니터 사용자 및 성능 로그 사용자 등의 그룹에 추가되어야 합니다. <br/><br/> **Linux:** 검색하려는 Linux 서버의 루트 계정이 필요합니다. |
| **운영 체제** | Windows [Server](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 2003 및 SUSE Linux를 제외한 모든 Windows 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제는 Azure에서 지원합니다.|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 사용합니다. 물리적 서버의 어플라이언스는 VM 또는 물리적 컴퓨터에서 실행할 수 있습니다. Azure 포털에서 다운로드하는 PowerShell 스크립트를 사용하여 어플라이언스를 설정합니다.

- 물리적 서버에 대한 [어플라이언스 요구 사항에](migrate-appliance.md#appliance---physical) 대해 알아봅니다.
- 어플라이언스가 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항을 요약합니다.

**디바이스** | **연결**
--- | ---
**기기** | TCP 포트 3389의 인바운드 연결로 어플라이언스에 대한 원격 데스크톱 연결을 허용합니다.<br/><br/> 포트 44368에서 URL을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하는 인바운드 연결:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 검색 및 성능 메타데이터를 Azure 마이그레이션에 전송하기 위해 포트 443(HTTPS)의 아웃바운드 연결입니다.
**물리적 서버** | **윈도우:** WinRM 포트 5985(HTTP) 및 5986(HTTPS)의 인바운드 연결을 통해 Windows 서버에서 구성 및 성능 메타데이터를 가져옵니다. <br/><br/> **리눅스 :**  포트 22(UDP)의 인바운드 연결로 Linux 서버에서 구성 및 성능 메타데이터를 가져옵니다. |

## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure를 평가하고 마이그레이션하려는 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약됩니다. 현재 는 실제 서버에 대해서만 에이전트 기반 종속성 분석이 지원됩니다.

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | 서버에 평가 도구가 추가된 Azure 마이그레이션 프로젝트가 있어야 합니다.<br/><br/>  Azure Migrate 어플라이언스를 설정하여 온-프레미스 컴퓨터를 검색한 후 종속성 시각화를 배포합니다.<br/><br/> 프로젝트를 처음 만드는 [방법에 대해 알아봅니다.](create-manage-projects.md)<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다.](how-to-assess.md)<br/> [하이퍼 V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버의 평가를 위해 Azure 마이그레이션 어플라이언스를 설정하는 방법에 대해 알아봅니다.
**Azure Government** | Azure 정부에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그의](../log-analytics/log-analytics-overview.md) [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용합니다.<br/><br/> 새 또는 기존 로그 분석 작업 영역을 Azure 마이그레이션 프로젝트와 연결합니다. Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트와 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵이 지원되는](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)지역에 있어야 합니다.<br/><br/> 로그 분석에서 Azure 마이그레이션과 연관된 작업 영역에 마이그레이션 프로젝트 키 및 프로젝트 이름이 태그가 지정됩니다.
**필수 에이전트** | 분석하려는 각 컴퓨터에서 다음 에이전트를 설치합니다.<br/><br/> [마이크로 소프트 모니터링 에이전트 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아봅니다.
**Log Analytics 작업 영역** | 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵이 지원되는](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)지역에 있어야 합니다.<br/><br/> Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다.
**비용** | 서비스 맵 솔루션은 처음 180일 동안(로그 애널리틱스 작업 영역을 Azure 마이그레이션 프로젝트와 연결하는 날부터) 요금이 발생하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 로그 분석 작업 영역에서 서비스 맵 이외의 솔루션을 사용하면 로그 분석에 대한 [표준 요금이 부과됩니다.](https://azure.microsoft.com/pricing/details/log-analytics/)<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제한 후 서비스 맵 사용량은 무료가 아니며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과됩니다.<br/><br/>Azure 마이그레이션 일반 가용성(GA- 2018년 2월 28일) 이전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 부과되었을 수 있습니다. 180일 후에만 결제를 보장하려면 GA 이전의 기존 작업 영역이 여전히 유료이므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 에이전트를 작업 영역에 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID와 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다.](../azure-monitor/platform/manage-access.md)<br/><br/> Azure 마이그레이션 프로젝트를 삭제하지 않는 한 Azure 마이그레이션에서 만든 작업 영역을 삭제하지 마십시오. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[물리적 서버 평가를 준비합니다.](tutorial-prepare-physical.md)
