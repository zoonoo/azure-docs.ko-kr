---
title: "Log Analytics의 용량 관리 솔루션 | Microsoft Docs"
description: "Log Analytics의 용량 계획 솔루션을 사용하여 System Center Virtual Machine Manager에서 관리되는 Hyper-V 서버의 용량을 이해할 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 57e7fbdaa393e078b62a6d6a0b181b67d532523d
ms.openlocfilehash: c34cda0da164c711c8effc78d2af38ad8df581aa


---
# <a name="capacity-management-solution-in-log-analytics"></a>Log Analytics의 용량 관리 솔루션
Log Analytics의 용량 관리 솔루션을 사용하여 Hyper-V 서버의 용량을 이해할 수 있습니다. 이 솔루션을 사용하려면 System Center Operations Manager 및 System Center Virtual Machine Manager가 모두 필요합니다. 직접 연결된 에이전트를 사용하면 용량 계획 솔루션이 작동하지 않습니다. 이 솔루션은 모니터링되는 서버에서 성능 카운터를 읽고 처리하도록 클라우드의 OMS 서비스로 사용 현황 데이터를 보냅니다. 논리는 사용 현황 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 시간이 지남에 따라 사용 패턴이 식별되고 현재 사용량에 따라 용량이 프로젝션됩니다.

예를 들어, 개별 서버에 대한 추가 프로세서 코어 또는 추가 메모리가 필요한 시기를 프로젝션이 식별할 수 있습니다. 이 예에서는 프로젝션이 30일 후 서버에 추가 메모리가 필요함을 나타낼 수 있습니다. 이 프로젝션은 서버의 다음 유지 관리 기간 동안 메모리 업그레이드를 계획할 수 있습니다.

> [!NOTE]
> 용량 관리 솔루션은 작업 영역에 추가할 수 없습니다. 용량 관리 솔루션을 설치한 고객은 이 솔루션을 계속 사용할 수 있습니다.  
> 
> 

대체 용량 및 성능 솔루션은 비공개 미리 보기 버전 상태입니다. 이 대체 솔루션은 원래 용량 관리 솔루션에서 문제가 발생한 고객 문제를 해결하기 위해 만들어졌습니다.

* Virtual Machine Manager 및 Operations Manager를 사용하기 위한 요구 사항
* 그룹을 기반으로 사용자 지정/필터링할 수 없음
* 시간별 데이터 집계가 충분히 자주 실행되지 않음
* VM 수준 유용한 정보 없음
* 데이터 안정성

새 용량 솔루션의 이점:

* 향상된 안정성 및 정확도로 세부적인 데이터 수집 지원
* VMM이 필요 없이 Hyper-V 지원
* VM 수준 사용률에 대한 유용한 정보

현재 새 솔루션에는 Hyper-V 서버 2012 이상이 필요합니다. 솔루션은 Hyper-V 환경에 대한 정보를 제공하고 호스트의 전체 사용률(CPU, 메모리 및 디스크) 및 해당 Hyper-V 서버에서 실행 중인 VM에 대한 가시성을 제공합니다. 메트릭은 실행 중인 모든 호스트 및 VM에 걸친 CPU, 메모리 및 디스크에 수집됩니다.

이 페이지에 대한 나머지 설명서는 이전 용량 관리 솔루션을 참조합니다. 새 솔루션이 공개 미리 보기 상태가 되면 이 문서는 업데이트됩니다.

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

