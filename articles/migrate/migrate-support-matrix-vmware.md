---
title: Azure 마이그레이션에서 VM웨어 평가 지원
description: Azure 마이그레이션 서버 평가를 통해 VMware VM 평가에 대한 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532207"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가를 위한 지원 매트릭스 

이 문서에서는 Azure 마이그레이션:서버 평가](마이그레이션-서비스-개요.md#azure-마이그레이션-서버-평가 도구) 도구를 사용 하 여 Azure로 마이그레이션하기 위한 VM웨어 VM을 평가할 때 필수 구성 조건 및 지원 요구 사항을 요약합니다. VMware VM을 Azure로 마이그레이션하려면 마이그레이션 [지원 매트릭스를](migrate-support-matrix-vmware-migration.md)검토합니다.

VMware VM을 평가하려면 Azure 마이그레이션 프로젝트를 만든 다음 프로젝트에 서버 평가 도구를 추가합니다. 도구가 추가된 후 Azure [마이그레이션 어플라이언스를](migrate-appliance.md)배포합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색하고 컴퓨터 메타데이터 및 성능 데이터를 Azure로 보냅니다. 검색이 완료되면 검색된 컴퓨터를 그룹으로 수집하고 그룹에 대한 평가를 실행합니다.

## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**프로젝트 한도** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다.<br/><br/> 단일 [프로젝트에서](migrate-support-matrix.md#azure-migrate-projects)최대 35,000개의 VMware VM을 검색하고 평가할 수 있습니다. 프로젝트에는 각각에 대한 평가 한도까지 물리적 서버 및 Hyper-V VM도 포함될 수 있습니다.
**검색** | Azure 마이그레이션 어플라이언스는 vCenter 서버에서 최대 10,000개의 VMware VM을 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35,000개의 VM을 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요.](concepts-assessment-calculation.md)


## <a name="application-discovery"></a>애플리케이션 검색

서버 평가는 컴퓨터를 검색하는 것 외에도 컴퓨터에서 실행되는 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색하면 온-프레미스 워크로드에 맞게 조정된 마이그레이션 경로를 식별하고 계획할 수 있습니다. 

**지원** | **세부 정보**
--- | ---
**지원되는 컴퓨터** | 앱 검색은 현재 VMware VM에서만 지원됩니다.
**검색** | 앱 검색은 에이전트가 없습니다. 컴퓨터 게스트 자격 증명을 사용하고 WMI 및 SSH 호출을 사용하여 컴퓨터에 원격으로 액세스합니다.
**VM 지원** | 앱 검색은 모든 Windows 및 Linux 버전에서 지원됩니다.
**v센터 자격 증명** | 앱 검색에는 읽기 전용 액세스 권한이 있는 vCenter Server 계정이 필요하며 가상 컴퓨터 > 게스트 작업에 사용할 수 있는 권한이 필요합니다.
**VM 자격 증명** | 앱 검색은 현재 모든 Windows 서버에 대해 하나의 자격 증명과 모든 Linux 서버에 대해 하나의 자격 증명을 사용하는 것을 지원합니다.<br/><br/> Windows VM용 게스트 사용자 계정과 모든 Linux VM에 대한 일반/일반 사용자 계정(비sudo 액세스)을 만듭니다.
**VM웨어 도구** | VMware 도구를 설치하고 검색하려는 VM에서 실행해야 합니다. <br/> VMware 도구 버전은 10.2.0 이상이어야 합니다.
**PowerShell** | VM에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.
**포트 액세스** | 검색하려는 VM을 실행하는 ESXi 호스트에서 Azure 마이그레이션 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.
**제한** | 앱 검색을 위해 각 Azure 마이그레이션 어플라이언스에서 최대 10000개의 VM을 검색할 수 있습니다.



## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**VMware VM** | 평가는 모든 Windows 및 Linux 운영 체제에 대해 지원됩니다.
**v센터 서버** | 검색 및 평가하려는 컴퓨터는 vCenter Server 버전 5.5, 6.0, 6.5 또는 6.7에서 관리해야 합니다.
**사용 권한(평가)** | vCenter 서버 읽기 전용 계정입니다.
**권한(앱 검색)** | vCenter 서버 계정 읽기 전용 액세스 및 가상 시스템에 사용할 수 있는 권한 **> 게스트 작업.**
**사용 권한(종속성 시각화)** | 읽기 전용 액세스 권한이 있는 Center Server 계정 및 **가상 컴퓨터** > **게스트 작업에**대해 사용할 수 있는 권한.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 사용합니다. OVA 템플릿을 사용하거나 vCenter 서버로 가져오거나 [PowerShell 스크립트를](deploy-appliance-script.md)사용하여 어플라이언스를 VMWare VM으로 배포할 수 있습니다.

