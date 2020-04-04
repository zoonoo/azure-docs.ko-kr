---
title: Azure Synapse에 대한 열 수준 보안이란 무엇입니까?
description: 열 수준 보안을 사용하면 고객이 사용자의 실행 컨텍스트 또는 그룹 구성원 자격에 따라 데이터베이스 테이블 열에 대한 액세스를 제어하여 응용 프로그램의 보안 설계 및 코딩을 간소화하고 열 액세스에 대한 제한을 구현할 수 있습니다.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 61a3e2eadaf79cdb30a931b31cff709298d0a22c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631292"
---
# <a name="column-level-security"></a>열 수준 보안

열 수준 보안을 사용하면 고객이 사용자의 실행 컨텍스트 또는 그룹 구성원 자격에 따라 테이블 열에 대한 액세스를 제어할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
이 비디오가 게시된 이후 [행 수준 보안은](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Azure Synapse에서 사용할 수 있게 되었습니다.

열 수준 보안은 응용 프로그램의 보안 설계 및 코딩을 단순화하여 중요한 데이터를 보호하기 위해 열 액세스를 제한할 수 있도록 합니다. 예를 들어 특정 사용자가 해당 부서와 관련된 테이블의 특정 열에만 액세스할 수 있도록 합니다. 액세스 제한 논리는 다른 애플리케이션 계층의 데이터와 다소 떨어진 데이터베이스 계층에 위치합니다. 데이터베이스는 모든 계층에서 데이터 액세스를 시도할 때마다 액세스 제한을 적용합니다. 이러한 제한으로 인해 전체 보안 시스템의 표면적을 줄여 보안을 더욱 안정적이고 견고하게 만들 수 있습니다. 또한 열 수준 보안은 사용자에게 액세스 제한을 적용하기 위해 열을 필터링하기 위해 뷰를 도입할 필요가 없습니다.

[GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 문을 사용하면 열 수준 보안을 구현할 수 있습니다. 이 메커니즘을 사용하면 SQL 및 AAD(Azure Active Directory) 인증이 모두 지원됩니다.

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>구문

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>예제

다음 예제에서는 테이블의 `TestUser` `SSN` 열에 액세스하는 `Membership` 것을 제한하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주

사회 `Membership` 보장 번호를 저장하는 데 사용되는 SSN 열을 사용하여 테이블을 만듭니다.

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

중요한 `TestUser` 데이터가 있는 SSN 열을 제외한 모든 열에 액세스할 수 있도록 허용합니다.

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

SSN `TestUser` 열을 포함하는 경우 실행되는 쿼리는 실패합니다.

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>사용 사례

현재 열 수준 보안이 사용되는 방법의 몇 가지 예는 다음과 같은 것입니다.

- 금융 서비스 회사는 계정 관리자만 고객 SSN(사회 보장 번호), 전화 번호 및 기타 PII(개인 식별 정보)에 액세스할 수 있도록 허용합니다.
- 의료 서비스 제공자만 의사와 간호사가 민감한 의료 기록에 액세스할 수 있도록 허용하는 동시에 청구 부서의 구성원이 이 데이터를 볼 수 없도록 합니다.