* 용량 관리 솔루션을 사용하려면 Operations Manager가 필요합니다.
* 용량 관리 솔루션을 사용하려면 Virtual Machine Manager가 필요합니다.
* VMM(Virtual Machine Manager)과 Operations Manager의 연결이 필요합니다. 시스템 연결에 대한 자세한 내용은 [Operations Manager와 VMM을 연결하는 방법](http://technet.microsoft.com/library/hh882396.aspx)을 참조하세요.
* Operations Manager를 Log Analytics에 연결해야 합니다.
* [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 용량 관리 솔루션을 추가합니다.  추가 구성은 필요 없습니다.

## <a name="capacity-management-data-collection-details"></a>용량 관리 데이터 수집 정보
용량 관리는 사용하도록 설정한 에이전트를 통해 성능 데이터, 메타데이터 및 상태 데이터를 수집합니다.

다음 표에서는 데이터 수집 방법 및 용량 관리를 위해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![아니요](./media/log-analytics-capacity/oms-bullet-red.png) |![예](./media/log-analytics-capacity/oms-bullet-green.png) |![아니요](./media/log-analytics-capacity/oms-bullet-red.png) |![예](./media/log-analytics-capacity/oms-bullet-green.png) |![예](./media/log-analytics-capacity/oms-bullet-green.png) |매시간 |

다음 표에서는 용량 관리에 의해 수집된 데이터 형식 예제를 보여 줍니다.

| **데이터 형식** | **필드** |
| --- | --- |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| 성능 |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| 시스템 상태 |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="capacity-management-page"></a>용량 관리 페이지
용량 계획 솔루션이 설치된 후, OMS의 **개요** 페이지에서 **용량 계획** 타일을 사용하여 모니터링되는 서버의 용량을 볼 수 있습니다.

![용량 계획 타일의 이미지](./media/log-analytics-capacity/oms-capacity01.png)

타일은 서버 용량의 요약을 볼 수 있는 **용량 관리** 대시보드를 엽니다. 페이지는 클릭할 수 있는 다음 타일을 표시합니다.

* *가상 컴퓨터 개수*: 가상 컴퓨터의 용량에 대해 남은 기간(일)을 표시합니다.
* *계산*: 프로세서 코어와 사용 가능한 메모리를 표시합니다.
* *저장소*: 사용된 디스크 공간 및 평균 디스크 대기 시간을 표시합니다.
* *검색*: OMS 시스템의 모든 데이터를 검색하는 데 사용할 수 있는 데이터 탐색기입니다.

![용량 관리 대시보드의 이미지](./media/log-analytics-capacity/oms-capacity02.png)

### <a name="to-view-a-capacity-page"></a>용량 페이지를 보려면
* **개요** 페이지에서 **용량 관리**를 클릭하고 **계산** 또는 **저장소**를 클릭합니다.

## <a name="compute-page"></a>계산 페이지
Microsoft Azure OMS의 **계산** 대시보드를 사용하여 사용률, 일일 예상 용량 및 인프라와 관련된 효율성에 대한 정보를 볼 수 있습니다. **사용률** 영역을 사용하여 가상 컴퓨터 호스트에서 CPU 코어 및 메모리 사용률을 볼 수 있습니다. 프로젝션 도구를 사용하여 지정된 날짜 범위에 사용할 수 있을 것으로 추정되는 용량을 예상할 수 있습니다. **효율성** 영역을 사용하여 가상 컴퓨터 호스트의 효율성을 참조할 수 있습니다. 클릭하여 연결된 항목에 대한 세부 정보를 볼 수 있습니다.

다음 범주에 대한 Excel 통합 문서를 생성할 수 있습니다.

* 코어 사용률이 가장 높은 상위 호스트
* 메모리 사용률이 가장 높은 상위 호스트
* 비효율적인 가상 컴퓨터가 있는 상위 호스트
* 사용률별 상위 호스트
* 사용률별 하위 호스트

![용량 관리 계산 페이지의 이미지](./media/log-analytics-capacity/oms-capacity03.png)

다음 영역은 **계산** 대시보드에 표시됩니다.

**사용률**: 가상 컴퓨터 호스트에서 CPU 코어 및 메모리 사용률을 봅니다.

* *사용된 코어*: 모든 호스트(CPU 사용량 백분율에 호스트의 실제 코어 수를 곱한)에 대한 합계입니다.
* *사용 가능한 코어*: 사용된 코어를 뺀 총 실제 코어 수입니다.
* *사용 가능한 코어 백분율*: 실제 코어의 총 수로 나눈 사용 가능한 실제 코어입니다.
* *VM 당 가상 코어*: T시스템에서 가상 컴퓨터의 총 수로 나눈 시스템의 총 가상 코어입니다.
* *실제 코어 대 가상 코어 비율*: 총 실제 코어 대 시스템의 가상 컴퓨터에서 사용되는 실제 코어의 비율입니다.
* *사용 가능한 가상 코어 수*: 가상 코어 대 사용 가능한 실제 코어를 곱한 실제 코어 비율입니다.
* *사용한 메모리*: 모든 호스트에서 사용 되는 메모리의 합입니다.
* *사용 가능한 메모리*: 사용된 메모리를 뺀 총 실제 메모리입니다.
* *사용 가능한 메모리 백분율*: 총 실제 메모리로 나눈 사용 가능한 실제 메모리입니다.
* *VM 당 가상 메모리*: 시스템의 총 가상 컴퓨터 수로 나눈 시스템의 총 가상 메모리입니다.
* *가상 메모리 대 실제 메모리 비율*: 시스템의 총 실제 메모리를 나눈 시스템의 총 가상 메모리입니다.
* *사용 가능한 가상 메모리*: 사용 가능한 실제 메모리를 곱한 가상 메모리 대 실제 메모리 비율입니다.

**프로젝션 도구**

프로젝션 도구를 사용하여 리소스 사용률에 대한 과거의 추세를 볼 수 있습니다. 가상 컴퓨터, 메모리, 코어 및 저장소에 대한 사용량 추세를 포함합니다. 프로젝션 기능은 각 리소스에서 실행하는 시기를 알 수 있도록 프로젝션 알고리즘을 사용합니다. 이 기능을 사용하면 용량(예: 메모리, 코어 또는 저장소)을 추가로 구매해야 할 때를 알 수 있도록 적절한 용량 계획을 계산할 수 있습니다.

**효율성**

* *유휴 VM*: 지정된 시간 동안 10% 미만의 CPU와 10%의 메모리를 사용합니다.
* *VM 사용 과다*: 지정된 시간 동안 90% 초과의 CPU와 90%의 메모리를 사용합니다.
* *유휴 호스트*: 지정된 시간 동안 10% 미만의 CPU와 10%의 메모리를 사용합니다.
* *호스트 사용 과다*: 지정된 시간 동안 90% 초과의 CPU와 90%의 메모리를 사용합니다.

### <a name="to-work-with-items-on-the-compute-page"></a>계산 페이지에서 항목으로 작업하려면
1. **계산** 대시보드의 **사용률** 영역에서, 사용 중인 CPU 코어와 메모리에 대한 용량 정보를 봅니다.
2. **검색** 페이지에서 항목을 클릭하여 열고 항목에 대한 세부 정보를 봅니다.
3. **프로젝션** 도구에서 날짜 슬라이더를 이동하여 선택한 날짜에 사용될 용량의 프로젝션을 표시합니다.
4. **효율성** 영역에서 가상 컴퓨터 및 가상 컴퓨터 호스트에 대한 용량 효율성 정보를 봅니다.

## <a name="direct-attached-storage-page"></a>직접 연결 저장소 페이지
OMS의 **직접 연결 저장소** 대시보드를 사용하여 저장소 사용률, 디스크 성능 및 예상되는 디스크 용량 기간에 대한 용량 정보를 보려면 볼 수 있습니다. **사용률** 영역을 사용하여 가상 컴퓨터 호스트에서 디스크 공간 사용량을 볼 수 있습니다. **디스크 성능** 영역을 사용하여 가상 컴퓨터 호스트의 대기 시간 및 디스크 처리량을 볼 수 있습니다. 프로젝션 도구를 사용하여 지정된 날짜 범위에 사용할 수 있을 것으로 추정되는 용량을 예상할 수도 있습니다. 클릭하여 연결된 항목에 대한 세부 정보를 볼 수 있습니다.

다음 범주에 대한 이 용량 정보에서 Excel 통합 문서를 생성할 수 있습니다.

* 호스트별 상위 디스크 공간 사용량
* 호스트별 상위 평균 대기 시간

다음 영역은 **저장소** 페이지에 표시됩니다.

* *사용률*: 가상 컴퓨터 호스트의 디스크 공간 사용량을 봅니다.
* *총 디스크 공간*: 모든 호스트에 대한 합계(논리 디스크 공간)
* *사용된 디스크 공간*: 모든 호스트에 대한 합계(사용된 논리 디스크 공간)
* *사용 가능한 디스크 공간*: 사용된 디스크 공간을 제외한 전체 디스크 공간
* *사용된 디스크 백분율*:총 디스크 공간으로 나눈 사용된 디스크 공간
* *사용 가능한 디스크 백분율*:총 디스크 공간으로 나눈 사용 가능한 디스크 공간

![용량 관리 직접 연결 저장소 페이지의 이미지](./media/log-analytics-capacity/oms-capacity04.png)

**디스크 성능**

OMS를 사용하여 디스크 공간 사용률에 대한 과거 추세를 볼 수 있습니다. 프로젝션 기능은 차후 사용률에 대한 알고리즘을 사용합니다. 특히 공간 사용률에 대해 프로젝션 기능을 사용하면 디스크 공간이 부족해지는 시기를 예상할 수 있습니다. 이 프로젝션을 통해 적절한 저장소를 계획하고 저장소를 추가로 구입해야 하는 시기를 알 수 있습니다.

**프로젝션 도구**

프로젝션 도구를 사용하여 디스크 공간 사용률에 대한 과거의 추세를 볼 수 있습니다. 프로젝션 기능을 사용하여 디스크 공간이 부족한 시기를 예상할 수도 있습니다. 이 프로젝션을 통해 적절한 용량을 계획하고 저장소 용량을 추가로 구입해야 하는 시기를 알 수 있습니다.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>직접 연결 저장소 페이지에서 항목을 작업하려면
1. **직접 연결 저장소** 대시보드의 **사용률** 영역에서 디스크 사용률 정보를 볼 수 있습니다.
2. **검색** 페이지에서 연결된 항목을 클릭하여 열고 항목에 대한 세부 정보를 봅니다.
3. **디스크 성능** 영역에서, 디스크 처리량 및 대기 시간 정보를 볼 수 있습니다.
4. **프로젝션 도구**에서 날짜 슬라이더를 이동하여 선택한 날짜에 사용될 용량의 프로젝션을 표시합니다.

## <a name="next-steps"></a>다음 단계
* [Log Analytics에서 로그 검색](log-analytics-log-searches.md) 을 사용하여 자세한 용량 관리 데이터를 볼 수 있습니다.




<!--HONumber=Nov16_HO3-->


