---
title: 서버 매개 변수 구성-Azure Portal-유연한 서버 Azure Database for MySQL
description: 이 문서에서는 Azure Portal를 사용 하 Azure Database for MySQL 유연한 서버에서 MySQL 서버 매개 변수를 구성 하는 방법을 설명 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 9568dfc2cfd678d0ce2dea8475328358906e16d1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92525213"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버에서 서버 매개 변수 구성

서버 매개 변수를 사용 하 여 유연한 서버 구성 Azure Database for MySQL 관리할 수 있습니다. 서버 매개 변수는 서버를 만들 때 기본 및 권장 값으로 구성 됩니다.  

이 문서에서는 Azure Portal를 사용 하 여 서버 매개 변수를 확인 하 고 구성 하는 방법을 설명 합니다. Azure Portal의 서버 매개 변수 블레이드에는 수정 가능 하 고 수정 불가능 한 서버 매개 변수가 모두 표시 됩니다. 수정할 수 없는 서버 매개 변수는 회색으로 표시 됩니다.

>[!Note]
> 서버 매개 변수는 서버 수준에서 전역적으로 업데이트 될 수 있으며 [Azure CLI](./how-to-configure-server-parameters-cli.md) 또는 [Azure Portal](./how-to-configure-server-parameters-portal.md)를 사용할 수 있습니다.

## <a name="configure-server-parameters"></a>서버 매개 변수 구성

1. [Azure Portal](https://portal.azure.com)에 로그인 한 다음 유연한 Azure Database for MySQL 서버를 찾습니다.
2. **설정** 섹션에서 **서버 매개 변수** 를 클릭 하 여 Azure Database for MySQL 유연한 서버에 대 한 서버 매개 변수 페이지를 엽니다.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. 조정 해야 하는 서버 매개 변수를 찾습니다. **설명** 열을 검토하여 용도 및 허용되는 값을 이해합니다.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. **저장** 을 클릭 하 여 변경 내용을 저장 합니다.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. 정적 매개 변수는 서버를 다시 부팅 해야 적용 됩니다. 정적 매개 변수를 수정 하는 경우 **지금 다시 시작** 하거나 **나중에 다시 시작**하 라는 메시지가 표시 됩니다.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정**을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>수정할 수정 불가능 서버 매개 변수 설정

업데이트 하려는 서버 매개 변수를 수정할 수 없는 경우 필요에 따라를 사용 하 여 연결 수준에서 매개 변수를 설정할 수 있습니다 `init_connect` . 서버에 연결 하는 각 클라이언트에 대 한 서버 매개 변수를 설정 합니다. 

1. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 Azure Database for MySQL 서버에 대한 서버 매개 변수 페이지를 엽니다.
2. 검색 `init_connect`
3. 서버 매개 변수를 다음 형식으로 추가 합니다. 값 열에 값을 추가 `SET parameter_name=YOUR_DESIRED_VALUE` 합니다.

    예를 들어를로 설정 하 여 서버의 문자 집합을 변경할 수 있습니다. `init_connect``SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. **저장**을 클릭하여 변경 내용을 저장합니다.

>[!Note]
> `init_connect`를 사용하여 세션 수준에서 SUPER 권한이 필요하지 않은 매개 변수를 변경할 수 있습니다. `init_connect`를 사용하여 매개 변수를 설정할 수 있는지 확인하려면 `set session parameter_name=YOUR_DESIRED_VALUE;` 명령을 실행하고 **액세스 거부됨; SUPER 권한 필요** 오류가 발생한 경우 'init_connect'를 사용하여 매개 변수를 설정할 수 없습니다.

## <a name="working-with-the-time-zone-parameter"></a>표준 시간대 매개 변수 작업

### <a name="populating-the-time-zone-tables"></a>표준 시간대 테이블 채우기

MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하면 서버의 표준 시간대 테이블을 채울 수 있습니다.

> [!NOTE]
> MySQL Workbench에서 `mysql.az_load_timezone` 명령을 실행하는 경우, 먼저 `SET SQL_SAFE_UPDATES=0;`을 사용하여 안전한 업데이트 모드를 꺼야 할 수 있습니다.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> 표준 시간대 테이블이 제대로 채워지도록 하려면 서버를 다시 시작해야 합니다.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

사용 가능한 표준 시간대 값을 보려면 다음 명령을 실행합니다.

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>전역 수준 표준 시간대 설정

전역 수준 표준 시간대는 Azure Portal의 **서버 매개 변수** 페이지에서 설정할 수 있습니다. 아래에서는 전역 표준 시간대를 “US/Pacific” 값으로 설정합니다.

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="Azure Portal 서버 매개 변수 페이지":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>세션 수준 표준 시간대 설정

세션 수준 표준 시간대는 MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `SET time_zone` 명령을 실행하여 설정할 수 있습니다. 아래 예제에서는 표준 시간대를 **US/Pacific** 표준 시간대로 설정합니다.

```sql
SET time_zone = 'US/Pacific';
```

[날짜 및 시간 함수](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)에 대한 MySQL 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure CLI에서 서버 매개 변수](./how-to-configure-server-parameters-cli.md) 를 구성 하는 방법
