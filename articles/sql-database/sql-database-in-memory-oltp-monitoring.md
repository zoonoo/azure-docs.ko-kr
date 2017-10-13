---
title: "XTP 메모리 내 저장소 모니터링 | Microsoft Docs"
description: "XTP 메모리 내 저장소 사용, 용량을 예측 및 모니터링합니다. 41823 용량 오류를 해결합니다."
services: sql-database
documentationcenter: 
author: jodebrui
manager: jhubbard
editor: 
ms.assetid: b617308e-692c-4938-8fa2-070034a3ecef
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jodebrui
ms.openlocfilehash: e953b60493c5a7c7a7ad74533471bd321d42abef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-in-memory-oltp-storage"></a>메모리 내 OLTP 저장소 모니터링
[메모리 내 OLTP](sql-database-in-memory.md)를 사용하는 경우 메모리에 최적화된 테이블 및 테이블 변수에 있는 데이터는 메모리 내 OLTP 저장소에 상주합니다. 각 프리미엄 서비스 계층은 최대 메모리 내 OLTP 저장소 크기가 있으며 이는 [단일 데이터베이스 리소스 제한](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) 및 [탄력적 풀 리소스 제한](sql-database-resource-limits.md#elastic-pool-change-storage-size)에서 설명합니다. 이 제한이 초과되면 삽입 및 업데이트 작업이 실패(오류 41823)하기 시작할 수 있습니다. 해당 시점에서 데이터를 삭제하여 하나에 메모리를 회수하거나 데이터베이스의 성능 계층을 업그레이드해야 합니다.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>데이터가 메모리 내 저장소 용량에 맞는지 여부 결정
다른 프리미엄 서비스 계층의 저장소 용량을 결정합니다. [단일 데이터베이스 리소스 제한](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) 및 [탄력적 풀 리소스 제한](sql-database-resource-limits.md#elastic-pool-change-storage-size)을 참조하세요.

메모리에 최적화된 테이블에 대한 메모리 요구 사항을 추정하면 Azure SQL 데이터베이스에서 SQL Server가 작동과 동일한 방식으로 작동합니다. [MSDN](https://msdn.microsoft.com/library/dn282389.aspx)의 항목을 검토하는 데 몇 분이 걸립니다.

테이블 및 테이블 변수 행 뿐만 아니라 인덱스가 사용자 데이터 크기를 계산합니다. 또한 ALTER TABLE은 전체 테이블 및 인덱스의 새 버전을 만들기 위해 충분한 공간이 필요합니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
메모리 내 저장소 사용량을 [Azure Portal](https://portal.azure.com/)에서 성능 계층에 대한 저장소 용량 비율로 모니터링할 수 있습니다. 

1. 데이터베이스 블레이드에서 리소스 사용률 상자를 찾고 편집을 클릭합니다.
2. 메트릭 `In-Memory OLTP Storage percentage`을 참조하세요.
3. 경고를 추가하려면 리소스 사용률 상자 클릭하여 메트릭 블레이드를 연 다음 경고 추가를 클릭합니다.

또는 다음 쿼리를 사용하여 메모리 내 저장소 사용률을 표시합니다.

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>메모리 부족 상황 수정 - 오류 41823
메모리가 부족하면 오류 메시지 41823과 함께 삽입, 업데이트 및 만들기 작업이 실패합니다.

오류 메시지 41823은 메모리 액세스에 최적화된 테이블 및 테이블 변수가 최대 크기를 초과했음을 나타냅니다.

이 오류를 해결하려면

* 잠재적으로 데이터를 기존의 디스크 기반 테이블에 오프로드딩하여 메모리에 최적화된 테이블에서 데이터를 삭제합니다. 또는
* 메모리에 최적화된 테이블에서 유지하는 데 필요한 데이터에 대한 충분한 메모리 내 저장소가 있는 서비스 계층을 업그레이드합니다.

## <a name="next-steps"></a>다음 단계
모니터링 지침은 [동적 관리 뷰를 사용하여 Azure SQL Database 모니터링](sql-database-monitoring-with-dmvs.md)을 참조하세요.
