---
title: Azure SQL Data Warehouse 릴리스 정보 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245510"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL Data Warehouse 릴리스 정보
이 문서에서는 [Azure 가상 머신의 SQL Server](sql-data-warehouse-overview-what-is.md) 최신 릴리스에 제공되는 새 기능과 향상된 기능을 요약합니다. 이 문서에는 릴리스와 직접적인 관련은 없지만 동일한 시간대에 게시된 주목할 만한 콘텐츠 업데이트도 나열되어 있습니다. 다른 Azure 서비스의 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse 버전 10.0.10106.0(1월)

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
| **쿼리 재시작 기능 - CTAS 및 Insert/Select** | 드물지만(즉, 일시적인 네트워크 연결 문제, 노드 실패) Azure SQL DW의 쿼리 실행이 실패할 수 있습니다. [CREATE TABLE AS SELECT(CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) 및 INSERT-SELECT 작업과 같은 장기 실행 문에서 이러한 문제가 발생할 가능성이 더 높습니다. 이 릴리스에서는 Azure SQL DW가 이전에 발표된 SELECT 문 외에도 CTAS 및 INSERT-SELECT 문에 대한 재시도 논리를 구현합니다. 이번 변화로 인해 서비스에서 일시적인 문제를 투명하게 처리하여 쿼리 실패를 방지할 수 있습니다. 다시 시도 횟수와 처리된 일시적 오류 목록은 시스템에서 구성됩니다.|
|**Return Order By Optimization**|SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.   이제 모든 컴퓨팅 노드는 단일 컴퓨팅 노드로 결과를 보내서 병합 및 정렬하며, 이 결과는 컴퓨팅 노드를 통해 사용자에게 반환됩니다.  쿼리 결과 세트에 많은 수의 행이 포함되어 있을 때 단일 컴퓨팅 노드를 통해 병합하면 성능이 크게 향상됩니다. 이전에는 쿼리 실행 엔진이 각 컴퓨팅 노드에서 결과를 정렬하고 제어 노드로 스트리밍하면 제어 노드에서 결과가 병합되었습니다.|
|**PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선**|Azure SQL Data Warehouse Gen2에서 ShuffleMove 형식의 데이터 이동 단계는 [성능 향상 블로그](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)에 설명된 인스턴트 데이터 이동 기술을 사용합니다. 이번 릴리스에서는 동일한 인스턴트 데이터 이동 기술을 통해 데이터 이동 형식 PartitionMove 및 BroadcastMove가 지원됩니다. 이러한 유형의 데이터 이동 단계를 사용하는 사용자 쿼리의 성능이 개선됩니다. 이러한 성능 향상을 얻기 위해 코드를 변경할 필요는 없습니다.|

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
|없음 | |
| | |

## <a name="next-steps"></a>다음 단계
[SQL Data Warehouse 만들기](./create-data-warehouse-portal.md)

## <a name="more-information"></a>자세한 정보
- [블로그 - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [고객 자문 팀 블로그](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 포럼](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 용어집](../azure-glossary-cloud-terminology.md)