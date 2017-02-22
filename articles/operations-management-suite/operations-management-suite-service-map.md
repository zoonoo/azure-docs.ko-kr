---
title: "OMS(Operations Management Suite)의 서비스 맵 | Microsoft Azure"
description: "서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하여 서비스 간 통신을 매핑하는 OMS(Operations Management Suite) 솔루션입니다.  이 문서에서는 사용자 환경에 서비스 맵을 배포하고 다양한 시나리오에서 사용하는 것에 대해 자세히 설명합니다."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cf3e083f17bf8b2245373bced5823afd21fe1af9
ms.openlocfilehash: d2e55846667cccec824e31f648beac1c84fbcf50


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>OMS(Operations Management Suite)의 서비스 맵 솔루션 사용
서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다.  서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다.

이 문서에서는 서비스 맵 사용에 대한 세부 정보를 설명합니다.  서비스 맵 구성 및 에이전트 탑재에 대한 정보는 [OMS(Operations Management Suite)에서 서비스 맵 솔루션 구성](operations-management-suite-service-map-configure.md)을 참조하세요.


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>사용 사례: IT 프로세스 종속성 인식

### <a name="discovery"></a>검색
서비스 맵은 서버, 프로세스 및 타사 서비스 간 종속성에 대한 일반적인 참조 맵을 자동으로 작성합니다.  모든 TCP 종속성을 검색 및 매핑하여 예기치 않은 연결, 사용 중인 원격 타사 시스템, Active Directory 등 기존의 어두운 영역에 대한 종속성을 식별합니다.  서비스 맵은 관리되는 시스템에서 시도하는 실패한 네트워크 연결을 검색하여 잠재적인 서버 구성 오류, 서비스 중단 및 네트워크 문제를 파악할 수 있습니다.

### <a name="incident-management"></a>인시던트 관리
서비스 맵을 통해 시스템이 어떻게 연결되고 서로 어떻게 영향을 주는지 표시할 수 있으므로 문제 격리의 추측을 없앨 수 있습니다.  실패한 연결 외에도, 연결된 클라이언트에 대한 정보를 통해 잘못 구성된 부하 분산 장치, 중요한 서비스에서 예기치 않거나 과도한 로드, 프로덕션 시스템과 통신 중인 개발자 컴퓨터와 같은 불량 클라이언트를 식별할 수 있습니다.  OMS 변경 내용 추적과 워크플로를 통합하여 백 엔드 컴퓨터 또는 서비스에서 변경 이벤트로 인시던트의 근본 원인을 설명할 수 있는지 확인할 수 있습니다.

### <a name="migration-assurance"></a>마이그레이션 보증
서비스 맵을 통해 Azure 마이그레이션을 효과적으로 계획, 가속화 및 유효성 검사할 수 있으므로 모든 작업을 투명하게 예기치 않은 중단 없이 처리할 수 있습니다.  함께 마이그레이션해야 하는 모든 상호 종속적인 시스템을 검색하고 시스템 구성 및 용량을 평가하며 실행 중인 시스템이 여전히 사용자에게 서비스 중인지, 마이그레이션 대신 서비스 해제가 적합한지 여부를 식별합니다.  이동이 완료되면 클라이언트 로드 및 ID를 확인하여 테스트 시스템과 고객이 연결 중인지 확인할 수 있습니다.  서브넷 계획 및 방화벽 정의에 문제가 있으면 서비스 맵 맵에서 실패한 연결을 통해 사용자는 연결이 필요한 시스템으로 안내됩니다.

### <a name="business-continuity"></a>비즈니스 연속성
Azure Site Recovery를 사용 중이고 응용 프로그램 환경에 대한 복구 시퀀스를 정의하는 데 도움이 필요한 경우 서비스 맵은 시스템이 서로 어떻게 의존하여 복구 계획의 안정성을 보장하는지를 자동으로 보여 줄 수 있습니다.  중요한 서버를 선택하여 해당 클라이언트를 보고 중요한 서버가 복원되어 사용 가능해진 후에만 복구되어야 하는 프런트 엔드 시스템을 식별할 수 있습니다.  반대로, 중요한 서버의 백 엔드 종속성을 확인하여 중심 시스템이 복원되기 전에 복구해야 하는 시스템을 식별할 수 있습니다.

