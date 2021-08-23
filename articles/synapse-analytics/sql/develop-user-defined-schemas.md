---
title: Synapse SQL 내의 사용자 정의 스키마
description: 아래 섹션에서는 T-SQL 사용자 정의 스키마를 사용하여 Azure Synapse Analytics의 Synapse SQL 기능을 갖춘 솔루션을 개발하기 위한 다양한 팁을 찾을 수 있습니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5bbe3e3471101df5d147c5079d6cd4a7e5ce00cb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287557"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 내의 사용자 정의 스키마

아래 섹션에서는 T-SQL 사용자 정의 스키마를 사용하여 Synapse SQL 내에서 솔루션을 개발하기 위한 다양한 팁을 찾을 수 있습니다.

## <a name="schemas-for-application-boundaries"></a>애플리케이션 경계에 대한 스키마

일반적인 분석 아키텍처는 워크로드, 도메인 또는 보안을 기반으로 응용 프로그램 경계를 만들기 위해 별도 데이터베이스를 사용합니다. 예를 들어, 기존의 SQL Server 분석 인프라는 준비 데이터베이스, 분석 데이터베이스 및 데이터 마트 데이터베이스를 포함할 수 있습니다. 이 토폴로지에서 각 데이터베이스는 아키텍처에서 워크로드 및 보안 경계로 작동합니다.

대신 Synapse SQL은 하나의 데이터베이스 내에서 전체 분석 워크로드를 실행합니다. 데이터베이스 간 조인은 허용되지 않습니다. Synapse SQL은 웨어하우스에서 사용된 모든 테이블을 하나의 데이터베이스 내에 저장할 것으로 예상합니다.

> [!NOTE]
> 전용 SQL 풀이 모든 종류의 데이터베이스 간 쿼리를 지원하지는 않습니다. 따라서 이 패턴을 활용하는 분석 구현을 수정해야 합니다. 서버리스 SQL 풀은 데이터베이스 간 쿼리를 지원합니다.

## <a name="user-defined-schema-recommendations"></a>사용자 정의 스키마 권장 사항

사용자 정의 스키마를 사용하여 워크로드, 보안, 도메인 및 기능 경계를 통합하기 위한 권장 사항이 포함되어 있습니다.

- 하나의 데이터베이스를 사용하여 전체 분석 워크로드를 실행합니다.
- 기존 분석 환경을 통합하여 하나의 데이터베이스를 사용합니다.
- **사용자 정의 스키마** 를 활용하여 데이터베이스를 사용하여 이전에 구현된 경계를 제공합니다.

사용자 정의 스키마를 이전에 사용하지 않은 경우에는 빈 슬레이트가 있습니다. Synapse SQL 데이터베이스에서 사용자 정의 스키마에 대한 기준으로 이전 데이터베이스 이름을 사용하면 됩니다.

스키마가 이미 사용된 경우 몇 가지 옵션이 있습니다.

- 레거시 스키마 이름을 제거하고 새로 시작합니다.
- 레거시 스키마 이름을 테이블 이름으로 미리 보류하여 레거시 스키마 이름을 유지합니다
- 이전 스키마 구조를 다시 만드는 추가 스키마에서 테이블에 대한 뷰를 구현하여 레거시 스키마 이름을 유지합니다.

> [!NOTE]
> 첫 번째 검사에서 옵션 3은 가장 매력적인 선택처럼 보일 수 있습니다. 뷰는 Synapse SQL에서만 읽을 수 있습니다. 기본 테이블에 대해 모든 데이터 또는 테이블을 수정해야 합니다. 또한 옵션 3은 시스템으로의 뷰 레이어를 소개합니다. 이미 아키텍처에서 뷰를 사용 중인 경우 추가로 고려할 수 있습니다.
> 
> 

### <a name="examples"></a>예

데이터베이스 이름을 기반으로 사용자 정의 스키마를 구현합니다.

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

테이블 이름으로 미리 보류하여 레거시 스키마 이름을 유지합니다. 워크로드 경계에 대한 스키마를 사용합니다.

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

뷰를 사용하여 레거시 스키마 이름을 유지합니다.

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
> 스키마 전략을 변경하려면 데이터베이스에 대한 보안 모델을 검토해야 합니다. 대부분의 경우 스키마 수준에서 사용 권한을 할당하여 보안 모델을 단순화할 수 있습니다.

더 세부적인 권한이 필요한 경우 데이터베이스 역할을 사용할 수 있습니다. 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할 및 사용자 관리](/sql/relational-databases/security/authentication-access/database-level-roles) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [Synapse SQL 개발 개요](develop-overview.md)를 참조하세요.