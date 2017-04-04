---
title: "Azure SQL Data Warehouse의 분산 데이터 작동 원리 | Microsoft Docs"
description: "SQL Data Warehouse 및 병렬 데이터 웨어하우스의 대규모 병렬 처리(MPP) 시스템에 데이터가 분산되는 원리와 분산 테이블의 옵션에 대해 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: 195b78a7f634d01f228c90efb34763e4175708ac
ms.lasthandoff: 01/24/2017


---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>대규모 병렬 처리(MPP)에 대한 분산 데이터 및 분산 테이블
Microsoft의 대규모 병렬 처리(MPP) 시스템인 SQL Data Warehouse 및 병렬 데이터 웨어하우스의 사용자 데이터가 분산되는 원리에 대해 알아봅니다. 분산 데이터를 효율적으로 사용하도록 데이터 웨어하우스를 디자인하면 MPP 아키텍처의 쿼리 처리 장점을 얻을 수 있습니다. 몇 가지 데이터베이스 디자인 선택에 따라 쿼리 성능 향상에 상당한 영향을 미칠 수 있습니다.  

> [!NOTE]
> Azure SQL Data Warehouse 및 병렬 데이터 웨어하우스는 동일한 대규모 병렬 처리(MPP) 디자인을 사용하지만, 기본 플랫폼 때문에 몇 가지 차이점이 있습니다. SQL Data Warehouse는 Azure에서 실행되는 PaaS(Platform as a Service)입니다. 병렬 데이터 웨어하우스는 Windows Server에서 실행되는 온-프레미스 어플라이언스인 APS(분석 플랫폼 시스템)에서 실행됩니다.
> 
> 

## <a name="what-is-distributed-data"></a>분산 데이터란?
SQL Data Warehouse 및 병렬 데이터 웨어하우스에서 분산 데이터란 MPP 시스템의 여러 위치에 저장되는 사용자 데이터를 말합니다. 이러한 각 위치는 데이터의 해당 부분에서 쿼리를 실행하는 독립 저장소이자 처리 장치로써 역할을 수행합니다. 분산 데이터는 쿼리를 병렬로 실행하여 높은 쿼리 성능을 얻기 위한 기본 사항입니다.

데이터를 분산하기 위해 데이터 웨어하우스는 사용자 테이블의 각 행을 하나의 분산 위치에 할당합니다.  해시 분산 메서드 또는 라운드 로빈 메서드를 사용하여 테이블을 분산할 수 있습니다. 분산 메서드는 CREATE TABLE 문에서 지정합니다. 

## <a name="hash-distributed-tables"></a>해시 분산 테이블
다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다. 결정적 함수는 각 행이 한 분산에 속하도록 각 행을 할당합니다. 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

분산 열을 선택할 때 고유성, 데이터 오차, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

![분산 테이블](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "분산 테이블")  

* 각 행은 하나의 분산에 속합니다.  
* 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다.  
* 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블
라운드 로빈 분산 테이블은 각 행을 순차적으로 분산에 할당하여 행을 분산합니다. 데이터를 라운드 로빈 테이블에 로드하는 속도는 빠르지만 쿼리 성능이 느려질 수 있습니다.  일반적으로 라운드 로빈 테이블을 조인하려면 쿼리에서 정확한 결과를 도출할 수 있도록 행을 다시 섞어야 하며, 이 작업에 시간이 걸립니다.

## <a name="distributed-storage-locations-are-called-distributions"></a>분산된 저장소 위치를 분산이라고 함
분산된 각 위치를 분산이라고 부릅니다. 쿼리가 병렬로 실행되면 각 분산은 데이터의 해당 부분에서 SQL 쿼리를 수행합니다. SQL Data Warehouse는 SQL Database를 사용하여 쿼리를 실행합니다. 병렬 데이터 웨어하우스는 SQL Server를 사용하여 쿼리를 실행합니다. 이 비공유 아키텍처 설계는 확장 병렬 컴퓨팅을 구축하기 위한 기본입니다.