### <a name="patch-management"></a>패치 관리
서비스 맵은 다른 어떤 팀과 서버가 사용자의 서비스에 종속되어 있는지를 보여 주어 OMS 시스템 업데이트 평가 사용을 개선하므로 패치 적용을 위해 시스템이 중단되기 전에 사전에 알릴 수 있습니다.  또한 서비스 맵은 서비스가 사용 가능한지, 패치 적용되어 다시 시작된 후 제대로 연결되었는지를 표시하여 OMS에서 패치 관리를 개선합니다.


## <a name="mapping-overview"></a>매핑 개요
서비스 맵 에이전트는 에이전트가 설치된 서버에서 각 프로세스에 대한 인바운드 및 아웃바운드 연결에 대한 정보를 비롯하여 모든 TCP 연결 프로세스에 대한 정보를 수집합니다.  서비스 맵 솔루션의 왼쪽에서 컴퓨터 목록을 사용하여, 서비스 맵 에이전트가 설치된 컴퓨터에서 선택한 시간 범위 동안 해당 종속성을 시각화하도록 선택할 수 있습니다.  컴퓨터 종속성 맵은 특정 컴퓨터에 집중하고 해당 컴퓨터의 직접 TCP 클라이언트 또는 서버인 모든 컴퓨터를 보여 줍니다.

![서비스 맵 개요](media/oms-service-map/service-map-overview.png)

선택한 시간 범위 동안 활성 네트워크 연결을 통해 실행 중인 프로세스를 표시 하도록 맵에서 컴퓨터를 확장할 수 있습니다.  서비스 맵 에이전트가 있는 원격 컴퓨터가 프로세스 세부 정보를 표시하도록 확장되면 중심 컴퓨터와 통신 중인 프로세스만 표시됩니다.  중심 컴퓨터에 연결 중인 에이전트가 없는 프런트 엔드 컴퓨터 수는 연결되는 프로세스 왼쪽에 표시됩니다.  포커스 컴퓨터가 에이전트 없이 백 엔드 시스템에 대한 연결을 설정하는 경우 해당 백 엔드 서버는 동일한 포트 번호에 대한 다른 연결과 함께 서버 포트 그룹에 포함됩니다.

기본적으로 서비스 맵 맵은 최근 10분 간의 종속성 정보를 표시합니다.  왼쪽 상단에 있는 시간 컨트롤을 사용하여 맵에서 과거 시간 범위를&1;시간까지 쿼리하여 과거에 종속성이 어땠는지(예: 인시던트 중 또는 변경되기 전) 쿼리할 수 있습니다.    서비스 맵 데이터는 유료 작업 영역에서 30일 동안, 무료 작업 영역에서는 7일 동안 저장됩니다.

## <a name="status-badges"></a>상태 배지
맵의 각 서버 하단에는 서버에 대한 상태 정보를 전달하는 상태 배지 목록이 있을 수 있습니다.  이러한 배지가 있으면 OMS 솔루션 통합 중 하나에서 제공된 서버 관련 정보가 있는 것입니다.  배지를 클릭하면 오른쪽 창의 상태 세부 정보로 직접 이동됩니다.  현재 사용 가능한 상태 배지에는 경고, 변경, 보안 및 업데이트가 포함됩니다.

![실패한 연결](media/oms-service-map/status-badges.png)

## <a name="failed-connections"></a>실패한 연결
실패한 연결은 프로세스 및 컴퓨터에 대한 서비스 맵 맵에 표시됩니다. 이때 클라이언트 시스템이 프로세스 또는 포트에 도달할 수 없는 경우 빨간색 점선으로 표시됩니다.  실패한 연결은 시스템에서 실패한 연결을 시도하는 경우 서비스 맵 에이전트가 배포된 모든 시스템에서 보고됩니다.  서비스 맵은 연결 설정에 실패한 TCP 소켓을 관찰하여 이를 측정합니다.  이것은 방화벽, 클라이언트 또는 서버에서 구성 오류 또는 원격 서비스를 사용할 수 없기 때문일 수 있습니다.

![실패한 연결](media/oms-service-map/failed-connections.png)

실패한 연결을 이해하면 문제 해결, 마이그레이션 유효성 검사, 보안 분석 및 전체 아키텍처를 이해하는 데 도움이 됩니다.  실패한 연결이 문제가 되지 않을 때도 있지만 보통은 문제로 이어집니다. 예를 들어 장애 조치 환경에 갑자기 연결할 수 없게 되거나 클라우드 마이그레이션 후 두 응용 프로그램 계층이 통신할 수 없게 되기도 합니다.

