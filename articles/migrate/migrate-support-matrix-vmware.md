---
title: Azure Migrate의 VMware 평가 지원
description: Azure Migrate Server 평가를 사용 하 여 VMware VM 평가 지원에 대해 알아봅니다.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 7e0bc21fde2c030de7a836d82384c09c78d993ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047828"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가에 대한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Azure로 마이그레이션하기 위해 VMware 환경에서 실행 중인 서버를 검색 하 고 평가 하는 경우 필수 구성 요소 및 지원 요구 사항을 요약 합니다. 서버를 평가 하려면 프로젝트에 서버 평가 도구를 추가 하는 Azure Migrate 프로젝트를 만듭니다. 도구가 추가된 후 Azure Migrate 어플라이언스를 배포합니다. 어플라이언스는 온-프레미스 서버를 지속적으로 검색 하 고 구성 및 성능 메타 데이터를 Azure로 전송 합니다. 검색이 완료 되 면 검색 된 서버를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.

VMware 서버를 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토 하세요.



## <a name="limitations"></a>제한 사항

**요구 사항** | **세부 정보**
--- | ---
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다.<br/><br/> 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 VMware 환경에 대 한 최대 5만 서버를 검색 하 고 평가할 수 있습니다. 프로젝트에는 물리적 서버와 Hyper-v 환경의 서버 (평가 제한까지)가 포함 될 수도 있습니다.
**검색** | Azure Migrate 어플라이언스는 vCenter Server에서 최대 1만 개의 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 서버를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 대의 서버를 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).


## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**vCenter Server** | 검색 하 고 평가 하려는 서버는 vCenter Server 버전 5.5, 6.0, 6.5, 6.7 또는 7.0에서 관리 해야 합니다.<br/><br/> 어플라이언스에서 ESXi 호스트 세부 정보를 제공 하 여 서버를 검색 하는 것은 현재 지원 되지 않습니다.
**권한** | 서버 평가에는 검색 및 평가를 위한 vCenter Server 읽기 전용 계정이 필요 합니다.<br/><br/> 설치 된 응용 프로그램 및 에이전트 없는 종속성 분석의 검색을 수행 하려면 계정에 **Virtual Machines**  >  **게스트 작업** 에 대해 사용 권한이 있어야 합니다.

## <a name="server-requirements"></a>서버 요구 사항
**VMware** | **세부 정보**
--- | ---
**OS 지원** | 모든 Windows 및 Linux 운영 체제는 마이그레이션을 평가할 수 있습니다.
**스토리지** | SCSI, IDE 및 SATA 기반 컨트롤러에 연결 된 디스크가 지원 됩니다.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. VCenter Server로 가져오거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용 하 여 ova 템플릿을 사용 하 여 VMware 환경에서 서버를 서버 (어플라이언스)로 배포할 수 있습니다.

- VMware에 대한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware)에 대해 알아봅니다.
- Azure Government에서는 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.
- 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스 해야 하는 url을 검토 합니다.


## <a name="port-access-requirements"></a>포트 액세스 요구 사항

