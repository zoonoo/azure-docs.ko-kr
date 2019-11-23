---
title: 기능 제한 사항
description: Azure SQL Database 기능 제한은 공격자가 데이터베이스의 정보에 대 한 액세스 권한을 얻을 수 있도록 데이터베이스의 기능을 제한 하 여 데이터베이스 보안을 향상 시킵니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 03/22/2019
ms.openlocfilehash: ce10daca23299f838e4086426fa89d9cade314ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823442"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database 기능 제한 사항

SQL Server 공격의 일반적인 원인 중 하나는 데이터베이스에 대 한 정보를 공개할 하는 데 다양 한 형태의 SQL 삽입 공격이 사용 되는 데이터베이스에 액세스 하는 웹 응용 프로그램을 사용 하는 것입니다.  응용 프로그램 코드가 SQL 삽입을 허용 하지 않도록 개발 되는 것이 이상적입니다.  그러나 레거시 코드와 외부 코드를 포함 하는 많은 코드 베이스에서는 모든 사례가 해결 되었는지 확인할 수 없기 때문에 SQL 주입은에서 보호 해야 하는 수명입니다.  기능 제한의 목표는 SQL 삽입이 성공적으로 수행 된 경우에도 일부 형식의 SQL 삽입이 데이터베이스에 대 한 정보를 누출 하지 못하도록 하는 것입니다.

## <a name="enabling-feature-restrictions"></a>기능 제한 사용

다음과 같이 `sp_add_feature_restriction` 저장 프로시저를 사용 하 여 기능 제한을 설정 합니다.

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

다음 기능을 제한할 수 있습니다.

| 기능          | 설명 |
|------------------|-------------|
| N'ErrorMessages' | 제한 되 면 오류 메시지 내의 모든 사용자 데이터가 마스킹 됩니다. [오류 메시지 기능 제한](#error-messages-feature-restriction) 을 참조 하세요 |
| N'Waitfor'       | 제한 되 면 지연 없이 명령이 즉시 반환 됩니다. [WAITFOR 기능 제한](#waitfor-feature-restriction) 을 참조 하세요. |

`object_class` 값은 `N'User'` 또는 데이터베이스의 사용자 이름 또는 역할 이름 `object_name` 인지 여부를 나타내는 `N'Role'` 수 있습니다.

다음 예에서는 사용자 `MyUser`에 대 한 모든 오류 메시지를 마스킹 합니다.

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>기능 제한 사용 안 함

다음과 같이 `sp_drop_feature_restriction` 저장 프로시저를 사용 하 여 기능 제한을 비활성화할 수 있습니다.

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

다음 예에서는 사용자 `MyUser`에 대 한 오류 메시지 마스킹을 사용 하지 않도록 설정 합니다.

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>기능 제한 보기

`sys.sql_feature_restrictions` 뷰는 데이터베이스에 대해 현재 정의 된 모든 기능 제한을 표시 합니다. 다음과 같은 열이 있습니다.

| 열 이름 | 데이터 형식 | 설명 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 제한이 적용 되는 개체의 클래스입니다. |
| object      | nvarchar(256) | 제한이 적용 되는 개체의 이름입니다. |
| 기능과     | nvarchar(128) | 제한 된 기능 |

## <a name="feature-restrictions"></a>기능 제한

### <a name="error-messages-feature-restriction"></a>오류 메시지 기능 제한

일반적인 SQL 삽입 공격 방법 중 하나는 오류를 발생 시키는 코드를 삽입 하는 것입니다.  공격자는 오류 메시지를 검토 하 여 시스템에 대 한 정보를 파악 하 고 더 많은 대상 지정 공격을 수행할 수 있습니다.  이 공격은 응용 프로그램에서 쿼리 결과를 표시 하지 않지만 오류 메시지를 표시 하는 경우에 특히 유용할 수 있습니다.

다음 형식의 요청을 포함 하는 웹 응용 프로그램을 고려해 보세요.

```html
http://www.contoso.com/employee.php?id=1
```

다음 데이터베이스 쿼리를 실행 합니다.

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

웹 응용 프로그램 요청에 `id` 매개 변수로 전달 된 값이 데이터베이스 쿼리에서 $EmpId를 대체 하도록 복사 된 경우 공격자는 다음 요청을 수행할 수 있습니다.

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

그리고 다음과 같은 오류가 반환 되어 공격자가 데이터베이스의 이름을 알 수 있습니다.

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

데이터베이스의 응용 프로그램 사용자에 대해 오류 메시지 기능 제한을 사용 하도록 설정한 후에는 데이터베이스에 대 한 내부 정보가 유출 되지 않도록 반환 되는 오류 메시지가 마스킹 됩니다.

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

마찬가지로 공격자는 다음 요청을 수행할 수 있습니다.

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

공격자가 직원의 급여를 배울 수 있도록 다음과 같은 오류가 반환 됩니다.

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

오류 메시지 기능 제한을 사용 하 여 데이터베이스는 다음을 반환 합니다.

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 기능 제한

블라인드 SQL 삽입은 응용 프로그램이 삽입 된 SQL 또는 오류 메시지의 결과를 공격자에 게 제공 하지 않지만 공격자는 두 개의 조건부 분기를 사용 하는 조건부 쿼리를 구성 하 여 데이터베이스에서 정보를 유추할 수 있는 경우입니다. 실행 하는 데 다른 시간을 사용 합니다. 공격자는 응답 시간을 비교 하 여 실행 된 분기를 파악 하 여 시스템에 대 한 정보를 알 수 있습니다. 이 공격의 가장 간단한 변형은 `WAITFOR` 문을 사용 하 여 지연을 도입 하는 것입니다.

다음 형식의 요청을 포함 하는 웹 응용 프로그램을 고려해 보세요.

```html
http://www.contoso.com/employee.php?id=1
```

다음 데이터베이스 쿼리를 실행 합니다.

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

웹 응용 프로그램 요청에 id 매개 변수로 전달 된 값이 데이터베이스 쿼리의 $EmpId를 대체 하도록 복사 되는 경우 공격자는 다음 요청을 수행할 수 있습니다.

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

`sa` 계정이 사용 되는 경우 쿼리는 추가 5 초를 소요 합니다. 데이터베이스에서 `WAITFOR` 기능 제한이 사용 되지 않도록 설정 된 경우이 공격을 사용 하 여 `WAITFOR` 문이 무시 되 고 정보가 유출 됩니다.