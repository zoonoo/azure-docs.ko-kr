---
title: 사용자 정의 스키마 사용
description: T-sql 사용자 정의 스키마를 사용 하 여 Synapse SQL 풀에서 솔루션을 개발 하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633462"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Synapse SQL 풀의 사용자 정의 스키마
이 문서에서는 T-sql 사용자 정의 스키마를 사용 하 여 Synapse SQL 풀에서 솔루션을 개발 하는 방법에 대 한 몇 가지 팁을 설명 합니다.

## <a name="schemas-for-application-boundaries"></a>애플리케이션 경계에 대한 스키마

일반적인 데이터 웨어하우스는 작업, 도메인 또는 보안에 따라 애플리케이션 경계를 만들기 위해 별도 데이터베이스를 사용합니다. 

예를 들어 기존 SQL Server 데이터 웨어하우스에는 준비 데이터베이스, 데이터 웨어하우스 데이터베이스 및 일부 데이터 마트 데이터베이스가 포함 될 수 있습니다. 이 토폴로지에서 각 데이터베이스는 아키텍처에서 작업 및 보안 경계로 작동 합니다.

이와 대조적으로 SQL 풀은 한 데이터베이스 내에서 전체 데이터 웨어하우스 워크 로드를 실행 합니다. 크로스 데이터베이스 조인은 허용 되지 않습니다. SQL 풀은 웨어하우스에서 사용 되는 모든 테이블을 하나의 데이터베이스 내에 저장할 것으로 예상 합니다.

> [!NOTE]
> SQL 풀은 모든 종류의 데이터베이스 간 쿼리를 지원 하지 않습니다. 따라서 이 패턴을 활용하는 데이터 웨어하우스 구현을 수정해야 합니다.
> 
> 

## <a name="recommendations"></a>권장 사항
사용자 정의 스키마를 사용 하 여 작업, 보안, 도메인 및 기능 경계를 통합 하기 위한 권장 사항은 다음과 같습니다.

- 하나의 SQL 풀 데이터베이스를 사용 하 여 전체 데이터 웨어하우스 워크 로드를 실행 합니다.
- 기존 데이터 웨어하우스 환경을 통합 하 여 하나의 SQL 풀 데이터베이스를 사용 합니다.
- **사용자 정의 스키마** 를 활용하여 데이터베이스를 사용하여 이전에 구현된 경계를 제공합니다.

사용자 정의 스키마를 이전에 사용 하지 않은 경우에는 깨끗 한 슬레이트가 있습니다. 이전 데이터베이스 이름을 SQL 풀 데이터베이스의 사용자 정의 스키마에 대 한 기반으로 사용 합니다.

스키마를 이미 사용 하는 경우 몇 가지 옵션이 있습니다.

- 레거시 스키마 이름을 제거 하 고 새로 시작 합니다.
- 레거시 스키마 이름을 테이블 이름으로 미리 보류 하 여 레거시 스키마 이름을 유지 합니다.
- 이전 스키마 구조를 다시 만들도록 추가 스키마의 테이블 위에 뷰를 구현하여 레거시 스키마 이름을 유지합니다.

> [!NOTE]
> 첫 번째 검사에서 옵션 3은 가장 매력적인 옵션처럼 보일 수 있습니다. 그러나 자세히 보면 어렵습니다. SQL 풀에서는 뷰가 읽기 전용입니다. 기본 테이블에 대해 모든 데이터 또는 테이블을 수정해야 합니다. 또한 옵션 3은 시스템으로의 뷰 레이어를 소개합니다. 이미 아키텍처에서 뷰를 사용 중인 경우 추가로 고려할 수 있습니다.
> 
> 

### <a name="examples"></a>예:
데이터베이스 이름에 따라 사용자 정의 스키마를 구현 합니다.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

이전 스키마 이름을 테이블 이름으로 미리 보류 하 여 유지 합니다. 작업 경계에 스키마를 사용 합니다.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

뷰를 사용 하 여 레거시 스키마 이름 유지:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
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
> 스키마 전략의 변경 내용은 데이터베이스에 대한 보안 모델을 검토해야 합니다. 대부분의 경우 스키마 수준에서 사용 권한을 할당 하 여 보안 모델을 단순화할 수 있습니다. 더 세부적인 권한이 필요한 경우 데이터베이스 역할을 사용할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

