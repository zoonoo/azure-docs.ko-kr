---
title: Azure Migrate의 물리적 서버 평가 지원
description: Azure Migrate Server 평가를 사용 하 여 물리적 서버 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 31fd676a339a6c82cec84e0f355ac875f68a653c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983672"
---
# <a name="support-matrix-for-physical-server-assessment"></a>물리적 서버 평가를 위한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Azure로의 마이그레이션에 대 한 물리적 서버를 평가 하는 경우 필수 구성 요소 및 지원 요구 사항을 요약 합니다. 물리적 서버를 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-physical-migration.md)를 검토 하세요.


물리적 서버를 평가 하려면 Azure Migrate 프로젝트를 만들고 프로젝트에 서버 평가 도구를 추가 합니다. 도구가 추가 된 후 [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고, 컴퓨터 메타 데이터 및 성능 데이터를 Azure로 전송 합니다. 검색이 완료 되 면 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한** | 단일 [Azure Migrate 프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 대의 물리적 서버를 검색 하 고 평가할 수 있습니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 물리적 서버 외에도 프로젝트에는 각각에 대 한 평가 제한까지 VMware Vm 및 Hyper-v Vm이 포함 될 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 최대 250 대의 물리적 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 대의 컴퓨터를 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .

## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **물리적 서버 배포**       | 물리적 서버는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **권한**           | **Windows:** 검색 하려는 모든 Windows 서버의 도메인 관리자 또는 로컬 관리자 여야 합니다. 사용자 계정은 원격 관리 사용자, 성능 모니터 사용자 및 성능 로그 사용자 그룹에 추가 되어야 합니다. <br/><br/> **Linux:** 검색하려는 Linux 서버의 루트 계정이 필요합니다. |
| **운영 체제** | Windows Server 2003 및 SUSE Linux를 제외 하 고 Azure에서 지 원하는 모든 [windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) server 운영 체제입니다.<br/><br/> Windows 10 및 Windows 8 클라이언트 운영 체제 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. 물리적 서버에 대 한 어플라이언스는 VM 또는 물리적 컴퓨터에서 실행할 수 있습니다. 

- 물리적 서버의 [어플라이언스 요구 사항](migrate-appliance.md#appliance---physical) 에 대해 알아봅니다.
- 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스 해야 하는 url에 대해 알아봅니다.
- Azure Portal에서 다운로드 하는 [PowerShell 스크립트](how-to-set-up-appliance-physical.md) 를 사용 하 여 어플라이언스를 설정 합니다.
Azure Government에서 [이 스크립트를 사용 하 여](deploy-appliance-script-government.md)어플라이언스를 배포 합니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하는 TCP 포트 3389에 대 한 인바운드 연결입니다.<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위해 포트 44368에서 인바운드 연결:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443 (HTTPS)의 아웃 바운드 연결
**물리적 서버** | **Windows:** Windows 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 인바운드 연결 <br/><br/> **Linux:**  Linux 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 포트 22 (UDP)에서 인바운드 연결 |

## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석](concepts-dependency-visualization.md) 을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정 하기 위한 요구 사항이 요약 되어 있습니다. 현재 실제 서버에 대해서는 에이전트 기반 종속성 분석만 지원 됩니다.

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다.<br/><br/>  온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법에 대해 알아봅니다](create-manage-projects.md) .<br/> 기존 프로젝트에 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/> [Hyper-v](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버를 평가 하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
**Azure Government** | Azure Government에서 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 의 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용 합니다.<br/><br/> 새 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 합니다. Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 유럽 서부 지역에 상주해 야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결 된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 지정 됩니다.
**필요한 에이전트** | 분석 하려는 각 컴퓨터에서 다음 에이전트를 설치 합니다.<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)입니다.<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남 아시아 및 유럽 서부 지역에 거주 하는 작업 영역을 지원 합니다.<br/><br/>  작업 영역은 [서비스 맵 지원](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다.
**원가** | 서비스 맵 솔루션은 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 하는 날부터 처음 180 일 동안 요금이 발생 하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 Log Analytics에 대 한 [표준 요금이 부과](https://azure.microsoft.com/pricing/details/log-analytics/) 됩니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 사용이 가능 하지 않으며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 청구 됩니다.<br/><br/>일반 공급을 Azure Migrate 하기 전에 만든 프로젝트가 있는 경우 (GA-28 2 월 2018) 추가 서비스 맵 요금이 발생 했을 수 있습니다. 180 일 후에만 지불 되도록 하려면 GA 이전의 기존 작업 영역이 아직 청구 가능한 않으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공 하는 ID 및 키를 사용 합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결 된 Azure Migrate 프로젝트를 삭제 하면 작업 영역이 자동으로 삭제 되지 않습니다. [수동으로 삭제](../azure-monitor/platform/manage-access.md)합니다.<br/><br/> Azure Migrate 프로젝트를 삭제 하지 않으면 Azure Migrate으로 만든 작업 영역을 삭제 하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 설치 해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[물리적 서버 평가를 준비](tutorial-prepare-physical.md)합니다.
