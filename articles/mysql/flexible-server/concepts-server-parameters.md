---
title: 서버 매개 변수-Azure Database for MySQL 유연한 서버
description: 이 항목에서는 Azure Database for MySQL 유연한 서버에서 서버 매개 변수를 구성 하기 위한 지침을 제공 합니다.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497006"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 유연한 서버에서 서버 매개 변수

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

이 문서에서는 Azure Database for MySQL 유연한 서버에서 서버 매개 변수를 구성 하기 위한 고려 사항 및 지침을 제공 합니다.

## <a name="what-are-server-variables"></a>서버 변수 란? 

MySQL 엔진은 엔진 동작을 구성 하 고 조정 하는 데 사용할 수 있는 다양 한 [서버 변수/매개 변수](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) 를 제공 합니다. 일부 매개 변수는 런타임 중에 동적으로 설정할 수 있으며, 다른 매개 변수는 "정적" 이므로 적용 하기 위해 서버를 다시 시작 해야 합니다.

Azure Database for MySQL 유연한 서버는 [Azure Portal](./how-to-configure-server-parameters-portal.md) 를 사용 하 여 다양 한 MySQL 서버 매개 변수의 값을 변경 하는 기능을 제공 하 고 워크 로드 요구 사항에 맞게 [Azure CLI](./how-to-configure-server-parameters-cli.md) 합니다.

## <a name="configurable-server-parameters"></a>구성 가능한 서버 매개 변수

서버 매개 변수를 사용 하 여 유연한 서버 구성 Azure Database for MySQL 관리할 수 있습니다. 서버 매개 변수는 서버를 만들 때 기본 및 권장 값으로 구성 됩니다. Azure Portal의 서버 매개 변수 블레이드에는 수정 가능 하 고 수정 불가능 한 서버 매개 변수가 모두 표시 됩니다. 수정할 수 없는 서버 매개 변수는 회색으로 표시 됩니다.

지원되는 서버 매개 변수 목록은 계속 확장됩니다. Azure Portal의 서버 매개 변수 탭을 사용 하 여 전체 목록을 보고 서버 매개 변수 값을 구성할 수 있습니다.

다음 섹션을 참조 하 여 일반적으로 업데이트 되는 여러 서버 매개 변수의 제한에 대해 자세히 알아보세요. 제한은 서버의 계산 계층 및 크기 (vCores)에 따라 결정 됩니다.

> [!NOTE]
> 수정할 수 없는 서버 매개 변수를 수정 하려고 하지만 사용자 환경에 대 한 수정이 필요한 경우 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) 항목을 열거나 우선 순위를 지정 하는 데 도움이 될 수 있는 피드백이 이미 있는 경우 투표 하세요.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL 유연한 서버에서는 이진 로그가 항상 사용 되도록 설정 됩니다. 즉, `log_bin` 가 ON으로 설정 되어 있습니다. 트리거를 사용 하려는 경우에는 *슈퍼 권한이 없고 이진 로깅이 사용 하도록 설정 된 `log_bin_trust_function_creators`* 것과 유사한 오류가 발생 합니다. 즉, 안전 하지 않은 변수를 사용 하는 것이 좋습니다. 

이진 로깅 형식은 항상 **행** 이며 서버에 대 한 모든 연결은 **항상** 행 기반 이진 로깅을 사용 합니다. 행 기반 이진 로깅을 사용할 경우 보안 문제가 존재 하지 않으며 이진 로깅이 중단 되지 않으므로 안전 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) 하 게를 **TRUE** 로 설정할 수 있습니다.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

이 매개 변수에 대한 자세한 내용은 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)를 참조하세요.

|**가격 책정 계층**|**vCore**|**메모리 크기 (GiB)**|**기본값 (바이트)**|**Min value (바이트)**|**Max 값 (바이트)**|
|---|---|---|---|---|---|
|B1s (기병 양성소)|1|1|134217728|33554432|134217728|
|B1ms (기병 양성소)|1|2|536870912|134217728|536870912|
|버스터 블|2|4|2147483648|134217728|2147483648|
|범용|2|8|6442450944|134217728|6442450944|
|범용|4|16|12884901888|134217728|12884901888|
|범용|8|32|25769803776|134217728|25769803776|
|범용|16|64|51539607552|134217728|51539607552|
|범용|32|128|103079215104|134217728|103079215104|
|범용|48|192|154618822656|134217728|154618822656|
|범용|64|256|206158430208|134217728|206158430208|
|메모리 최적화|2|16|12884901888|134217728|12884901888|
|메모리 최적화|4|32|25769803776|134217728|25769803776|
|메모리 최적화|8|64|51539607552|134217728|51539607552|
|메모리 최적화|16|128|103079215104|134217728|103079215104|
|메모리 최적화|32|256|206158430208|134217728|206158430208|
|메모리 최적화|48|384|309237645312|134217728|309237645312|
|메모리 최적화|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL은 테이블을 만드는 동안 제공된 구성에 따라 InnoDB 테이블을 다른 테이블스페이스에 저장합니다. [시스템 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)는 InnoDB 데이터 사전의 스토리지 영역입니다. [file-per-table 테이블스페이스](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)에는 단일 InnoDB 테이블에 대한 데이터 및 인덱스를 포함하며 파일 시스템에 자체 데이터 파일로 저장됩니다. 이 동작은 `innodb_file_per_table` 서버 매개 변수에 의해 제어됩니다. `innodb_file_per_table`을 `OFF`로 설정하면 InnoDB가 시스템 테이블스페이스에 테이블을 만듭니다. 그렇지 않으면 InnoDB는 file-per-table 테이블스페이스에 테이블을 만듭니다.

