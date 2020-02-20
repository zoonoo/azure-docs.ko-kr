---
title: Azure Migrate에서 컴퓨터 종속성을 사용하여 컴퓨터 그룹화 | Microsoft Docs
description: Azure Migrate 서비스에서 컴퓨터 종속성을 사용하여 평가를 만드는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: bf6798d557cb1d27030565e4706864e945de6f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472097"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>평가에 대 한 종속성 시각화 설정

이 문서에서는 Azure Migrate: 서버 평가에서 종속성 매핑을 설정 하는 방법을 설명 합니다.

종속성 매핑은 평가 하 고 마이그레이션하려는 컴퓨터 간의 종속성을 시각화 하는 데 도움이 됩니다.

- Azure Migrate: 서버 평가에서 평가를 위해 컴퓨터를 함께 수집 합니다. 일반적으로 함께 마이그레이션해야 하는 컴퓨터입니다.
- 신뢰 수준이 높은 그룹을 평가 하려는 경우 일반적으로 종속성 매핑을 사용 합니다.
- 종속성 매핑은 평가 및 마이그레이션을 실행 하기 전에 컴퓨터 종속성을 교차 확인 하는 데 도움이 됩니다.
- 종속성 매핑 및 시각화를 통해 Azure로의 마이그레이션을 효과적으로 계획할 수 있습니다. 이 기능을 사용 하면 아무것도 유지 되지 않으므로 마이그레이션하는 동안 예기치 않은 중단이 발생 하지 않습니다.
- 매핑을 사용 하 여 함께 마이그레이션해야 하는 상호 의존적인 시스템을 검색할 수 있습니다. 또한 실행 중인 시스템이 여전히 사용자에 게 서비스를 제공 하 고 있는지 또는 마이그레이션 대신 서비스 해제에 대 한 후보가 있는지 확인할 수 있습니다.

종속성 시각화에 [대해 자세히 알아보세요](concepts-dependency-visualization.md#how-does-it-work) .

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인 합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 서버 평가 도구를 [추가](how-to-assess.md) 했는지 확인 합니다.
- Azure Migrate에서 컴퓨터를 검색 했는지 확인 합니다. [VMware](how-to-set-up-appliance-vmware.md) 또는 [hyper-v](how-to-set-up-appliance-hyper-v.md)에 대 한 Azure Migrate 어플라이언스를 설정 하 여이 작업을 수행할 수 있습니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다. [자세히 알아봅니다](migrate-appliance.md).


**기능** | **참고**
--- | ---
가용성 | Azure Government에서 종속성 시각화를 사용할 수 없습니다.
서비스 맵 | 종속성 시각화는 Azure Monitor에서 서비스 맵 솔루션을 사용 합니다. [서비스 맵](../azure-monitor/insights/service-map.md) 는 서버 간의 연결을 자동으로 검색 하 고 표시 합니다.
에이전트 | 종속성 시각화를 사용 하려면 매핑할 컴퓨터에 다음 에이전트를 설치 합니다.<br/> - [Log Analytics 에이전트](../azure-monitor/platform/log-analytics-agent.md) 에이전트 (이전에는 MICROSOFT MONITORING AGENT (MMA) 이라고 함)<br/> [서비스 맵 종속성 에이전트](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)를 - 합니다.<br/><br/> 에이전트 설치를 자동화 하려면 Azure Migrate에 대 한 에이전트 배포 솔루션이 있는 Configuration Manager와 같은 배포 도구를 사용할 수 있습니다.
종속성 에이전트 | Windows 및 Linux에 대 한 [종속성 에이전트 지원](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) 을 검토 합니다.<br/><br/> 스크립트를 사용 하 여 종속성 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) .
Log Analytics 에이전트 (MMA) | MMA 설치 방법에 [대해 자세히 알아보세요](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) .<br/><br/> System Center Operations Manager 2012 R2 이상에서 모니터링 하는 컴퓨터의 경우 MMA 에이전트를 설치할 필요가 없습니다. 서비스 맵 Operations Manager와 통합 됩니다. [여기](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)에서 지침에 따라 통합을 사용하도록 설정할 수 있습니다. 그러나 종속성 에이전트는 이러한 컴퓨터에 설치 되어야 합니다.<br/><br/> Log Analytics 에이전트에서 지 원하는 Linux 운영 체제를 [검토](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) 합니다.
평가 그룹 | 종속성을 시각화하려는 그룹에는 10개 이하의 컴퓨터만 포함되어 있어야 합니다. 컴퓨터가 10 대 이상인 경우 종속성을 시각화 하기 위해 작은 그룹으로 분할 합니다.

## <a name="associate-a-log-analytics-workspace"></a>Log Analytics 작업 영역 연결

종속성 시각화를 사용 하려면 Azure Migrate 프로젝트와 [Log Analytics 작업 영역](../azure-monitor/platform/manage-access.md) 을 연결 해야 합니다.

