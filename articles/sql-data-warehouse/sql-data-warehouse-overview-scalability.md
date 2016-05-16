<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 성능 확장성 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 성능 확장 기능을 이해합니다. 또는 DWU를 조정하거나 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 성능 확장성

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-scalability.md)
- [포털](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

SQL 데이터 웨어하우스는 저장소와 계산을 분리하여 각각의 성능을 독립적으로 조정할 수 있습니다. 따라서 성능을 확장하는 동시에 필요한 성능에 대해서만 지불하여 비용을 절감할 수 있습니다.

이 개요는 SQL 데이터 웨어하우스에 대한 다음과 같은 성능 확장 기능에 대해 설명하고 사용 방법 및 시기에 대한 권장 사항을 제공합니다.

- 데이터 웨어하우스 단위(DWU)를 조정하여 성능 조정
- 계산 리소스 일시 중지 또는 다시 시작

<a name="scale-performance-bk"></a>

## 성능 조정

SQL 데이터 웨어하우스에서 CPU의 계산 리소스, 메모리, I/O 대역폭을 늘리거나 줄여 성능을 빠르게 확장 또는 축소할 수 있습니다. 성능을 조정할 경우 SQL 데이터 웨어하우스에서 데이터베이스에 할당하는 DWU(데이터 웨어하우스 단위) 수를 조정하기만 하면 됩니다. SQL 데이터 웨어하우스가 빠르게 변경하고 하드웨어 또는 소프트웨어의 모든 기본 변경 사항을 처리합니다.

>[AZURE.NOTE] 데이터 웨어하우스의 성능을 높이기 위해 어떤 유형의 프로세서, 얼마나 많은 메모리, 어떤 유형의 저장소가 필요한지 조사해야 했던 시절은 지나갔습니다. 클라우드에 데이터 웨어하우스를 배치함으로써 더 이상 하위 수준의 하드웨어 문제를 처리하지 않아도 됩니다. 대신 SQL 데이터 웨어하우스는 다음과 같은 질문을 합니다. 데이터를 얼마나 빨리 분석하고 싶나요?

### DWU란?

*DWU(데이터 웨어하우스 단위)*는 특정 시간에 데이터베이스의 기본 계산 기능을 측정하는 단위입니다. DWU 수가 증가하면 SQL 데이터 웨어하우스가 더 많이 배포된 CPU 및 메모리 리소스를 통해 작업을 병렬로 실행합니다(예: 데이터 로드 또는 쿼리). 대기 시간이 줄어들고 성능이 향상됩니다.

DWU는 로드 및 검색 속도를 기준으로 합니다. DWU를 증가할수록 로드 속도와 검사 속도가 향상됩니다.

- **로드 속도**. SQL 데이터 웨어하우스가 초당 읽어드릴 수 있는 기록의 수입니다. 특히 SQL 데이터 웨어하우스가 PolyBase를 사용하여 Azure Blob 저장소에서 클러스터된 columnstore 인덱스로 가져올 수 있는 기록의 수를 나타냅니다. 

- **검색 속도**. 쿼리가 SQL 데이터 웨어하우스에서 검색할 수 있는 초당 기록의 수입니다. 특히 SQL 데이터 웨어하우스가 클러스터된 columnstore 인덱스에서 데이터 웨어하우징 쿼리를 실행하여 반환할 수 있는 기록의 수를 나타냅니다. 검색 속도를 테스트할 경우 많은 수의 행을 검색한 다음 복잡한 집계를 수행하는 표준 데이터 웨어하우징 쿼리를 사용합니다. 이 작업은 많은 IO와 CPU를 사용합니다.

>[AZURE.NOTE] Microsoft는 중요한 성능 개선 사항을 측정하고 있으며 곧 예상 속도를 공유할 예정입니다. 미리 보기 중에 이러한 비율을 높이고 예측 가능하게 확장할 수 있도록 지속적인 개선에 힘쓰겠습니다(예: 압축 및 캐싱 증가).

DWU 목록을 보려면 [용량 제한][] 문서에서 서비스 수준 목표를 참조하세요.

### 성능을 조정하려면 어떻게 해야 합니까?

계산 성능을 탄력적으로 증가 또는 감소하려면 데이터베이스의 DWU(데이터 웨어하우스 단위)를 변경하기만 하면 됩니다. SQL 데이터 웨어하우스가 SQL 데이터베이스의 빠르고 간단한 배포 역량을 사용하여 CPU와 메모리 할당을 변경합니다.

DWU는 100개 단위로 할당되지만 모든 블록을 사용할 수 있는 것은 아닙니다. DWU가 성능을 선형적으로 증가합니다. 높은 DWU 수준에서 성능이 크게 향상되었음을 감지하려면 DWU를 100개 이상 추가해야 합니다. 의미 있는 DWU 증가분을 선택할 때에는 최고의 결과를 제공하는 DWU 수준을 참조할 수 있습니다.
 
DWU를 조정할 경우 다음과 같은 개별 방법을 사용할 수 있습니다.

- [Azure 포털로 성능 조정][]
- [PowerShell로 성능 조정][]
- [REST API로 성능 조정][]
- [TSQL로 성능 조정][]

### 얼마나 많은 DWU를 사용해야 합니까?
 
SQL 데이터 웨어하우스의 성능은 선형적으로 조정되며 하나의 계산 규모에서 다른 규모로의 변경은(예를 들어 100DWU에서 2000DWU로) 몇 초 안에 실행됩니다. 따라서 시나리오에 가장 적합한 설정을 결정할 때까지 다양한 DWU 설정을 유연하게 실험해볼 수 있습니다.

> [AZURE.NOTE] 데이터 볼륨이 적을 때에는 예상한 성능 확장이 나타나지 않을 수 있습니다. 정확한 성능 테스트 결과를 얻으려면 데이터 볼륨을 1TB 이상으로 시작하는 것이 좋습니다.

워크로드에 가장 적합한 DWU를 찾는 방법에 대한 권장 사항:

1. 개발 중인 데이터 웨어하우스의 경우 가장 먼저 적은 수의 DWU를 선택합니다.
2. 응용 프로그램 성능을 모니터링하여 선택한 DWU 수와 관찰한 성능을 비교합니다.
3. 선형 크기 조정을 가정하여 요구 사항에 맞는 최적 성능 수준을 달성하기 위해 성능이 얼마나 더 빠르거나 느려야 하는지 확인합니다.
4. 원하는 워크로드 성능에 비례하여 DWU 수를 늘리거나 줄입니다. 그러면 서비스가 신속하게 응답하고 새로운 DWU 요구 사항에 맞게 계산 리소스를 조정합니다.
5. 비즈니스 요구 사항에 맞는 최적 성능 수준에 도달할 때까지 계속 조정합니다.

### 언제 DWU를 조정해야 하나요?

전반적으로 DWU를 간단하게 만드는 것이 좋습니다. 결과가 빨리 필요하면, DWU를 높이고 더 높은 성능에 대한 비용을 지불합니다. 계산 파워가 덜 필요하면, DWU를 낮추고 필요한 것에만 비용을 지불합니다.

DWU 성능 조정 시기에 대한 권장 사항:

1. 응용 프로그램 워크로드에 변동이 많은 경우 최대치와 최저치에 맞게 DWU 수준을 늘리거나 줄입니다. 예를 들어 일반적으로 월말에 워크로드가 최대치에 도달하는 경우 이 시기에 DWU를 추가하고 이 시기가 끝나면 다시 축소하는 계획을 세웁니다.
1. 대량의 데이터 로딩 또는 변환 작업을 수행하기 전에 데이터를 더욱 빠르게 사용할 수 있도록 DWU를 확장합니다.

<a name="pause-compute-bk"></a>

## 계산 일시 중지

[AZURE.INCLUDE [SQL 데이터 웨어하우스 일시 중지 설명](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 다음과 같은 개별 방법을 사용합니다.

- [Azure 포털을 사용하여 계산 일시 중지][]
- [PowerShell을 사용하여 계산 일시 중지][]
- [REST API를 사용하여 계산 일시 중지][]

<a name="resume-compute-bk"></a>

## 계산 다시 시작

[AZURE.INCLUDE [SQL 데이터 웨어하우스 다시 시작 설명](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 다시 시작하려면 다음과 같은 개별 방법을 사용합니다.

- [Azure 포털을 사용하여 계산 다시 시작][]
- [PowerShell을 사용하여 계산 다시 시작][]
- [REST API를 사용하여 계산 다시 시작][]

<a name="next-steps-bk"></a>

## 다음 단계
일부 추가적인 주요 성능 및 확장성 개념을 이해하는 데 도움이 필요하다면 다음 문서들을 참조하십시오.

- [동시성 모델][]
- [테이블 디자인][]
- [테이블에 대한 해시 배포 키를 선택합니다.][]
- [성능 향상을 위해 통계][]

<!--Image reference-->

<!--Article references-->

[Azure 포털로 성능 조정]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-1-scale-performance
[PowerShell로 성능 조정]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-1-scale-performance
[REST API로 성능 조정]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-1-scale-performance
[TSQL로 성능 조정]: ./sql-data-warehouse-manage-scale-out-tasks-tsql.md

[용량 제한]: ./sql-data-warehouse-service-capacity-limits.md

[Azure 포털을 사용하여 계산 일시 중지]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-2-pause-compute
[PowerShell을 사용하여 계산 일시 중지]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-2-pause-compute
[REST API를 사용하여 계산 일시 중지]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-2-pause-compute
[Azure 포털을 사용하여 계산 다시 시작]: ./sql-data-warehouse-manage-scale-out-tasks.md#task-3-resume-compute
[PowerShell을 사용하여 계산 다시 시작]: ./sql-data-warehouse-manage-scale-out-tasks-powershell.md#task-3-resume-compute
[REST API를 사용하여 계산 다시 시작]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md#task-3-resume-compute

[동시성 모델]: sql-data-warehouse-develop-concurrency.md
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[테이블에 대한 해시 배포 키를 선택합니다.]: sql-data-warehouse-develop-hash-distribution-key.md
[성능 향상을 위해 통계]: sql-data-warehouse-develop-statistics.md
[development overview]: sql-data-warehouse-overview-develop.md



<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->