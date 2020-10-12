---
title: Synapse SQL 내의 사용자 정의 스키마
description: 아래 섹션에는 Azure Synapse Analytics의 Synapse SQL 기능을 사용 하 여 솔루션을 개발 하는 데 T-sql 사용자 정의 스키마를 사용 하는 다양 한 팁이 나와 있습니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: aebe1d995f3cb6da4663876b8d39d36a1a8b16c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030170"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 내의 사용자 정의 스키마

아래 섹션에는 T-sql 사용자 정의 스키마를 사용 하 여 Synapse SQL 내에서 솔루션을 개발 하기 위한 다양 한 팁이 나와 있습니다.

## <a name="schemas-for-application-boundaries"></a>애플리케이션 경계에 대한 스키마

일반적인 분석 아키텍처는 일반적으로 별도의 데이터베이스를 사용 하 여 작업, 도메인 또는 보안에 따라 응용 프로그램 경계를 만듭니다. 예를 들어 기존 SQL Server 분석 인프라에는 준비 데이터베이스, 분석 데이터베이스 및 데이터 마트 데이터베이스가 포함 될 수 있습니다. 이 토폴로지에서 각 데이터베이스는 아키텍처에서 작업 및 보안 경계로 작동 합니다.

대신 Synapse SQL은 단일 데이터베이스 내에서 전체 분석 작업을 실행 합니다. 크로스 데이터베이스 조인은 허용 되지 않습니다. Synapse SQL은 웨어하우스에서 사용 되는 모든 테이블을 하나의 데이터베이스 내에 저장할 것으로 예상 합니다.

> [!NOTE]
> SQL 풀은 모든 종류의 데이터베이스 간 쿼리를 지원 하지 않습니다. 따라서이 패턴을 활용 하는 분석 구현을 수정 해야 합니다. SQL 주문형 (미리 보기)는 데이터베이스 간 쿼리를 지원 합니다.

## <a name="user-defined-schema-recommendations"></a>사용자 정의 스키마 권장 사항

사용자 정의 스키마를 사용 하 여 작업, 보안, 도메인 및 기능 경계를 통합 하기 위한 권장 사항이 포함 되어 있습니다.

- 단일 데이터베이스를 사용 하 여 전체 분석 워크 로드를 실행 합니다.
- 기존 분석 환경을 통합 하 여 하나의 데이터베이스를 사용 합니다.
- **사용자 정의 스키마** 를 활용하여 데이터베이스를 사용하여 이전에 구현된 경계를 제공합니다.

사용자 정의 스키마를 이전에 사용 하지 않은 경우에는 깨끗 한 슬레이트가 있습니다. Synapse SQL 데이터베이스의 사용자 정의 스키마에 대 한 기반으로 이전 데이터베이스 이름을 사용 합니다.

스키마를 이미 사용 하는 경우 몇 가지 옵션이 있습니다.

- 레거시 스키마 이름을 제거하고 새로 시작합니다.
- 레거시 스키마 이름을 테이블 이름으로 미리 보류 하 여 레거시 스키마 이름을 유지 합니다.
- 이전 스키마 구조를 다시 만드는 추가 스키마에서 테이블에 대 한 뷰를 구현 하 여 레거시 스키마 이름을 유지 합니다.

> [!NOTE]
> 첫 번째 검사에서 옵션 3은 가장 매력적인 선택 처럼 보일 수 있습니다. Synapse SQL에서는 뷰가 읽기 전용입니다. 기본 테이블에 대해 모든 데이터 또는 테이블을 수정해야 합니다. 또한 옵션 3은 시스템으로의 뷰 레이어를 소개합니다. 아키텍처에서 뷰를 이미 사용 하 고 있는 경우이를 추가로 고려해 야 할 수 있습니다.
> 
> 

### <a name="examples"></a>예제

데이터베이스 이름을 기반으로 사용자 정의 스키마를 구현 합니다.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

이전 스키마 이름을 테이블 이름으로 미리 보류 하 여 레거시 스키마 이름을 유지 합니다. 워크로드 경계에 대한 스키마를 사용합니다.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

뷰를 사용 하 여 레거시 스키마 이름을 유지 합니다.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> 스키마 전략을 변경 하려면 데이터베이스에 대 한 보안 모델을 검토 해야 합니다. 대부분의 경우 스키마 수준에서 사용 권한을 할당 하 여 보안 모델을 단순화할 수 있습니다.

더 세부적인 권한이 필요한 경우 데이터베이스 역할을 사용할 수 있습니다. 데이터베이스 역할에 대 한 자세한 내용은 [데이터베이스 역할 및 사용자 관리](../../analysis-services/analysis-services-database-users.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [Synapse SQL 개발 개요](develop-overview.md)를 참조하세요.