- Azure Migrate 프로젝트 구독 에서만 작업 영역을 연결할 수 있습니다.
- 기존 작업 영역을 연결 하거나 새 작업 영역을 만들 수 있습니다.
- 컴퓨터에 대 한 종속성 시각화를 처음 설정할 때 작업 영역을 연결 합니다.
- Azure Migrate 프로젝트에서 컴퓨터를 검색 한 후에만 작업 영역을 연결할 수 있습니다. [VMware](how-to-set-up-appliance-vmware.md) 또는 [hyper-v](how-to-set-up-appliance-hyper-v.md)에 대 한 Azure Migrate 어플라이언스를 설정 하 여이 작업을 수행할 수 있습니다. 어플라이언스는 온-프레미스 컴퓨터를 검색 하 고 메타 데이터 및 성능 데이터를 Azure Migrate: 서버 평가로 보냅니다. [자세히 알아봅니다](migrate-appliance.md).

다음과 같이 작업 영역을 연결 합니다.

1. **Azure Migrate: 서버 평가**에서 **개요**를 클릭 합니다. 서버 평가 도구를 아직 추가 하지 않은 경우이를 [먼저 수행](how-to-assess.md)합니다.
2. **개요**에서 아래쪽 화살표를 클릭 하 여 **Essentials**를 확장 합니다.
3. **OMS 작업 영역**에서 **구성 필요**를 클릭 합니다.
4. **작업 영역 구성**에서 새 작업 영역을 만들지 아니면 기존 작업 영역을 사용할지를 지정 합니다.

    ![작업 영역 추가](./media/how-to-create-group-machine-dependencies/workspace.png)

    - 새 작업 영역에 대 한 이름을 지정 하 고 나면 작업 영역을 만들 [지역을](https://azure.microsoft.com/global-infrastructure/regions/) 선택할 수 있습니다.
    - 기존 작업 영역을 연결하는 경우에는 마이그레이션 프로젝트와 동일한 구독에서 사용 가능한 모든 작업 영역을 선택할 수 있습니다.
    - 연결할 수 있으려면 작업 영역에 대 한 읽기 권한이 있어야 합니다.
    - 프로젝트에 연결 된 후에는 프로젝트와 연결 된 작업 영역을 수정할 수 없습니다.

## <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

종속성 매핑을 사용 하 여 시각화 하려는 각 온-프레미스 컴퓨터에 에이전트를 다운로드 하 여 설치 합니다.

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. 종속성 시각화를 사용 하려는 각 컴퓨터에 대해 **에이전트 설치 필요**를 클릭 합니다.
3. **MMA를 다운로드 하 여 설치**하 > 컴퓨터의 **종속성** 페이지에서 적절 한 에이전트를 다운로드 하 고 아래 설명에 따라 설치 합니다.
4. **종속성 에이전트 다운로드 및 설치**에서 적절 한 에이전트를 다운로드 하 고 아래 설명에 따라 설치 합니다.
5. **MMA 에이전트 구성**에서 작업 영역 ID 및 키를 복사 합니다. MMA 에이전트를 설치할 때 필요 합니다.

### <a name="install-the-mma"></a>MMA 설치

#### <a name="install-the-agent-on-a-windows-machine"></a>Windows 머신에 에이전트 설치

Windows 컴퓨터에 에이전트를 설치하려면

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하거나 수정하고 **다음**을 클릭합니다.
4. **에이전트 설치 옵션**에서 **Azure Log Analytics** > **다음**을 차례로 선택합니다.
5. **추가**를 클릭하여 새로운 Log Analytics 작업 영역을 추가합니다. 포털에서 복사한 작업 영역 ID와 키를 붙여넣습니다. **다음**을 클릭합니다.

명령줄에서 또는 Configuration Manager 또는 [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196)같은 자동화 된 방법을 사용 하 여 에이전트를 설치할 수 있습니다. 이 방법을 사용하여 MMA 에이전트를 설치하는 방법을 [자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent). MMA 에이전트는 이 [스크립트](https://go.microsoft.com/fwlink/?linkid=2104394)를 사용하여 설치할 수도 있습니다.

MMA에서 지 원하는 Windows 운영 체제에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) .

#### <a name="install-the-agent-on-a-linux-machine"></a>Linux 머신에 에이전트 설치

Linux 컴퓨터에 에이전트를 설치하려면

1. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
2. --install 인수를 사용하여 번들을 설치합니다.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA에서 지원하는 Linux 운영 체제 목록을 [자세히 확인](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems)해 보세요. 

### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치
1. Windows 컴퓨터에 종속성 에이전트를 설치하려면 설치 파일을 두 x 누르고 마법사를 따릅니다.
2. Linux 컴퓨터에 종속성 에이전트를 설치하려면 다음 명령을 사용하여 루트로 설치합니다.

    ```sh InstallDependencyAgent-Linux64.bin```

스크립트를 사용하여 종속성 에이전트를 설치하는 방법에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples).

종속성 에이전트에서 지 원하는 운영 체제에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) .


## <a name="create-a-group-using-dependency-visualization"></a>종속성 시각화를 사용 하 여 그룹 만들기

