---
title: Azure Migrate의 Hyper-v 평가 지원
description: Azure Migrate의 Hyper-v 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245812"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-v 평가를 위한 지원 매트릭스

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 를 사용 하 여 hyper-v vm을 평가 하기 위한 지원 설정 및 제한 사항을 요약 합니다. Hyper-v Vm을 Azure로 마이그레이션하는 방법에 대 한 정보를 찾고 있는 경우 [마이그레이션 지원 매트릭스](migrate-support-matrix-hyper-v-migration.md)를 검토 하세요.

## <a name="overview"></a>개요

이 문서를 사용 하 여 Azure로의 마이그레이션에 대 한 온-프레미스 컴퓨터를 평가 하려면 Azure Migrate: 서버 평가 도구를 Azure Migrate 프로젝트에 추가 합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고 구성 및 성능 데이터를 Azure로 전송 합니다. 컴퓨터 검색 후에는 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한**| 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 35000 hyper-v vm을 검색 하 고 평가 합니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트는 VMware Vm, Hyper-v Vm 및 물리적 서버를 평가 제한까지 포함할 수 있습니다.
**조사** | Azure Migrate 어플라이언스는 5000 Hyper-v Vm을 검색할 수 있습니다.<br/><br/> 어플라이언스는 최대 300 Hyper-v 호스트에 연결할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .



## <a name="hyper-v-host-requirements"></a>Hyper-v 호스트 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **호스트 배포**       | Hyper-v 호스트는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **사용 권한**           | Hyper-v 호스트에 대 한 관리자 권한이 필요 합니다. <br/> 또는 관리자 권한을 할당 하지 않으려는 경우 로컬 또는 도메인 사용자 계정을 만들고 이러한 그룹에 사용자를 추가 합니다. 원격 관리 사용자, Hyper-v 관리자 및 성능 모니터 사용자입니다. |
| **호스트 운영 체제** | Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2.<br/> Windows Server 2012를 실행하는 Hyper-V 호스트에 있는 VM은 평가할 수 없습니다. |
| **PowerShell 원격 작업**   | 각 호스트에서를 사용 하도록 설정 해야 합니다. |
| **Hyper-v 복제본**       | Hyper-v 복제본을 사용 하는 경우 (또는 VM 식별자가 같은 vm이 여러 개 있는 경우) Azure Migrate를 사용 하 여 원래 vm과 복제 된 Vm을 모두 검색 하는 경우 Azure Migrate에서 생성 된 평가가 정확 하지 않을 수 있습니다. |


## <a name="hyper-v-vm-requirements"></a>Hyper-v VM 요구 사항

| **지원**                  | **세부 정보**               
| :----------------------------- | :------------------- |
| **운영 체제** | Azure에서 지 원하는 모든 [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 운영 체제입니다. |
| **Integration Services**       | 운영 체제 정보를 캡처하기 위해 평가 하는 Vm에서 [hyper-v Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) 실행 중 이어야 합니다. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. Hyper-v에 대 한 어플라이언스는 Hyper-v VM에서 실행 되며, Azure Portal에서 다운로드 하는 압축 된 Hyper-v VHD를 사용 하 여 배포 됩니다. 

- Hyper-v에 대 한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---hyper-v) 에 대해 알아봅니다.
- 어플라이언스에서 액세스 해야 하는 [url](migrate-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**기기가** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ``` https://<appliance-ip-or-name>:44368 ```<br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443 (HTTPS), 5671 및 5672 (AMQP)의 아웃 바운드 연결
**Hyper-v 호스트/클러스터** | CIM(Common Information Model) (CIM) 세션을 사용 하 여 Hyper-v Vm의 구성과 성능 메타 데이터를 끌어오기 위해 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 인바운드 연결

## <a name="agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화

[종속성 시각화](concepts-dependency-visualization.md) 를 사용 하면 평가 하 고 마이그레이션하려는 컴퓨터 간에 종속성을 시각화할 수 있습니다. 에이전트 기반 시각화의 경우 요구 사항 및 제한 사항이 다음 표에 요약 되어 있습니다.


**요구 사항** | **세부 정보**
--- | ---
**배포** | 종속성 시각화를 배포 하기 전에 Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다. 온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> Azure Government에서 종속성 시각화를 사용할 수 없습니다.
**서비스 맵** | 에이전트 기반 종속성 시각화는 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)에 [서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) 솔루션을 사용 합니다.<br/><br/> 를 배포 하려면 새 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 합니다.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남 아시아 및 유럽 서부 지역에 거주 하는 작업 영역을 지원 합니다.<br/><br/>  작업 영역은 [서비스 맵 지원](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다.
**요금** | 서비스 맵 솔루션은 Log Analytics 작업 영역을 연결한 날부터 Azure Migrate 프로젝트와 관련 하 여 처음 180 일에 대 한 요금을 부과 하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 표준 Log Analytics 요금이 발생 합니다.<br/><br/> Azure Migrate 프로젝트를 삭제 하면 작업 영역이 삭제 되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 무료로 제공 되며 각 노드에는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과 됩니다.
**에이전트** | 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 컴퓨터에 두 개의 에이전트를 설치 해야 합니다.<br/><br/> - [MMA (Microsoft Monitoring agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [종속성 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)를 - 합니다. 
**인터넷 연결** | 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 설치 해야 합니다.


## <a name="next-steps"></a>다음 단계

[Hyper-v VM 평가 준비](tutorial-prepare-hyper-v.md)
