---
title: 서버 매개 변수 구성-Azure Portal-Azure Database for MySQL
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL에서 사용 가능한 서버 매개 변수를 구성하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/11/2020
ms.openlocfilehash: f592d6fb8fed3f15bd11d5e6ebe6ee358953748c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837231"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL에서 서버 매개 변수 구성

MySQL용 Azure Database는 일부 서버 매개 변수 구성을 지원합니다. 이 문서에서는 Azure Portal을 사용하여 이러한 매개 변수를 구성하는 방법을 설명합니다. 일부 서버 매개 변수를 조정할 수 있습니다.

## <a name="configure-server-parameters"></a>서버 매개 변수 구성

1. Azure Portal에 로그인한 다음 Azure Database for MySQL 서버를 찾습니다.
2. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 Azure Database for MySQL 서버에 대한 서버 매개 변수 페이지를 엽니다.
![Azure Portal 서버 매개 변수 페이지](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. 조정해야 하는 설정을 모두 찾습니다. **설명** 열을 검토하여 용도 및 허용되는 값을 이해합니다.
![드롭다운 열거](./media/howto-server-parameters/3-toggle_parameter.png)
4. **저장** 을 클릭 하 여 변경 내용을 저장 합니다.
![변경 내용 저장 또는 제거](./media/howto-server-parameters/4-save_parameters.png)
5. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정**을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
![모두 기본값으로 다시 설정](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>나열 되지 않은 매개 변수 설정

업데이트 하려는 서버 매개 변수가 Azure Portal에 나열 되어 있지 않으면를 사용 하 여 연결 수준에서 매개 변수를 선택적으로 설정할 수 있습니다 `init_connect` . 서버에 연결 하는 각 클라이언트에 대 한 서버 매개 변수를 설정 합니다. 

1. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 Azure Database for MySQL 서버에 대한 서버 매개 변수 페이지를 엽니다.
2. 검색`init_connect`
3. 서버 매개 변수를 다음 형식으로 추가 합니다. 값 열에 값을 추가 `SET parameter_name=YOUR_DESIRED_VALUE` 합니다.

    예를 들어를로 설정 하 여 서버의 문자 집합을 변경할 수 있습니다. `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. **저장**을 클릭하여 변경 내용을 저장합니다.

## <a name="working-with-the-time-zone-parameter"></a>표준 시간대 매개 변수 작업

### <a name="populating-the-time-zone-tables"></a>표준 시간대 테이블 채우기

MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하면 서버의 표준 시간대 테이블을 채울 수 있습니다.

> [!NOTE]
> MySQL Workbench에서 `mysql.az_load_timezone` 명령을 실행하는 경우, 먼저 `SET SQL_SAFE_UPDATES=0;`을 사용하여 안전한 업데이트 모드를 꺼야 할 수 있습니다.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 표준 시간대 테이블이 제대로 채워지도록 하려면 서버를 다시 시작해야 합니다. 서버를 다시 시작하려면 [Azure Portal](howto-restart-server-portal.md) 또는 [CLI](howto-restart-server-cli.md)를 사용합니다.

사용 가능한 표준 시간대 값을 보려면 다음 명령을 실행합니다.

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>전역 수준 표준 시간대 설정

전역 수준 표준 시간대는 Azure Portal의 **서버 매개 변수** 페이지에서 설정할 수 있습니다. 아래에서는 전역 표준 시간대를 “US/Pacific” 값으로 설정합니다.

![표준 시간대 매개 변수 설정](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>세션 수준 표준 시간대 설정

세션 수준 표준 시간대는 MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `SET time_zone` 명령을 실행하여 설정할 수 있습니다. 아래 예제에서는 표준 시간대를 **US/Pacific** 표준 시간대로 설정합니다.

```sql
SET time_zone = 'US/Pacific';
```

[날짜 및 시간 함수](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)에 대한 MySQL 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL에 대 한 연결 라이브러리](concepts-connection-libraries.md)입니다.