## <a name="client-groups"></a>클라이언트 그룹
클라이언트 그룹은 맵에서 종속성 에이전트가 없는 클라이언트 컴퓨터를 나타내는 상자입니다.  단일 클라이언트 그룹은 개별 프로세스에 대한 클라이언트를 나타냅니다.

![클라이언트 그룹](media/oms-service-map/client-groups.png)

클라이언트 그룹의 서버 IP 주소를 확인하려면 그룹을 선택합니다.  속성 패널에는 그룹의 내용이 나열됩니다.

![클라이언트 그룹 속성](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>서버 포트 그룹
서버 그룹은 종속성 에이전트가 없는 서버의 서버 포트를 나타내는 상자입니다.  이 상자는 서버 포트와 해당 포트에 대한 연결이 설정된 서버 수를 나열합니다.  개별 서버 및 연결을 보려면 상자를 확장합니다.  상자에 서버가 하나만 있으면 이름 또는 IP 주소가 나열됩니다.

![서버 포트 그룹](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>상황에 맞는 메뉴
서버 오른쪽 위에 있는 세 개의 점을 클릭하면 해당 서버에 대한 상황에 맞는 메뉴가 표시됩니다.

![실패한 연결](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>서버 로드 맵
서버 로드 맵을 선택하면 선택한 서버를 새로운 주요 컴퓨터로 표시하는 새로운 맵으로 이동됩니다.

### <a name="showhide-self-links"></a>자체 링크 표시/숨기기
자체 링크 표시를 클릭하면 서버 내에서 프로세스를 시작 및 종료하는 TCP 연결에 해당하는 자체 링크를 포함하는 서버 노드가 다시 그려집니다.  자체 링크가 표시되면 메뉴가 자체 링크 숨기기로 변경되므로 사용자는 자체 링크 그리기를 전환할 수 있습니다.



## <a name="computer-and-process-properties"></a>컴퓨터 및 프로세스 속성
서비스 맵 맵을 탐색할 때는 해당 속성에 대한 추가 컨텍스트를 얻으려면 컴퓨터 및 프로세스를 선택할 수 있습니다.  컴퓨터는 DNS 이름, IPv4 주소, CPU 및 메모리 용량, VM 유형, 운영 체제 버전, 마지막으로 다시 부팅 시간 및 해당 OMS 및 서비스 맵 에이전트의 ID에 대한 정보를 제공합니다.

![컴퓨터 속성](media/oms-service-map/machine-properties.png)

프로세스 이름, 프로세스 설명, 사용자 이름 및 도메인(Windows), 회사 이름, 제품 이름, 제품 버전, 작업 디렉터리, 명령줄 및 프로세스 시작 시간을 포함하여 실행 중인 프로세스에 대한 운영 체제 메타데이터에서 프로세스 정보를 수집합니다.

![프로세스 속성](media/oms-service-map/process-properties.png)

프로세스 요약 패널은 바인딩된 포트, 인바운드 및 아웃바운드 연결, 실패한 연결을 포함하여 해당 프로세스의 연결에 대한 추가 정보를 제공합니다.

![프로세스 요약](media/oms-service-map/process-summary.png)

## <a name="computer-summary"></a>컴퓨터 요약
컴퓨터 요약 패널에는 서버 운영 체제 및 종속성 계수의 개요, 성능 메트릭, 변경 추적, 보안, 업데이트 등을 비롯한 다른 OMS 솔루션의 다양한 데이터가 포함됩니다.

![컴퓨터 요약](media/oms-service-map/machine-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS 변경 내용 추적 통합
변경 내용 추적과 서비스 맵 통합은 두 솔루션이 사용하도록 설정되고 OMS 작업 영역에서 구성된 경우 자동입니다.

컴퓨터 변경 내용 추적 패널에는 로그 검색의 추가 정보를 들여다 볼 수 있는 링크와 함께 모든 변경 내용 목록이 최근순으로 표시됩니다.
![컴퓨터 변경 내용 추적 패널](media/oms-service-map/change-tracking.png)

다음은 **Log Analytics에서 표시**를 선택한 후 구성 변경 이벤트의 드릴 다운 보기입니다.
![구성 변경 이벤트](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>OMS 성능 통합
컴퓨터 성능 패널에는 선택한 서버에 대한 표준 성능 메트릭이 표시됩니다.  이 메트릭에는 CPU Utilization, Memory Utilization, Network Bytes Sent 및 Network Bytes Received와 Network Bytes Sent 및 Network Bytes Received에 의한 상위 프로세스 목록이 포함됩니다.
![컴퓨터 변경 내용 추적 패널](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>OMS 보안 통합
보안 및 감사와 서비스 맵 통합은 두 솔루션이 사용하도록 설정되고 OMS 작업 영역에서 구성된 경우 자동입니다.

컴퓨터 보안 패널에는 선택한 서버에 대한 OMS 보안 및 감사 솔루션의 데이터가 표시됩니다.  이 패널은 선택한 시간 범위 동안 서버에 대한 미해결된 보안 문제의 요약을 표시합니다.  보안 문제를 클릭하면 보안 문제에 대한 세부 정보를 표시하는 로그 검색으로 드릴다운됩니다.
![컴퓨터 변경 내용 추적 패널](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>OMS 업데이트 통합
업데이트 관리와 서비스 맵 통합은 두 솔루션이 사용하도록 설정되고 OMS 작업 영역에서 구성된 경우 자동입니다.

컴퓨터 업데이트 패널에는 선택한 서버에 대한 OMS 업데이트 관리 솔루션의 데이터가 표시됩니다.  이 패널은 선택한 시간 범위 동안 서버에 대한 누락된 업데이트의 요약을 표시합니다.
![컴퓨터 변경 내용 추적 패널](media/oms-service-map/machine-updates.png)


## <a name="oms-alerts-integration"></a>OMS 경고 통합
서비스 맵은 OSM 경고와 통합되어 선택한 시간 범위에서 선택한 서버에 대해 발생된 경고를 표시합니다.  현재 경고가 있으면 서버에 아이콘이 표시되고 컴퓨터 경고 패널에 경고가 표시됩니다.

![컴퓨터 경고 패널](media/oms-service-map/machine-alerts.png)

서비스 맵에 관련 경고를 표시할 수 있으려면 특정 컴퓨터에 대해 발생하도록 경고 규칙을 만들어야 합니다.  적절한 경고를 만들려면
- 컴퓨터별로 그룹화하기 위해 “by Computer interval 1minute" 절 포함
- 미터법을 기준으로 경고하도록 선택

![경고 구성](media/oms-service-map/alert-configuration.png)


## <a name="log-analytics-records"></a>Log Analytics 레코드
서비스 맵의 컴퓨터 및 프로세스 인벤토리 데이터는 Log Analytics에서 [검색](../log-analytics/log-analytics-log-searches.md)에 사용할 수 있습니다.  이것은 마이그레이션 계획, 용량 분석, 검색 및 임시 성능 문제 해결 등의 시나리오에 적용할 수 있습니다.

프로세스 또는 컴퓨터가 시작되거나 서비스 맵에 등록된 경우 생성되는 레코드 외에도 고유한 각 컴퓨터 및 프로세스에 대해 시간당 하나의 레코드가 생성됩니다.  이러한 레코드는 다음 표의 속성을 가집니다.  ServiceMapComputer_CL 이벤트의 필드 및 값은 ServiceMap ARM API의 컴퓨터 리소스 필드에 매핑됩니다.  ServiceMapProcess_CL 이벤트의 필드 및 값은 ServiceMap ARM API의 프로세스 리소스 필드에 매핑됩니다.  ResourceName_s 필드는 해당하는 ARM 리소스의 이름 필드와 일치합니다. 노트 - 서비스 맵 기능이 확장되면 이러한 필드도 변경될 수 있습니다.


고유한 프로세스 및 컴퓨터를 식별하는 데 사용할 수 있는 내부적으로 생성된 속성이 있습니다.

- 컴퓨터 - ResourceId 또는 ResourceName_s를 사용하여 OMS 작업 영역 내에서 컴퓨터를 고유하게 식별합니다.
- 프로세스 - ResourceId를 사용하여 OMS 작업 영역 내에서 프로세스를 고유하게 식별합니다. ResourceName_s는 프로세스가 실행 중인 컴퓨터(MachineResourceName_s)의 컨텍스트 내에서 고유합니다. 

지정된 시간 범위 내에서 지정된 프로세스 및 컴퓨터에 대해 여러 레코드가 존재할 수 있으므로 쿼리는 동일한 컴퓨터 또는 프로세스에 대해 둘 이상의 레코드를 반환할 수 있습니다. 가장 최근 레코드만 포함하려면 쿼리에 "| dedup ResourceId"를 추가합니다.

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 레코드
**ServiceMapComputer_CL** 형식의 레코드는 서비스 맵 에이전트가 있는 서버에 대한 인벤토리 데이터를 포함합니다.  이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| 형식 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ResourceName_s | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ComputerName_s | 컴퓨터 FQDN |
| Ipv4Addresses_s | 서버의 IPv4 주소 목록 |
| Ipv6Addresses_s | 서버의 IPv6 주소 목록 |
| DnsNames_s | DNS 이름 배열 |
| OperatingSystemFamily_s | Windows 또는 Linux |
| OperatingSystemFullName_s | 운영 체제 전체 이름  |
| Bitness_s | 컴퓨터 비트 수(32비트) 또는 (64비트) |
| PhysicalMemory_d | 실제 메모리(MB) |
| Cpus_d | CPU 수 |
| CPUSpeed_d | CPU 속도(MHz)|
| VirtualizationState_s | "unknown", "physical", "virtual", "hypervisor" |
| VirtualMachineType_s | "hyperv", "vmware" 등 |
| VirtualMachineNativeMachineId_g | 해당 하이퍼바이저에 의해 할당된 VM ID |
| VirtualMachineName_s | VM 이름 |
| BootTime_t | 부팅 시간 |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 형식 레코드
**ServiceMapProcess_CL** 형식의 레코드는 서비스 맵 에이전트가 있는 서버에서 TCP 연결 프로세스에 대한 인벤토리 데이터를 포함합니다.  이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| 형식 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 프로세스에 대한 고유 식별자 |
| ResourceName_s | 프로세스를 실행 중인 컴퓨터 내에서 프로세스에 대한 고유 식별자|
| MachineResourceName_s | 컴퓨터 리소스 이름 |
| ExecutableName_s | 프로세스 실행 파일 이름 |
| StartTime_t | 프로세스 풀 시작 시간 |
| FirstPid_d | 프로세스 풀의 첫 번째 PID |
| Description_s | 프로세스 설명 |
| CompanyName_s | 회사 이름 |
| InternalName_s | 내부 이름 |
| ProductName_s | 제품 이름 |
| ProductVersion_s | 제품 버전 |
| FileVersion_s | 파일 버전 |
| CommandLine_s | 명령줄 |
| ExecutablePath _s | 실행 파일 경로 |
| WorkingDirectory_s | 작업 디렉터리 |
| 사용자 이름 | 프로세스가 실행 중인 계정 |
| UserDomain | 프로세스가 실행 중인 도메인 |


## <a name="sample-log-searches"></a>샘플 로그 검색

### <a name="list-all-known-machines"></a>알려진 모든 컴퓨터 나열
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>모든 관리되는 컴퓨터의 실제 메모리 용량을 나열합니다.
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>컴퓨터 이름, DNS, IP 및 OS를 나열합니다.
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>명령줄에서 "sql"로 모든 프로세스 찾기
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>리소스 이름으로 컴퓨터(가장 최근 레코드) 찾기
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP 주소로 컴퓨터(가장 최근 레코드) 찾기
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>지정된 컴퓨터의 알려진 모든 프로세스 나열
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>SQL을 실행하는 모든 컴퓨터를 나열합니다.
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>내 데이터 센터에서 curl의 고유한 모든 제품 버전 목록
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS를 실행하는 모든 컴퓨터의 컴퓨터 그룹 만들기
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="diagnostic-and-usage-data"></a>진단 및 사용 현황 데이터
Microsoft는 서비스 맵 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스 맵 서비스의 품질, 보안 및 무결성을 제공 및 개선합니다. 데이터에는 운영 체제 및 버전과 같은 소프트웨어 구성에 대한 정보가 포함되며 정확하고 효율적인 문제 해결 기능을 제공하기 위해 IP 주소, DNS 이름 및 워크스테이션 이름도 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인 정보 취급 방침](hhttps://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.


## <a name="next-steps"></a>다음 단계
- 서비스 맵이 수집한 데이터를 검색하기 위한 Log Analytics의 [로그 검색](../log-analytics/log-analytics-log-searches.md)에 대해 자세히 알아봅니다.


## <a name="feedback"></a>사용자 의견
서비스 맵 또는 이 설명서에 대한 의견이 있습니까?  기능을 제안하거나 기존 제안에 투표할 수 있는 [사용자 의견 페이지](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)를 방문하세요.



<!--HONumber=Jan17_HO1-->