- VMware의 [어플라이언스 요구 사항에](migrate-appliance.md#appliance---vmware) 대해 알아봅니다.
- 어플라이언스가 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- Azure 정부에서는 스크립트를 사용하여 어플라이언스를 배포해야 합니다.


## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
어플라이언스 | TCP 포트 3389의 인바운드 연결로 어플라이언스에 대한 원격 데스크톱 연결을 허용합니다.<br/><br/> URL을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위해 포트 44368의 인바운드 연결:```https://<appliance-ip-or-name>:44368``` <br/><br/>검색 및 성능 메타데이터를 Azure 마이그레이션에 전송하기 위해 포트 443(HTTPS)의 아웃바운드 연결입니다.
vCenter Server | TCP 포트 443의 인바운드 연결은 어플라이언스가 평가를 위한 구성 및 성능 메타데이터를 수집할 수 있도록 합니다. <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결됩니다. vCenter 서버가 다른 포트에서 수신되는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
ESXi 호스트(앱 검색/에이전트 없는 종속성 분석) | [앱 검색](how-to-discover-applications.md) 또는 에이전트 [없는 종속성 분석을](concepts-dependency-visualization.md#agentless-analysis)수행하려는 경우 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결하여 응용 프로그램을 검색하고 VM에서 에이전트 없는 종속성 시각화를 실행합니다.

## <a name="agentless-dependency-analysis-requirements"></a>에이전트 없는 종속성 분석 요구 사항

[종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure를 평가하고 마이그레이션하려는 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 없는 종속성 분석을 설정하기 위한 요구 사항이 요약됩니다. 

**요구 사항** | **세부 정보**
--- | --- 
**배포 전** | 서버에 평가 도구가 추가된 Azure 마이그레이션 프로젝트가 있어야 합니다.<br/><br/>  Azure Migrate 어플라이언스를 설정하여 온-프레미스 VMWare 컴퓨터를 검색한 후 종속성 시각화를 배포합니다.<br/><br/> 프로젝트를 처음 만드는 [방법에 대해 알아봅니다.](create-manage-projects.md)<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다.](how-to-assess.md)<br/> VMware VM 평가를 위해 Azure 마이그레이션 어플라이언스를 설정하는 [방법에 대해 알아봅니다.](how-to-set-up-appliance-vmware.md)
**VM 지원** | 현재 VMware VM에서만 지원됩니다.
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> 윈도우 서버 2008 R2 (64 비트).
**Windows 계정** |  종속성 분석을 위해 Azure 마이그레이션 어플라이언스는 Windows VM에 액세스하려면 로컬 또는 도메인 관리자 계정이 필요합니다.
**Linux VM** | 레드 햇 엔터프라이즈 리눅스 7, 6, 5<br/> 우분투 리눅스 14.04, 16.04<br/> Debian 7, 8<br/> 오라클 리눅스 6, 7<br/> 센트OS 5, 6, 7.
**리눅스 계정** | 종속성 분석의 경우 Linux 컴퓨터에서 Azure Migrate 어플라이언스에 루트 권한이 있는 사용자 계정이 필요합니다.<br/><br/> 또는 사용자 계정에는 /bin/netstat 및 /bin/ls 파일(CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE)에 대한 이러한 권한이 필요합니다.
**필수 에이전트** | 분석하려는 컴퓨터에 에이전트가 필요하지 않습니다.
**VMware Tools** | VMware 도구(10.2 이상)는 분석하려는 각 VM에 설치및 실행되어야 합니다.
**v센터 서버 자격 증명** | 종속성 시각화에는 읽기 전용 액세스 권한이 있는 vCenter Server 계정이 필요하며 가상 컴퓨터 > 게스트 작업에 사용할 수 있는 권한이 필요합니다. 
**PowerShell** | VM에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.
**포트 액세스** | 분석하려는 VM을 실행하는 ESXi 호스트에서 Azure 마이그레이션 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.


## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure를 평가하고 마이그레이션하려는 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약됩니다. 

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | Azure 마이그레이션: 서버 평가 도구가 프로젝트에 추가된 Azure 마이그레이션 프로젝트가 있어야 합니다.<br/><br/>  Azure Migrate 어플라이언스를 설정하여 온-프레미스 컴퓨터를 검색한 후 종속성 시각화를 배포합니다.<br/><br/> 프로젝트를 처음 만드는 [방법에 대해 알아봅니다.](create-manage-projects.md)<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다.](how-to-assess.md)<br/> [하이퍼 V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버의 평가를 위해 Azure 마이그레이션 어플라이언스를 설정하는 방법에 대해 알아봅니다.
**Azure Government** | Azure 정부에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그의](../log-analytics/log-analytics-overview.md) [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용합니다.<br/><br/> 새 또는 기존 로그 분석 작업 영역을 Azure 마이그레이션 프로젝트와 연결합니다. Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트와 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵이 지원되는](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)지역에 있어야 합니다.<br/><br/> 로그 분석에서 Azure 마이그레이션과 연관된 작업 영역에 마이그레이션 프로젝트 키 및 프로젝트 이름이 태그가 지정됩니다.
**필수 에이전트** | 분석하려는 각 컴퓨터에서 다음 에이전트를 설치합니다.<br/><br/> [마이크로 소프트 모니터링 에이전트 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아봅니다.
**Log Analytics 작업 영역** | 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵이 지원되는](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)지역에 있어야 합니다.<br/><br/> Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다.
**비용** | 서비스 맵 솔루션은 처음 180일 동안(로그 애널리틱스 작업 영역을 Azure 마이그레이션 프로젝트와 연결하는 날부터) 요금이 발생하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 로그 분석 작업 영역에서 서비스 맵 이외의 솔루션을 사용하면 로그 분석에 대한 [표준 요금이 부과됩니다.](https://azure.microsoft.com/pricing/details/log-analytics/)<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제한 후 서비스 맵 사용량은 무료가 아니며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과됩니다.<br/><br/>Azure 마이그레이션 일반 가용성(GA- 2018년 2월 28일) 이전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 부과되었을 수 있습니다. 180일 후에만 결제를 보장하려면 GA 이전의 기존 작업 영역이 여전히 유료이므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 에이전트를 작업 영역에 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID와 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다.](../azure-monitor/platform/manage-access.md)<br/><br/> Azure 마이그레이션 프로젝트를 삭제하지 않는 한 Azure 마이그레이션에서 만든 작업 영역을 삭제하지 마십시오. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

- 평가 를 만들기 위한 모범 사례를 [검토합니다.](best-practices-assessment.md)
- [VMware 평가를 준비합니다.](tutorial-prepare-vmware.md)
