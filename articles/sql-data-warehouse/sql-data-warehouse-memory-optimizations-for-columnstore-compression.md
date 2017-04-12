---
title: "Azure SQL의 Columnstore 인덱스 성능 개선 | Microsoft Docs"
description: "메모리 요구 사항을 줄이거나 사용 가능한 메모리를 늘려 columnstore 인덱스가 각 행 그룹으로 압축되는 행 수를 최대화합니다."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/18/2016
ms.author: shigu;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8d189256ed4c876859203406cda95ce0be36c96c
ms.lasthandoff: 03/29/2017


---

# <a name="memory-optimizations-for-columnstore-compression"></a>Columnstore 압축을 위한 메모리 최적화

메모리 요구 사항을 줄이거나 사용 가능한 메모리를 늘려 columnstore 인덱스가 각 행 그룹으로 압축되는 행 수를 최대화합니다.  이 방법을 사용하여 columnstore 인덱스에 대한 압축 비율 및 쿼리 성능을 개선시킬 수 있습니다.

## <a name="why-the-rowgroup-size-matters"></a>행 그룹 크기가 중요한 이유
columnstore 인덱스는 개별 행 그룹의 열 세그먼트를 검색하여 테이블을 검색하므로 각 행 그룹에서 행 수를 최대화하면 쿼리 성능이 향상됩니다. 행 그룹에 행 수가 많은 경우 데이터 압축이 향상되며 따라서 디스크에서 읽어올 데이터가 줄어듭니다.

