---
title: Azure Monitor에서 이벤트에 대해 IIS 및 테이블 저장소에 대 한 blob 저장소 사용 | Microsoft Docs
description: 테이블 저장소 또는 blob 저장소에 기록 된 IIS 로그에 진단 정보를 기록 하는 Azure 서비스에 대 한 로그를 읽을 수 Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 095fd0b534c0dffaf80d2464fb9734f295335b84
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317181"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Azure 진단 확장에서 Azure Monitor 로그로 데이터 수집
Azure 진단 확장은 가상 컴퓨터를 포함 하 여 Azure 계산 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집 하는 [Azure Monitor의 에이전트](agents-overview.md) 입니다. 이 문서에서는 Azure Storage에서 진단 확장을 통해 수집 된 데이터를 Azure Monitor 로그에 수집 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Monitor의 Log Analytics 에이전트는 일반적으로 게스트 운영 체제에서 Azure Monitor 로그로 데이터를 수집 하는 기본 방법입니다. 에이전트의 자세한 비교는 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요.

## <a name="supported-data-types"></a>지원되는 데이터 형식
Azure 진단 확장은 Azure Storage 계정에 데이터를 저장 합니다. 이 데이터를 수집 하는 Azure Monitor 로그는 다음 위치에 있어야 합니다.

| 로그 형식 | 리소스 종류 | 위치 |
| --- | --- | --- |
| IIS 로그 |Virtual Machines <br> 웹 역할 <br> 작업자 역할 |wad-iis-logfiles(Blob Storage) |
| syslog |Virtual Machines |LinuxsyslogVer2v0(Table Storage) |
| Service Fabric 작업 이벤트 |Service Fabric 노드 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 이벤트 |Service Fabric 노드 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 이벤트 |Service Fabric 노드 |WADServiceFabricReliableServiceEventTable |
| Windows 이벤트 로그 |Service Fabric 노드 <br> Virtual Machines <br> 웹 역할 <br> 작업자 역할 |WADWindowsEventLogsTable(Table Storage) |
| Windows ETW 로그 |Service Fabric 노드 <br> Virtual Machines <br> 웹 역할 <br> 작업자 역할 |WADETWEventTable(Table Storage) |

## <a name="data-types-not-supported"></a>지원 되지 않는 데이터 형식

- 게스트 운영 체제의 성능 데이터
- Azure 웹 사이트의 IIS 로그


## <a name="enable-azure-diagnostics-extension"></a>Azure 진단 확장 사용
진단 확장 설치 및 구성에 대 한 자세한 내용은 [Windows Azure 진단 확장 설치 및 구성 (WAD)](diagnostics-extension-windows-install.md) 또는 [Linux 진단 확장을 사용 하 여 메트릭 및 로그 모니터링을](../../virtual-machines/extensions/diagnostics-linux.md) 참조 하세요. 그러면 저장소 계정을 지정 하 고 Azure Monitor 로그로 전달 하려는 데이터 컬렉션을 구성 하는 데 도움이 됩니다.


## <a name="collect-logs-from-azure-storage"></a>Azure Storage에서 로그 수집
다음 절차를 사용 하 여 Azure Storage 계정에서 진단 확장 데이터 수집을 사용 하도록 설정할 수 있습니다.

1. Azure Portal에서 **Log Analytics 작업** 영역으로 이동 하 고 작업 영역을 선택 합니다.
1. 메뉴의 **작업 영역 데이터 원본** 섹션에서 **저장소 계정 로그** 를 클릭 합니다.
2. **추가**를 클릭 합니다.
3. 수집할 데이터가 포함 된 **저장소 계정을** 선택 합니다.
4. 수집 하려는 **데이터 형식을** 선택 합니다.
5. 원본의 값은 데이터 형식에 따라 자동으로 채워집니다.
6. **확인**을 클릭하여 구성을 저장합니다.
7. 추가 데이터 형식에 대해 반복 합니다.

약 30 분 후 Log Analytics 작업 영역에서 저장소 계정의 데이터를 볼 수 있습니다. 구성이 적용된 후에 스토리지에 기록된 데이터만 볼 수 있습니다. 작업 영역에서 저장소 계정의 기존 데이터를 읽지 않습니다.

> [!NOTE]
> 포털에서는 원본이 저장소 계정에 있는지 또는 새 데이터를 쓰고 있는지 확인 하지 않습니다.



## <a name="next-steps"></a>다음 단계

* 지원되는 Azure 서비스에 대해 [Azure 서비스에 대한 로그 및 메트릭 수집](./resource-logs.md#send-to-log-analytics-workspace)
* [솔루션을 사용하도록 설정](../insights/solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](../log-query/log-query-overview.md) 하여 데이터를 분석합니다.

