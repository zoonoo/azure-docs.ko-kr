---
title: OMS(Operations Management Suite)에서 ADM(Application Dependency Monitor) | Microsoft Docs
description: ADM(Application Dependency Monitor)은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑하는 OMS(Operations Management Suite) 솔루션입니다.  이 문서에서는 사용자 환경에 ADM를 배포하고 다양한 시나리오에서 사용하는 것에 대해 자세히 설명합니다.
services: operations-management-suite
documentationcenter: ''
author: daseidma
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2016
ms.author: daseidma;bwren

---
# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>OMS(Operations Management Suite)에서 Application Dependency Monitor 솔루션 사용
![경고 관리 아이콘](media/operations-management-suite-application-dependency-monitor/icon.png) ADM(Application Dependency Monitor)은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다.  Application Dependency Monitor는 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다.

이 문서에서는 Application Dependency Monitor 사용에 대해 자세히 설명합니다.  ADM 구성 및 에이전트 탑재에 대한 정보는 [OMS(Operations Management Suite)에서 Application Dependency Monitor 솔루션 구성](operations-management-suite-application-dependency-monitor-configure.md)을 참조하세요.

> [!NOTE]
> Application Dependency Monitor는 현재 비공개 미리 보기 상태입니다.  [https://aka.ms/getadm](https://aka.ms/getadm)에서 ADM 비공개 미리 보기에 대한 액세스를 요청할 수 있습니다.
> 
> 비공개 미리 보기 중에 모든 OMS 계정은 ADM에 대한 무제한 액세스 권한을 포함합니다.  ADM 노드는 무료이지만 AdmComputer_CL 및 AdmProcess_CL 형식에 대한 Log Analytics 데이터는 다른 솔루션처럼 측정됩니다.
> 
> ADM이 공개 미리 보기로 전환된 후에는 OMS 요금제에 나와 있는 Insight & Analytics 무료 및 유료 고객에게만 제공됩니다.  무료 계층 계정은 ADM 노드가 5개로 제한됩니다.  비공개 미리 보기에 참여하고 있고 ADM이 공개 미리 보기로 전환될 때 OMS 요금제에 등록하지 않은 경우 그 시점부터 ADM을 사용할 수 없게 됩니다. 
> 
> 

## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>사용 사례: IT 프로세스 종속성 인식
### <a name="discovery"></a>검색
ADM은 서버, 프로세스 및 타사 서비스 간 종속성에 대한 일반적인 참조 맵을 자동으로 작성합니다.  모든 TCP 종속성을 검색 및 매핑하여 예기치 않은 연결, 사용 중인 원격 타사 시스템, DNS 및 AD 등 기존의 어두운 영역에 대한 종속성을 식별합니다.  ADM은 관리되는 시스템에서 시도하는 실패한 네트워크 연결을 검색하여 잠재적인 서버 구성 오류, 서비스 중단 및 네트워크 문제를 파악할 수 있습니다.

### <a name="incident-management"></a>인시던트 관리
ADM을 통해 시스템이 어떻게 연결되고 서로 어떻게 영향을 주는지 표시할 수 있으므로 문제 격리의 추측을 없앨 수 있습니다.  실패한 연결 외에도, 연결된 클라이언트에 대한 정보를 통해 잘못 구성된 부하 분산 장치, 중요한 서비스에서 예기치 않거나 과도한 로드, 프로덕션 시스템과 통신 중인 개발자 컴퓨터와 같은 불량 클라이언트를 식별할 수 있습니다.  OMS 변경 내용 추적과 워크플로를 통합하여 백 엔드 컴퓨터 또는 서비스에서 변경 이벤트로 인시던트의 근본 원인을 설명할 수 있는지 확인할 수 있습니다.

### <a name="migration-assurance"></a>마이그레이션 보증
ADM을 통해 Azure 마이그레이션을 효과적으로 계획, 가속화 및 유효성 검사할 수 있으므로 모든 작업을 투명하게 예기치 않은 중단 없이 처리할 수 있습니다.  함께 마이그레이션해야 하는 모든 상호 종속적인 시스템을 검색하고 시스템 구성 및 용량을 평가하며 실행 중인 시스템이 여전히 사용자에게 서비스 중인지, 마이그레이션 대신 서비스 해제가 적합한지 여부를 식별합니다.  이동이 완료되면 클라이언트 로드 및 ID를 확인하여 테스트 시스템과 고객이 연결 중인지 확인할 수 있습니다.  서브넷 계획 및 방화벽 정의에 문제가 있으면 ADM 맵에서 실패한 연결을 통해 사용자는 연결이 필요한 시스템으로 안내됩니다.

### <a name="business-continuity"></a>비즈니스 연속성
Azure Site Recovery를 사용 중이고 응용 프로그램 환경에 대한 복구 시퀀스를 정의하는 데 도움이 필요한 경우 ADM은 시스템이 서로 어떻게 의존하여 복구 계획의 안정성을 보장하는지를 자동으로 보여 줄 수 있습니다.  중요한 서버를 선택하여 해당 클라이언트를 보고 중요한 서버가 복원되어 사용 가능해진 후에만 복구되어야 하는 프런트 엔드 시스템을 식별할 수 있습니다.  반대로, 중요한 서버의 백 엔드 종속성을 확인하여 중심 시스템이 복원되기 전에 복구해야 하는 시스템을 식별할 수 있습니다.

### <a name="patch-management"></a>패치 관리
ADM은 다른 어떤 팀과 서버가 사용자의 서비스에 종속되어 있는지를 보여 주어 OMS 시스템 업데이트 평가 사용을 개선하므로 패치 적용을 위해 시스템이 중단되기 전에 사전에 알릴 수 있습니다.  또한 ADM은 서비스가 사용 가능한지, 패치 적용되어 다시 시작된 후 제대로 연결되었는지를 표시하여 OMS에서 패치 관리를 개선합니다. 

## <a name="mapping-overview"></a>매핑 개요
ADM 에이전트는 에이전트가 설치된 서버에서 각 프로세스에 대한 인바운드 및 아웃바운드 연결에 대한 정보를 비롯하여 모든 TCP 연결 프로세스에 대한 정보를 수집합니다.  ADM 솔루션의 왼쪽에서 컴퓨터 목록을 사용하여, ADM 에이전트가 설치된 컴퓨터에서 선택한 시간 범위 동안 해당 종속성을 시각화하도록 선택할 수 있습니다.  컴퓨터 종속성 맵은 특정 컴퓨터에 집중하고 해당 컴퓨터의 직접 TCP 클라이언트 또는 서버인 모든 컴퓨터를 보여 줍니다.

![ADM 개요](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

선택한 시간 범위 동안 활성 네트워크 연결을 통해 실행 중인 프로세스를 표시 하도록 맵에서 컴퓨터를 확장할 수 있습니다.  ADM 에이전트가 있는 원격 컴퓨터가 프로세스 세부 정보를 표시하도록 확장되면 중심 컴퓨터와 통신 중인 프로세스만 표시됩니다.  중심 컴퓨터에 연결 중인 에이전트가 없는 프런트 엔드 컴퓨터 수는 연결되는 프로세스 왼쪽에 표시됩니다.  중심 컴퓨터가 에이전트 없이 백 엔드 컴퓨터에 연결 중이면 백 엔드는 IPv4 주소가 표시되는 맵에서 노드로 나타나며 이 노드를 확장하여 중심 컴퓨터가 통신 중인 개별 포트 및 서비스를 표시할 수 있습니다.

기본적으로 ADM 맵은 최근 10분 간의 종속성 정보를 표시합니다.  왼쪽 상단에 있는 시간 컨트롤을 사용하여 맵에서 과거 시간 범위를 1시간까지 쿼리하여 과거에 종속성이 어땠는지(예: 인시던트 중 또는 변경되기 전) 쿼리할 수 있습니다.    ADM 데이터는 유료 작업 영역에서 30일 동안, 무료 작업 영역에서는 7일 동안 저장됩니다.

![선택한 컴퓨터 속성이 있는 컴퓨터 맵](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>실패한 연결
실패한 연결은 프로세스 및 컴퓨터에 대한 ADM 맵에 표시됩니다. 이때 클라이언트 시스템이 프로세스 또는 포트에 도달할 수 없는 경우 빨간색 점선으로 표시됩니다.  실패한 연결은 시스템에서 실패한 연결을 시도하는 경우 ADM 에이전트가 배포된 모든 시스템에서 보고됩니다.  ADM은 연결 설정에 실패한 TCP 소켓을 관찰하여 이를 측정합니다.  이것은 방화벽, 클라이언트 또는 서버에서 구성 오류 또는 원격 서비스를 사용할 수 없기 때문일 수 있습니다. 

![실패한 연결](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

실패한 연결을 이해하면 문제 해결, 마이그레이션 유효성 검사, 보안 분석 및 전체 아키텍처를 이해하는 데 도움이 됩니다.  실패한 연결이 문제가 되지 않을 때도 있지만 보통은 문제로 이어집니다. 예를 들어 장애 조치 환경에 갑자기 연결할 수 없게 되거나 클라우드 마이그레이션 후 두 응용 프로그램 계층이 통신할 수 없게 되기도 합니다.  위의 이미지에서는 IIS 및 WebSphere가 둘 다 실행 중이지만 연결할 수 없습니다. 

## <a name="computer-and-process-properties"></a>컴퓨터 및 프로세스 속성
ADM 맵을 탐색할 때는 해당 속성에 대한 추가 컨텍스트를 얻으려면 컴퓨터 및 프로세스를 선택할 수 있습니다.  컴퓨터는 DNS 이름, IPv4 주소, CPU 및 메모리 용량, VM 유형, 운영 체제 버전, 마지막으로 다시 부팅 시간 및 해당 OMS 및 ADM 에이전트의 ID에 대한 정보를 제공합니다.

프로세스 이름, 프로세스 설명, 사용자 이름 및 도메인(Windows), 회사 이름, 제품 이름, 제품 버전, 작업 디렉터리, 명령줄 및 프로세스 시작 시간을 포함하여 실행 중인 프로세스에 대한 운영 체제 메타데이터에서 프로세스 정보를 수집합니다.

![프로세스 속성](media/operations-management-suite-application-dependency-monitor/process-properties.png)

프로세스 요약 패널은 바인딩된 포트, 인바운드 및 아웃바운드 연결, 실패한 연결을 포함하여 해당 프로세스의 연결에 대한 추가 정보를 제공합니다. 

![프로세스 요약](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS 변경 내용 추적 통합
변경 내용 추적과 ADM 통합은 두 솔루션이 사용하도록 설정되고 OMS 작업 영역에서 구성된 경우 자동입니다.

컴퓨터 요약 패널은 선택한 시간 범위 동안 선택한 컴퓨터에서 변경 내용 추적 이벤트가 발생했는지 여부를 나타냅니다.

![컴퓨터 요약 패널](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

컴퓨터 변경 내용 추적 패널에는 로그 검색의 추가 정보를 들여다 볼 수 있는 링크와 함께 모든 변경 내용 목록이 최근순으로 표시됩니다.
![컴퓨터 변경 내용 추적 패널](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

다음은 **Log Analytics에서 표시**를 선택한 후 구성 변경 이벤트의 드릴 다운 보기입니다.
![구성 변경 이벤트](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)

## <a name="log-analytics-records"></a>Log Analytics 레코드
ADM의 컴퓨터 및 프로세스 인벤토리 데이터는 Log Analytics에서 [검색](../log-analytics/log-analytics-log-searches.md)에 사용할 수 있습니다.  이것은 마이그레이션 계획, 용량 분석, 검색 및 임시 성능 문제 해결 등의 시나리오에 적용할 수 있습니다. 

프로세스 또는 컴퓨터가 시작되거나 ADM에 등록된 경우 생성되는 레코드 외에도 고유한 각 컴퓨터 및 프로세스에 대해 시간당 하나의 레코드가 생성됩니다.  이러한 레코드는 다음 표의 속성을 가집니다. 

고유한 프로세스 및 컴퓨터를 식별하는 데 사용할 수 있는 내부적으로 생성된 속성이 있습니다.

* PersistentKey_s는 프로세스 구성에서 고유하게 정의됩니다(예: 명령줄 및 사용자 ID).  지정된 컴퓨터에 대해 고유하지만 컴퓨터 간에 반복될 수 있습니다.
* ProcessId_s 및 ComputerId_s는 ADM 모델에서 전역적으로 고유합니다.

### <a name="admcomputer_cl-records"></a>AdmComputer_CL 레코드
**AdmComputer_CL** 형식의 레코드는 ADM 에이전트가 있는 서버에 대한 인벤토리 데이터를 포함합니다.  이러한 레코드는 다음 표의 속성을 가집니다.  

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*AdmComputer_CL* |
| SourceSystem |*OpsManager* |
| ComputerName_s |Windows 또는 Linux 컴퓨터 이름 |
| CPUSpeed_d |CPU 속도(MHz) |
| DnsNames_s |이 컴퓨터에 대한 모든 DNS 이름의 목록 |
| IPv4s_s |이 컴퓨터에 사용되는 모든 IPv4 주소 목록 |
| IPv6s_s |이 컴퓨터에 사용되는 모든 IPv6 주소 목록  (ADM은 IPv6 주소를 식별하지만 IPv6 종속성은 검색하지 않습니다.) |
| Is64Bit_b |OS 종류에 따라 true 또는 false |
| MachineId_s |OMS 작업 영역 간에 고유한 내부 GUID |
| OperatingSystemFamily_s |Windows 또는 Linux |
| OperatingSystemVersion_s |긴 OS 버전 문자열 |
| TimeGenerated |레코드가 생성된 날짜 및 시간입니다. |
| TotalCPUs_d |CPU 코어 수 |
| TotalPhysicalMemory_d |메모리 용량(MB) |
| VirtualMachine_b |OS가 VM 게스트인지에 따라 true 또는 false |
| VirtualMachineID_g |Hyper-V VM ID |
| VirtualMachineName_g |Hyper-V VM 이름 |
| VirtualMachineType_s |Hyperv, Vmware, Xen, Kvm, Ldom, Lpar, Virtualpc |

### <a name="admprocess_cl-type-records"></a>AdmProcess_CL 형식 레코드
**AdmProcess_CL** 형식의 레코드는 ADM 에이전트가 있는 서버에서 TCP 연결 프로세스에 대한 인벤토리 데이터를 포함합니다.  이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*AdmProcess_CL* |
| SourceSystem |*OpsManager* |
| CommandLine_s |프로세스의 전체 명령줄 |
| CompanyName_s |회사 이름(Windows PE 또는 Linux RPM에서) |
| Description_s |긴 프로세스 설명(Windows PE 또는 Linux RPM에서) |
| FileVersion_s |실행 파일 버전(Windows PE, Windows에서만) |
| FirstPid_d |OS 프로세스 ID |
| InternalName_s |실행 파일의 내부 이름(Windows PE, Windows에서만) |
| MachineId_s |OMS 작업 영역 간에 고유한 내부 GUID |
| Name_s |프로세스 실행 파일 이름 |
| Path_s |프로세스 실행 파일의 파일 시스템 경로 |
| PersistentKey_s |이 컴퓨터 내에서 고유한 내부 GUID |
| PoolId_d |유사한 명령줄을 토대로 프로세스를 집계하기 위한 내부 ID입니다. |
| ProcessId_s |OMS 작업 영역 간에 고유한 내부 GUID |
| ProductName_s |제품 이름 문자열(Windows PE 또는 Linux RPM에서) |
| ProductVersion_s |제품 버전 문자열(Windows PE 또는 Linux RPM에서) |
| StartTime_t |로컬 컴퓨터 클록에서 프로세스 시작 시간 |
| TimeGenerated |레코드가 생성된 날짜 및 시간입니다. |
| UserDomain_s |프로세스 소유자의 도메인(Windows만) |
| UserName_s |프로세스 소유자의 이름(Windows만) |
| WorkingDirectory_s |프로세스 작업 디렉터리 |

## <a name="sample-log-searches"></a>샘플 로그 검색
### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>모든 관리되는 컴퓨터의 실제 메모리 용량을 나열합니다.
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>컴퓨터 이름, DNS, IP 및 OS 버전을 나열합니다.
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>명령줄에서 "sql"로 모든 프로세스 찾기
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>지정된 프로세스에 대한 이벤트 데이터를 본 후 해당 컴퓨터 ID를 사용하여 컴퓨터의 이름을 검색합니다.
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>SQL을 실행하는 모든 컴퓨터를 나열합니다.
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>내 데이터 센터에서 curl의 고유한 모든 제품 버전 목록
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS를 실행하는 모든 컴퓨터의 컴퓨터 그룹 만들기
![ADM 쿼리 예제](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)

## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터
Microsoft는 Application Dependency Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 Application Dependency Monitor 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 데이터에는 운영 체제 및 버전과 같은 소프트웨어 구성에 대한 정보가 포함되며 정확하고 효율적인 문제 해결 기능을 제공하기 위해 IP 주소, DNS 이름 및 워크스테이션 이름도 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인 정보 취급 방침](hhttps://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [로그 검색](../log-analytics/log-analytics-log-searches.md\] in Log Analytics to retrieve data collected by Application Dependency Monitor..md)에 대한 자세한 정보

<!--HONumber=Oct16_HO2-->