1. **Azure Migrate: 서버 평가**에서 검색 된 **서버**를 클릭 합니다.
2. **종속성** 열에서 검토 하려는 각 컴퓨터에 대 한 **종속성 보기** 를 클릭 합니다.
3. 종속성 맵에서 다음을 확인할 수 있습니다.
    - 컴퓨터와의 인바운드 (클라이언트) 및 아웃 바운드 (서버) TCP 연결
    - 종속성 에이전트가 설치 되어 있지 않은 종속 컴퓨터는 포트 번호로 그룹화 됩니다.
    - 종속성 에이전트가 설치 된 종속 컴퓨터는 별도의 상자로 표시 됩니다.
    - 컴퓨터 내에서 실행 되는 프로세스입니다. 각 컴퓨터 상자를 확장 하 여 프로세스를 봅니다.
    - 컴퓨터 속성 (FQDN, 운영 체제, MAC 주소 포함) 각 컴퓨터 상자를 클릭 하면 세부 정보를 볼 수 있습니다.

4. 시간 범위 레이블의 시간 범위를 클릭하여 여러 시간 범위의 종속성을 살펴볼 수 있습니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.

    > [!NOTE]
    > 시간 범위는 최대 1 시간까지 가능 합니다. 더 긴 범위가 필요한 경우 Azure Monitor를 사용 하 여 더 긴 기간 동안 종속 데이터를 쿼리 합니다.

5. 함께 그룹화 하려는 종속 컴퓨터를 식별 한 후에는 Ctrl + 클릭을 사용 하 여 맵에서 여러 컴퓨터를 선택 하 고 **그룹 컴퓨터**를 클릭 합니다.
6. 그룹 이름을 지정합니다.
7. Azure Migrate에서 종속 컴퓨터가 검색되는지 확인합니다.

    - Azure Migrate: 서버 평가에서 종속 컴퓨터를 검색 하지 않는 경우 그룹에 추가할 수 없습니다.
    - 컴퓨터를 추가 하려면 검색을 다시 실행 하 고 컴퓨터가 검색 되는지 확인 합니다.

8. 이 그룹에 대한 평가를 만들려면 그룹에 대한 새 평가를 만드는 확인란을 선택합니다.
8. **확인**을 클릭하여 그룹을 저장합니다.

그룹을 만든 후에는 그룹의 모든 컴퓨터에 에이전트를 설치한 다음 전체 그룹에 대 한 종속성을 시각화 하는 것이 좋습니다.

## <a name="query-dependency-data-in-azure-monitor"></a>Azure Monitor에서 종속성 데이터 쿼리

Azure Migrate 프로젝트와 연결 된 Log Analytics 작업 영역에서 서비스 맵 하 여 캡처한 종속성 데이터를 쿼리할 수 있습니다. Log Analytics은 Azure Monitor 로그 쿼리를 작성 하 고 실행 하는 데 사용 됩니다.

- Log Analytics에서 서비스 맵 데이터를 검색 하 [는 방법을 알아봅니다](../azure-monitor/insights/service-map.md#log-analytics-records) .
- [Log Analytics](../azure-monitor/log-query/get-started-portal.md)에서 로그 쿼리를 작성 하는 방법에 대 한 [개요를](../azure-monitor/log-query/get-started-queries.md) 확인 하세요.

다음과 같이 종속성 데이터에 대 한 쿼리를 실행 합니다.

1. 에이전트를 설치한 후 포털로 이동하고 **개요**를 클릭합니다.
2. **Azure Migrate: 서버 평가**에서 **개요**를 클릭 합니다. 아래쪽 화살표를 클릭 하 여 **Essentials**를 확장 합니다.
3. **OMS 작업 영역**에서 작업 영역 이름을 클릭 합니다.
3. Log Analytics 작업 영역 페이지에서 **일반**> **로그**를 클릭 합니다.
4. 쿼리를 작성 하 고 **실행**을 클릭 합니다.

### <a name="sample-queries"></a>샘플 쿼리

종속성 데이터를 추출 하는 데 사용할 수 있는 숫자 샘플 쿼리를 제공 합니다.

- 선호하는 데이터 요소를 추출하도록 쿼리를 수정할 수 있습니다.
- 종속성 데이터 레코드의 전체 목록을 [검토](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) 합니다.
- 추가 샘플 쿼리를 [검토](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) 합니다.

#### <a name="sample-review-inbound-connections"></a>샘플: 인바운드 연결 검토

Vm 집합의 인바운드 연결을 검토 합니다.

- 연결 메트릭에 대 한 테이블의 레코드 (VMConnection)는 개별 실제 네트워크 연결을 나타내지 않습니다.
- 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다.
- VMConnection에서 실제 네트워크 연결 데이터를 집계 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) .

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

#### <a name="sample-summarize-sent-and-received-data"></a>샘플: 보내고 받은 데이터 요약

이 샘플은 컴퓨터 집합 간의 인바운드 연결에서 송수신 되는 데이터의 양을 요약 합니다.

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

그룹에 대 한 [평가를 만듭니다](how-to-create-assessment.md) .
