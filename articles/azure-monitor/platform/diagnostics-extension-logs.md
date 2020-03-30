---
title: Azure 모니터의 이벤트에 IIS 및 테이블 저장소에 Blob 저장소 사용 | 마이크로 소프트 문서
description: Azure Monitor는 테이블 저장소에 진단을 작성하는 Azure 서비스의 로그를 읽거나 Blob 저장소에 작성된 IIS 로그를 읽을 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672398"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Azure 진단 확장에서 Azure 모니터 로그에 대한 데이터 수집
Azure 진단 확장은 [Azure 모니터의 에이전트로](agents-overview.md) 가상 컴퓨터를 포함한 Azure 컴퓨팅 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집합니다. 이 문서에서는 Azure 저장소에서 Azure 모니터 로그에 대한 진단 확장으로 수집된 데이터를 수집하는 방법을 설명합니다.

> [!NOTE]
> Azure 모니터의 로그 분석 에이전트는 일반적으로 게스트 운영 체제에서 Azure 모니터 로그로 데이터를 수집하는 데 선호되는 방법입니다. 에이전트에 대한 자세한 비교는 [Azure Monitor 에이전트 개요를](agents-overview.md) 참조하십시오.

## <a name="supported-data-types"></a>지원되는 데이터 형식
Azure 진단 확장 프로그램은 Azure 저장소 계정에 데이터를 저장합니다. Azure 모니터 로그가 이 데이터를 수집하려면 다음 위치에 있어야 합니다.

| 로그 형식 | 리소스 종류 | 위치 |
| --- | --- | --- |
| IIS 로그 |Virtual Machines <br> 웹 역할 <br> 작업자 역할 |wad-iis-logfiles(Blob Storage) |
| syslog |Virtual Machines |LinuxsyslogVer2v0(Table Storage) |
| Service Fabric 작업 이벤트 |Service Fabric 노드 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 이벤트 |Service Fabric 노드 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 이벤트 |Service Fabric 노드 |WADServiceFabricReliableServiceEventTable |
| Windows 이벤트 로그 |Service Fabric 노드 <br> Virtual Machines <br> 웹 역할 <br> 작업자 역할 |WADWindowsEventLogsTable(Table Storage) |
| Windows ETW 로그 |Service Fabric 노드 <br> Virtual Machines <br> 웹 역할 <br> 작업자 역할 |WADETWEventTable(Table Storage) |

## <a name="data-types-not-supported"></a>지원되지 않는 데이터 형식

- 게스트 운영 체제의 성능 데이터
- Azure 웹 사이트에서 IIS 로그


## <a name="enable-azure-diagnostics-extension"></a>Azure 진단 확장 사용
[설치 및 Windows Azure 진단 확장(WAD) 또는](diagnostics-extension-windows-install.md) Linux 진단 확장 을 사용하여 진단 확장 설치 및 구성에 대한 자세한 내용은 메트릭 및 [로그를 모니터링합니다.](../../virtual-machines/extensions/diagnostics-linux.md) 이렇게 하면 저장소 계정을 지정하고 Azure Monitor Log로 전달할 데이터의 컬렉션을 구성할 수 있습니다.


## <a name="collect-logs-from-azure-storage"></a>Azure 저장소에서 로그 수집
다음 절차를 사용하여 Azure Storage 계정에서 진단 확장 데이터 컬렉션을 활성화합니다.

1. Azure 포털에서 **로그 분석 작업 영역으로** 이동하여 작업 영역을 선택합니다.
1. 메뉴의 작업 영역 **데이터 원본** 섹션에서 저장소 **계정 로그를** 클릭합니다.
2. **추가를 클릭합니다.**
3. 수집할 데이터가 포함된 **저장소 계정을** 선택합니다.
4. 수집할 **데이터 유형을** 선택합니다.
5. Source값은 데이터 형식에 따라 자동으로 채워집니다.
6. **확인**을 클릭하여 구성을 저장합니다.
7. 추가 데이터 형식에 대해 반복합니다.

약 30분 만에 Log Analytics 작업 영역에서 저장소 계정의 데이터를 볼 수 있습니다. 구성이 적용된 후에 스토리지에 기록된 데이터만 볼 수 있습니다. 작업 영역은 저장소 계정에서 기존 데이터를 읽지 않습니다.

> [!NOTE]
> 포털은 원본이 저장소 계정에 있는지 또는 새 데이터가 기록되고 있는지 여부를 확인하지 않습니다.



## <a name="next-steps"></a>다음 단계

* 지원되는 Azure 서비스에 대해 [Azure 서비스에 대한 로그 및 메트릭 수집](collect-azure-metrics-logs.md)
* [솔루션을 사용하도록 설정](../../azure-monitor/insights/solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](../../azure-monitor/log-query/log-query-overview.md) 하여 데이터를 분석합니다.
