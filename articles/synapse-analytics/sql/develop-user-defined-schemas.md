---
title: 시냅스 SQL 내에서 사용자 정의 스키마
description: 아래 섹션에서는 T-SQL 사용자 정의 스키마를 사용하여 Azure Synapse Analytics의 Synapse SQL 기능을 사용하여 솔루션을 개발하기 위한 다양한 팁을 찾을 수 있습니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428707"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>시냅스 SQL 내에서 사용자 정의 스키마

아래 섹션에서는 T-SQL 사용자 정의 스키마를 사용하여 Synapse SQL 내에서 솔루션을 개발하기 위한 다양한 팁을 찾을 수 있습니다.

## <a name="schemas-for-application-boundaries"></a>애플리케이션 경계에 대한 스키마

기존의 분석 아키텍처는 종종 별도의 데이터베이스를 사용하여 워크로드, 도메인 또는 보안을 기반으로 응용 프로그램 경계를 만듭니다. 예를 들어 기존 SQL Server 분석 인프라에는 스테이징 데이터베이스, 분석 데이터베이스 및 데이터 마트 데이터베이스가 포함될 수 있습니다. 이 토폴로지에서 각 데이터베이스는 아키텍처의 워크로드 및 보안 경계로 작동합니다.

대신 Synapse SQL은 하나의 데이터베이스 내에서 전체 분석 워크로드를 실행합니다. 교차 데이터베이스 조인은 허용되지 않습니다. Synapse SQL은 웨어하우스에서 사용하는 모든 테이블이 하나의 데이터베이스 내에 저장될 것으로 예상합니다.

> [!NOTE]
> SQL 풀은 모든 종류의 교차 데이터베이스 쿼리를 지원하지 않습니다. 따라서 이 패턴을 활용하는 분석 구현을 수정해야 합니다. SQL 온디맨드(미리 보기)는 교차 데이터베이스 쿼리를 지원합니다.

## <a name="user-defined-schema-recommendations"></a>사용자 정의 스키마 권장 사항

사용자 정의 스키마를 사용하여 워크로드, 보안, 도메인 및 기능 경계를 통합하기 위한 권장 사항이 포함되어 있습니다.

- 하나의 데이터베이스를 사용하여 전체 분석 워크로드를 실행합니다.
- 기존 분석 환경을 통합하여 하나의 데이터베이스를 사용합니다.
- **사용자 정의 스키마** 를 활용하여 데이터베이스를 사용하여 이전에 구현된 경계를 제공합니다.

사용자 정의 스키마가 이전에 사용되지 않은 경우 깨끗한 슬레이트가 있습니다. 이전 데이터베이스 이름을 Synapse SQL 데이터베이스에서 사용자 정의 스키마의 기준으로 사용합니다.

스키마가 이미 사용된 경우 다음과 같은 몇 가지 옵션이 있습니다.

- 레거시 스키마 이름을 제거하고 새로 시작합니다.
- 레거시 스키마 이름을 테이블 이름에 미리 보류하여 레거시 스키마 이름을 유지합니다.
- 이전 스키마 구조를 다시 만드는 추가 스키마에서 테이블에 대한 뷰를 구현하여 레거시 스키마 이름을 유지합니다.

> [!NOTE]
> 첫 번째 검사에서 옵션 3이 가장 매력적인 선택처럼 보일 수 있습니다. 보기는 시냅스 SQL에서만 읽습니다. 기본 테이블에 대해 모든 데이터 또는 테이블을 수정해야 합니다. 또한 옵션 3은 시스템으로의 뷰 레이어를 소개합니다. 아키텍처에서 뷰를 이미 사용하고 있는 경우 이에 대해 몇 가지 추가 생각을 할 수 있습니다.
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

레거시 스키마 이름을 테이블 이름에 미리 보류하여 유지합니다. 워크로드 경계에 대한 스키마를 사용합니다.

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

보다 세분화된 사용 권한이 필요한 경우 데이터베이스 역할을 사용할 수 있습니다. 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할 및 사용자 관리](../../analysis-services/analysis-services-database-users.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 개발 팁은 [Synapse SQL 개발 개요를](develop-overview.md)참조하십시오.