### <a name="can-i-view-the-distributions"></a>분산을 볼 수 있나요?
각 분산은 분산 ID를 갖고 있으며 SQL Data Warehouse 및 병렬 데이터 웨어하우스와 관련된 시스템 뷰에 표시됩니다. 분산 ID를 사용하여 쿼리 성능 및 기타 문제를 해결할 수 있습니다. 시스템 뷰 목록은 [MPP 시스템 뷰](sql-data-warehouse-reference-tsql-statements.md)를 참조하세요.

## <a name="difference-between-a-distribution-and-a-compute-node"></a>분산과 계산 노드의 차이점
분산은 분산 데이터를 저장하고 병렬 쿼리를 처리하는 기본 단위입니다. 분산은 계산 노드로 그룹화됩니다. 각 계산 노드는 하나 이상의 분산을 추적합니다.  

* 분석 플랫폼 시스템은 하드웨어 아키텍처 및 확장 기능의 중앙 구성 요소로 계산 노드를 사용합니다. 해시 분산 테이블에 대한 다이어그램처럼 항상 계산 노드 하나당&8;개의 분산을 사용합니다. 계산 노드의 수 그리고 그에 따른 분산의 수는 어플라이언스에 대해 구매하는 계산 노드의 수에 따라 결정됩니다. 예를 들어 8개의 계산 노드를 구매하면 64개의 분산(계산 노드 8개 x 분산 8개/노드)을 얻게 됩니다. 
* SQL Data Warehouse는 분산의 수는 60개로 고정되어 있고 계산 노드의 수는 유연하게 결정할 수 있습니다. 계산 노드는 Azure 컴퓨팅 및 저장소 리소스를 통해 구현됩니다. 데이터 웨어하우스에 대해 지정하는 백 엔드 서비스 워크로드 및 컴퓨팅 용량(DWU)에 따라 계산 노드의 수가 달라질 수 있습니다. 계산 노드의 수가 변경되면 계산 노드당 분산의 수도 변경됩니다. 

### <a name="can-i-view-the-compute-nodes"></a>계산 노드를 볼 수 있나요?
각 계산 노드는 노드 ID를 갖고 있으며 SQL Data Warehouse 및 병렬 데이터 웨어하우스와 관련된 시스템 뷰에 표시됩니다.  시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 계산 노드를 볼 수 있습니다. 시스템 뷰 목록은 [MPP 시스템 뷰](sql-data-warehouse-reference-tsql-statements.md)를 참조하세요.

## <a name="Replicated"></a>병렬 데이터 웨어하우스의 복제 테이블
적용 대상: 병렬 데이터 웨어하우스

병렬 데이터 웨어하우스는 분산 테이블을 사용할 뿐 아니라 테이블을 복제하는 옵션을 제공합니다. *복제 테이블*은 각 계산 노드에 통째로 저장된 테이블입니다. 테이블을 복제하면 계산 노드 간에 테이블 행을 전송하지 않아도 조인 또는 집계에 테이블을 사용할 수 있습니다. 테이블 복제는 테이블 크기가 작은 경우에만 가능합니다. 각 계산 노드의 전체 테이블을 저장하려면 추가 저장소가 필요하기 때문입니다.  

다음은 각 계산 노드에 저장된 복제 테이블을 보여 주는 다이어그램입니다. 복제 테이블은 해당 계산 노드에 할당된 모든 디스크에 저장됩니다. 이 디스크 전략은 SQL Server 파일 그룹을 사용하여 구현됩니다.  

![복제 테이블](media/sql-data-warehouse-distributed-data/replicated-table.png "복제 테이블") 

## <a name="next-steps"></a>다음 단계
분산 테이블을 효과적으로 사용하려면 [SQL Data Warehouse의 테이블 분산](sql-data-warehouse-tables-distribute.md)을 참조하세요.  


