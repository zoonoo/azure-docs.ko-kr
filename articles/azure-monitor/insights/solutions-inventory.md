---
title: Azure에서 솔루션을 모니터링 하는 인벤토리 | Microsoft Docs
description: Azure Monitor의 모니터링 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다.  이 문서에서는 Microsoft 및 해당 메서드 및 데이터 컬렉션 빈도 대 한 세부 정보에서 사용할 수 있는 솔루션을 모니터링의 목록을 제공 합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234028"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Azure에서 솔루션을 모니터링 하는 것에 대 한 인벤토리 및 데이터 수집 세부 정보
[모니터링 솔루션](solutions.md) 특정 응용 프로그램 또는 서비스의 작업에 대 한 추가 정보를 제공 하는 Azure에서 서비스를 활용 합니다. 모니터링 솔루션은 일반적으로 로그 데이터를 수집하고, 수집된 데이터를 분석하기 위한 쿼리 및 보기를 제공합니다. 사용하는 애플리케이션 및 서비스의 Azure Monitor에 모니터링 솔루션을 추가할 수 있습니다. 관리 솔루션은 일반적으로 무료로 제공되지만 데이터 수집 시 사용 요금이 발생할 수 있습니다.

이 문서에는 목록이 포함 되어 있습니다 [montioring 솔루션](solutions.md) 자세한 설명서 링크를 사용 하 여 Microsoft에서 제공 합니다.  Azure Monitor에 데이터를 수집하는 메서드 및 빈도에 대한 정보도 제공합니다.  사용 가능한 다른 솔루션을 확인 하 고 다양 한 모니터링 솔루션에 대 한 데이터 흐름 및 연결 요구 사항을 이해 하려면이 문서의 정보를 사용할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>모니터링 솔루션의 목록

다음 표에서 [모니터링 솔루션](solutions.md) Microsoft에서 제공 하는 Azure에서. 열의 항목은 솔루션이 해당 메서드를 사용하여 Azure Monitor로 데이터를 수집한다는 것을 의미합니다.  솔루션에서 선택된 열이 없으면 해당 솔루션은 다른 Azure 서비스에서 Azure Monitor에 바로 데이터를 씁니다. 자세한 내용을 보려면 각 솔루션의 링크를 따라 자세한 설명서로 이동하세요.

열에 대한 설명은 다음과 같습니다.

- **Microsoft 모니터링 에이전트** -에이전트에서 SCOM 관리 팩을 실행 하려면 Windows 및 Linux에서 사용 되 고 Azure에서 솔루션을 모니터링 합니다. 이 구성에서 에이전트는 Operations Manager 관리 그룹에 연결되지 않고 Azure Monitor에 직접 연결됩니다. 
- **Operations Manager** - Microsoft 모니터링 에이전트와 동일한 에이전트입니다. 이 구성에서는 Azure Monitor에 연결된 [Operations Manager 관리 그룹에 연결](../platform/om-agents.md)됩니다. 
-  **Azure Storage** -솔루션이 Azure 스토리지 계정에서 데이터를 수집합니다. 
- **Operations Manager 필요 여부** -연결 된 Operations Manager 관리 그룹은 모니터링 솔루션에서 데이터 수집을 위해 필요 합니다. 
- **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** - 에이전트가 [SCOM 관리 그룹에 연결된 경우](../platform/om-agents.md) 데이터가 관리 서버에서 Azure Monitor로 전송됩니다. 이 경우 에이전트가 Azure Monitor에 직접 연결할 필요가 없습니다. 이 상자를 선택하지 않으면 에이전트가 SCOM 관리 그룹에 연결되어 있어도 데이터가 에이전트에서 Azure Monitor로 직접 전송됩니다. 에이전트가 [Log Analytics 게이트웨이](../platform/gateway.md)를 통해 Azure Monitor와 통신할 수 있어야 합니다.
- **컬렉션 빈도** -빈도 지정 모니터링 솔루션에서 데이터가 수집 되는 합니다. 



| **모니터링 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [활동 로그 분석](../platform/activity-log-collect.md) | Azure | | | | | | 알림 시 |
| [AD 평가](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 일 |
| [AD 복제 상태](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5일 |
| [에이전트 상태](solution-agenthealth.md) | Windows 및 Linux | &#8226; | &#8226; | | | &#8226; | 1분 |
| [경고 관리](../platform/alert-management-solution.md)(Nagios) |Linux |&#8226; | | | | |도착 시 |
| [경고 관리](../platform/alert-management-solution.md)(Zabbix) |Linux |&#8226; | | | | |1분 |
| [경고 관리](../platform/alert-management-solution.md)(Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3분 |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | 해당 없음 |
| [Application Insights 커넥터(사용되지 않음)](../platform/app-insights-connector.md) | Azure | | | |  |  | 알림 시 |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 해당 없음 |
| [Azure Application Gateway 분석](azure-networking-analytics.md) | Azure |  |  |  |  |  | 알림 시 |
| **모니터링 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| [Azure 네트워크 보안 그룹 분석(사용되지 않음)](azure-networking-analytics.md) | Azure |  |  |  |  |  | 알림 시 |
| [Azure SQL Analytics(미리 보기)](azure-sql.md) | Windows | | | | | | 1분 |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 알림 시 |
| [용량 및 성능(미리 보기)](capacity-performance.md) | Windows |&#8226; |&#8226; | | |&#8226; |도착 시 |
| [변경 내용 추적](../../automation/change-tracking.md) | Windows |&#8226; |&#8226; | | |&#8226; |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [변경 내용 추적](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[varies](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [컨테이너](containers.md) | Windows 및 Linux | &#8226; | &#8226; |  |  |  | 3분 |
| [Key Vault 분석](azure-key-vault.md) |Windows | | | | | |알림 시 |
| [맬웨어 평가](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |매시간 |
| [네트워크 성능 모니터](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP는 5초마다 핸드셰이크를 수행하며 3분마다 데이터가 전송됩니다. |
| [Office 365 분석(미리 보기)](solution-office-365.md) |Windows | | | | | |알림 시 |
| **모니터링 솔루션** | **플랫폼** | **Microsoft 모니터링 에이전트** | **Operations Manager 에이전트** | **Azure 저장소** | **Operations Manager 필요 여부** | **관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터** | **수집 빈도** |
| [Service Fabric Analytics](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5분 |
| [서비스 맵](service-map.md) | Windows 및 Linux | &#8226; | &#8226; |  |  |  | 15초 |
| [SQL 평가](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 일 |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |도착 시 |
| [System Center Operations Manager 평가(미리 보기)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7일 |
| [업데이트 관리](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |하루에 최소 2회 및 업데이트를 설치하고 15분 후 |
| [업그레이드 준비](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2일 |
| [VMware 모니터링(사용되지 않음)](vmware.md) | Linux | &#8226; |  |  |  |  | 3분 |
| [Wire Data 2.0(미리 보기)](wire-data.md) |Windows(2012 R2/8.1 이상) |&#8226; |&#8226; | | | | 1분 |




## <a name="next-steps"></a>다음 단계
* 설명 하는 방법 [설치 및 사용 모니터링 솔루션](solutions.md)합니다.
* 모니터링 솔루션에서 수집한 데이터를 분석하는 [쿼리 만들기](../log-query/log-query-overview.md) 방법을 알아봅니다.
