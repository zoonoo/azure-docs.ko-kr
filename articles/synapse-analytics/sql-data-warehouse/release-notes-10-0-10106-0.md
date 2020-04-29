---
title: 릴리스 정보
description: Azure Synapse Analytics에 대 한 릴리스 정보입니다.
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
ms.openlocfilehash: 813baba37684525c336bc34a49e496f54a19288d
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509740"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure Synapse Analytics 릴리스 정보

이 문서에서는 Azure Synapse의 최신 [SYNAPSE SQL](sql-data-warehouse-overview-what-is.md) 릴리스에 대 한 새로운 기능 및 향상 된 기능을 요약 합니다. 이 문서에는 릴리스와 직접 관련 되지 않지만 동일한 시간 프레임에 게시 되는 중요 한 콘텐츠 업데이트도 나열 됩니다. 다른 Azure 서비스의 향상 된 기능에 대해서는 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조 하세요.

## <a name="check-your-azure-synapse-version"></a>Azure Synapse 버전 확인

새 기능이 모든 지역에 롤아웃 됨에 따라 인스턴스에 배포 된 버전과 기능 가용성에 대 한 최신 릴리스 정보를 확인 합니다. 버전을 확인 하려면 SSMS (SQL Server Management Studio)를 통해 SQL 풀에 연결 하 고를 `SELECT @@VERSION;` 실행 하 여 현재 버전을 반환 합니다. 이 버전을 사용 하 여 SQL 풀에 적용 된 릴리스를 확인 합니다. 출력의 날짜는 SQL 풀에 적용 된 릴리스의 월을 식별 합니다. 이는 서비스 수준의 향상 된 기능에만 적용 됩니다. 

도구 개선을 위해 릴리스 정보에 올바른 버전을 설치 했는지 확인 합니다. 