유연한 서버는 단일 데이터 파일에서 최대 **4 TB** 를 지 원하는 Azure Database for MySQL 합니다. 데이터베이스 크기가 2TB 보다 큰 경우 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) 테이블 스페이스에 테이블을 만들어야 합니다. 단일 테이블 크기가 4 TB 보다 큰 경우 파티션 테이블을 사용 해야 합니다.

### <a name="max_connections"></a>max_connections

Max_connection의 값은 서버의 메모리 크기에 따라 결정 됩니다. 

|**가격 책정 계층**|**vCore**|**메모리 크기 (GiB)**|**기본값**|**최솟값**|**최댓값**|
|---|---|---|---|---|---|
|B1s (기병 양성소)|1|1|85|10|171|
|B1ms (기병 양성소)|1|2|171|10|341|
|버스터 블|2|4|341|10|683|
|범용|2|8|683|10|1365|
|범용|4|16|1365|10|2731|
|범용|8|32|2731|10|5461|
|범용|16|64|5461|10|10923|
|범용|32|128|10923|10|21845|
|범용|48|192|16384|10|32768|
|범용|64|256|21845|10|43691|
|메모리 최적화|2|16|1365|10|2731|
|메모리 최적화|4|32|2731|10|5461|
|메모리 최적화|8|64|5461|10|10923|
|메모리 최적화|16|128|10923|10|21845|
|메모리 최적화|32|256|21845|10|43691|
|메모리 최적화|48|384|32768|10|65536|
|메모리 최적화|64|504|43008|10|86016|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류 1040(08004): 연결이 너무 많음

> [!IMPORTANT]
> 최상의 환경을 위해 ProxySQL과 같은 연결 풀을 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

MySQL에 대한 새 클라이언트 연결을 만들려면 시간이 필요하며, 일단 설정되면 이러한 연결은 유휴 상태일 때에도 데이터베이스 리소스를 차지합니다. 대부분의 애플리케이션은 많은 단기 연결을 요청합니다. 이는 이러한 상황을 복잡하게 만듭니다. 결과적으로 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이러한 문제를 방지하는 데 도움이 됩니다. ProxySQL 설정에 대해 알아보려면 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)을 방문하세요.

>[!Note]
>ProxySQL은 오픈 소스 커뮤니티 도구입니다. Microsoft에서 최상의 노력으로 지원 됩니다. 신뢰할 수 있는 지침을 사용 하 여 프로덕션 지원을 받으려면 [Proxysql 제품 지원 서비스](https://proxysql.com/services/support/)를 평가 하 고이에 도달할 수 있습니다.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"행 크기 너무 큼 (> 8126)"과 유사한 오류가 표시 되는 경우 매개 변수 **innodb_strict_mode** 를 해제할 수 있습니다. 행 데이터 크기가 8k 보다 크면 서버 매개 변수 **innodb_strict_mode** 를 서버 수준에서 전역적으로 수정할 수 없습니다. 데이터가 오류 없이 잘려 데이터가 손실 될 수 있습니다. 페이지 크기 제한에 맞게 스키마를 수정 하는 것이 좋습니다. 

이 매개 변수는를 사용 하 여 세션 수준에서 설정할 수 있습니다 `init_connect` . 세션 수준에서 **innodb_strict_mode** 설정 하려면 [나열 되지 않은 매개 변수 설정](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters)을 참조 하세요.

> [!NOTE]
> 복제 서버를 복제 하는 경우 원본 서버의 세션 수준에서 **innodb_strict_mode** 을 OFF로 설정 하면 복제가 중단 됩니다. 복제본을 읽은 경우 매개 변수를 OFF로 설정 하는 것이 좋습니다.

### <a name="time_zone"></a>time_zone

초기 배포 시 Azure for MySQL 유연한 서버는 표준 시간대 정보에 대 한 시스템 테이블을 포함 하지만 이러한 테이블은 채워지지 않습니다. MySQL 명령줄 또는 MySQL Workbench와 같은 도구에서 `mysql.az_load_timezone` 저장 프로시저를 호출하여 표준 시간대 테이블을 채울 수 있습니다. 저장 프로시저를 호출하고 글로벌 또는 세션 수준 표준 시간대를 설정하는 방법은 [Azure Portal](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) 또는 [Azure CLI](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 문서를 참조하세요.

## <a name="non-modifiable-server-parameters"></a>수정할 수정 불가능 서버 매개 변수

Azure Portal의 서버 매개 변수 블레이드에는 수정 가능 하 고 수정 불가능 한 서버 매개 변수가 모두 표시 됩니다. 수정할 수 없는 서버 매개 변수는 회색으로 표시 됩니다. 세션 수준에서 수정할 수 없는 서버 매개 변수를 구성 하려는 경우를 사용 하 여 연결 수준에서 매개 변수를 설정 하려면 [Azure Portal](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) 또는 [Azure CLI](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) 문서를 참조 `init_connect` 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 서버 매개 변수](./how-to-configure-server-parameters-portal.md)를 구성하는 방법
- [Azure CLI에서 서버 매개 변수](./how-to-configure-server-parameters-cli.md) 를 구성 하는 방법
