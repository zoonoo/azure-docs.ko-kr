---
title: "MySQL용 Azure Database에서 서버 매개 변수 구성 방법 | Microsoft Docs"
description: "이 문서에서는 Azure Portal을 사용하여 MySQL용 Azure Database에서 사용 가능한 서버 매개 변수를 구성하는 방법을 설명합니다."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure Portal을 사용하여 MySQL용 Azure Database에서 서버 매개 변수 구성 방법 | Microsoft Docs

MySQL용 Azure Database는 일부 서버 매개 변수 구성을 지원합니다. 이 문서는 Azure Portal을 사용하여 이러한 매개 변수를 구성하는 방법을 설명하고 지원되는 매개 변수, 기본 값 및 유효한 값 범위를 나열합니다. 일부 서버 매개 변수를 조정할 수 있습니다. 여기에 나열된 항목만 지원됩니다.

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Azure Portal의 서버 매개 변수 블레이드로 이동

Azure Portal에 로그인 한 다음 MySQL용 Azure Database 서버 이름을 클릭합니다. **설정** 섹션에서 **서버 매개 변수**를 클릭하여 MySQL용 Azure Database에 대한 서버 매개 변수 블레이드를 엽니다.

![Azure Portal 서버 매개 변수 블레이드](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>구성 가능한 서버 매개 변수 목록

다음 테이블에 현재 지원되는 서버 매개 변수가 나열되어 있습니다. 응용 프로그램 요구 사항에 따라 매개 변수를 구성할 수 있습니다.

> [!div class="mx-tableFixed"]
|매개 변수 이름|기본값|범위|설명|
|---|---|---|---|
|binlog_group_commit_sync_delay|1000|0, 11-1000000|이진 로그 파일을 디스크에 동기화하기 전에 이진 로그 커밋이 대기하는 시간(마이크로초)을 제어합니다.|
|binlog_group_commit_sync_no_delay_count|0|0-1000000|binlog-group-commit-sync-delay에 의해 지정된 대로 현재 지연을 중단하기 전에 대기하는 트랜잭션의 최대 수입니다.|
|character_set_server|LATIN1|BIG5, UTF8MB4 등.|기본 서버 문자 집합으로 charset_name을 사용합니다.|
|div_precision_increment|4|0-30|나누기 작업의 결과 규모를 증가시키는 데 사용되는 자릿수입니다.|
|group_concat_max_len|1024|4-16777216|GROUP_CONCAT()에 대한 최대 허용된 결과 길이(바이트)입니다.|
|innodb_adaptive_hash_index|켜기|켜기, 끄기|innodb 적응 해시 인덱스의 사용 가능 여부를 나타냅니다.|
|innodb_lock_wait_timeout|50|1-3600|중단하기 전에 InnoDB 트랜잭션이 행 잠금을 대기하는 시간(초)입니다.|
|interactive_timeout|1800|10-1800|닫기 전에 대화형 연결에서 서버가 활동을 대기하는 시간(초)입니다.|
|log_bin_trust_function_creators|끄기|켜기, 끄기|이 변수는 이진 로깅이 사용되면 적용됩니다. 저장 함수 작성자가 이진 로그에 기록할 안전하지 않은 이벤트를 발생시키는 저장 함수를 만들지 않는다고 신뢰할 수 있는지 여부를 제어합니다.|
|log_queries_not_using_indexes|끄기|켜기, 끄기|느린 쿼리 로그의 모든 행을 검색해야 하는 쿼리를 기록합니다.|
|log_slow_admin_statements|끄기|켜기, 끄기|느린 관리 문을 느린 쿼리 로그에 작성된 문에 포함합니다.|
|log_throttle_queries_not_using_indexes|0|0-4294967295|느린 쿼리 로그에 작성할 수 있는 분당 쿼리 개수를 제한합니다.|
|long_query_time|10|1-1E+100|쿼리가 이 초 수보다 더 오래 걸리면, 서버는 Slow_queries 상태 변수를 확장합니다.|
|max_allowed_packet|536870912|1024-1073741824|한 패킷 또는 mysql_stmt_send_long_data() C API 함수에서 보낸 모든 생성/중간 문자열이나 매개 변수의 최대 크기입니다.|
|min_examined_row_limit|0|0-18446744073709551615|느린 쿼리 로그에 구성된 행 수보다 큰 쿼리를 기록합니다. |
|server_id|3293747068|1000-4294967295|각 마스터를 지정하고 고유 ID를 종속하도록 복제에서 사용되는 서버 ID입니다.|
|slave_net_timeout|60|30-3600|슬레이브가 연결 해제를 고려하고, 읽기를 중단하고, 다시 연결을 시도하기 전에 마스터의 추가 데이터를 대기하는 시간(초)입니다.|
|slow_query_log|끄기|켜기, 끄기|느린 쿼리 로그를 사용 또는 사용하지 않도록 설정합니다.|
|sql_mode|0개 선택됨|ALLOW_INVALID_DATES, IGNORE_SPACE, 등.|현재 서버 SQL 모드입니다.|
|time_zone|SYSTEM|예: -8:00, +05:30|서버 표준 시간대입니다.|
|wait_timeout|120|60-240|닫기 전에 비대화형 연결에서 서버가 활동을 대기하는 시간(초)입니다.|

## <a name="next-steps"></a>다음 단계
- [MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)

