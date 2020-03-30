---
title: Azure 마이그레이션에서 하이퍼 V 평가 지원
description: Azure 마이그레이션 서버 평가를 통해 하이퍼 V 평가 지원에 대해 자세히 알아보기
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: e8a698b110f19dff593a93a41e9d6f20eb80cdb0
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389004"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>하이퍼 V 평가를 위한 지원 매트릭스

이 문서에서는 [Azure 마이그레이션:서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 Azure로 마이그레이션하기 위한 Hyper-V VM을 평가할 때 필수 구성 프로그램 및 지원 요구 사항을 요약합니다. 하이퍼-V VM을 Azure로 마이그레이션하려면 마이그레이션 [지원 매트릭스를](migrate-support-matrix-hyper-v-migration.md)검토합니다.

Hyper-V VM 평가를 설정하려면 Azure 마이그레이션 프로젝트를 만들고 프로젝트에 서버 평가 도구를 추가합니다. 도구가 추가된 후 Azure [마이그레이션 어플라이언스를](migrate-appliance.md)배포합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색하고 컴퓨터 메타데이터 및 성능 데이터를 Azure로 보냅니다. 검색이 완료되면 검색된 컴퓨터를 그룹으로 수집하고 그룹에 대한 평가를 실행합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 한도** | 단일 Azure 마이그레이션 프로젝트에서 최대 35,000개의 Hyper-V VM을 검색하고 평가할 수 [있습니다.](migrate-support-matrix.md#azure-migrate-projects)
**프로젝트 한도** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트에는 Hyper-V VM 외에도 VMware VM 및 물리적 서버가 포함될 수 있으며 각 VM의 평가 한도까지 포함됩니다.
**검색** | Azure 마이그레이션 어플라이언스는 최대 5,000개의 하이퍼 VVM을 검색할 수 있습니다.<br/><br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 그룹에 대한 단일 평가에서 최대 35,000개의 VM을 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요.](concepts-assessment-calculation.md)



## <a name="hyper-v-host-requirements"></a>하이퍼 V 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **Hyper-V 호스트**       | Hyper-V 호스트는 독립 실행형이거나 클러스터에 배포될 수 있습니다.<br/><br/> 하이퍼-V 호스트는 Windows Server 2019, Windows 서버 2016 또는 Windows 서버 2012 R2를 실행할 수 있습니다.<br/> Windows Server 2012를 실행하는 Hyper-V 호스트에 있는 VM은 평가할 수 없습니다.
| **권한을**           | Hyper-V 호스트에 대한 관리자 권한이 필요합니다. <br/> 관리자 권한을 할당하지 않으려면 로컬 또는 도메인 사용자 계정을 만들고 이러한 그룹인 원격 관리 사용자, Hyper-V 관리자 및 성능 모니터 사용자에게 사용자 계정을 추가합니다. |
| **PowerShell 원격 기능**   | [PowerShell 원격은](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) 각 Hyper-V 호스트에서 활성화되어야 합니다. |
| **Hyper-V 복제본**       | 하이퍼 V 복제본을 사용하거나 동일한 VM 식별자가 있는 여러 VM을 사용하고 Azure 마이그레이션을 사용하여 원본 및 복제된 VM을 모두 검색하는 경우 Azure Migrate에서 생성된 평가가 정확하지 않을 수 있습니다. |


## <a name="hyper-v-vm-requirements"></a>하이퍼 V VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | 모든 [윈도우와](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) [리눅스](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제. |
| **Integration Services**       | 운영 체제 정보를 캡처하려면 [하이퍼 V 통합 서비스가](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 평가되는 VM에서 실행되어야 합니다. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 사용합니다. 포털에서 다운로드하는 압축 하이퍼 VVHD를 사용하거나 [PowerShell 스크립트를](deploy-appliance-script.md)사용하여 어플라이언스를 배포할 수 있습니다.

- 하이퍼-V에 대한 [어플라이언스 요구 사항에](migrate-appliance.md#appliance---hyper-v) 대해 알아봅니다.
- 어플라이언스가 액세스해야 하는 [URL에](migrate-appliance.md#url-access) 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항을 요약합니다.

**디바이스** | **연결**
--- | ---
**기기** | TCP 포트 3389의 인바운드 연결로 어플라이언스에 대한 원격 데스크톱 연결을 허용합니다.<br/><br/> URL을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위해 포트 44368의 인바운드 연결:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> 검색 및 성능 메타데이터를 Azure 마이그레이션에 전송하기 위해 포트 443(HTTPS)의 아웃바운드 연결입니다.
**하이퍼 V 호스트/클러스터** | WinRM 포트 5985(HTTP) 및 5986(HTTPS)의 인바운드 연결로 CIM(공통 정보 모델) 세션을 사용하여 Hyper-VM에 대한 메타데이터 및 성능 데이터를 가져옵니다.

## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure를 평가하고 마이그레이션하려는 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약됩니다. 하이퍼-V는 현재 에이전트 기반 종속성 시각화만 지원합니다. 

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | 서버에 평가 도구가 추가된 Azure 마이그레이션 프로젝트가 있어야 합니다.<br/><br/>  Azure Migrate 어플라이언스를 설정하여 온-프레미스 컴퓨터를 검색한 후 종속성 시각화를 배포합니다.<br/><br/> 프로젝트를 처음 만드는 [방법에 대해 알아봅니다.](create-manage-projects.md)<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다.](how-to-assess.md)<br/> [하이퍼 VM](how-to-set-up-appliance-hyper-v.md)의 평가를 위해 Azure 마이그레이션 어플라이언스를 설정하는 방법에 대해 알아봅니다.
**Azure Government** | Azure 정부에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그의](../log-analytics/log-analytics-overview.md) [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용합니다.<br/><br/> 새 또는 기존 로그 분석 작업 영역을 Azure 마이그레이션 프로젝트와 연결합니다. Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트와 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵이 지원되는](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)지역에 있어야 합니다.<br/><br/> 로그 분석에서 Azure 마이그레이션과 연관된 작업 영역에 마이그레이션 프로젝트 키 및 프로젝트 이름이 태그가 지정됩니다.
**필수 에이전트** | 분석하려는 각 컴퓨터에서 다음 에이전트를 설치합니다.<br/><br/> [마이크로 소프트 모니터링 에이전트 (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> 온-프레미스 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)설치에 대해 자세히 알아봅니다.
**로그 분석 작업 영역** | 작업 영역은 Azure 마이그레이션 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵이 지원되는](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)지역에 있어야 합니다.<br/><br/> Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다.
**비용** | 서비스 맵 솔루션은 처음 180일 동안(로그 애널리틱스 작업 영역을 Azure 마이그레이션 프로젝트와 연결하는 날부터) 요금이 발생하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 로그 분석 작업 영역에서 서비스 맵 이외의 솔루션을 사용하면 로그 분석에 대한 [표준 요금이 부과됩니다.](https://azure.microsoft.com/pricing/details/log-analytics/)<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제한 후 서비스 맵 사용량은 무료가 아니며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과됩니다.<br/><br/>Azure 마이그레이션 일반 가용성(GA- 2018년 2월 28일) 이전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 부과되었을 수 있습니다. 180일 후에만 결제를 보장하려면 GA 이전의 기존 작업 영역이 여전히 유료이므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 에이전트를 작업 영역에 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID와 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다.](../azure-monitor/platform/manage-access.md)<br/><br/> Azure 마이그레이션 프로젝트를 삭제하지 않는 한 Azure 마이그레이션에서 만든 작업 영역을 삭제하지 마십시오. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 컴퓨터가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.

## <a name="next-steps"></a>다음 단계

[하이퍼 V VM 평가 준비](tutorial-prepare-hyper-v.md)
