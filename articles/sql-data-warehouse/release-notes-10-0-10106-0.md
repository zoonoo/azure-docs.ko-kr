---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 12월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 8e82e352ebea4634b1b99864245adcf606352657
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469342"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Azure SQL Data Warehouse 버전 10.0.10106.0의 새로운 기능
Azure SQL DW(SQL Data Warehouse)는 지속적으로 개선됩니다. 이 문서에서는 SQL DW 버전 10.0.10106.0에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="query-restartability---ctas-and-insertselect"></a>쿼리 재시작 기능 - CTAS 및 Insert/Select
드문 경우지만(즉, 일시적인 네트워크 연결 문제, 노드 실패) Azure SQL DW의 쿼리 실행이 실패할 수 있습니다. [CREATE TABLE AS SELECT(CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) 및 INSERT-SELECT 작업과 같은 장기 실행 문에서 이러한 문제가 발생할 가능성이 더 높습니다. 이번 릴리스에서 Azure SQL DW는 CTAS 및 INSERT-SELECT 문(이전에 발표한 SELECT 문에 추가로)에 대해 다시 시도 논리를 구현하여 시스템이 이러한 일시적인 문제를 투명하게 처리하도록 하고 쿼리가 실패하지 않도록 합니다. 다시 시도 횟수와 처리된 일시적 오류 목록은 시스템에서 구성됩니다.

## <a name="return-order-by-optimization"></a>Return Order By Optimization
SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.  이전에는 쿼리 실행 엔진이 각 컴퓨팅 노드에서 결과를 정렬하고 제어 노드로 스트리밍하면 제어 노드에서 결과가 병합되었습니다. 이번에 기능이 개선되면서, 모든 컴퓨팅 노드가 결과를 단일 컴퓨팅 노드로 보내면 이 컴퓨팅 노드에서 결과가 병합되고 정렬된 결과가 컴퓨팅 노드를 통해 사용자에게 반환됩니다.  따라서 쿼리 결과 세트에 많은 수의 행이 포함되어 있으면 성능이 크게 향상됩니다.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선
Azure SQL Data Warehouse Gen2에서 ShuffleMove 형식의 데이터 이동 단계는 [여기의 성능 향상 블로그](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)에 설명된 인스턴트 데이터 이동 기술을 활용합니다.  이번 릴리스에서는 동일한 인스턴트 데이터 이동 기술을 통해 데이터 이동 형식 PartitionMove 및 BroadcastMove가 지원됩니다.  이러한 유형의 데이터 이동 단계를 사용하는 사용자 쿼리에서는 성능이 크게 개선됩니다.  이러한 성능 향상을 활용하기 위해 코드를 변경할 필요는 없습니다.

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 학습하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