> [!NOTE]
> SELECT @@VERSION 에서 반환 된 제품 이름은 Microsoft Azure SQL Data Warehouse에서 Azure Synapse Analytics로 변경 됩니다. 변경을 수행 하기 전에 고급 공지를 보냅니다. 이 변경은 응용 프로그램 코드의 SELECT @@VERSION 결과에서 제품 이름을 구문 분석 하는 고객과 관련이 있습니다. 제품 재 브랜딩으로 인 한 응용 프로그램 코드 변경을 방지 하려면 다음 명령을 사용 하 여 데이터베이스 제품 이름 및 버전에 대 한 SERVERPROPERTY을 쿼리하여 버전 번호 XX를 반환 합니다. XXXXX. X (제품 이름 없음) 다음 명령을 사용 합니다.
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics (Formerly SQL Data Warehouse):
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```



## <a name="april-2020"></a>2020년 4월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터베이스 호환성 수준 (미리 보기)**| 이 릴리스에서는 이제 사용자가 데이터베이스의 호환성 수준을 설정 하 여 특정 버전의 Synapse SQL 엔진에 대 한 Transact-sql 언어 및 쿼리 처리 동작을 얻을 수 있습니다. 자세한 내용은 database_scoped_configurations 및 [Alter Database 범위 구성](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요 [.](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**Sp_describe_undeclared_parameters**| 사용자가 Transact-sql 일괄 처리에서 선언 되지 않은 매개 변수에 대 한 메타 데이터를 볼 수 있도록 허용 합니다. 자세한 내용은 [sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.|

## <a name="march-2020"></a>2020년 3월

| 도구 개선                                         | 세부 정보                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](/visualstudio/releases/2019/release-notes-preview) -SQL SERVER DATA TOOLS (SSDT)** | 이 릴리스에는 SSDT에 대 한 다음과 같은 향상 된 기능 및 수정 사항이 포함 되어 있습니다. </br> </br> -구체화 된 뷰 (MV)에서 참조 하는 테이블을 변경 하면 MVs에서 지원 되지 않는 Alter View 문이 생성 되는 문제 해결<br/><br/> -데이터베이스 또는 프로젝트에 행 수준 보안 개체가 있을 때 스키마 비교 작업이 실패 하지 않도록 하기 위해 변경 내용을 구현 했습니다. 행 수준 보안 개체는 현재 SSDT에 대해 지원 되지 않습니다.  <br/><br/> -데이터베이스에 많은 수의 개체를 나열할 때 시간 제한이 발생 하지 않도록 SQL Server 개체 탐색기 시간 제한 임계값이 증가 했습니다.<br/><br/> -개체 탐색기를 채울 때 불안정성을 줄이고 성능을 향상 시키기 위해 데이터베이스 개체 목록을 SQL Server 개체 탐색기 검색 하는 방법 최적화 |

## <a name="january-2020"></a>2020년 1월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**작업 관리 포털 메트릭 (미리 보기)**|10 월 이전에 preview에 대 한 [워크 로드 격리](sql-data-warehouse-workload-isolation.md) 릴리스를 사용 하 여 사용자는 시스템 리소스를 효율적으로 관리 하 고 비즈니스 sla를 충족할 수 있도록 자신의 [작업 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 을 만들 수 있습니다.  Azure Synapse Analytics에 대 한 전반적인 [워크 로드](sql-data-warehouse-workload-management.md) 향상의 일환으로 이제 새로운 [워크 로드 관리 모니터링 메트릭을](sql-data-warehouse-workload-management-portal-monitor.md) 사용할 수 있습니다.</br> </br> 이제 워크 로드 모니터링에는 다음과 같은 메트릭이 더 많이 있습니다. </br> -유효 cap 리소스 백분율  </br> -유효 min 리소스 백분율 </br> -작업 그룹 활성 쿼리 </br> -최대 리소스% 별 작업 그룹 할당 </br> -시스템 percent의 작업 그룹 할당 </br> -작업 그룹 쿼리 시간 제한 </br> -작업 그룹 큐에 대기 중인 쿼리 </br></br> 이러한 메트릭을 사용 하 여 [작업 그룹 병목 상태](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck) 또는 [미달 사용 작업 격리](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)로 구성 된 작업 그룹을 식별할 수 있습니다.  이러한 메트릭은 작업 그룹별 분할을 허용 하는 Azure Portal에서 사용할 수 있습니다.  자주 사용 하는 그래프를 필터링 하 고 대시보드에 고정 하 여 정보에 빠르게 액세스할 수 있습니다.|
|**포털 모니터링 메트릭**| 전체 쿼리 작업을 모니터링 하기 위해 다음 메트릭이 포털에 추가 되었습니다. </br> -활성 쿼리 </br> -큐에 대기 중인 쿼리 </br> </br>이러한 메트릭은 [리소스 사용률 모니터링 및 쿼리 작업 설명서](sql-data-warehouse-concept-resource-utilization-query-activity.md)의 기존 메트릭과 함께 설명 됩니다.|

## <a name="october-2019"></a>2019년 10월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**복사 (미리 보기)**|데이터 수집을 위한 간단 하 고 유연한 COPY 문의 공개 미리 보기를 발표 하 게 되어 기쁘게 생각 합니다. 문을 하나만 사용 하면 높은 권한이 있는 사용자를 요구 하지 않고도 추가 유연성을 사용 하 여 데이터를 원활 하 게 수집할 수 있습니다. 자세한 내용은 [복사 명령 설명서](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.|
|**워크 로드 격리 (미리 보기)**|고객의 데이터 웨어하우스를 기준으로 고객을 지원 하기 위해 지능형 워크 로드 관리를 위한 새로운 기능을 발표 하 고 있습니다. 새 [워크 로드 격리](sql-data-warehouse-workload-isolation.md) 기능을 사용 하 여 데이터 웨어하우스 리소스에 대 한 유연성과 제어를 제공 하는 동시에 유형이 다른 워크 로드의 실행을 관리할 수 있습니다. 이로 인해 실행 예측 가능성이 향상 되 고 미리 정의 된 Sla를 충족 하는 기능이 향상 됩니다. </br>워크 로드 격리 외에도 이제 추가 옵션을 [작업 분류](sql-data-warehouse-workload-classification.md)에 사용할 수 있습니다.  로그인 분류 이외에 [워크 로드 분류자 생성](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문은 쿼리 레이블, 세션 컨텍스트 및 하루 중 시간을 기준으로 요청을 분류 하는 기능을 제공 합니다.|
|**예측 (미리 보기)**|이제 데이터 웨어하우스 내에서 기계 학습 모델의 점수를 매기고 크고 복잡 한 데이터 이동에 대 한 필요성을 피할 수 있습니다. T-sql PREDICT 함수는 개방형 모델 프레임 워크를 사용 하 고 데이터 및 기계 학습 모델을 입력으로 사용 하 여 예측을 생성 합니다.
|**GA (SSDT CI/CD)**|오늘 SSDT (SQL Analytics – SQL Server Data Tools) 데이터베이스 프로젝트에 대해 [가장 많이 요청 된 기능의](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t) 일반 공급을 발표 하 게 되어 기쁘게 생각 합니다. 이 릴리스에는 엔터프라이즈 수준 배포에 대 한 기본 제공 CI/CD (지속적인 통합 및 배포) 기능을 제공 하는 Azure DevOps와 네이티브 플랫폼 통합과 함께 Visual Studio 2019와의 SSDT에 대 한 지원이 포함 되어 있습니다. |
|**구체화 된 뷰 (GA)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 [구체화 된 뷰로 성능 튜닝](performance-tuning-materialized-views.md)을 참조 하세요.  구체화 된 뷰를 스크립팅 하려면 [SQL Server Management Studio 18.4](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 이상을 설치 합니다.|
|**GA (동적 데이터 마스킹)**|동적 데이터 마스킹 (DDM)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 즉시 난독 처리 하 여 데이터 웨어하우스의 중요 한 데이터에 대 한 무단 액세스를 방지 합니다.자세한 내용은 [SQL Database 동적 데이터 마스킹](../../sql-database/sql-database-dynamic-data-masking-get-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조 하세요.|
|**커밋된 읽기 스냅숏 격리 (GA)**|ALTER DATABASE를 사용 하 여 사용자 데이터베이스에 대 한 스냅숏 격리를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 현재 작업에 영향을 주지 않으려면 데이터베이스 유지 관리 기간 동안이 옵션을 설정 하거나 데이터베이스에 대 한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [Alter database set options](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요.|
|**정렬 된 GA (클러스터형 Columnstore 인덱스)**|Columnstore는 대량의 데이터를 저장 하 고 효율적으로 쿼리 하기 위한 핵심 수단이 됩니다. 순서가 지정 된 클러스터형 columnstore 인덱스는 효율적인 세그먼트 제거를 사용 하 여 쿼리 실행을 더욱 최적화 합니다.자세한 내용은 [정렬 된 클러스터형 columnstore 인덱스를 사용 하 여 성능 튜닝](performance-tuning-ordered-cci.md)을 참조 하세요.|
|**GA (결과 집합 캐싱)**|결과 집합 캐싱이 설정 되어 있으면 반복적인 사용을 위해 쿼리 결과가 사용자 데이터베이스에 자동으로 캐시 됩니다. 이렇게 하면 다시 계산 기능가 필요 하지 않도록 이후 쿼리 실행에서 지속형 캐시에서 직접 결과를 가져올 수 있습니다. 결과 집합 캐싱은 쿼리 성능을 향상 시키고 계산 리소스 사용량을 줄입니다. 또한 캐시 된 결과 집합을 사용 하는 쿼리는 동시성 슬롯을 사용 하지 않으므로 기존 동시성 제한에 대해 계산 되지 않습니다. 보안을 위해 사용자가 캐시 된 결과를 만드는 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시 된 결과에 액세스할 수 있습니다. 자세한 내용은 [결과 집합 캐싱을 사용한 성능 튜닝](performance-tuning-result-set-caching.md)을 참조 하세요. 버전 10.0.10783.0 이상에 적용 됩니다.|

## <a name="september-2019"></a>2019년 9월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Azure 개인 링크 (미리 보기)**|[Azure 개인 링크](https://azure.microsoft.com/blog/announcing-azure-private-link/)를 사용 하 여 Virtual Network (VNet)에서 개인 끝점을 만들고 SQL 풀에 매핑할 수 있습니다. 그런 다음 VNet의 개인 IP 주소를 통해 이러한 리소스에 액세스할 수 있으므로 Azure Express 경로 개인 피어 링 및/또는 VPN gateway를 통해 온-프레미스에서 연결을 사용할 수 있습니다. 전반적으로이를 통해 공용 IP 주소를 열지 않아도 네트워크 구성이 간단해 집니다. 또한 데이터 반출 위험 으로부터 보호할 수 있습니다. 자세한 내용은 [개요](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 및 [SQL 분석 설명서](../../sql-database/sql-database-private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조 하세요.|
|**GA (데이터 검색 & 분류)**|[데이터 검색 및 분류](../../sql-database/sql-database-data-discovery-and-classification.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 기능이 이제 일반 공급 됩니다. 이 기능은 데이터베이스의 중요 한 데이터를 **검색, 분류, 레이블 지정 &** 하는 고급 기능을 제공 합니다.|
|**한 번 클릭으로 통합 Azure Advisor**|Azure Synapse의 SQL Analytics는 이제 한 번의 클릭 환경을 제공 하는 것과 함께 개요 블레이드의 Azure Advisor 권장 사항에 직접 통합 됩니다. 이제 Azure advisor 블레이드로 이동 하는 대신 개요 블레이드에서 권장 사항을 찾을 수 있습니다. [여기](sql-data-warehouse-concept-recommendations.md)에서 권장 사항에 대해 자세히 알아보세요.|
|**커밋된 읽기 스냅숏 격리 (미리 보기)**|ALTER DATABASE를 사용 하 여 사용자 데이터베이스에 대 한 스냅숏 격리를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.  현재 작업에 영향을 주지 않으려면 데이터베이스 유지 관리 기간 동안이 옵션을 설정 하거나 데이터베이스에 대 한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [Alter database set options](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요.|
|**EXECUTE AS(Transact-SQL)**| 다음 [으로 실행](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 이제 고객이 세션의 실행 컨텍스트를 지정 된 사용자로 설정할 수 있도록 t-sql 지원을 사용할 수 있습니다.|
|**추가 T-sql 지원**|Synapse SQL에 대 한 T-sql 언어 노출 영역은 다음에 대 한 지원을 포함 하도록 확장 되었습니다. </br> - [FORMAT (Transact-sql)](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_PARSE (Transact-sql)](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CAST (Transact-sql)](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CONVERT (Transact-sql)](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys. user_token (Transact-sql)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019년 7월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**구체화 된 뷰 (미리 보기)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 다음을 참조하세요. </br> - [구체화 된 뷰를 CREATE &#40;Transact-sql&#41;를 선택 합니다.](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER 구체화 된 뷰 &#40;Transact-sql&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br> - [Synapse SQL에서 지원 되는 t-sql 문](sql-data-warehouse-reference-tsql-statements.md)|
|**추가 T-sql 지원**|Synapse SQL에 대 한 T-sql 언어 노출 영역은 다음에 대 한 지원을 포함 하도록 확장 되었습니다. </br> - [AT TIME ZONE (Transact-sql)](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [STRING_AGG (Transact-sql)](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**결과 집합 캐싱 (미리 보기)**|이전에 발표 된 결과 집합 캐시를 관리 하기 위해 DBCC 명령이 추가 되었습니다. 자세한 내용은 다음을 참조하세요. </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-sql&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-sql&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br></br> 또한 실행 된 쿼리가 결과 집합 캐시를 사용 하는 경우를 표시 하는 dm_pdw_exec_requests의 새 result_set_cache 열을 참조 하세요 [.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**순서가 지정 된 클러스터형 columnstore 인덱스 (미리 보기)**|새 열 column_store_order_ordinal은 (는) [index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 에 추가 되어 순서가 지정 된 클러스터형 columnstore 인덱스의 열 순서를 식별 합니다.|

## <a name="may-2019"></a>2019년 5월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**동적 데이터 마스킹 (미리 보기)**|동적 데이터 마스킹 (DDM)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 즉시 난독 처리 하 여 데이터 웨어하우스의 중요 한 데이터에 대 한 무단 액세스를 방지 합니다.자세한 내용은 [SQL Database 동적 데이터 마스킹](../../sql-database/sql-database-dynamic-data-masking-get-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조 하세요.|
|**현재 일반 공급 되는 워크 로드 중요도**|작업 관리 분류 및 중요도는 쿼리 실행 순서에 영향을 줄 수 있는 기능을 제공 합니다. 작업 중요도에 대 한 자세한 내용은 설명서의 [분류](sql-data-warehouse-workload-classification.md) 및 [중요도](sql-data-warehouse-workload-importance.md) 개요 문서를 참조 하세요. [워크 로드 분류자 문서 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 도 확인 합니다.<br/><br/>아래 비디오에서 작업의 작업 중요도를 참조 하세요.<br/> -[워크 로드 관리 개념](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[워크 로드 관리 시나리오](https://www.youtube.com/embed/_2rLMljOjw8)|
|**추가 T-sql 지원**|Synapse SQL에 대 한 T-sql 언어 노출 영역은 다음에 대 한 지원을 포함 하도록 확장 되었습니다. </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**JSON 함수**|비즈니스 분석가는 이제 익숙한 T-sql 언어를 사용 하 여 다음과 같은 새로운 JSON 함수를 통해 JSON 데이터로 서식이 지정 된 문서를 쿼리하고 조작할 수 있습니다.</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**결과 집합 캐싱 (미리 보기)**|결과 집합 캐싱은 비즈니스 분석가 및 보고 사용자에 대 한 정보를 파악 하는 동시에 쿼리 응답 시간을 단축 합니다. 자세한 내용은 다음을 참조하세요.</br> - [ALTER DATABASE (Transact-sql)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 옵션 (Transact-sql)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [결과 집합 캐싱 설정 (Transact-sql)](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET 문 (Transact-sql)](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys. 데이터베이스 (Transact-sql)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**순서가 지정 된 클러스터형 columnstore 인덱스 (미리 보기)**|Columnstore는 대량의 데이터를 저장 하 고 효율적으로 쿼리 하기 위한 핵심 수단이 됩니다. 각 테이블에 대해 들어오는 데이터를 행 그룹으로 나누고 행 그룹의 각 열이 디스크의 세그먼트를 형성 합니다.  순서가 지정 된 클러스터형 columnstore 인덱스는 효율적인 세그먼트 제거를 사용 하 여 쿼리 실행을 더욱 최적화 합니다.자세한 내용은 다음을 참조하세요.</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX (transact-sql)](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="march-2019"></a>2019년 3월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 검색 & 분류**|이제 Synapse SQL에 대 한 공개 미리 보기에서 데이터 검색 & 분류를 사용할 수 있습니다. 중요 한 데이터를 보호 하 고 고객의 개인 정보를 보호 하는 것이 중요 합니다. 비즈니스 및 고객 데이터 자산이 성장 함에 따라 데이터를 검색 하 고 분류 하 고 보호 하는 것은 관리할 수 없게 됩니다. Synapse SQL을 기본적으로 도입 하는 데이터 검색 및 분류 기능은 데이터를 보다 효율적으로 보호 하는 데 도움이 됩니다. 이 기능의 전체적인 이점은 다음과 같습니다.<br/>&bull;&nbsp; 데이터 개인 정보 보호 표준 및 규정 준수 요구 사항을 충족 합니다.<br/>&bull;&nbsp; 매우 중요 한 데이터를 포함 하는 데이터 웨어하우스의 보안에 대 한 액세스를 제한 하 고 보안 강화<br/>&bull;&nbsp; 중요 한 데이터에 대 한 비정상적인 액세스를 모니터링 하 고 경고 합니다.<br/>&bull;&nbsp; Azure Portal 중앙 대시보드의 중요 한 데이터 시각화 </br></br>데이터 검색 & 분류는 모든 Azure 지역에서 사용할 수 있으며, 취약점 평가 및 위협 감지를 비롯 한 고급 데이터 보안의 일부입니다. 데이터 검색 & 분류에 대 한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/) 및 온라인 [설명서](../../sql-database/sql-database-data-discovery-and-classification.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조 하세요.|
|**GROUP BY ROLLUP**|ROLLUP은 이제 지원 되는 GROUP BY 옵션입니다.   ROLLUP BY ROLLUP은 열 식의 각 조합에 대 한 그룹을 만듭니다. 그룹화 기준 또한 결과를 부분합과 총합계로 "롤업" 합니다. GROUP BY 함수는 오른쪽에서 왼쪽으로 처리 하 여 그룹 및 집계를 만드는 열 식의 수를 줄입니다.  열 순서는 ROLLUP 출력에 영향을 주며, 결과 집합의 행 수에도 영향을 줄 수 있습니다.<br/><br/>GROUP BY ROLLUP에 대 한 자세한 내용은 [GROUP by (transact-sql)](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 를 참조 하세요.
|**DWU 사용 및 CPU 포털 메트릭에 대 한 정확도 향상**|Synapse SQL은 Azure Portal에서 메트릭 정확도를 크게 향상 시킵니다.  이 릴리스에는 모든 계산 노드에서 작업을 올바르게 반영 하기 위해 CPU 및 DWU 사용에 대 한 수정이 포함 되어 있습니다. 이 문제를 해결 하기 전에 메트릭 값은 보고 됩니다. Azure Portal에서 DWU 사용 및 CPU 메트릭이 증가 하는 것을 볼 수 있습니다. |
|**행 수준 보안**|행 수준 보안 기능은 11 월 2017 일에 다시 도입 되었습니다. 이제 이러한 지원이 외부 테이블에도 확장 되었습니다. 또한 보안 필터 조건자를 정의 하는 데 필요한 인라인 테이블 반환 함수 (인라인 Tvf)에서 비결 정적 함수를 호출 하는 지원을 추가 했습니다. 이 추가 기능을 사용 하면 보안 필터 조건자에 IS_ROLEMEMBER (), USER_NAME () 등을 지정할 수 있습니다. 자세한 내용은 [행 수준 보안 설명서](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 예제를 참조 하세요.|
|**추가 T-sql 지원**|Synapse SQL에 대 한 T-sql 언어 노출 영역이 [STRING_SPLIT (transact-sql)](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 대 한 지원을 포함 하도록 확장 되었습니다.
|**향상 된 쿼리 최적화 프로그램** |쿼리 최적화는 모든 데이터베이스의 중요 한 구성 요소입니다. 쿼리를 가장 잘 실행 하는 방법에 대 한 최적의 선택은 상당한 향상을 얻을 수 있습니다.  분산 된 환경에서 복잡 한 분석 쿼리를 실행할 때 실행 되는 작업의 수는 중요 합니다. 더 나은 품질 계획을 생성 하 여 쿼리 성능이 향상 되었습니다. 이러한 계획은 데이터 전송 작업과 반복 되는 하위 쿼리 등의 중복 계산을 최소화 합니다. 자세한 내용은이 Azure Synapse [블로그 게시물](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)을 참조 하세요.|
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019년 1월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Return Order By Optimization**|SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.   이제 모든 계산 노드가 결과를 단일 계산 노드로 보냅니다. 이 노드는 결과를 병합 하 고 정렬 한 다음 사용자에 게 반환 합니다.  쿼리 결과 세트에 많은 수의 행이 포함되어 있을 때 단일 컴퓨팅 노드를 통해 병합하면 성능이 크게 향상됩니다. 이전에는 쿼리 실행 엔진이 각 계산 노드에서 결과를 정렬 합니다. 결과는 제어 노드로 스트리밍됩니다. 그러면 제어 노드가 결과를 병합 합니다.|
|**PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선**|ShuffleMove 형식의 데이터 이동 단계는 인스턴트 데이터 이동 기법을 사용 합니다.  자세한 내용은 [성능 향상 블로그](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)(영문)를 참조 하세요. 이 릴리스에서는 현재와 같은 즉시 데이터 이동 기법을 사용 하 여 파티션 BroadcastMove 수 있습니다. 이러한 유형의 데이터 이동 단계를 사용 하는 사용자 쿼리는 향상 된 성능으로 실행 됩니다. 이러한 성능 향상을 얻기 위해 코드를 변경할 필요는 없습니다.|
|**주목할 만한 버그**|잘못 된 Azure Synapse version `SELECT @@VERSION` -잘못 된 버전인 10.0.9999.0를 반환할 수 있습니다. 현재 릴리스의 올바른 버전은 10.0.10106.0입니다. 이 버그가 보고되었으며 검토 중입니다.
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
|**Virtual Network 서비스 엔드포인트 일반 공급**|이 릴리스에는 모든 Azure 지역에서 Azure Synapse의 SQL Analytics에 대 한 VNet (Virtual Network) 서비스 끝점의 일반 공급이 포함 되어 있습니다. VNet 서비스 엔드포인트를 사용하면 가상 네트워크 내 지정된 서브넷 또는 서브넷 세트의 논리 서버 연결을 격리할 수 있습니다. VNet에서 Azure Synapse에 대 한 트래픽은 항상 Azure 백본 네트워크 내에 유지 됩니다. 가상 어플라이언스나 온-프레미스를 통해 인터넷 트래픽을 가져오는 특정 경로보다 이러한 직접 경로를 사용하는 것이 좋습니다. 서비스 엔드포인트를 통한 가상 네트워크 액세스에 대해서는 추가 요금이 부과되지 않습니다. [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) 에 대 한 현재 가격 책정 모델은 있는 그대로 적용 됩니다.<br/><br/>이번 릴리스에서는 [ABFS(Azure Blob FileSystem)](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 드라이버를 통해 [ADLS(Azure Data Lake Storage Gen2)](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)로의 PolyBase 연결도 활성화되었습니다. Azure Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 Azure Storage에 제공합니다. 두 개의 기존 Azure Storage 서비스인 Azure Blob Storage 및 Azure Data Lake Storage Gen1 기능이 통합됩니다. 파일 시스템 의미 체계, 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 기능이 [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.<br/><br/>Polybase를 사용 하 여 VNet에 보안 Azure Storage에서 Azure Synapse의 SQL Analytics로 데이터를 가져올 수도 있습니다. 마찬가지로 Azure Synapse에서 VNet에 대해 보호 되는 Azure Storage로 데이터 내보내기도 Polybase를 통해 지원 됩니다.<br/><br/>Azure Synapse의 VNet 서비스 끝점에 대 한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) 또는 [설명서](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조 하세요.|
|**자동 성능 모니터링(미리 보기)**|[쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 은 현재 Azure SYNAPSE의 SQL Analytics의 미리 보기에서 사용할 수 있습니다. 쿼리 저장소는 데이터 웨어하우스의 작업 및 성능을 모니터링하기 위해 쿼리, 쿼리 계획, 런타임 통계 및 쿼리 기록을 추적함으로써 쿼리 성능 문제를 해결하는 데 도움을 주도록 디자인되었습니다. 쿼리 저장소는 다음을 지원하는 내부 저장소 및 DMV(동적 관리 뷰) 세트입니다.<br/><br/>&bull;&nbsp; 상위 리소스 소비 쿼리 식별 및 조정<br/>&bull;&nbsp; 계획 되지 않은 워크 로드 식별 및 개선<br/>&bull;&nbsp; 통계, 인덱스 또는 시스템 크기 (dwu 설정)를 변경 하 여 쿼리 성능 및 계획에 대 한 영향 평가<br/>&bull;&nbsp; 실행 되는 모든 쿼리에 대 한 전체 쿼리 텍스트를 참조 하세요.<br/><br/>쿼리 저장소에는 다음과 같은 3가지 실제 저장소가 포함되어 있습니다.<br/>&bull;&nbsp; 실행 계획 정보를 유지 하는 계획 저장소<br/>&bull;&nbsp; 실행 통계 정보를 유지 하기 위한 런타임 통계 저장소입니다.<br/>&bull;&nbsp; 대기 통계 저장소는 대기 통계 정보를 유지 하기 위한 것입니다.<br/><br/>Azure Synapse의 SQL 분석은 이러한 저장소를 자동으로 관리 하며, 지난 7 일 동안 추가 비용 없이 많은 쿼리를 제한 없이 제공 합니다. 쿼리 저장소를 사용하도록 설정하는 것은 ALTER DATABASE T-SQL 문을 실행하는 것만큼 간단합니다. <br/>sql----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------쿼리 저장소에 대 한 자세한 내용은 쿼리 저장소을 [사용 하 여 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)및 쿼리 저장소 dmv (예 [: query_store_query)](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)문서를 참조 하세요. 다음은 출시 소식을 발표하는 [블로그 게시물](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)입니다.|
|**SQL Analytics의 하위 계산 계층**|이제 Azure Synapse의 SQL Analytics에서 낮은 계산 계층을 지원 합니다. 고객은 100 cDWU ([데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md))로 시작 하는 Azure Synapse의 최고의 성능, 유연성 및 보안 기능을 제공 하 고 몇 분 안에 3만 cdwu로 확장할 수 있습니다. 2018년 12월 중순부터, 고객은 [지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 하위 컴퓨팅 계층에 대한 Gen2 성능 및 유연성을 누릴 수 있으며, 나머지 지역의 경우 2019년 중에 서비스가 지원될 예정입니다.<br/><br/>Microsoft는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다. 고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다. SQL Analytics는 일시 중지 및 재개 작업을 계속 지원 하 고 계산의 유연성을 능가 합니다. 또한 Gen2에서는 쿼리당 2.5배 더 많은 메모리, 최대 128개의 동시 쿼리 및 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) 기능과 함께 제한없는 columnstore 스토리지 용량을 지원합니다. 평균의 이러한 기능은 동일한 가격으로 Gen1의 동일한 데이터 웨어하우스 단위에 비해 5 배 더 많은 성능을 제공 합니다. 지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure Synapse의 SQL Analytics는를 확장할 준비가 되었습니다.|
|**Columnstore 백그라운드 병합**|기본적으로 Azure SQL 데이터는 [행 그룹](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)이라는 마이크로 파티션이 있는 열 형식으로 데이터를 저장 합니다. 경우에 따라 인덱스 작성 또는 데이터 로드 시의 메모리 제한으로 인해 rowgroup이 최적 크기인 1백만 개 행보다 더 적은 수로 압축될 수 있습니다. 또한 삭제로 인해 rowgroup이 조각화될 수도 있습니다. 작아지거나 조각화된 rowgroup은 더 많은 메모리를 사용하고 쿼리 실행 효율성이 떨어질 수 있습니다. 이 릴리스에서 columnstore 백그라운드 유지 관리 작업은 작은 압축 행 그룹을 병합 하 여 더 큰 행 그룹을 만들어 메모리를 보다 효율적으로 활용 하 고 쿼리 실행 속도를 높일 수 있습니다.
| | |

## <a name="october-2018"></a>2018년 10월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 웨어하우징을 위한 DevOps**|Azure Synapse의 Synapse SQL에 대해 요청 된 기능은 이제 Visual Studio의 SSDT (SQL Server 데이터 도구 지원)를 사용 하 여 미리 보기로 제공 됩니다. 이제 개발자 팀에서는 버전이 제어되는 단일 코드 베이스를 통해 공동으로 작업하고 전 세계의 모든 인스턴스에 변경 내용을 신속하게 배포할 수 있습니다. 관심이 있으세요? 이 기능은 현재 미리 보기로 제공되고 있습니다! [Visual Studio SQL Server Data Tools (SSDT)-Preview 등록 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)방문 하 여 등록할 수 있습니다. 수요가 많기 때문에 고객에게 최상의 환경을 유지하기 위해 미리 보기 사용 인원을 관리하고 있습니다. 미리 보기에 등록하시면 저희 쪽에서 7영업일 내에 상태를 확인할 것입니다.|
|**행 수준 보안 일반 공급**|이제 Azure Synapse의 Synapse SQL은 중요 한 데이터를 보호 하는 강력한 기능을 추가 하는 RLS (행 수준 보안)를 지원 합니다. RLS가 도입된 덕분에, 행에 액세스할 수 있는 사람을 제어하는 방법과 마찬가지로 테이블의 행에 대한 액세스를 제어하는 보안 정책을 구현할 수 있습니다. RLS를 사용하면 데이터 웨어하우스를 다시 디자인할 필요 없이 세밀한 액세스 제어가 가능합니다. RLS는 액세스 제한 논리가 다른 애플리케이션의 데이터가 아닌 데이터베이스 계층 자체에 있으므로 보안 모델이 전체적으로 간소화됩니다. 또한 RLS는 액세스 제어 관리용 행을 필터링하는 보기를 도입할 필요가 없습니다. 모든 고객은 이 엔터프라이즈급 보안 기능을 추가 비용 없이 사용할 수 있습니다.|
|**고급 Advisor**|Azure Synapse의 Synapse SQL에 대 한 고급 조정은 추가 데이터 웨어하우스 권장 사항 및 메트릭을 사용 하는 것이 더 간단 합니다. Azure Advisor를 통해 고객이 원하는 대로 사용할 수 있는 다음과 같은 고급 성능 권장 사항이 추가로 제공됩니다.<br/><br/>1. 적응 캐시 – 캐시 사용률을 최적화 하도록 크기를 조정 하는 경우에 권장 됩니다.<br/>2. 테이블 배포 – 데이터 이동을 줄이고 워크 로드 성능을 향상 시키기 위해 테이블을 복제할 시기를 결정 합니다.<br/>3. tempdb – tempdb 경합을 줄이기 위해 리소스 클래스의 크기를 조정 하 고 구성 하는 시기를 이해 합니다.<br/><br/>개요 블레이드의 거의 실시간 메트릭을 위한 향상된 사용 지정 가능한 모니터링 차트 등 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)와 데이터 웨어하우스 메트릭이 더욱 긴밀하게 통합되었습니다. 사용량을 모니터링하거나 데이터 웨어하우스 권장 사항의 유효성을 검사하고 이 권장 사항을 적용할 때 Azure Monitor 메트릭에 액세스하기 위해 더 이상 데이터 웨어하우스 개요 블레이드에서 나갈 필요가 없습니다. 또한 tempdb, 적응형 캐시 사용률 등 성능 권장 사항을 보완하는 데 사용할 수 있는 새로운 메트릭이 있습니다.|
|**Advisor가 통합된 고급 튜닝**|Azure Synapse에 대 한 고급 조정은 추가 데이터 웨어하우스 권장 사항 및 메트릭과, Azure Advisor 및 Azure Monitor와 통합 된 환경을 제공 하는 포털 개요 블레이드를 다시 디자인 하는 것이 더 간단 합니다.|
|**ADR(가속 데이터베이스 복구)**|Azure ADR (Synapse 가속화 된 데이터베이스 복구)는 이제 공개 미리 보기로 제공 됩니다. ADR은 현재 복구 프로세스를 처음부터 다시 설계하여 데이터베이스, 특히 장기 실행 트랜잭션이 있는 데이터베이스의 가용성을 크게 향상하는 새로운 SQL Server 엔진입니다. ADR의 가장 큰 장점은 빠르고 일관적인 데이터베이스 복구와 즉각적인 트랜잭션 롤백입니다.|
|**Azure Monitor 리소스 로그**|이제 Azure Synapse를 사용 하면 Azure Monitor 리소스 로그와 직접 통합 하 여 분석 작업에 대 한 통찰력을 향상 시킬 수 있습니다. 이 새 기능을 통해 개발자는 오랫동안 워크로드 동작을 분석하고 쿼리 최적화 또는 용량 관리에 대한 합리적 결정을 내릴 수 있습니다. 이제 데이터 웨어하우스 워크 로드에 대 한 추가 정보를 제공 하는 [Azure Monitor 리소스 로그](../../azure-monitor/platform/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs) 를 통해 외부 로깅 프로세스가 도입 되었습니다. 이제 단추를 한 번 클릭 하면 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용 하 여 기록 쿼리 성능 문제 해결 기능에 대 한 리소스 로그를 구성할 수 있습니다. Azure Monitor 리소스 로그는 감사를 위해 저장소 계정에 로그를 저장 하 고, 실시간 원격 분석 정보를 거의 실시간으로 이벤트 허브로 스트리밍하는 기능, 로그 쿼리를 통해 Log Analytics를 사용 하 여 로그를 분석 하는 기능을 제공 하 여 사용자 지정 가능한 보존 기간을 지원 합니다. 리소스 로그는 Azure Synapse에서 SQL Analytics에 대해 가장 일반적으로 사용 되는 성능 문제 해결 Dmv에 해당 하는 데이터 웨어하우스의 원격 분석 뷰로 구성 되어 있습니다. 이 초기 릴리스의 경우 다음 시스템 동적 관리 뷰에 대한 보기가 설정되었습니다.<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**Columnstore 메모리 관리**|압축된 열 저장소 행 그룹의 수가 증가하면 이러한 행 그룹에 대한 내부 열 세그먼트 메타데이터를 관리하는 데 필요한 메모리도 증가합니다.  결과적으로 일부 Columnstore DMV(동적 관리 뷰)에 대해 실행되는 쿼리 및 쿼리 성능이 저하될 수 있습니다.  이번 릴리스에서는 이러한 경우에 대한 내부 메타데이터 크기가 최적화되어 이러한 쿼리의 환경 및 성능이 개선되도록 수정되었습니다.|
|**Azure Data Lake Storage Gen2 통합(GA)**|Synapse Analytics는 이제 Azure Data Lake Storage Gen2와의 기본 통합을 포함 합니다. 이제 고객은 ABFS의 외부 테이블을 사용 하 여 Synapse SQL 풀로 데이터를 로드할 수 있습니다. 이 기능을 통해 고객은 데이터 레이크를 Data Lake Storage Gen2에 통합할 수 있습니다.|
|**주목할 만한 버그**|DW2000 등의 데이터 웨어하우스의 작은 리소스 클래스에서 Parquet 실패 CETAS-이 수정은 Create External Table As to Parquet 코드 경로에서 null 참조를 정확 하 게 식별 합니다.<br/><br/>일부 CTAS 작업에서 id 열 값이 손실 될 수 있습니다. 다른 테이블에 CTASed 하는 경우에는 id 열 값이 유지 되지 않을 수 있습니다. [블로그](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)에 보고 되었습니다.<br/><br/>내부 오류 일부 경우 쿼리가 실행 되는 동안 세션이 종료 되는 경우-쿼리가 계속 실행 중일 때 세션이 종료 되는 경우이 수정 프로그램은 InvalidOperationException를 트리거합니다.<br/><br/>(2018년 11월에 배포됨) Polybase를 사용하여 ADLS(Gen1)에서 여러 작은 파일을 로드하려고 하는 경우 고객에게 최적이 아닌 성능 문제가 발생했습니다. -AAD 보안 토큰을 확인 하는 동안 시스템 성능에 병목 현상이 발생 했습니다. 보안 토큰의 캐싱을 사용하여 성능 문제를 완화했습니다. |
| | |

## <a name="next-steps"></a>다음 단계

- [SQL 풀 만들기](create-data-warehouse-portal.md)

## <a name="more-information"></a>추가 정보

- [블로그-Azure Synapse 분석](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [고객 자문 팀 블로그](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 용어집](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