행 그룹 에 대한 자세한 내용은 [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.

## <a name="target-size-for-rowgroups"></a>행 그룹의 대상 크기
최적의 쿼리 성능을 위해 columnstore 인덱스에서 행 그룹당 행 수를 최대화하는 것을 목표로 합니다. 행 그룹은 최대 1,048,576개의 행을 포함할 수 있습니다. 행 그룹당 최대 행 수를 포함하지 않는 것이 좋습니다. Columnstore 인덱스는 행 그룹에 100,000개 이상의 행이 있을 때 적절한 성능을 달성합니다.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>압축 중에 행 그룹을 잘라낼 수 있음

대량 로드 또는 columnstore 인덱스 다시 작성 중에는 각 행 그룹에 대해 지정된 모든 행을 압축하기에 메모리가 부족할 수 있습니다. 메모리가 부족할 때 columnstore 인덱스는 columnstore로 압축이 성공할 수 있도록 행 그룹 크기를 자릅니다.

10,000개 이상의 행을 각 행 그룹으로 압축하기에 메모리가 부족한 경우 SQL Data Warehouse에서 오류를 생성합니다.

대량 로드에 대한 자세한 내용은 [클러스터형 columnstore 인덱스로 대량 로드](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index)를 참조하세요.

## <a name="how-to-estimate-memory-requirements"></a>메모리 요구 사항을 예측하는 방법

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

한 개의 행 그룹을 압축하는 데 필요한 최대 메모리는 대략적으로 다음과 같습니다.

- 72MB +
- \#행 \* \#열 \* 8바이트 +
- \#행 \* \#short-string-columns \* 32바이트 +
- 압축 사전인 경우 \#long-string-columns \* 16MB

여기서 short-string-columns는 32바이트 이하의 문자열 데이터 형식을 사용하고 long-string-columns는 32바이트를 초과하는 문자열 데이터 형식을 사용합니다.

긴 문자열은 텍스트 압축용으로 고안된 압축 방법으로 압축됩니다. 이 압축 방법은 *사전*을 사용하여 텍스트 패턴을 저장합니다. 사전의 최대 크기는 16MB입니다. 행 그룹에는 긴 문자열 각각에 대해 사전이 한 개만 있습니다.

columnstore 메모리 요구 사항에 대한 자세한 내용은 [Azure SQL Data Warehouse 확장: 구성 및 지침](https://myignite.microsoft.com/videos/14822) 비디오를 참조하세요.

## <a name="ways-to-reduce-memory-requirements"></a>메모리 요구 사항을 줄이는 방법

다음 기술을 사용하여 행 그룹을 columnstore 인덱스로 압축하기 위한 메모리 요구 사항을 줄일 수 있습니다.

### <a name="use-fewer-columns"></a>적은 수의 열 사용
가능한 경우 열 수를 줄여서 테이블을 설계하세요. 행 그룹이 columnstore로 압축된 경우 columnstore 인덱스는 각 열 세그먼트를 별도로 압축합니다. 따라서 열 수가 늘어나면 행 그룹을 압축하기 위한 메모리 요구 사항이 증가합니다.


### <a name="use-fewer-string-columns"></a>적은 수의 문자열 열 사용
문자열 데이터 형식의 열에는 숫자 및 날짜 데이터 형식보다 더 많은 메모리가 필요합니다. 메모리 요구 사항을 줄이려면 팩트 테이블에서 문자열 열을 제거하고 더 작은 차원 테이블에 배치하는 것이 좋습니다.

문자열 압축을 위한 추가 메모리 요구 사항:

- 32자 이하의 문자열 데이터 형식에는 값당 32바이트가 더 필요할 수 있습니다.
- 32자를 초과하는 문자열 데이터 형식은 사전 방법을 사용하여 압축됩니다.  행 그룹의 각 열에는 사전을 작성하기 위해 최대 16MB가 더 필요할 수 있습니다.

### <a name="avoid-over-partitioning"></a>오버 분할 방지

Columnstore 인덱스는 파티션당 행 그룹을 하나 이상 만듭니다. SQL Data Warehouse에서는 데이터가 배포되고 각 배포가 분할되므로 파티션 수가 금방 증가합니다. 테이블에 너무 많은 파티션이 있으면 행 그룹을 채우기에 충분하지 않을 수 있습니다. 행이 부족하다고 해서 압축 중에 메모리 부족이 발생하지는 않지만 행 그룹에서 최적의 columnstore 쿼리 성능을 달성하지 못하게 됩니다.

오버 분할을 방지하는 다른 이유는 행을 분할된 테이블의 columnstore 인덱스에 로드하는 데 메모리 오버헤드가 있다는 점입니다. 로드하는 동안 많은 파티션이 들어오는 행을 수신할 수 있으며 각 파티션이 압축할 행을 충분히 포함할 때까지 행은 메모리에 보관됩니다. 너무 많은 파티션이 있으면 메모리 부족이 발생합니다.

### <a name="simplify-the-load-query"></a>로드 쿼리 간소화

데이터베이스는 쿼리에서 모든 연산자 간 쿼리에 대한 메모리 부여를 공유합니다. 로드 쿼리에 복잡한 정렬 및 조인이 있는 경우 압축에 사용 가능한 메모리가 줄어듭니다.

쿼리를 로드하는 데만 집중할 로드 쿼리를 디자인합니다. 데이터 변환을 실행해야 할 경우 로드 쿼리와 별도로 실행합니다. 예를 들어, 힙 테이블의 데이터를 준비하고 변환을 실행한 후 준비 테이블을 columnstore 인덱스에 로드합니다. 또한 데이터를 먼저 로드한 후 MPP 시스템을 사용하여 데이터를 변환할 수도 있습니다.

### <a name="adjust-maxdop"></a>MAXDOP 조정

각 배포에서는 배포당 사용 가능한 CPU 코어가 두 개 이상 있는 경우 행 그룹을 columnstore로 병렬로 압축합니다. 병렬 처리는 메모리 부족 및 행 그룹 트리밍을 야기할 수 있는 추가 메모리 리소스를 필요로 합니다.

메모리 부족을 줄이기 위해 MAXDOP 쿼리 힌트를 사용하여 로드 작업이 각 배포 내에서 직렬 모드로 강제로 실행되도록 할 수 있습니다.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>더 많은 메모리를 할당하는 방법

DWU 크기와 사용자 리소스 클래스를 함께 사용하여 사용자 쿼리에 사용 가능한 메모리 양을 결정합니다. 로드 쿼리에 대한 메모리 부여를 늘리려면 DWU 수를 늘리거나 리소스 클래스 수를 늘리면 됩니다.

- DWU 수를 늘리려면 [성능을 조정하려면 어떻게 해야 합니까?](sql-data-warehouse-manage-compute-overview.md#scale-compute)를 참조하세요.
- 쿼리에 대한 리소스 클래스를 변경하려면 [사용자 리소스 클래스 변경 예제](sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example)를 참조하세요.

예를 들어 DWU 100에서는 smallrc 리소스 클래스의 사용자는 배포당 100MB의 메모리를 사용할 수 있습니다. 자세한 내용은 [SQL Data Warehouse의 동시성](sql-data-warehouse-develop-concurrency.md)을 참조하세요.

고품질 행 그룹 크기를 가져오려면 700MB의 메모리가 필요하다고 판단된다고 가정해 보겠습니다. 다음 예제는 충분한 메모리로 로드 쿼리를 실행하는 방법을 보여 줍니다.

- DWU 1000 및 mediumrc를 사용하면 메모리 부여는 800MB입니다.
- DWU 600 및 largerc를 사용하면 메모리 부여는 800MB입니다.


## <a name="next-steps"></a>다음 단계

SQL Data Warehouse의 성능을 향상시키기 위해 여러 가지 방법을 찾으려면 [성능 개요](sql-data-warehouse-overview-manage-user-queries.md)를 참조하세요.

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

