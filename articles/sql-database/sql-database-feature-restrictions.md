---
title: Azure SQL Database 기능 제한 사항 | Microsoft Docs
description: Azure SQL Database 기능 제한 사항에 대 한 정보에 액세스 하는 공격자가 될 수 있는 데이터베이스의 기능을 제한 하 여 데이터베이스 보안을 향상 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: ac7a074e78def504a10b4daa07971f919f414a88
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259454"
---
# <a name="azure-sql-database-feature-restrictions"></a>Azure SQL Database 기능 제한 사항

SQL Server 공격의 하나의 공통 소스 이며 데이터베이스에 액세스 하는 웹 응용 프로그램을 통해 다양 한 형태의 SQL 주입 공격 데이터베이스에 대 한 정보 수집에 사용 되는 위치  이상적으로 응용 프로그램 코드는 하므로 SQL 삽입에 대 한 허용 하지 않습니다.  그러나 대규모 코드 기반 레거시 및 외부 코드를 포함 하는에서 하나 수 없습니다 모든 사례가 해결 된 있는지 하므로 SQL 삽입 으로부터 보호 해야 하는 현실입니다.  일부 형태의 SQL 삽입은 성공 하는 경우에 데이터베이스에 대 한 정보를 누출 되지 않도록 SQL 주입 공격을 방지 하기 위해 기능 제한의 목표가입니다.

## <a name="enabling-feature-restrictions"></a>기능 제한 사용

기능 제한 사용 이루어집니다를 사용 하 여는 `sp_add_feature_restriction` 다음과 같이 저장 프로시저:

```sql
EXEC sp_add_feature_restriction <feature>, <object_class>, <object_name>
```

다음과 같은 기능을 제한할 수 있습니다.

| 기능          | 설명 |
|------------------|-------------|
| N'ErrorMessages' | 제한, 오류 메시지 내에서 사용자 데이터가 마스킹됩니다. 참조 [오류 메시지 기능 제한](#error-messages-feature-restriction) |
| N'Waitfor'       | 제한, 지연 없이 명령이 즉시 반환 됩니다. 참조 [WAITFOR 기능 제한](#waitfor-feature-restriction) |

값 `object_class` 일 수 있습니다 `N'User'` 또는 `N'Role'` 나타내기 위해 여부를 `object_name` 사용자 이름 또는 데이터베이스에서 역할 이름을 합니다.

다음 예제에서는 사용자에 대 한 모든 오류 메시지를 하면 `MyUser` 마스킹됩니다.

```sql
EXEC sp_add_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="disabling-feature-restrictions"></a>기능 제한을 사용 하지 않도록 설정

이루어집니다 기능 제한을 사용 하지 않도록 설정 사용 하 여는 `sp_drop_feature_restriction` 저장 프로시저를 다음과 같이 합니다.

```sql
EXEC sp_drop_feature_restriction <feature>, <object_class>, <object_name>
```

다음 예제에서는 사용자에 대 한 오류 메시지 마스킹을 사용 하지 않도록 설정 `MyUser`:

```sql
EXEC sp_drop_feature_restriction N'ErrorMessages', N'User', N'MyUser'
```

## <a name="viewing-feature-restrictions"></a>보기 기능 제한 사항

`sys.sql_feature_restrictions` 보기 데이터베이스에서 모든 현재 정의 된 기능 제한을 제공 합니다. 다음 열에

| 열 이름 | 데이터 형식 | 설명 |
|-------------|-----------|-------------|
| class       | nvarchar(128) | 제한이 적용 되는 개체의 클래스 |
| 개체(object)      | nvarchar(256) | 제한이 적용 되는 개체의 이름 |
| 기능     | nvarchar(128) | 제한 된 기능 |

## <a name="feature-restrictions"></a>기능 제한

### <a name="error-messages-feature-restriction"></a>오류 메시지 기능 제한

하나의 일반적인 SQL 주입 공격 방법은 오류가 발생 하는 코드를 삽입 하는 경우  오류 메시지를 검사 하 여 공격자가 배울 시스템에 대 한 정보 추가 구체적인된 공격을 사용 하도록 설정 합니다.  이 공격은 응용 프로그램 쿼리 결과 표시 되지 않지만 오류 메시지를 표시 하는 경우 특히 유용할 수 있습니다.

형식의 요청에는 웹 응용 프로그램을 고려해 야 합니다.

```html
http://www.contoso.com/employee.php?id=1
```

다음 데이터베이스 쿼리를 실행 하는:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

이 값으로 전달 하는 경우는 `id` 웹 응용 프로그램 요청에 매개 변수를 대체 데이터베이스 쿼리에서 $EmpId 복사할 공격자가 다음 요청을 만들 수 없습니다.

```html
http://www.contoso.com/employee.php?id=1 AND CAST(DB_NAME() AS INT)=0
```

반환 하는 다음 오류는 공격자는 데이터베이스의 이름에 알아봅니다.

```sql
Conversion failed when converting the nvarchar value 'HR_Data' to data type int.
```

사용 하도록 설정 하면 오류 메시지는 응용 프로그램 사용자 데이터베이스에 대 한 제한 기능을 한 후 반환 된 오류 메시지 손실 되는 데이터베이스에 대 한 내부 정보가 없습니다 있도록 마스킹:

```sql
Conversion failed when converting the ****** value '******' to data type ******.
```

마찬가지로, 공격자가 다음 요청을 만들 수 없습니다.

```html
http://www.contoso.com/employee.php?id=1 AND CAST(Salary AS TINYINT)=0
```

반환 하는 다음 오류는 공격자는 직원의 연봉을 알아봅니다.

```sql
Arithmetic overflow error for data type tinyint, value = 140000.
```

오류 메시지 기능 제한 사용 데이터베이스는 반환 합니다.

```sql
Arithmetic overflow error for data type ******, value = ******.
```

### <a name="waitfor-feature-restriction"></a>WAITFOR 기능 제한

블라인드 SQL 주입은 응용 프로그램에서 공격자가 삽입 된 SQL의 결과 또는 오류 메시지를 제공 하지 않습니다 하지만 공격자가 있는 조건부 쿼리를 생성 하 여 데이터베이스에서 정보를 추론할 수 있습니다 하는 경우 두 조건부 분기 다른 실행 시간이 소요 됩니다. 응답 시간을 비교 하 여 공격자가 알 수 분기를 실행 하므로 시스템에 대 한 정보를 알아봅니다. 이 공격은 가장 간단한 변형을 사용 하 여는 `WAITFOR` 문을 지연을 소개 합니다.

형식의 요청에는 웹 응용 프로그램을 고려해 야 합니다.

```html
http://www.contoso.com/employee.php?id=1
```

다음 데이터베이스 쿼리를 실행 하는:

```sql
SELECT Name FROM EMPLOYEES WHERE Id=$EmpId
```

웹 응용 프로그램 요청 id 매개 변수로 전달 되는 값에 대체 데이터베이스 쿼리에서 $EmpId 복사 하는 경우 공격자가 다음 요청을 가능 합니다.

```html
http://www.contoso.com/employee.php?id=1; IF SYSTEM_USER='sa' WAITFOR DELAY '00:00:05'
```

경우는 쿼리를 추가 하는 5 초 소요 및는 `sa` 계정을 사용 하는 동안. 하는 경우 `WAITFOR` 데이터베이스의 기능 제한 되지를 `WAITFOR` 문이 무시 되 고 정보가 아닌이 공격을 사용 하 여 손실 됩니다.