---
title: Azure Data Factory를 사용 하 여 Azure SQL Database 테이블을 Azure CosmosDB로 마이그레이션
description: Azure SQL Database에서 기존의 정규화 된 데이터베이스 스키마를 가져와 Azure Data Factory를 사용 하 여 Azure CosmosDB 비 정규화 된 컨테이너로 마이그레이션합니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 3d2ef6fb0cd7af444b9bff755eee4eee70d03d15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82691892"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>정규화된 데이터베이스 스키마를 Azure SQL Database에서 Azure CosmosDB 비정규화 컨테이너로 마이그레이션

이 가이드에서는 Azure SQL Database에서 기존의 정규화 된 데이터베이스 스키마를 가져와서 azure CosmosDB로 로드 하기 위해 Azure CosmosDB 비 정규화 된 스키마로 변환 하는 방법을 설명 합니다.

일반적으로 SQL 스키마는 세 번째 일반 형태를 사용 하 여 모델링 되므로 높은 수준의 데이터 무결성을 제공 하는 정규화 된 스키마와 중복 된 데이터 값이 줄어듭니다. 쿼리는 테이블 간에 엔터티를 결합 하 여 읽을 수 있습니다. CosmosDB은 문서 내에 자체 포함 된 데이터를 사용 하 여 비 정규화 된 스키마를 통해 컬렉션이 나 컨테이너 내에서의 슈퍼 빠른 트랜잭션과 쿼리를 위해 최적화 되었습니다.

Azure Data Factory를 사용 하 여 단일 매핑 데이터 흐름을 사용 하는 파이프라인을 빌드하여 기본 키와 외래 키를 포함 하는 두 Azure SQL Database 개의 정규화 된 테이블을 엔터티 관계로 읽습니다. ADF는 데이터 흐름 Spark 엔진을 사용 하 여 이러한 테이블을 단일 스트림으로 조인 하 고, 조인 된 행을 배열로 수집 하 고, 새 Azure CosmosDB 컨테이너에 삽입할 개별 정리 된 문서를 생성 합니다.

이 가이드에서는 ```SalesOrderHeader``` ```SalesOrderDetail``` 표준 SQL Server AdventureWorks 예제 데이터베이스의 및 테이블을 사용 하는 "주문" 이라는 즉석에서 새 컨테이너를 작성 합니다. 이러한 테이블은에 의해 조인 된 판매 트랜잭션을 나타냅니다 ```SalesOrderID``` . 각 고유 정보 레코드에는의 고유한 기본 키가 있습니다 ```SalesOrderDetailID``` . 헤더와 세부 정보 간의 관계는 ```1:M``` 입니다. ```SalesOrderID```ADF에서 가입한 다음 각 관련 정보 레코드를 "detail" 이라는 배열로 롤포워드합니다.

이 가이드의 대표적인 SQL 쿼리는 다음과 같습니다.

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

결과 CosmosDB 컨테이너는 내부 쿼리를 단일 문서에 포함 하 고 다음과 같이 표시 됩니다.

