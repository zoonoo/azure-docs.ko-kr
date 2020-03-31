---
title: 릴리스 정보
description: Azure 시냅스 분석을 위한 릴리스 정보입니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 3/26/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6d04756bfba965e49a8b51457d2c679eb7bbfd13
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350766"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure 시냅스 분석 릴리스 정보

이 문서에서는 Azure Synapse의 [SQL 분석의](sql-data-warehouse-overview-what-is.md) 최근 릴리스에서 새로운 기능과 개선 사항에 대해 간략히 요약합니다. 이 문서에는 릴리스와 직접적인 관련이 없지만 동일한 기간 내에 게시된 주목할 만한 콘텐츠 업데이트도 나열되어 있습니다. 다른 Azure 서비스에 대한 개선 사항은 [서비스 업데이트를](https://azure.microsoft.com/updates)참조하십시오.

## <a name="check-your-azure-synapse-version"></a>Azure 시냅스 버전 확인

모든 지역에 새로운 기능이 출시되면 인스턴스에 배포된 버전과 최신 릴리스 정보에서 기능 가용성을 확인하십시오. 버전을 확인하려면 SQL 서버 관리 스튜디오(SSMS)를 통해 SQL `SELECT @@VERSION;` 풀에 연결하고 실행하여 현재 버전을 반환합니다.

식별된 버전을 사용하여 SQL 풀에 적용된 릴리스를 확인합니다. 출력의 날짜는 SQL 풀에 적용된 릴리스의 월을 식별합니다.

> [!NOTE]
> SELECT @에서@VERSION 반환되는 제품 이름은 Microsoft Azure SQL 데이터 웨어하우스에서 Azure 시냅스 분석으로 변경됩니다. 변경되기 전에 사전 통지를 보내드립니다. 이 변경 사항은 응용 프로그램 코드에서 SELECT @@VERSION 의 결과에서 제품 이름을 구문 분석하는 고객에게 적합합니다. 제품 브랜딩으로 인한 응용 프로그램 코드 변경을 방지하려면 다음 명령을 사용하여 데이터베이스 제품 이름 및 버전: 버전 번호 XX를 반환하려면 SERVERPROPERTY를 쿼리하십시오. X.XXXXX. X(제품 이름 제외)는 다음 명령을 사용합니다. 
> ```
> SELECT SERVERPROPERTY('ProductVersion')
> ```
> 엔진 에디션을 반환하려면 Azure Synapse 분석(이전 SQL 데이터 웨어하우스)에 대해 6을 반환하는 이 명령을 사용합니다. 
> ```
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="march-2020"></a>2020년 3월

| 서비스 개선 사항                                         | 세부 정보                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[비주얼 스튜디오 16.6 미리 보기 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview) - SQL 서버 데이터 도구(SSDT)** | 이 릴리스에는 다음과 같은 개선 사항 및 수정 사항이 포함되어 있습니다. </br> </br> - MV(구체화된 뷰)에서 참조하는 테이블을 변경하면 MV에서 지원되지 않는 Alter View 문이 생성되는 문제를 해결했습니다.<br/><br/> - 데이터베이스 또는 프로젝트에 행 수준 보안 개체가 있을 때 스키마 비교 작업이 실패하지 않도록 변경 사항을 구현했습니다. 행 수준 보안 개체는 현재 SSDT에서 지원되지 않습니다.  <br/><br/> - SQL Server 개체 탐색기 시간 초과 임계값이 증가하여 데이터베이스에 많은 수의 개체를 나열할 때 시간 초과를 방지합니다.<br/><br/> - SQL Server 개체 탐색기가 데이터베이스 개체 목록을 검색하는 방식을 최적화하여 개체 탐색기를 채울 때 불안정을 줄이고 성능을 향상시키십시오. |

## <a name="february-2020"></a>2020년 2월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터베이스 호환성 수준(미리 보기)**| 이 릴리스를 통해 사용자는 데이터베이스의 호환성 수준을 설정하여 특정 버전의 SQL 분석 엔진의 Transact-SQL 언어 및 쿼리 처리 동작을 얻을 수 있습니다. 자세한 내용은 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?view=azure-sqldw-latest&branch=pr-en-us-13797) 및 [데이터베이스 범위 구성 변경](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?view=sql-server-ver15)을 참조하십시오. |
|**Sp_describe_undeclared_parameters**| 사용자가 Transact-SQL 일괄 처리에서 선언되지 않은 매개 변수에 대한 메타데이터를 볼 수 있도록 허용합니다. 자세한 내용은 [sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?view=sql-server-ver15)를 참조하십시오.|

## <a name="january-2020"></a>2020년 1월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**워크로드 관리 포털 메트릭(미리 보기)**|지난 10월 미리 보기용 [워크로드 격리가](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation) 릴리스되면 사용자는 자체 [워크로드 그룹을](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 만들어 시스템 리소스를 효율적으로 관리하고 비즈니스 SLA를 충족할 수 있습니다.  Azure Synapse Analytics의 전반적인 [워크로드 관리](/azure/sql-data-warehouse/sql-data-warehouse-workload-management) 개선 사항의 일부로 새로운 [워크로드 관리 모니터링 메트릭을](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor) 사용할 수 있습니다.</br> </br> 이제 워크로드를 모니터링하면 다음 메트릭을 통해 더 많은 통찰력을 얻을 수 있습니다. </br> - 유효 캡 자원 퍼센트  </br> - 유효 최소 자원 퍼센트 </br> - 워크로드 그룹 활성 쿼리 </br> - 최대 리소스 %에 의한 워크로드 그룹 할당 </br> - 시스템 퍼센트별 워크로드 그룹 할당 </br> - 워크로드 그룹 쿼리 시간 시간 </br> - 워크로드 그룹 큐에 대기된 쿼리 </br></br> 이러한 메트릭을 사용하여 [사용률이 낮은 워크로드 격리로](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#underutilized-workload-isolation)구성된 [워크로드 그룹 병목 현상](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#workload-group-bottleneck) 또는 워크로드 그룹을 식별합니다.  이러한 메트릭은 워크로드 그룹별로 분할할 수 있는 Azure 포털에서 사용할 수 있습니다.  즐겨찾는 그래프를 대시보드에 필터링하고 고정하여 인사이트에 빠르게 액세스할 수 있습니다.|
|**포털 모니터링 메트릭**| 전체 쿼리 활동을 모니터링하기 위해 다음 메트릭이 포털에 추가되었습니다. </br> - 활성 쿼리 </br> - 큐에 대기된 쿼리 </br> </br>이러한 메트릭은 [모니터링 리소스 사용률 및 쿼리 활동 설명서의](/azure/sql-data-warehouse/sql-data-warehouse-concept-resource-utilization-query-activity)기존 메트릭과 함께 설명됩니다.|


## <a name="october-2019"></a>2019년 10월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**복사(미리 보기)**|데이터 수집을 위한 간단하고 유연한 COPY 문의 공개 미리 보기를 발표하게 되어 기쁩니다. 하나의 문만 사용하면 권한이 높은 사용자가 필요 없이 추가적인 유연성으로 데이터를 원활하게 수집할 수 있습니다. 자세한 내용은 [COPY 명령 설명서를](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)참조하십시오.|
|**워크로드 격리(미리 보기)**|고객이 데이터 웨어하우스를 민주화할 때 고객을 지원하기 위해 지능형 워크로드 관리를 위한 새로운 기능을 발표합니다. 새로운 [워크로드 격리](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation) 기능을 사용하면 이기종 워크로드의 실행을 관리하는 동시에 데이터 웨어하우스 리소스에 대한 유연성과 제어 기능을 제공할 수 있습니다. 이를 통해 실행 예측 가능성이 향상되고 미리 정의된 SLA를 충족하는 기능이 향상됩니다. </br>워크로드 격리 외에도 [워크로드 분류에](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)대한 추가 옵션을 사용할 수 있습니다.  로그인 분류 외에도 [워크로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 구문은 쿼리 레이블, 세션 컨텍스트 및 시간 등을 기반으로 요청을 분류하는 기능을 제공합니다.|
|**예측(미리 보기)**|이제 크고 복잡한 데이터 이동을 피할 수 있는 데이터 웨어하우스 내에서 기계 학습 모델을 채점할 수 있습니다. T-SQL PREDICT 함수는 개방형 모델 프레임워크에 의존하며 데이터 및 기계 학습 모델을 입력으로 사용하여 예측을 생성합니다.
|**SSDT CI/CD(조지아 주)**|오늘 우리는 SQL 분석 - SQL 서버 데이터 도구 (SSDT) 데이터베이스 프로젝트에 대한 [가장 많이 요청된 기능의](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t) 일반 가용성을 발표하게되어 기쁘게 생각합니다. 이 릴리스에는 Visual Studio 2019를 지원하는 SSDT지원과 엔터프라이즈 수준 배포를 위한 CI/CD(기본 제공 지속적인 통합 및 배포) 기능을 제공하는 Azure DevOps와의 기본 플랫폼 통합이 포함됩니다. |
|**구체화된 뷰(GA)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 [구체화된 뷰를 사용하여 성능 튜닝을](/azure/sql-data-warehouse/performance-tuning-materialized-views)참조하십시오.  구체화된 뷰를 스크립팅하려면 [SQL Server 관리 Studio 18.4 이상설치합니다.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)|
|**동적 데이터 마스킹(GA)**|DDM(동적 데이터 마스킹)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 즉시 난독 처리하여 데이터 웨어하우스의 중요한 데이터에 대한 무단 액세스를 방지합니다.자세한 내용은 [SQL Database 동적 데이터 마스킹](/azure/sql-database/sql-database-dynamic-data-masking-get-started)을 참조하십시오.|
|**커밋된 스냅샷 격리 읽기(GA)**|ALTER DATABSE를 사용하여 사용자 데이터베이스에 대한 스냅샷 격리를 사용하거나 비활성화할 수 있습니다. 현재 워크로드에 영향을 미치지 않으려면 데이터베이스 유지 관리 기간 동안 이 옵션을 설정하거나 데이터베이스에 대한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [데이터베이스 집합 변경 옵션을](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)참조하십시오.|
|**정렬된 클러스터된 열 저장소 인덱스(GA)**|Columnstore는 많은 양의 데이터를 저장하고 효율적으로 쿼리하는 핵심 지원자입니다. 정렬된 클러스터된 columnstore 인덱스는 효율적인 세그먼트 제거를 통해 쿼리 실행을 더욱 최적화합니다.자세한 내용은 [정렬된 클러스터된 columnstore 인덱스를 통해 성능 조정을](/azure/sql-data-warehouse/performance-tuning-ordered-cci)참조하십시오.|
|**결과 세트 캐싱(GA)**|결과 집합 캐싱을 사용하도록 설정하면 SQL Analytics는 반복적으로 사용하기 위해 사용자 데이터베이스의 쿼리 결과를 자동으로 캐시합니다. 이렇게 하면 후속 쿼리 실행이 지속된 캐시에서 직접 결과를 얻을 수 있으므로 다시 계산할 필요가 없습니다. 결과 집합 캐싱은 쿼리 성능을 향상시키고 계산 리소스 사용량을 줄입니다. 또한 캐시된 결과 집합을 사용하는 쿼리는 동시성 슬롯을 사용하지 않으므로 기존 동시성 제한에 포함되지 않습니다. 보안을 위해 사용자는 캐시된 결과를 만드는 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시된 결과에 액세스할 수 있습니다. 자세한 내용은 [결과 집합 캐싱을 통해 성능 튜닝을](/azure/sql-data-warehouse/performance-tuning-result-set-caching)참조하십시오. 버전 10.0.10783.0 이상에 적용됩니다.|

## <a name="september-2019"></a>2019년 9월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Azure 개인 링크(미리 보기)**|[Azure 개인 링크를](https://azure.microsoft.com/blog/announcing-azure-private-link/)사용하면 가상 네트워크(VNet)에서 개인 끝점을 만들고 SQL 풀에 매핑할 수 있습니다. 그런 다음 이러한 리소스는 VNet의 개인 IP 주소를 통해 액세스할 수 있으므로 Azure ExpressRoute 개인 피어링 및/또는 VPN 게이트웨이를 통해 온-프레미스에서 연결을 사용할 수 있습니다. 전반적으로 공용 IP 주소로 열지 않아도 네트워크 구성이 단순화됩니다. 또한 데이터 유출 위험에 대한 보호를 가능하게 합니다. 자세한 내용은 [개요](/azure/private-link/private-link-overview) 및 [SQL 분석 설명서를](/azure/sql-database/sql-database-private-endpoint-overview)참조하십시오.|
|**데이터 검색 & 분류(GA)**|[이제 데이터 검색 및 분류](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/sql-data-warehouse/toc.json) 기능을 사용할 수 있습니다. 이 기능은 데이터베이스의 중요한 데이터를 보호하기 & **검색, 분류, 레이블 지정을** 위한 고급 기능을 제공합니다.|
|**Azure 어드바이저 원클릭 통합**|이제 Azure Synapse의 SQL Analytics는 클릭 한 번으로 환경을 제공하는 것과 함께 개요 블레이드의 Azure Advisor 권장 사항과 직접 통합됩니다. 이제 Azure 어드바이저 블레이드로 이동하지 않고 개요 블레이드에서 권장 사항을 찾을 수 있습니다. [여기에서](sql-data-warehouse-concept-recommendations.md)권장 사항에 대해 자세히 알아보십시오.|
|**커밋된 스냅숏 격리 읽기(미리 보기)**|ALTER DATABSE를 사용하여 사용자 데이터베이스에 대한 스냅샷 격리를 사용하거나 비활성화할 수 있습니다.  현재 워크로드에 영향을 미치지 않으려면 데이터베이스 유지 관리 기간 동안 이 옵션을 설정하거나 데이터베이스에 대한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [데이터베이스 집합 변경 옵션을](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)참조하십시오.|
|**EXECUTE AS(Transact-SQL)**| [로 실행](/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest) 이제 SQL Analytics에서 T-SQL 지원을 사용할 수 있으므로 고객은 세션의 실행 컨텍스트를 지정된 사용자로 설정할 수 있습니다.|
|**추가 T-SQL 지원**|SQL 분석에 대한 T-SQL 언어 영역이 확장되어 다음에 대한 지원이 포함됩니다. </br> - [형식(거래-SQL)](/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE(SQL 거래)](/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST(SQL 거래)](/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT(거래-SQL)](/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [sys.user_token (거래 SQL)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019년 7월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**구체화된 뷰(미리 보기)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 다음을 참조하세요. </br> - [선택 &#40;거래 SQL&#41;구체화된 뷰 만들기](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [트랜스액트 SQL&#41;&#40;구체화된 뷰 변경](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [SQL 분석에서 지원되는 T-SQL 문](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**추가 T-SQL 지원**|SQL 분석에 대한 T-SQL 언어 영역이 확장되어 다음에 대한 지원이 포함됩니다. </br> - [표준 시간대(거래-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG(SQL 거래)](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**결과 세트 캐싱(미리 보기)**|DBCC 명령은 이전에 발표된 결과 집합 캐시를 관리하기 위해 추가되었습니다. 자세한 내용은 다음을 참조하세요. </br> - [DBCC 드롭결과셋캐시 &#40;거래-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC 쇼결과캐시스페이스사용 &#40;거래-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 또한 실행된 쿼리가 결과 집합 캐시를 사용한 시기를 보여 주는 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 새 result_set_cache 열을 참조하십시오.|
|**정렬된 클러스터된 열 저장소 인덱스(미리 보기)**|[sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest) 새 열column_store_order_ordinal 추가되어 정렬된 클러스터된 열 저장소 인덱스에서 열의 순서를 식별합니다.|

## <a name="may-2019"></a>2019년 5월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**동적 데이터 마스킹(미리 보기)**|DDM(동적 데이터 마스킹)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 즉시 난독 처리하여 데이터 웨어하우스의 중요한 데이터에 대한 무단 액세스를 방지합니다.자세한 내용은 [SQL Database 동적 데이터 마스킹](/azure/sql-database/sql-database-dynamic-data-masking-get-started)을 참조하십시오.|
|**워크로드 중요도 는 이제 일반적으로 사용 가능**|워크로드 관리 분류 및 중요도는 쿼리실행 순서에 영향을 줄 수 있는 기능을 제공합니다. 워크로드 중요도에 대한 자세한 내용은 설명서의 [분류](sql-data-warehouse-workload-classification.md) 및 [중요도](sql-data-warehouse-workload-importance.md) 개요 문서를 참조하십시오. [워크로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 문서도 확인하십시오.<br/><br/>아래 비디오에서 워크로드 중요도를 확인하세요.<br/> -[워크로드 관리 개념](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[워크로드 관리 시나리오](https://www.youtube.com/embed/_2rLMljOjw8)|
|**추가 T-SQL 지원**|SQL 분석에 대한 T-SQL 언어 영역이 확장되어 다음에 대한 지원이 포함됩니다. </br> - [트림](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 기능**|이제 비즈니스 분석가는 익숙한 T-SQL 언어를 사용하여 다음과 같은 새로운 JSON 함수를 사용하여 JSON 데이터로 포맷된 문서를 쿼리하고 조작할 수 있습니다.</br> - [이시슨 (미국)](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [오픈제이슨](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**결과 세트 캐싱(미리 보기)**|결과 집합 캐싱을 사용하면 즉각적인 쿼리 응답 시간을 유지하면서 비즈니스 분석가 및 보고 사용자에 대한 인사이트 시간을 단축할 수 있습니다. 자세한 내용은 다음을 참조하세요.</br> - [데이터베이스 변경(거래-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [데이터베이스 세트 옵션 변경(SQL 거래)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [결과 집합 캐칭(거래-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 문(거래-SQL)](/sql/t-sql/statements/set-statements-transact-sql)</br> - [sys.데이터베이스(거래-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**정렬된 클러스터된 열 저장소 인덱스(미리 보기)**|Columnstore는 많은 양의 데이터를 저장하고 효율적으로 쿼리하는 핵심 지원자입니다. 각 테이블에 대해 들어오는 데이터를 행 그룹으로 나누고 행 그룹의 각 열은 디스크의 세그먼트를 형성합니다.  정렬된 클러스터된 columnstore 인덱스는 효율적인 세그먼트 제거를 통해 쿼리 실행을 더욱 최적화합니다.자세한 내용은 다음을 참조하세요.</br> -  [테이블 만들기(SQL 분석)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [COLUMNSTORE 인덱스(거래-SQL)를 만듭니다.](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)|

## <a name="march-2019"></a>2019년 3월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 검색 & 분류**|데이터 검색 & 분류는 이제 SQL 분석에 대한 공개 미리 보기에서 사용할 수 있습니다. 중요한 데이터와 고객의 개인 정보를 보호하는 것이 중요합니다. 비즈니스 및 고객 데이터 자산이 증가함에 따라 데이터를 검색, 분류 및 보호할 수 없게 됩니다. SQL Analytics를 통해 기본적으로 도입하는 데이터 검색 및 분류 기능은 데이터 보호를 보다 쉽게 관리할 수 있도록 도와줍니다. 이 기능의 전반적인 이점은 다음과 같습니다.<br/>&bull;&nbsp; 데이터 개인 정보 보호 표준 및 규정 준수 요구 사항 충족<br/>&bull;&nbsp; 매우 중요한 데이터를 포함하는 데이터 웨어하우스에 대한 액세스를 제한하고 보안을 강화합니다.<br/>&bull;&nbsp; 중요한 데이터에 대한 비정상적인 액세스를 모니터링하고 경고합니다.<br/>&bull;&nbsp; Azure 포털의 중앙 대시보드에서 중요한 데이터의 시각화 </br></br>데이터 검색 & 분류는 모든 Azure 지역에서 SQL Analytics에 사용할 수 있으며 취약점 평가 및 위협 감지를 비롯한 고급 데이터 보안의 일부입니다. 데이터 검색 & 분류에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/) 및 온라인 [설명서를](/azure/sql-database/sql-database-data-discovery-and-classification)참조하십시오.|
|**GROUP BY ROLLUP**|롤업은 이제 Azure 시냅스의 SQL 분석에서 지원되는 그룹 별 옵션입니다.   그룹 BY 롤업은 열 식의 각 조합에 대한 그룹을 만듭니다. 그룹 BY는 또한 결과를 소계 및 총계로 "롤업"합니다. GROUP BY 함수는 오른쪽에서 왼쪽으로 처리하여 그룹 및 집계를 만드는 열 표현식의 수를 줄입니다.  열 순서는 ROLLUP 출력에 영향을 주며, 결과 집합의 행 수에도 영향을 줄 수 있습니다.<br/><br/>롤업별 그룹별에 대한 자세한 내용은 [그룹 BY(거래-SQL)를](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest) 참조하십시오.
|**DWU 사용 및 CPU 포털 메트릭의 정확도 향상**|SQL Analytics는 Azure 포털에서 메트릭 정확도를 크게 향상시킵니다.  이 릴리스에는 모든 계산 노드에서 워크로드를 적절하게 반영하기 위한 CPU 및 DWU 사용 메트릭 정의에 대한 수정 사항이 포함되어 있습니다. 이 수정 전에는 메트릭 값이 과소 보고되었습니다. Azure 포털에서 사용되는 DWU 및 CPU 메트릭이 증가할 것으로 예상합니다. |
|**행 수준 보안**|2017년 11월에 행 수준 보안 기능을 도입했습니다. 이제 이 지원을 외부 테이블로도 확장했습니다. 또한 보안 필터 조건자 정의에 필요한 인라인 테이블 값 함수(인라인 TVF)에서 비결정적 함수를 호출하는 데 대한 지원이 추가되었습니다. 이렇게 하면 보안 필터 조건자에서 IS_ROLEMEMBER(), USER_NAME() 등을 지정할 수 있습니다. 자세한 내용은 [행 수준 보안 설명서의](/sql/relational-databases/security/row-level-security)예제를 참조하십시오.|
|**추가 T-SQL 지원**|SQL 분석의 T-SQL 언어 영역이 [STRING_SPLIT(Transact-SQL)에](/sql/t-sql/functions/string-split-transact-sql)대한 지원을 포함하도록 확장되었습니다.
|**쿼리 최적화 프로그램 개선 사항** |쿼리 최적화는 모든 데이터베이스의 중요한 구성 요소입니다. 쿼리를 가장 잘 실행하는 방법을 최적으로 선택하면 크게 개선될 수 있습니다.  분산 환경에서 복잡한 분석 쿼리를 실행할 때 실행된 작업 수입니다. 더 나은 품질 계획을 만들어 쿼리 성능이 향상되었습니다. 이러한 계획은 비용이 많이 드는 데이터 전송 작업과 반복된 하위 쿼리와 같은 중복 계산을 최소화합니다. 자세한 내용은 이 Azure Synapse [블로그 게시물을](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)참조하십시오.|
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019년 1월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Return Order By Optimization**|SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.   이제 모든 계산 노드는 결과를 단일 계산 노드로 보냅니다. 이 노드는 결과를 병합하고 정렬하여 사용자에게 반환합니다.  쿼리 결과 세트에 많은 수의 행이 포함되어 있을 때 단일 컴퓨팅 노드를 통해 병합하면 성능이 크게 향상됩니다. 이전에는 쿼리 실행 엔진이 각 계산 노드에서 결과를 정렬했습니다. 결과는 컨트롤 노드로 스트리밍됩니다. 그러면 컨트롤 노드가 결과를 병합합니다.|
|**PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선**|SQL 분석에서 ShuffleMove 형식의 데이터 이동 단계는 인스턴트 데이터 이동 기술을 사용합니다.  자세한 내용은 [성능 향상 블로그를](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)참조하십시오. 이 릴리스에서는 PartitionMove 및 BroadcastMove가 동일한 인스턴트 데이터 이동 기술에 의해 구동됩니다. 이러한 유형의 데이터 이동 단계를 사용하는 사용자 쿼리는 성능이 향상되어 실행됩니다. 이러한 성능 향상을 얻기 위해 코드를 변경할 필요는 없습니다.|
|**주목할 만한 버그**|잘못된 Azure 시냅스 버전 `SELECT @@VERSION` - 잘못된 버전 인 10.0.9999.0을 반환할 수 있습니다. 현재 릴리스의 올바른 버전은 10.0.10106.0입니다. 이 버그가 보고되었으며 검토 중입니다.
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
|none | |
| | |

## <a name="december-2018"></a>2018년 12월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Virtual Network 서비스 엔드포인트 일반 공급**|이 릴리스에는 모든 Azure 리전에서 Azure 시냅스에서 SQL 분석을 위한 VNet(VNet) 서비스 엔드포인트의 일반 가용성이 포함되어 있습니다. VNet 서비스 엔드포인트를 사용하면 가상 네트워크 내 지정된 서브넷 또는 서브넷 세트의 논리 서버 연결을 격리할 수 있습니다. VNet에서 Azure Synapse로의 트래픽은 항상 Azure 백본 네트워크 내에 유지됩니다. 가상 어플라이언스나 온-프레미스를 통해 인터넷 트래픽을 가져오는 특정 경로보다 이러한 직접 경로를 사용하는 것이 좋습니다. 서비스 엔드포인트를 통한 가상 네트워크 액세스에 대해서는 추가 요금이 부과되지 않습니다. [Azure Synapse의](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) 현재 가격 책정 모델은 있는 대로 적용됩니다.<br/><br/>이번 릴리스에서는 [ABFS(Azure Blob FileSystem)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) 드라이버를 통해 [ADLS(Azure Data Lake Storage Gen2)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)로의 PolyBase 연결도 활성화되었습니다. Azure Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 Azure Storage에 제공합니다. 두 개의 기존 Azure Storage 서비스인 Azure Blob Storage 및 Azure Data Lake Storage Gen1 기능이 통합됩니다. 파일 시스템 의미 체계, 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능이 [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.<br/><br/>Polybase를 사용하면 VNet에 보안된 Azure 저장소에서 Azure 시냅스에서 SQL 분석으로 데이터를 가져올 수도 있습니다. 마찬가지로 Azure 시냅스에서 VNet으로 보안되는 Azure 저장소로 데이터를 내보내는 것도 Polybase를 통해 지원됩니다.<br/><br/>Azure Synapse의 VNet 서비스 끝점에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) 또는 [설명서를](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)참조하십시오.|
|**자동 성능 모니터링(미리 보기)**|이제 Azure Synapse의 SQL 분석에서 미리 보기에서 [쿼리 저장소를](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) 사용할 수 있습니다. 쿼리 저장소는 데이터 웨어하우스의 작업 및 성능을 모니터링하기 위해 쿼리, 쿼리 계획, 런타임 통계 및 쿼리 기록을 추적함으로써 쿼리 성능 문제를 해결하는 데 도움을 주도록 디자인되었습니다. 쿼리 저장소는 다음을 지원하는 내부 저장소 및 DMV(동적 관리 뷰) 세트입니다.<br/><br/>&bull;&nbsp; 상위 리소스 소비 쿼리 식별 및 조정<br/>&bull;&nbsp; 계획되지 않은 워크로드 식별 및 개선<br/>&bull;&nbsp; 통계, 인덱스 또는 시스템 크기(DWU 설정)의 변경으로 쿼리 성능 및 계획에 미치는 영향 평가<br/>&bull;&nbsp; 실행된 모든 쿼리에 대한 전체 쿼리 텍스트 보기<br/><br/>쿼리 저장소에는 다음과 같은 3가지 실제 저장소가 포함되어 있습니다.<br/>&bull;&nbsp; 실행 계획 정보를 유지하기 위한 계획 저장소<br/>&bull;&nbsp; 실행 통계 정보를 유지하기 위한 런타임 통계 저장소<br/>&bull;&nbsp; 대기 통계는 대기 통계 정보를 지속하기 위해 저장합니다.<br/><br/>Azure Synapse의 SQL Analytics는 이러한 저장소를 자동으로 관리하며 추가 비용 없이 지난 7일 동안 저장된 무제한의 쿼리를 제공합니다. 쿼리 저장소를 사용하도록 설정하는 것은 ALTER DATABASE T-SQL 문을 실행하는 것만큼 간단합니다. <br/>sql ----ALTER 데이터베이스 [DatabaseName] set QUERY_STORE = ON;-------쿼리 저장소에 대한 자세한 내용은 문서, [쿼리 저장소를 사용하여 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)및 [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)같은 쿼리 저장소 DMV를 참조하십시오. 다음은 출시 소식을 발표하는 [블로그 게시물](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)입니다.|
|**SQL 분석을 위한 계산 계층 감소**|이제 Azure Synapse의 SQL 분석이 낮은 계산 계층을 지원합니다. 고객은 100cDWU(데이터[웨어하우스 단위)로](what-is-a-data-warehouse-unit-dwu-cdwu.md)시작하는 Azure Synapse의 선도적인 성능, 유연성 및 보안 기능을 경험하고 몇 분 만에 30,000cDWU로 확장할 수 있습니다. 2018년 12월 중순부터, 고객은 [지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 하위 컴퓨팅 계층에 대한 Gen2 성능 및 유연성을 누릴 수 있으며, 나머지 지역의 경우 2019년 중에 서비스가 지원될 예정입니다.<br/><br/>Microsoft는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다. 고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다. SQL Analytics는 일시 중지 및 재개 작업을 계속 지원하며 컴퓨팅의 유연성을 넘어섭니다. 또한 Gen2에서는 쿼리당 2.5배 더 많은 메모리, 최대 128개의 동시 쿼리 및 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) 기능과 함께 제한없는 columnstore 스토리지 용량을 지원합니다. 이러한 기능은 평균적으로 동일한 가격으로 Gen1의 동일한 데이터 웨어하우스 Unit에 비해 5배 더 많은 성능을 제공합니다. 지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure Synapse의 SQL 분석은 현재 수행할 수 있는 크기를 조정할 준비가 되었습니다.|
|**Columnstore 백그라운드 병합**|기본적으로 Azure SQL Data는 데이터를 열 형식에 저장하며, 마이크로 파티션은 [행 그룹입니다.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md) 경우에 따라 인덱스 작성 또는 데이터 로드 시의 메모리 제한으로 인해 rowgroup이 최적 크기인 1백만 개 행보다 더 적은 수로 압축될 수 있습니다. 또한 삭제로 인해 rowgroup이 조각화될 수도 있습니다. 작아지거나 조각화된 rowgroup은 더 많은 메모리를 사용하고 쿼리 실행 효율성이 떨어질 수 있습니다. 이 릴리스에서는 columnstore 백그라운드 유지 관리 태스크가 작은 압축 된 행 그룹을 병합하여 더 큰 행 그룹을 만들어 메모리를 더 잘 활용하고 쿼리 실행 속도를 높일 수 있습니다.
| | |

## <a name="october-2018"></a>2018년 10월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 웨어하우징을 위한 DevOps**|Azure Synapse에서 SQL 분석에 대해 매우 요청된 기능은 이제 Visual Studio에서 SQL Server 데이터 도구(SSDT)를 지원하여 미리 보기상태입니다! 이제 개발자 팀에서는 버전이 제어되는 단일 코드 베이스를 통해 공동으로 작업하고 전 세계의 모든 인스턴스에 변경 내용을 신속하게 배포할 수 있습니다. 관심이 있으세요? 이 기능은 현재 미리 보기로 제공되고 있습니다! Visual Studio SQL [Server 데이터 도구(SSDT) - 미리 보기 등록 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)방문하여 등록할 수 있습니다. 수요가 많기 때문에 고객에게 최상의 환경을 유지하기 위해 미리 보기 사용 인원을 관리하고 있습니다. 미리 보기에 등록하시면 저희 쪽에서 7영업일 내에 상태를 확인할 것입니다.|
|**행 수준 보안 일반 공급**|Azure Synapse의 SQL Analytics는 이제 중요한 데이터를 보호하는 강력한 기능을 추가하는 행 수준 보안(RLS)을 지원합니다. RLS가 도입된 덕분에, 행에 액세스할 수 있는 사람을 제어하는 방법과 마찬가지로 테이블의 행에 대한 액세스를 제어하는 보안 정책을 구현할 수 있습니다. RLS를 사용하면 데이터 웨어하우스를 다시 디자인할 필요 없이 세밀한 액세스 제어가 가능합니다. RLS는 액세스 제한 논리가 다른 애플리케이션의 데이터가 아닌 데이터베이스 계층 자체에 있으므로 보안 모델이 전체적으로 간소화됩니다. 또한 RLS는 액세스 제어 관리용 행을 필터링하는 보기를 도입할 필요가 없습니다. 모든 고객은 이 엔터프라이즈급 보안 기능을 추가 비용 없이 사용할 수 있습니다.|
|**고급 Advisor**|Azure Synapse에서 SQL 분석을 위한 고급 튜닝은 추가 데이터 웨어하우스 권장 사항 및 메트릭을 통해 더 간단해졌습니다. Azure Advisor를 통해 고객이 원하는 대로 사용할 수 있는 다음과 같은 고급 성능 권장 사항이 추가로 제공됩니다.<br/><br/>1. 적응형 캐시 – 캐시 사용률을 최적화하기 위해 확장할 때 주의해야 합니다.<br/>2. 테이블 배포 - 데이터 이동을 줄이고 워크로드 성능을 높이기 위해 테이블을 복제할 시기를 결정합니다.<br/>3. Tempdb - tempdb 경합을 줄이기 위해 리소스 클래스를 확장하고 구성하는 시기를 이해합니다.<br/><br/>개요 블레이드의 거의 실시간 메트릭을 위한 향상된 사용 지정 가능한 모니터링 차트 등 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)와 데이터 웨어하우스 메트릭이 더욱 긴밀하게 통합되었습니다. 사용량을 모니터링하거나 데이터 웨어하우스 권장 사항의 유효성을 검사하고 이 권장 사항을 적용할 때 Azure Monitor 메트릭에 액세스하기 위해 더 이상 데이터 웨어하우스 개요 블레이드에서 나갈 필요가 없습니다. 또한 tempdb, 적응형 캐시 사용률 등 성능 권장 사항을 보완하는 데 사용할 수 있는 새로운 메트릭이 있습니다.|
|**Advisor가 통합된 고급 튜닝**|Azure Synapse에 대한 고급 튜닝은 추가 데이터 웨어하우스 권장 사항 및 메트릭과 Azure Advisor 및 Azure Monitor와의 통합환경을 제공하는 포털 개요 블레이드의 재설계를 통해 더 간단해졌습니다.|
|**ADR(가속 데이터베이스 복구)**|Azure Synapse ADR(가속 데이터베이스 복구)이 공개 미리 보기상태입니다. ADR은 현재 복구 프로세스를 처음부터 다시 설계하여 데이터베이스, 특히 장기 실행 트랜잭션이 있는 데이터베이스의 가용성을 크게 향상하는 새로운 SQL Server 엔진입니다. ADR의 가장 큰 장점은 빠르고 일관적인 데이터베이스 복구와 즉각적인 트랜잭션 롤백입니다.|
|**Azure Monitor 진단 로그**|이제 Azure Synapse를 사용하여 Azure Monitor 진단 로그와 직접 통합하여 분석 워크로드에 대한 향상된 통찰력을 얻을 수 있습니다. 이 새 기능을 통해 개발자는 오랫동안 워크로드 동작을 분석하고 쿼리 최적화 또는 용량 관리에 대한 합리적 결정을 내릴 수 있습니다. [Azure Monitor 진단 로그](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)를 통해 데이터 웨어하우스 워크로드에 대한 추가 정보를 제공하는 외부 로깅 프로세스를 도입했습니다. 이제 단추를 한 번만 클릭하면 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries)를 사용하여 기록 쿼리 성능 문제 해결 기능에 대한 진단 로그를 구성할 수 있습니다. Azure Monitor 진단 로그는 감사용으로 스토리지 계정에 로그를 저장하는 방식을 통한 사용자 지정 가능 보존 기간, 실시간에 가까운 원격 분석 인사이트 확인을 위해 로그를 이벤트 허브로 스트리밍하는 기능, 그리고 Log Analytics에서 로그 쿼리를 사용하여 로그를 분석하는 기능을 지원합니다. 진단 로그는 Azure Synapse에서 SQL 분석에 대해 가장 일반적으로 사용되는 성능 문제 해결 DMV와 동일한 데이터 웨어하우스의 원격 분석 보기로 구성됩니다. 이 초기 릴리스의 경우 다음 시스템 동적 관리 뷰에 대한 보기가 설정되었습니다.<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**Columnstore 메모리 관리**|압축된 열 저장소 행 그룹의 수가 증가하면 이러한 행 그룹에 대한 내부 열 세그먼트 메타데이터를 관리하는 데 필요한 메모리도 증가합니다.  결과적으로 일부 Columnstore DMV(동적 관리 뷰)에 대해 실행되는 쿼리 및 쿼리 성능이 저하될 수 있습니다.  이번 릴리스에서는 이러한 경우에 대한 내부 메타데이터 크기가 최적화되어 이러한 쿼리의 환경 및 성능이 개선되도록 수정되었습니다.|
|**Azure Data Lake Storage Gen2 통합(GA)**|시냅스 애널리틱스는 이제 Azure 데이터 레이크 스토리지 Gen2와 기본 통합을 제공합니다. 이제 고객은 ABFS의 외부 테이블을 사용하여 데이터를 SQL 분석으로 로드할 수 있습니다. 이 기능을 통해 고객은 데이터 레이크를 Data Lake Storage Gen2에 통합할 수 있습니다.|
|**주목할 만한 버그**|CETAS에서 DW2000 이상의 데이터 웨어하우스의 작은 리소스 클래스에서 마루 에 오류가 발생 - 이 수정 프로그램은 Parquet 코드 경로에 대한 외부 테이블 만들기에서 null 참조를 올바르게 식별합니다.<br/><br/>일부 CTAS 작업에서 ID 열 값이 손실될 수 있음 - CTASed가 다른 테이블에 있을 때 식별 열값이 보존되지 않을 수 있습니다. [블로그에](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)보고 .<br/><br/>쿼리가 계속 실행되는 동안 세션이 종료되는 경우 내부 오류 - 이 수정 프로그램은 쿼리가 여전히 실행 중일 때 세션이 종료되는 경우 InvalidOperationException을 트리거합니다.<br/><br/>(2018년 11월에 배포됨) Polybase를 사용하여 ADLS(Gen1)에서 여러 작은 파일을 로드하려고 하는 경우 고객에게 최적이 아닌 성능 문제가 발생했습니다. - AAD 보안 토큰 유효성 검사 중에 시스템 성능이 병목 현상을 발생했습니다. 보안 토큰의 캐싱을 사용하여 성능 문제를 완화했습니다. |
| | |

## <a name="next-steps"></a>다음 단계
- [SQL 풀 만들기](create-data-warehouse-portal.md)

## <a name="more-information"></a>자세한 정보
- [블로그 - Azure 시냅스 분석](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [고객 자문 팀 블로그](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [스택 오버플로우 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 용어집](../../azure-glossary-cloud-terminology.md)