**디바이스** | **연결**
--- | ---
**어플라이언스** | 어플라이언스에 대한 원격 데스크톱 연결을 허용하기 위한 TCP 포트 3389에서 인바운드 연결<br/><br/> URL ```https://<appliance-ip-or-name>:44368```을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위한 포트 44368에서 인바운드 연결 <br/><br/>Azure Migrate에 검색 및 성능 메타데이터를 보내기 위한 포트 443(HTTPS)의 아웃바운드 연결
**vCenter Server** | 어플라이언스에서 평가를 위한 구성 및 성능 메타데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결됩니다. vCenter Server가 다른 포트에서 수신 대기하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
**ESXi 호스트** | [설치 된 응용 프로그램](how-to-discover-applications.md)또는 [에이전트 없는 종속성 분석](concepts-dependency-visualization.md#agentless-analysis)의 검색을 수행 하려는 경우 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결 하 여 서버에 대 한 응용 프로그램 및 종속성을 검색 합니다.

## <a name="application-discovery-requirements"></a>응용 프로그램 검색 요구 사항

서버 평가는 서버를 검색 하는 것 외에도 서버에서 실행 되는 응용 프로그램, 역할 및 기능을 검색할 수 있습니다. 응용 프로그램 검색을 통해 온-프레미스 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획할 수 있습니다.

**지원** | **세부 정보**
--- | ---
**지원되는 서버** | 현재 VMware 환경의 서버에 대해서만 지원 됩니다. 각 Azure Migrate 어플라이언스에서 최대 1만 서버에 대해 응용 프로그램 검색을 수행할 수 있습니다.
**운영 체제** | 모든 Windows 및 Linux 버전을 실행 하는 서버가 지원 됩니다.
**VM 요구 사항** | 설치 된 응용 프로그램의 검색을 수행 하려면 서버에 VMware 도구를 설치 하 고 실행 해야 합니다. <br/><br/> VMware 도구 버전은 10.2.0 이상이어야 합니다.<br/><br/> Windows 서버에는 PowerShell 버전 2.0 이상이 설치 되어 있어야 합니다.
**검색** | 서버의 응용 프로그램 검색은 서버에 설치 된 VMware 도구를 사용 하 여 vCenter Server에서 수행 됩니다. 어플라이언스는 vSphere Api를 사용 하 여 vCenter Server에서 설치 된 응용 프로그램에 대 한 정보를 수집 합니다. 응용 프로그램 검색은 에이전트 없이 진행 됩니다. 서버에 에이전트가 설치 되어 있지 않으며 기기가 서버에 직접 연결 되지 않습니다. Windows 및 Linux 서버에서 각각 WMI 및 SSH를 사용 하도록 설정 하 고 사용할 수 있어야 합니다.
**사용자 계정 vCenter Server** | 평가에 사용 되는 vCenter Server 읽기 전용 계정으로,   >  응용 프로그램 검색용 서버와 상호 작용 하기 위해 **게스트 작업** Virtual Machines에 대 한 권한을 설정 해야 합니다.
**서버 액세스** | 응용 프로그램 검색을 위해 어플라이언스 구성 관리자에서 도메인 및 비도메인 (Windows/Linux) 자격 증명을 여러 개 추가할 수 있습니다.<br/><br/> Windows 서버에 게스트 사용자 계정이 필요 하 고 모든 Linux 서버에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)이 필요 합니다.
**포트 액세스** | Azure Migrate 어플라이언스는 응용 프로그램 검색을 수행 하려는 서버를 실행 하는 ESXi 호스트의 TCP 포트 443에 연결할 수 있어야 합니다. VCenter Server는 설치 된 응용 프로그램의 세부 정보가 포함 된 파일을 다운로드 하는 ESXi 호스트 연결을 반환 합니다.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>SQL Server 인스턴스 및 데이터베이스 검색을 위한 요구 사항

> [!Note]
> VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스의 검색 및 평가는 현재 미리 보기로 제공 됩니다. 이 기능을 사용해 보려면 [**이 링크**](https://aka.ms/AzureMigrate/SQL)를 사용하여 **오스트레일리아 동부** 지역에서 프로젝트를 만듭니다. 오스트레일리아 동부에 이미 프로젝트가 있고 이 기능을 사용해 보려는 경우 포털에서 이러한 [**필수 구성 요소**](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

[응용 프로그램 검색](how-to-discover-applications.md) 은 SQL Server 인스턴스를 식별 합니다. 어플라이언스는이 정보를 사용 하 여 어플라이언스에서 제공 되는 Windows 인증 또는 SQL Server 인증 자격 증명을 통해 해당 SQL Server 인스턴스에 연결을 시도 합니다. 연결 되 면 어플라이언스는 SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 데이터를 수집 합니다. SQL Server 구성 데이터는 24 시간 마다 업데이트 되며 성능 데이터는 30 초 마다 캡처됩니다.

**지원** | **세부 정보**
--- | ---
**지원되는 서버** | 현재 VMware 환경의 SQL Server에 대해서만 지원 됩니다. 최대 300 개의 SQL Server 인스턴스 또는 6000 SQL 데이터베이스 중 더 작은 데이터베이스를 검색할 수 있습니다.
**Windows 서버** | Windows Server 2008 이상이 지원 됩니다.
**Linux 서버** | 현재 지원 되지 않습니다.
**인증 메커니즘** | Windows 및 SQL Server 인증이 모두 지원 됩니다. 어플라이언스 구성 관리자에서 두 인증 유형의 자격 증명을 제공할 수 있습니다.
**SQL Server 액세스** | Azure Migrate에는 sysadmin 서버 역할의 멤버인 Windows 사용자 계정이 필요 합니다.
**SQL Server 버전** | SQL Server 2008 이상이 지원 됩니다.
**SQL Server 버전** | Enterprise, Standard, Developer 및 Express edition이 지원 됩니다.
**지원 되는 SQL 구성** | 현재 독립 실행형 SQL Server 인스턴스와 해당 데이터베이스의 검색만 지원 됩니다.<br/> 장애 조치 (Failover) 클러스터 및 Always On 가용성 그룹 식별은 지원 되지 않습니다.
**지원 되는 SQL 서비스** | SQL Server 데이터베이스 엔진만 지원 됩니다. <br/> SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) 및 SQL Server Analysis Services (SSAS) 검색은 지원 되지 않습니다.