![컬렉션](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **+ 새 파이프라인** 을 선택 하 여 새 파이프라인을 만듭니다.

2. 데이터 흐름 작업 추가

3. 데이터 흐름 작업에서 **새 매핑 데이터 흐름**을 선택 합니다.

4. 아래에서이 데이터 흐름 그래프를 구성 합니다.

![데이터 흐름 그래프](media/data-flow/cosmosb1.png)

5. "SourceOrderDetails"의 원본을 정의 합니다. 데이터 집합의 경우 테이블을 가리키는 새 Azure SQL Database 데이터 집합을 만듭니다 ```SalesOrderDetail``` .

6. "SourceOrderHeader"의 원본을 정의 합니다. 데이터 집합의 경우 테이블을 가리키는 새 Azure SQL Database 데이터 집합을 만듭니다 ```SalesOrderHeader``` .

7. 위쪽 원본에서 "SourceOrderDetails" 뒤에 파생 열 변환을 추가 합니다. 새 변환 "형식 캐스팅"를 호출 합니다. 열을 반올림 하 ```UnitPrice``` 고 CosmosDB에 대 한 double 데이터 형식으로 캐스팅 해야 합니다. 수식을로 설정 합니다. ```toDouble(round(UnitPrice,2))```

8. 다른 파생 열을 추가 하 고 "MakeStruct"로 호출 합니다. 여기서는 세부 정보 테이블의 값을 포함 하는 계층 구조를 만들게 됩니다. 세부 정보는 헤더에 대 한 관계를 염두에 두어야 ```M:1``` 합니다. 새 구조의 이름을 ```orderdetailsstruct``` 로 설정 하 고 이러한 방식으로 계층 구조를 만들어 각 하위 열을 들어오는 열 이름으로 설정 합니다.

![Create Structure](media/data-flow/cosmosb9.png)

9. 이제 sales 헤더 원본으로 이동 하겠습니다. 조인 변환을 추가 합니다. 오른쪽에서 "MakeStruct"를 선택 합니다. 내부 조인으로 설정 된 채로 두고 ```SalesOrderID``` 조인 조건의 양쪽에 대해 선택 합니다.

10. 이 시점까지 결과를 볼 수 있도록 추가한 새 조인에서 데이터 미리 보기 탭을 클릭 합니다. 정보 행과 조인 된 모든 머리글 행이 표시 되어야 합니다. 에서 형성 된 조인의 결과입니다 ```SalesOrderID``` . 다음으로 일반 행의 세부 정보를 세부 정보 구조체로 결합 하 고 공통 행을 집계 합니다.

![Join](media/data-flow/cosmosb4.png)

11. 이러한 행을 비 정규화 배열을 만들려면 먼저 불필요 한 열을 제거 하 고 데이터 값이 CosmosDB 데이터 형식과 일치 하는지 확인 해야 합니다.

12. 다음 Select 변환을 추가 하 고 필드 매핑을 다음과 같이 설정 합니다.

![열 스크러버](media/data-flow/cosmosb5.png)

13. 이제 통화 열을 다시 캐스팅 하겠습니다 ```TotalDue``` . 7 단계에서 설명한 것과 같이 수식을로 설정 ```toDouble(round(TotalDue,2))``` 합니다.

14. 다음은 공통 키를 기준으로 그룹화 하 여 행을 비 정규화는 위치 ```SalesOrderID``` 입니다. 집계 변환을 추가 하 고 group by를로 설정 ```SalesOrderID``` 합니다.

15. 집계 수식에서 "details" 라는 새 열을 추가 하 고이 수식을 사용 하 여 앞에서 만든 구조에서 값을 수집 ```orderdetailsstruct``` ```collect(orderdetailsstruct)``` 합니다.

16. 집계 변환은 집계 또는 group by 수식의 일부인 열만 출력 합니다. 따라서 sales 헤더의 열도 포함 해야 합니다. 이렇게 하려면 동일한 집계 변환에 열 패턴을 추가 합니다. 이 패턴은 출력의 다른 모든 열을 포함 합니다.

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. 동일한 열 이름을 유지 하 고 함수를 집계로 사용 하도록 다른 속성의 "this" 구문을 사용 합니다 ```first()``` .

![집계](media/data-flow/cosmosb6.png)

18. 싱크 변환을 추가 하 여 마이그레이션 흐름을 완료할 준비가 되었습니다. 데이터 집합 옆에 있는 "새로 만들기"를 클릭 하 고 CosmosDB 데이터베이스를 가리키는 CosmosDB 데이터 집합을 추가 합니다. 컬렉션의 경우 "orders" 라고 하며,이는 즉석에서 만들어지기 때문에 스키마를 포함 하지 않고 문서를 포함 하지 않습니다.

19. 싱크 설정에서 키를로 분할 하 ```\SalesOrderID``` 고 컬렉션 작업을 "다시 만들기"로 설정 합니다. 매핑 탭이 다음과 같이 표시 되는지 확인 합니다.

![싱크 설정](media/data-flow/cosmosb7.png)

20. 데이터 미리 보기를 클릭 하 여 이러한 32 행이 새 컨테이너에 새 문서로 삽입 되도록 설정 되어 있는지 확인 합니다.

![싱크 설정](media/data-flow/cosmosb8.png)

모든 항목이 양호 하면 이제 새 파이프라인을 만들고,이 데이터 흐름 활동을 파이프라인에 추가 하 고, 실행할 준비가 된 것입니다. 디버그 또는 트리거된 실행에서 실행할 수 있습니다. 몇 분 후에는 CosmosDB 데이터베이스에 "orders" 라는 이름의 정규화 되지 않은 새 컨테이너가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md)매핑을 사용 하 여 나머지 데이터 흐름 논리를 작성 합니다.
* 이 자습서에 대해 [완료 된 파이프라인 템플릿을 다운로드](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) 하 고 해당 템플릿을 팩터리에 가져옵니다.
