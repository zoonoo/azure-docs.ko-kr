<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 테이블 및 인덱스 관리 | Microsoft Azure"
   description="고려 사항, 모범 사례 및 Azure SQL 데이터 웨어하우스의 테이블 및 인덱스 관리를 위한 작업 개요입니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 테이블 및 인덱스 관리

고려 사항, 모범 사례 및 SQL 데이터 웨어하우스의 테이블 및 인덱스 관리를 위한 작업 개요입니다.



| Category | 작업 또는 고려 사항 | 설명 |
| :-----------------------| :---------------------------------------------- | :----------- |
| Columnstore 인덱스 | 데이터 로드 또는 삽입 후 인덱스 다시 빌드 | 기본적으로 SQL 데이터 웨어하우스는 클러스터형 columnstore 인덱스로 각 테이블을 저장합니다. 이는 특히 대형 테이블에 대해 더 나은 성능 및 데이터 압축을 제공합니다. columnstore 인덱스로 데이터를 수집하는 방법에 따라 모든 데이터가 columnstore 압축으로 저장되지 않을 수 있습니다. 이 경우 columnstore 인덱스에서 제공하도록 디자인된 성능을 얻지 못할 수 있습니다. <br/><br/>columnstore 인덱스가 정상 상태에 있는지 확인하려면 [columnstore 인덱스 관리][]를 참조하세요. |
| 해시 분산 테이블 | 데이터가 노드에 균일하게 분산되어 있는지 확인 | 해시 분산 테이블은 거의 대부분 대형 테이블의 조인 및 집계를 최적화하는 가장 좋은 방법입니다. SQL 데이터 웨어하우스는 지정된 분산 열을 기준으로 테이블을 분산시켰습니다. 일부 열은 좋은 분산 키이며 일부는 그렇지 않습니다. 행을 균일하게 분산시키는 것이 좋습니다. 어느 정도의 불균일 또는 데이터 기울이기는 괜찮지만 데이터 기울이기가 너무 클 경우 SQL 데이터 웨어하우스에서 제공하도록 디자인된 MMP(대규모 병렬 처리)의 이점을 누릴 수 없게 됩니다.<br/><br/>해시 분산 테이블에 너무 많은 데이터 기울이기가 없는지 확인하려면 [분산 데이터 기울이기 관리][]를 참조하세요. |





## 다음 단계

관리에 대한 추가 팁을 보려면 [관리][] 개요를 참조하세요.

<!--Image references-->

<!--Article references-->
[columnstore 인덱스 관리]: sql-data-warehouse-manage-columnstore-indexes.md
[분산 데이터 기울이기 관리]: sql-data-warehouse-manage-distributed-data-skew.md
[관리]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->