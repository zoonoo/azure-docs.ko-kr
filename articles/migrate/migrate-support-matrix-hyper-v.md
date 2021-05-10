---
title: Azure Migrate의 Hyper-V 평가 지원
description: Azure Migrate 검색 및 평가를 사용한 Hyper-V 평가 지원에 대해 알아보기
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 8531d0b2252e6ddff75509046b5a4576b99d339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870792"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V 평가 지원 매트릭스

이 문서에서는 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 도구를 사용하여 Azure로 마이그레이션하기 위해 Hyper-V 환경에서 실행 중인 온-프레미스 서버를 검색하고 평가하는 경우의 필수 구성 요소 및 지원 요구 사항을 요약해서 설명합니다. Hyper-V에서 실행되는 서버를 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-hyper-v-migration.md)를 검토하세요.

Hyper-V에서 실행되는 서버의 검색 및 평가를 설정하려면 프로젝트를 만들고 프로젝트에 Azure Migrate: 검색 및 평가 도구를 추가합니다. 도구가 추가된 후 [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포합니다. 이 어플라이언스는 지속적으로 온-프레미스 서버를 검색하고 서버 메타데이터 및 성능 데이터를 Azure에 보냅니다. 검색이 완료된 후에는 검색된 서버를 그룹으로 수집하고 그룹에 대한 평가를 실행합니다.

## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한** | 단일 [프로젝트](migrate-support-matrix.md#project)에서 최대 35,000개의 서버를 검색하고 평가할 수 있습니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. Hyper-V의 서버 외에도 프로젝트는 VMware의 서버 및 물리적 서버를 각각에 대한 평가 제한까지 포함할 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 Hyper-V에서 실행되는 최대 5,000개의 서버를 검색할 수 있습니다.<br/><br/> 어플라이언스는 최대 300개의 Hyper-V 호스트에 연결할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000개의 서버를 추가할 수 있습니다.<br/><br/> 그룹에 대한 단일 평가에서 최대 35,000개의 서버를 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).

## <a name="hyper-v-host-requirements"></a>Hyper-V 호스트 요구 사항

| **지원**                | **세부 정보**
| :-------------------       | :------------------- |
| **Hyper-V 호스트**       | Hyper-V 호스트는 독립 실행형이거나 클러스터에 배포할 수 있습니다.<br/><br/> Hyper-V 호스트에서 Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2가 실행되고 있어야 합니다. 이러한 운영 체제의 Server Core 설치도 지원됩니다. <br/>Windows Server 2012를 실행하는 Hyper-V 호스트에 있는 서버는 평가할 수 없습니다.
| **권한**           | Hyper-V 호스트에 대한 관리자 권한이 필요합니다. <br/> 관리자 권한을 할당하지 않으려면 로컬 또는 도메인 사용자 계정을 만들고, 사용자 계정을 해당 그룹(원격 관리 사용자, Hyper-V 관리자 및 성능 모니터 사용자)에 추가합니다. |
| **PowerShell 원격 기능**   | 각 Hyper-V 호스트에서 [PowerShell 원격 기능](/powershell/module/microsoft.powershell.core/enable-psremoting)을 사용하도록 설정해야 합니다. |
| **Hyper-V 복제본**       | Hyper-V 복제본을 사용하며(또는 동일한 서버 식별자가 있는 여러 서버를 사용하는 경우) Azure Migrate를 사용하여 원래 서버와 복제된 서버를 모두 검색하는 경우 Azure Migrate에서 생성된 평가가 정확하지 않을 수 있습니다. |

## <a name="server-requirements"></a>서버 요구 사항

| **지원**                  | **세부 정보**
| :----------------------------- | :------------------- |
| **운영 체제** | 마이그레이션이 가능한지 모든 운영 체제를 평가할 수 있습니다.  |
| **Integration Services**       | 운영 체제 정보를 캡처하기 위해 평가하는 서버에서 [Hyper-V Integration Services](/virtualization/hyper-v-on-windows/reference/integration-services)를 실행하고 있어야 합니다. |
| **스토리지** | 로컬 디스크, DAS, JBOD, 스토리지 공간, CSV, SMB VHD/VHDX가 저장된 이러한 Hyper-V 호스트 스토리지가 지원됩니다. <br/> IDE 및 SCSI 가상 컨트롤러가 지원됩니다.|

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. 포털에서 다운로드하거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 다운로드한 압축된 Hyper-V VHD를 사용하여 어플라이언스를 배포할 수 있습니다.

- Hyper-V에 대한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---hyper-v)에 대해 알아봅니다.
- 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- Azure Government에서는 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약되어 있습니다.

**디바이스** | **연결**
--- | ---
**어플라이언스** | 어플라이언스에 대한 원격 데스크톱 연결을 허용하기 위한 TCP 포트 3389에서 인바운드 연결<br/><br/> URL ``` https://<appliance-ip-or-name>:44368 ```을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위한 포트 44368에서 인바운드 연결<br/><br/> Azure Migrate에 검색 및 성능 메타데이터를 보내기 위한 포트 443(HTTPS)의 아웃바운드 연결
**Hyper-V 호스트/클러스터** | CIM(Common Information Model) 세션을 사용하여 Hyper-V의 서버에 대한 메타데이터 및 성능 데이터를 가져오기 위한 WinRM 포트 5985(HTTP) 또는 5986(HTTPS)의 인바운드 연결

## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하고 Azure로 마이그레이션하려는 온-프레미스 서버 간의 종속성을 파악할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다. Hyper-V는 현재 에이전트 기반 종속성 시각화만 지원합니다.

**요구 사항** | **세부 정보**
--- | --- 
**배포 전** | Azure Migrate: 검색 및 평가 도구를 프로젝트에 추가하여 준비합니다.<br/><br/>  온-프레미스 서버를 검색하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법을 알아봅니다](create-manage-projects.md).<br/> 기존 프로젝트에 Azure Migrate: 검색 및 평가 도구를 추가하는 [방법을 알아봅니다](how-to-assess.md).<br/> [Hyper-V의 서버](how-to-set-up-appliance-hyper-v.md)를 검색하고 평가하기 위해 어플라이언스를 설정하는 방법에 대해 알아봅니다.
**Azure Government** | Azure Government에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다.<br/><br/> 신규 또는 기존 Log Analytics 작업 영역을 프로젝트와 연결합니다. 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.
**필요한 에이전트** | 분석하려는 각 서버에서 다음 에이전트를 설치합니다.<br/><br/> [MMA(Microsoft Monitoring Agent) 업그레이드](../azure-monitor/agents/agent-windows.md)<br/> [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)<br/><br/> 온-프레미스 서버가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)를 설치하는 방법에 대한 자세한 내용을 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다.
**비용** | 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션에서 요금이 부과되지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 Log Analytics에 대한 [표준 요금](https://azure.microsoft.com/pricing/details/log-analytics/)이 발생합니다.<br/><br/> 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵 사용이 무료가 아니며 Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.<br/><br/>Azure Migrate GA(일반 공급 - 2018년 2월 28일) 전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 발생했을 수 있습니다. 180일 후에만 지불되도록 하려면 GA 이전의 기존 작업 영역은 여전히 비용이 부과될 수 있으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 프로젝트에서 제공하는 ID 및 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다](../azure-monitor/logs/manage-access.md).<br/><br/> 프로젝트를 삭제하지 않는 한, Azure Migrate가 만든 작업 영역을 삭제하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 서버가 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Hyper-V에서 실행되는 서버에 대한 평가를 준비](./tutorial-discover-hyper-v.md)합니다.