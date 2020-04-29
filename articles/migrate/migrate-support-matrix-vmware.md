---
title: Azure Migrate의 VMware 평가 지원
description: Azure Migrate Server 평가를 통한 VMware VM 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8a09562f14b95256ee9c2b5ba7d9c308cde66397
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81532207"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가를 위한 지원 매트릭스 

이 문서에서는 Azure Migrate: 서버 평가] (마이그레이션-서비스-개요. md # Azure-마이그레이션-서버 평가 도구) 도구를 사용 하 여 Azure로 마이그레이션하기 위한 VMware Vm을 평가 하는 경우 필수 구성 요소 및 지원 요구 사항을 요약 합니다. VMware Vm을 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토 하세요.

VMware Vm을 평가 하려면 Azure Migrate 프로젝트를 만든 다음 프로젝트에 서버 평가 도구를 추가 합니다. 도구가 추가 된 후 [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고, 컴퓨터 메타 데이터 및 성능 데이터를 Azure로 전송 합니다. 검색이 완료 되 면 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.

## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다.<br/><br/> 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 VMware vm을 검색 하 고 평가할 수 있습니다. 프로젝트에는 물리적 서버와 Hyper-v Vm이 각각에 대 한 평가 제한까지 포함 될 수도 있습니다.
**조사** | Azure Migrate 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .


## <a name="application-discovery"></a>애플리케이션 검색

서버 평가는 컴퓨터를 검색 하는 것 외에도 컴퓨터에서 실행 되는 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색 하면 온-프레미스 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획할 수 있습니다. 

**지원** | **세부 정보**
--- | ---
**지원 되는 컴퓨터** | 앱 검색은 현재 VMware Vm에 대해서만 지원 됩니다.
**조사** | 앱 검색은 에이전트 없는 앱입니다. 컴퓨터 게스트 자격 증명을 사용 하 고 WMI 및 SSH 호출을 사용 하 여 컴퓨터에 원격으로 액세스 합니다.
**VM 지원** | 앱 검색은 모든 Windows 및 Linux 버전에 대해 지원 됩니다.
**vCenter 자격 증명** | 앱 검색에는 읽기 전용 액세스 권한이 있는 vCenter Server 계정과 게스트 작업을 Virtual Machines > 사용 하도록 설정 된 권한이 필요 합니다.
**VM 자격 증명** | 앱 검색은 현재 모든 Windows 서버에 대해 하나의 자격 증명을 사용 하 고 모든 Linux 서버에 대해 하나의 자격 증명을 사용 하도록 지원 합니다.<br/><br/> Windows Vm에 대 한 게스트 사용자 계정과 모든 Linux Vm에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)을 만듭니다.
**VMware 도구** | 검색 하려는 Vm에 VMware 도구를 설치 하 고 실행 해야 합니다. <br/> VMware 도구 버전은 10.2.0 보다 이후 여야 합니다.
**PowerShell** | Vm에는 PowerShell 버전 2.0 이상이 설치 되어 있어야 합니다.
**포트 액세스** | 검색 하려는 Vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.
**제한** | 앱 검색의 경우 각 Azure Migrate 어플라이언스에서 최대 1만 개의 Vm을 검색할 수 있습니다.



## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**VMware VM** | 평가는 모든 Windows 및 Linux 운영 체제에서 지원 됩니다.
**vCenter Server** | 검색 하 고 평가 하려는 컴퓨터는 vCenter Server 버전 5.5, 6.0, 6.5 또는 6.7에서 관리 해야 합니다.
**사용 권한 (평가)** | 읽기 전용 계정을 vCenter Server 합니다.
**권한 (앱 검색)** | 읽기 전용 액세스 권한이 있는 vCenter Server 계정 및 가상 컴퓨터에 대 한 권한이 설정 된 **게스트 작업 >** 합니다.
**권한 (종속성 시각화)** | **가상 컴퓨터** > **게스트 작업**에 대해 읽기 전용 액세스 및 권한이 설정 된 센터 서버 계정


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. VCenter Server로 가져오거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용 하 여 ova 템플릿을 사용 하 여 장치를 VMWare VM으로 배포할 수 있습니다.

- VMware에 대 한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware) 에 대해 알아봅니다.
- 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스 해야 하는 url에 대해 알아봅니다.
- Azure Government에서 스크립트를 사용 하 여 어플라이언스를 배포 해야 합니다.


## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
어플라이언스 | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위해 포트 44368에서 인바운드 연결:```https://<appliance-ip-or-name>:44368``` <br/><br/>Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443 (HTTPS)의 아웃 바운드 연결
vCenter Server | 어플라이언스에서 평가를 위한 구성 및 성능 메타 데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결 됩니다. VCenter 서버가 다른 포트에서 수신 대기 하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
ESXi 호스트 (앱 검색/에이전트 없는 종속성 분석) | [앱 검색](how-to-discover-applications.md) 또는 [에이전트 없는 종속성 분석](concepts-dependency-visualization.md#agentless-analysis)을 수행 하려는 경우 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결 하 여 응용 프로그램을 검색 하 고 vm에서 에이전트 없는 종속성 시각화를 실행 합니다.

## <a name="agentless-dependency-analysis-requirements"></a>에이전트 없는 종속성 분석 요구 사항

[종속성 분석](concepts-dependency-visualization.md) 을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 없는 종속성 분석을 설정 하기 위한 요구 사항이 요약 되어 있습니다. 

**요구 사항** | **세부 정보**
--- | --- 
**배포 전** | 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다.<br/><br/>  온-프레미스 VMWare 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법에 대해 알아봅니다](create-manage-projects.md) .<br/> 기존 프로젝트에 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/> VMware Vm의 평가를 위해 Azure Migrate 어플라이언스를 설정 하 [는 방법에 대해 알아봅니다](how-to-set-up-appliance-vmware.md) .
**VM 지원** | 현재 VMware Vm에 대해서만 지원 됩니다.
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 비트)
**Windows 계정** |  종속성 분석의 경우 Azure Migrate 어플라이언스는 Windows Vm에 액세스할 수 있는 로컬 또는 도메인 관리자 계정이 필요 합니다.
**Linux VM** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Linux 계정** | 종속성 분석의 경우 Linux 컴퓨터에서 Azure Migrate 어플라이언스는 루트 권한이 있는 사용자 계정이 필요 합니다.<br/><br/> 또는 사용자 계정에/bin/netstat 및/bin/ls 파일에 대 한 다음 권한이 필요 합니다. CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE.
**필요한 에이전트** | 분석 하려는 컴퓨터에 에이전트가 필요 하지 않습니다.
**VMware Tools** | 분석 하려는 각 VM에 VMware 도구 (10.2 이상)를 설치 하 고 실행 해야 합니다.
**vCenter Server 자격 증명** | 종속성 시각화에는 읽기 전용 액세스 권한이 있는 vCenter Server 계정과 게스트 작업을 Virtual Machines > 하는 데 사용 되는 권한이 필요 합니다. 
**PowerShell** | Vm에는 PowerShell 버전 2.0 이상이 설치 되어 있어야 합니다.
**포트 액세스** | 분석 하려는 Vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.


## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석](concepts-dependency-visualization.md) 을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정 하기 위한 요구 사항이 요약 되어 있습니다. 

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다.<br/><br/>  온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법에 대해 알아봅니다](create-manage-projects.md) .<br/> 기존 프로젝트에 평가 도구를 추가 하 [는 방법에 대해 알아봅니다](how-to-assess.md) .<br/> [Hyper-v](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버를 평가 하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
**Azure Government** | Azure Government에서 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 의 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용 합니다.<br/><br/> 새 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 합니다. Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 유럽 서부 지역에 상주해 야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결 된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 지정 됩니다.
**필요한 에이전트** | 분석 하려는 각 컴퓨터에서 다음 에이전트를 설치 합니다.<br/><br/> [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)입니다.<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 다운로드 하 여 설치 해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남 아시아 및 유럽 서부 지역에 거주 하는 작업 영역을 지원 합니다.<br/><br/>  작업 영역은 [서비스 맵 지원](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다.
**원가** | 서비스 맵 솔루션은 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 하는 날부터 처음 180 일 동안 요금이 발생 하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 Log Analytics에 대 한 [표준 요금이 부과](https://azure.microsoft.com/pricing/details/log-analytics/) 됩니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 사용이 가능 하지 않으며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 청구 됩니다.<br/><br/>일반 공급을 Azure Migrate 하기 전에 만든 프로젝트가 있는 경우 (GA-28 2 월 2018) 추가 서비스 맵 요금이 발생 했을 수 있습니다. 180 일 후에만 지불 되도록 하려면 GA 이전의 기존 작업 영역이 아직 청구 가능한 않으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공 하는 ID 및 키를 사용 합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결 된 Azure Migrate 프로젝트를 삭제 하면 작업 영역이 자동으로 삭제 되지 않습니다. [수동으로 삭제](../azure-monitor/platform/manage-access.md)합니다.<br/><br/> Azure Migrate 프로젝트를 삭제 하지 않으면 Azure Migrate으로 만든 작업 영역을 삭제 하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 설치 해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원 되지 않습니다.


## <a name="next-steps"></a>다음 단계

- 평가 만들기에 대 한 모범 사례를 [검토](best-practices-assessment.md) 합니다.
- [VMware 평가를 준비](tutorial-prepare-vmware.md) 합니다.
