---
title: "Azure SQL Database 리소스 제한 | Microsoft Docs"
description: "이 페이지에서는 Azure SQL 데이터베이스에 대한 몇 가지 일반적인 리소스 제한을 설명합니다."
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 61eac09668b14a98a42b1907a54577d80eb933a6
ms.lasthandoff: 03/29/2017


---
# <a name="azure-sql-database-resource-limits"></a>Azure SQL 데이터베이스 리소스 제한
## <a name="overview"></a>개요
Azure SQL Database는 **리소스 관리**와 **제한 적용**이라는 서로 다른 두 메커니즘을 사용하는 데이터베이스에서 사용 가능한 리소스를 관리합니다. 이 항목에서는 리소스 관리의 다음 두 주요 영역을 설명합니다.

## <a name="resource-governance"></a>리소스 관리
Basic, Standard 및 Premium 서비스 계층의 설계 목표 중 하나는 데이터베이스가 다른 데이터베이스로부터 격리된 자체 컴퓨터에서 실행되는 것처럼 Azure SQL 데이터베이스가 동작하도록 하는 것입니다. 리소스 관리는 이러한 동작을 에뮬레이션합니다. 집계 리소스 사용률이 데이터베이스에 할당된 최대 사용 가능한 CPU, 메모리, 로그 I/O 및 데이터 I/O 리소스에 도달하면 리소스 관리가 실행 중인 쿼리를 큐에 대기시키고 리소스가 확보되는 대로 큐에 대기 중인 쿼리에 리소스를 할당합니다.

전용 컴퓨터에서도 사용 가능한 모든 리소스를 활용하면 현재 실행 중인 쿼리의 실행 시간이 길어지며 이것은 클라이언트에서 명령 시간 초과로 이어질 수 있습니다. 적극적인 재시도 논리를 사용하는 응용 프로그램 및 데이터베이스에 대한 쿼리 실행 빈도가 높은 응용 프로그램에서 동시 요청 수 제한에 도달한 경우 새 쿼리를 실행하려고 할 때 오류 메시지가 발생할 수 있습니다.

### <a name="recommendations"></a>권장 사항:
데이터베이스의 최대 사용률에 근접한 경우 리소스 사용률 및 평균 쿼리 응답 시간을 모니터링합니다. 쿼리 대기 시간이 높아졌을 때 일반적으로 세 가지 옵션이 있습니다.

1. 데이터베이스로 들어오는 요청 수를 줄여 시간 초과와 요청이 밀리는 일을 방지합니다.
2. 데이터베이스에 보다 높은 수준의 성능을 할당합니다.
3. 쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 Azure SQL 데이터베이스 성능 지침 문서의 쿼리 튜닝/힌트 섹션을 참조하세요.

## <a name="enforcement-of-limits"></a>제한 적용
CPU, 메모리, 로그 I/O 및 데이터 I/O 이외의 리소스는 제한에 도달할 때 새 요청을 거부하여 적용합니다. 데이터베이스가 구성된 최대 크기 제한에 도달하면 선택 및 삭제는 계속 진행되지만 데이터 크기를 늘리는 삽입 및 업데이트는 실패합니다. 클라이언트는 제한에 도달하면 [오류 메시지](sql-database-develop-error-messages.md)를 받게 됩니다.

예를 들어 SQL Database에 대한 연결 수와 처리할 수 있는 동시 요청 수는 제한되어 있습니다. SQL 데이터베이스는 데이터베이스에 대한 연결 수가 동시 요청 수를 초과하는 것을 허용하여 연결 풀링을 지원합니다. 사용할 수 있는 연결의 수는 응용 프로그램에서 쉽게 제어할 수 있지만, 병렬 요청 수는 예측하고 제어하기가 보통 어렵습니다. 특히 최대 로드 중에 응용 프로그램이 너무 많은 요청을 보내거나 데이터베이스가 리소스 한계에 도달하고 길어진 쿼리 실행 시간으로 작업자 스레드가 밀리기 시작한 경우 오류가 발생할 수 있습니다.

## <a name="service-tiers-and-performance-levels"></a>서비스 계층 및 성능 수준
단일 데이터베이스 및 탄력적 풀 모두에 대한 서비스 계층과 성능 수준이 있습니다.

### <a name="single-databases"></a>단일 데이터베이스
단일 데이터베이스에 대한 데이터베이스 제한은 데이터베이스 서비스 계층 및 성능 수준에 의해 정의됩니다. 다음 표에서는 다양한 성능 수준에서 Basic, Standard 및 Premium 데이터베이스의 특징을 설명합니다.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> P11 및 P15 성능 수준을 사용하는 고객은 추가 비용 없이 최대 4TB의 포함된 저장소를 사용할 수 있습니다. 이 4TB 옵션은 미국 동부2, 미국 서부, 유럽 서부, 동남 아시아, 일본 동부, 오스트레일리아 동부, 캐나다 중부 및 캐나다 동부에서 현재 공개 미리 보기 상태로 제공됩니다.
>

### <a name="elastic-pools"></a>탄력적 풀
[탄력적 풀](sql-database-elastic-pool.md) 은 풀의 데이터베이스에서 리소스를 공유합니다. 다음 표에서는 Basic, Standard 및 Premium 탄력적 풀의 특징을 설명합니다.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

이전 표에 나열된 각 리소스의 확장된 정의는 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)의 설명을 참조하세요. 서비스 계층에 대한 개요는 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)을 참조하세요.

## <a name="other-sql-database-limits"></a>기타 SQL 데이터베이스 제한
| 영역 | 제한 | 설명 |
| --- | --- | --- |
| 구독당 자동화된 내보내기를 사용하는 데이터베이스 |10 |자동화된 내보내기를 사용하면 SQL 데이터베이스 백업에 대한 사용자 지정 일정을 만들 수 있습니다. 이 기능의 미리 보기는 2017년 3월 1일에 종료됩니다.  |
| 서버당 데이터베이스 |최대 5000 |V12 서버에서는 서버당 최대 5,000개의 데이터베이스를 허용합니다. |
| 서버당 DTU |45000 |V12 서버에서는 독립 실행형 데이터베이스 및 탄력적 풀을 프로비전하는 데 서버당 45,000개의 DTU가 허용됩니다. 서버당 허용되는 독립 실행형 데이터베이스 및 풀의 총 수는 서버 DTU 수에 의해서만 제한됩니다.  

> [!IMPORTANT]
> Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md) 또는 [Azure Automation](../automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우, [GitHub에서 샘플 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다.
>


## <a name="resources"></a>리소스
[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)

[Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)

[SQL 데이터베이스 클라이언트 프로그램에 대한 오류 메시지](sql-database-develop-error-messages.md)