> [!Note]
> Azure Migrate는 Azure Migrate 어플라이언스와 원본 SQL Server 인스턴스 간의 통신을 암호화 합니다 (암호화 연결 속성은 TRUE로 설정 됨). 이러한 연결은 [**Trustservercertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (TRUE로 설정 됨)로 암호화 됩니다. 전송 계층에서는 SSL을 사용 하 여 채널을 암호화 하 고 인증서 체인을 무시 하 여 신뢰의 유효성을 검사 합니다. [**인증서의 루트 인증 기관을 신뢰**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)하도록 어플라이언스 서버를 설정 해야 합니다.<br/>
서버를 시작할 때 서버에서 인증서를 프로 비전 하지 않은 경우 SQL Server는 로그인 패킷을 암호화 하는 데 사용 되는 자체 서명 된 인증서를 생성 합니다. [**자세한 정보**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>종속성 분석 요구 사항 (에이전트 없는)

[종속성 분석](concepts-dependency-visualization.md) 을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 서버 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 없는 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다.

**지원** | **세부 정보**
--- | --- 
**지원되는 서버** | 현재 VMware 환경의 서버에 대해서만 지원 됩니다.
**Windows 서버** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2(64비트)<br/>Microsoft Windows Server 2008 (32 비트)
**Linux 서버** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7<br/> SUSE Linux Enterprise Server 11 이상
**서버 요구 사항** | 분석 하려는 서버에 VMware 도구 (10.2.0 이후 버전)를 설치 하 고 실행 해야 합니다.<br/><br/> 서버에는 PowerShell 버전 2.0 이상이 설치 되어 있어야 합니다.
**검색 방법** |  서버 간 종속성 정보는 VM에 설치 된 VMware 도구를 사용 하 여 vCenter Server에서 수집 됩니다. 어플라이언스는 vSphere Api를 사용 하 여 vCenter Server에서 정보를 수집 합니다. 서버에 에이전트가 설치 되어 있지 않으며 기기가 서버에 직접 연결 되지 않습니다. Windows 및 Linux 서버에서 각각 WMI 및 SSH를 사용 하도록 설정 하 고 사용할 수 있어야 합니다.
**vCenter 계정** | Azure Migrate에서 평가에 사용 하는 읽기 전용 계정에는 **게스트 작업 > Virtual Machines** 에 대해 사용 권한이 필요 합니다.
**Windows server 사용 권한** |  서버에 대 한 관리 권한이 있는 사용자 계정 (로컬 또는 도메인)
**Linux 계정** | /Bin/netstat 및/bin/ls 파일에 대 한 다음 권한이 있는 계정 또는 루트 사용자 계정 (CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE.<br/><br/> 다음 명령을 사용 하 여 이러한 기능을 설정 합니다. <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = ep/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = ep/bin/netstat
**포트 액세스** | Azure Migrate 어플라이언스는 종속성이 검색 하려는 서버를 실행 하는 ESXi 호스트의 TCP 포트 443에 연결할 수 있어야 합니다. VCenter Server는 종속성 데이터가 포함 된 파일을 다운로드 하는 ESXi 호스트 연결을 반환 합니다.


## <a name="dependency-analysis-requirements-agent-based"></a>종속성 분석 요구 사항 (에이전트 기반)

[종속성 분석](concepts-dependency-visualization.md) 을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 서버 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다.

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | Migrate 프로젝트가 준비되어 있고 Azure Migrate: Server Assessment 도구가 프로젝트에 추가되어 있어야 합니다.<br/><br/>  온-프레미스 서버를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법을 알아봅니다](create-manage-projects.md).<br/> 기존 프로젝트에 검색 및 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) 또는 물리적 서버의 평가를 위해 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
**지원되는 서버** | 온-프레미스 환경의 모든 서버에 대해 지원 됩니다.
**Log Analytics** | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)의 [서비스 맵](../azure-monitor/insights/service-map.md) 솔루션을 사용합니다.<br/><br/> 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결합니다. 프로젝트를 추가한 후에는 프로젝트의 작업 영역을 수정할 수 없습니다. <br/><br/> 작업 영역은 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.
**필요한 에이전트** | 분석 하려는 각 서버에서 다음 에이전트를 설치 합니다.<br/><br/> [MMA(Microsoft Monitoring Agent) 업그레이드](../azure-monitor/platform/agent-windows.md)<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> 온-프레미스 서버가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)를 설치하는 방법에 대한 자세한 내용을 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다.
**비용** | Azure Migrate 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션에서 요금이 부과되지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 Log Analytics에 대한 [표준 요금](https://azure.microsoft.com/pricing/details/log-analytics/)이 발생합니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵 사용이 무료가 아니며 Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.<br/><br/>Azure Migrate GA(일반 공급 - 2018년 2월 28일) 전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 발생했을 수 있습니다. 180일 후에만 지불되도록 하려면 GA 이전의 기존 작업 영역은 여전히 비용이 부과될 수 있으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID 및 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다](../azure-monitor/platform/manage-access.md).<br/><br/> Azure Migrate 프로젝트를 삭제하지 않는 한 Azure Migrate가 만든 작업 영역을 삭제하면 안 됩니다. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 서버가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 설치 해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

- 평가를 만드는 모범 사례를 [검토합니다](best-practices-assessment.md).
- [VMware VM 평가 준비](./tutorial-discover-vmware.md)