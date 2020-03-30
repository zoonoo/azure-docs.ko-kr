---
title: Azure 마이그레이션 서버 평가에서 에이전트 기반 종속성 분석 설정
description: 이 문서에서는 Azure 마이그레이션 서버 평가에서 에이전트 기반 종속성 분석을 설정하는 방법에 대해 설명합니다.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453618"
---
# <a name="set-up-dependency-visualization"></a>종속성 시각화 설정

이 문서에서는 Azure 마이그레이션:서버 평가에서 에이전트 기반 종속성 분석을 설정하는 방법에 대해 설명합니다. [종속성 분석을](concepts-dependency-visualization.md) 사용하면 Azure로 평가하고 마이그레이션하려는 컴퓨터 간의 종속성을 식별하고 이해하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 에이전트 기반 종속성 분석에 [대해 알아봅니다.](concepts-dependency-visualization.md#agent-based-analysis)
- [VMware VM,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)물리적 서버 및 [Hyper-V VM에](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)대한 에이전트 기반 종속성 시각화를 설정하기 위한 필수 구성 프로그램 및 지원 요구 사항을 [검토합니다.](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
- Azure 마이그레이션 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인합니다.
- 프로젝트를 이미 만든 경우 Azure 마이그레이션:서버 평가 도구를 [추가했는지](how-to-assess.md) 확인합니다.
- 온-프레미스 컴퓨터를 검색하기 위해 [Azure 마이그레이션 어플라이언스를](migrate-appliance.md) 설정했는지 확인합니다. [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)또는 [물리적 서버에](how-to-set-up-appliance-physical.md)대한 어플라이언스를 설정하는 방법에 대해 알아봅니다. 어플라이언스는 온-프레미스 컴퓨터를 검색하고 메타데이터, 성능 데이터를 Azure Migrate:Server 평가로 보냅니다.
- 종속성 시각화를 사용하려면 Log [Analytics 작업 영역을](../azure-monitor/platform/manage-access.md) Azure 마이그레이션 프로젝트와 연결합니다.
    - Azure 마이그레이션 어플라이언스를 설정하고 Azure 마이그레이션 프로젝트에서 컴퓨터를 검색한 후에만 작업 영역을 연결할 수 있습니다.
    - Azure 마이그레이션 프로젝트를 포함하는 작업 영역이 구독에 있는지 확인합니다.
    - 작업 영역은 미국 동부, 동남아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트와 연결할 수 없습니다.
    - 작업 영역은 [서비스 맵이 지원되는](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)지역에 있어야 합니다.
    - 새 또는 기존 로그 분석 작업 영역을 Azure 마이그레이션 프로젝트와 연결할 수 있습니다.
    - 컴퓨터에 대한 종속성 시각화를 처음 설정할 때 작업 영역을 연결합니다. Azure 마이그레이션 프로젝트의 작업 영역은 추가된 후에는 수정할 수 없습니다.
    - 로그 분석에서 Azure 마이그레이션과 연관된 작업 영역에 마이그레이션 프로젝트 키 및 프로젝트 이름이 태그가 지정됩니다.

## <a name="associate-a-workspace"></a>작업 영역 연결

1. 평가를 위해 컴퓨터를 검색한 후 **Azure** > **마이그레이션 서버: 서버 평가**, **개요를**클릭합니다.  
2. **Azure 마이그레이션: 서버 평가,** 필수 를 **클릭합니다.**
3. **OMS 작업 영역에서** **구성 필요를**클릭합니다.

     ![로그 분석 작업 영역 구성](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. **OMS 작업 영역 구성에서**새 작업 영역을 만들지 아니면 기존 작업 영역을 사용할지 지정합니다.
    - 마이그레이션 프로젝트 구독의 모든 작업 영역에서 기존 작업 영역을 선택할 수 있습니다.
    - 작업 영역에 대한 리더 액세스 가 필요합니다.
5. 새 작업 영역을 작성하는 경우 작업 영역의 위치를 선택합니다.

    ![새 작업 영역 추가](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

분석하려는 각 컴퓨터에서 에이전트를 설치합니다.

> [!NOTE]
> 시스템 센터 운영 관리자 2012 R2 이상에서 모니터링하는 컴퓨터의 경우 MMA 에이전트를 설치할 필요가 없습니다. 서비스 맵은 운영 관리자와 통합됩니다. 통합 지침을 [따릅니다.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)

1. **Azure 마이그레이션: 서버 평가**, **검색된 서버를 클릭합니다.**
2. 종속성 시각화를 사용하여 분석하려는 각 컴퓨터에 대해 **종속성** 열에서 **에이전트 설치 필요를**클릭합니다.
3. **종속성** 페이지에서 Windows 또는 Linux용 MMA 및 종속성 에이전트를 다운로드합니다.
4. **MMA 에이전트 구성에서**작업 영역 ID 및 키를 복사합니다. MMA 에이전트를 설치할 때 이러한 것이 필요합니다.

    ![에이전트 설치](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>MMA 설치

분석하려는 각 Windows 또는 Linux 컴퓨터에 MMA를 설치합니다.

### <a name="install-mma-on-a-windows-machine"></a>윈도우 머신에 MMA 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **시작** 페이지에서 **다음**을 클릭합니다. **사용권 약관** 페이지에서 라이선스 수락 **동의를** 클릭합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
4. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
5. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 클릭합니다.

명령줄에서 에이전트를 설치하거나 Configuration Manager 또는 [Intigua와](https://go.microsoft.com/fwlink/?linkid=2104196)같은 자동화된 방법을 사용할 수 있습니다.
- 이 방법을 사용하여 MMA 에이전트를 설치하는 방법을 [자세히 알아보세요](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration).
- MMA 에이전트는 이 [스크립트](https://go.microsoft.com/fwlink/?linkid=2104394)를 사용하여 설치할 수도 있습니다.
- MMA에서 지원하는 Windows 운영 체제에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)

### <a name="install-mma-on-a-linux-machine"></a>리눅스 컴퓨터에 MMA 를 설치

리눅스 컴퓨터에 MMA를 설치하려면:

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA에서 지원하는 Linux 운영 체제 목록을 [자세히 확인](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems)해 보세요. 

## <a name="install-the-dependency-agent"></a>종속성 에이전트 설치

1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

- 스크립트를 사용하여 종속성 에이전트를 설치하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples).
- 종속성 에이전트에서 지원하는 운영 체제에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems)


## <a name="create-a-group-using-dependency-visualization"></a>종속성 시각화를 사용하여 그룹 만들기

이제 평가를 위한 그룹을 만듭니다. 


> [!NOTE]
> 종속성을 시각화하려는 그룹에는 10개 이하의 컴퓨터만 포함되어 있어야 합니다. 10대 이상의 컴퓨터가 있는 경우 더 작은 그룹으로 분할합니다.

1. **Azure 마이그레이션: 서버 평가**, **검색된 서버를 클릭합니다.**
2. **종속성** 열에서 검토할 각 컴퓨터에 대한 **종속성 보기를** 클릭합니다.
3. 종속성 맵에서 다음을 볼 수 있습니다.
    - 컴퓨터와 의 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결.
    - 종속성 에이전트가 설치되어 있지 않은 종속 컴퓨터는 포트 번호로 그룹화됩니다.
    - 종속성 에이전트가 설치된 종속 컴퓨터는 별도의 상자로 표시됩니다.
    - 기계 내부에서 실행되는 프로세스입니다. 각 기계 상자를 확장하여 프로세스를 봅니다.
    - 기계 속성(FQDN, 운영 체제, MAC 주소 포함). 세부 정보를 보려면 각 컴퓨터 상자를 클릭합니다.

4. 시간 범위 레이블의 시간 범위를 클릭하여 여러 시간 범위의 종속성을 살펴볼 수 있습니다.
    - 범위는 기본적으로 1시간입니다. 
    - 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
    - 시간 범위는 최대 1시간입니다. 더 긴 범위가 필요한 경우 Azure Monitor를 사용하여 더 긴 기간 동안 종속 데이터를 쿼리합니다.

5. 함께 그룹화할 종속 컴퓨터를 식별한 후 Ctrl+Click을 사용하여 맵에서 여러 컴퓨터를 선택하고 **그룹 컴퓨터를**클릭합니다.
6. 그룹 이름을 지정합니다.
7. Azure Migrate에서 종속 컴퓨터가 검색되는지 확인합니다.

    - Azure 마이그레이션: 서버 평가에서 종속 컴퓨터를 검색하지 않는 경우 그룹에 추가할 수 없습니다.
    - 컴퓨터를 추가하려면 검색을 다시 실행하고 컴퓨터가 검색되었는지 확인합니다.

8. 이 그룹에 대한 평가를 만들려면 그룹에 대한 새 평가를 만드는 확인란을 선택합니다.
8. **확인**을 클릭하여 그룹을 저장합니다.

그룹을 만든 후에는 그룹의 모든 컴퓨터에 에이전트를 설치한 다음 전체 그룹에 대한 종속성을 시각화하는 것이 좋습니다.

## <a name="query-dependency-data-in-azure-monitor"></a>Azure 모니터의 쿼리 종속성 데이터

Azure 마이그레이션 프로젝트와 연결된 로그 분석 작업 영역에서 서비스 맵에서 캡처한 종속성 데이터를 쿼리할 수 있습니다. 로그 애널리틱스는 Azure Monitor 로그 쿼리를 작성하고 실행하는 데 사용됩니다.

- 로그 분석에서 서비스 맵 데이터를 [검색하는 방법을 알아봅니다.](../azure-monitor/insights/service-map.md#log-analytics-records)
- [로그 분석에서](../azure-monitor/log-query/get-started-portal.md)로그 쿼리 작성에 대한 [개요를 가져옵니다.](../azure-monitor/log-query/get-started-queries.md)

다음과 같이 종속성 데이터에 대한 쿼리를 실행합니다.

1. 에이전트를 설치한 후 포털로 이동하고 **개요**를 클릭합니다.
2. **Azure 마이그레이션: 서버 평가,** **개요를 클릭합니다.** 아래쪽 화살표를 클릭하여 **필수 를 확장합니다.**
3. **OMS 작업 영역에서**작업 영역 이름을 클릭합니다.
3. **로그**분석 작업 영역 페이지에서 일반 > **로그를 클릭합니다.**
4. 쿼리를 작성하고 **실행 을**클릭합니다.

### <a name="sample-queries"></a>샘플 쿼리

다음은 종속성 데이터를 추출하는 데 사용할 수 있는 몇 가지 샘플 쿼리입니다.

- 선호하는 데이터 요소를 추출하도록 쿼리를 수정할 수 있습니다.
- 종속성 데이터 레코드의 전체 목록을 [검토합니다.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)
- 추가 샘플 쿼리를 [검토합니다.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)

#### <a name="sample-review-inbound-connections"></a>샘플: 인바운드 연결 검토

VM 집합에 대한 인바운드 연결을 검토합니다.

- 연결 메트릭(VMConnection)에 대한 테이블의 레코드는 개별 물리적 네트워크 연결을 나타내지 않습니다.
- 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다.
- VMConnection에서 실제 네트워크 연결 데이터가 집계되는 방법에 대해 [자세히 알아봅니다.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>샘플: 전송 및 수신된 데이터 요약

이 샘플에서는 컴퓨터 집합 간의 인바운드 연결에서 보내고 받는 데이터의 양을 요약합니다.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>다음 단계

그룹에 대한 [평가를 만듭니다.](how-to-create-assessment.md)


