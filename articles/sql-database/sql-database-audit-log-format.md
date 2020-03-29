---
title: 감사 로그 형식
description: SQL Database 감사 로그가 어떻게 구성되는지 이해합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722087"
---
# <a name="sql-database-audit-log-format"></a>SQL 데이터베이스 감사 로그 형식

[Azure SQL Database 감사는](sql-database-auditing.md) 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 기록하거나 다운스트림 처리 및 분석을 위해 이벤트 허브 또는 로그 애널리틱스로 보냅니다.

## <a name="naming-conventions"></a>명명 규칙

### <a name="blob-audit"></a>Blob 감사

Blob 저장소에 저장된 감사 로그는 Azure `sqldbauditlogs` Storage 계정의 컨테이너에 저장됩니다. 컨테이너 내의 디렉터리 계층 구조는 폼입니다. `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` Blob 파일 이름 `<CreationTime>_<FileNumberInSession>.xel`형식은 `CreationTime` UTC `hh_mm_ss_ms` 형식이며 `FileNumberInSession` 세션 로그가 여러 Blob 파일에 걸쳐 있는 경우 실행 중인 인덱스입니다.

예를 들어 `Database1` `Server1` 다음의 데이터베이스의 경우 가능한 유효한 경로입니다.

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[읽기 전용 복제본](sql-database-read-scale-out.md) 감사 로그는 동일한 컨테이너에 저장됩니다. 컨테이너 내의 디렉터리 계층 구조는 폼입니다. `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` Blob 파일 이름은 동일한 형식을 공유합니다. 읽기 전용 복제본의 감사 로그는 동일한 컨테이너에 저장됩니다.


### <a name="event-hub"></a>이벤트 허브

감사 이벤트는 감사 구성 중에 정의된 네임스페이스 및 이벤트 허브에 기록되며 [아파치 아브로](https://avro.apache.org/) 이벤트 본문에 캡처되어 UTF-8 인코딩이 있는 JSON 서식을 사용하여 저장됩니다. 감사 로그를 읽으려면 [Avro 도구](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

감사 이벤트는 감사 구성 중에 정의된 Log Analytics 작업 `AzureDiagnostics` 영역에 범주가 `SQLSecurityAuditEvents`있는 테이블에 기록됩니다. Log Analytics 검색 언어 및 명령에 대한 유용한 추가 정보는 [Log Analytics 검색 참조](../log-analytics/log-analytics-log-search.md)를 참조하세요.

## <a name="audit-log-fields"></a><a id="subheading-1"></a>로그 필드 감사

| 이름(Blob) | 이름(이벤트 허브/로그 분석) | 설명 | Blob 유형 | 이벤트 허브/로그 분석 유형 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 동작의 ID입니다. | varchar(4) | 문자열 |
| action_name | action_name_s | 작업 이름 | 해당 없음 | 문자열 |
| additional_information | additional_information_s | XML로 저장된 이벤트에 대한 추가 정보 | nvarchar(4000) | 문자열 |
| affected_rows | affected_rows_d | 쿼리의 영향을 받는 행 수 | bigint | int |
| application_name | application_name_s| 클라이언트 응용 프로그램 이름 | nvarchar(128) | 문자열 |
| audit_schema_version | audit_schema_version_d | 항상 1 | int | int |
| class_type | class_type_s | 감사가 발생하는 감사 가능한 엔터티 유형 | varchar(2) | 문자열 |
| class_type_desc | class_type_description_s | 감사가 발생하는 감사 가능한 엔터티에 대한 설명 | 해당 없음 | 문자열 |
| client_ip | client_ip_s | 클라이언트 응용 프로그램의 소스 IP | nvarchar(128) | 문자열 |
| connection_id | 해당 없음 | 서버의 연결 ID | GUID | 해당 없음 |
| data_sensitivity_information | data_sensitivity_information_s | 데이터베이스의 분류된 열을 기반으로 감사된 쿼리에서 반환되는 정보 유형 및 민감도 레이블입니다. [Azure SQL 데이터베이스 데이터 검색 및 분류에](sql-database-data-discovery-and-classification.md) 대해 자세히 알아보기 | nvarchar(4000) | 문자열 |
| database_name | database_name_s | 작업이 발생한 데이터베이스 컨텍스트 | sysname | 문자열 |
| database_principal_id | database_principal_id_d | 작업이 수행되는 데이터베이스 사용자 컨텍스트의 ID | int | int |
| database_principal_name | database_principal_name_s | 작업이 수행되는 데이터베이스 사용자 컨텍스트의 이름 | sysname | 문자열 |
| duration_milliseconds | duration_milliseconds_d | 쿼리 실행 기간(밀리초) | bigint | int |
| event_time | event_time_t | 감사 가능한 작업이 실행되는 날짜 및 시간 | datetime2 | Datetime |
| host_name | 해당 없음 | 클라이언트 호스트 이름 | 문자열 | 해당 없음 |
| is_column_permission | is_column_permission_s | 열 수준 사용 권한임을 나타내는 플래그입니다. 1 = true, 0 = 거짓 | bit | 문자열 |
| 해당 없음 | is_server_level_audit_s | 이 감사가 서버 수준에 있는지 나타내는 플래그 지정 | 해당 없음 | 문자열 |
| object_ id | object_id_d | 감사가 수행된 대상 엔터티의 ID입니다. 여기에는 : 서버 개체, 데이터베이스, 데이터베이스 개체 및 스키마 개체가 포함됩니다. 엔터티가 서버 자체이거나 개체 수준에서 감사가 수행되지 않은 경우 0 | int | int |
| object_name | object_name_s | 감사가 수행된 대상 엔터티의 이름입니다. 여기에는 : 서버 개체, 데이터베이스, 데이터베이스 개체 및 스키마 개체가 포함됩니다. 엔터티가 서버 자체이거나 개체 수준에서 감사가 수행되지 않은 경우 0 | sysname | 문자열 |
| permission_bitmask | permission_bitmask_s | 해당되는 경우 부여, 거부 또는 취소된 사용 권한을 표시합니다. | varbinary(16) | 문자열 |
| response_rows | response_rows_d | 결과 집합에서 반환된 행 수 | bigint | int |
| schema_name | schema_name_s | 동작이 수행된 스키마 컨텍스트입니다. 스키마 외부에서 발생하는 감사에 대한 NULL | sysname | 문자열 |
| 해당 없음 | securable_class_type_s | 감사 중인 class_type 매핑되는 분리 가능한 개체 | 해당 없음 | 문자열 |
| sequence_group_id | sequence_group_id_g | 고유 식별자 | varbinary | GUID |
| sequence_number | sequence_number_d | 감사의 쓰기 버퍼에 맞지 않는 너무 큰 단일 감사 레코드 내의 레코드 시퀀스를 추적합니다. | int | int |
| server_instance_name | server_instance_name_s | 감사가 발생한 서버 인스턴스 이름 | sysname | 문자열 |
| server_principal_id | server_principal_id_d | 작업이 수행되는 로그인 컨텍스트의 ID | int | int |
| server_principal_name | server_principal_name_s | 현재 로그인 | sysname | 문자열 |
| server_principal_sid | server_principal_sid_s | 현재 로그인 SID | varbinary | 문자열 |
| session_id | session_id_d | 이벤트가 발생한 세션의 ID | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 세션에 대한 서버 주체 | sysname | 문자열 |
| statement | statement_s | 실행된 T-SQL 문(있는 경우) | nvarchar(4000) | 문자열 |
| succeeded | succeeded_s | 이벤트를 발생시킨 동작의 성공 여부를 나타냅니다. 로그인 및 일괄 처리 이외의 이벤트의 경우 사용 권한 검사가 성공했는지 실패했는지 여부만 보고되며 작업이 아닙니다. 1 = 성공, 0 = 실패 | bit | 문자열 |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 데이터베이스 보안 주체입니다. 0이 아닌 경우 | int | int |
| target_database_principal_name | target_database_principal_name_s | 동작의 대상 사용자입니다. 해당되지 않는 경우 NULL | 문자열 | 문자열 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 서버 보안 주체입니다. 해당되지 않는 경우 0을 반환합니다. | int | int |
| target_server_principal_name | target_server_principal_name_s | 동작의 대상 로그인입니다. 해당되지 않는 경우 NULL | sysname | 문자열 |
| target_server_principal_sid | target_server_principal_sid_s | 대상 로그인의 SID입니다. 해당되지 않는 경우 NULL | varbinary | 문자열 |
| transaction_id | transaction_id_d | SQL Server 전용(2016년부터) - Azure SQL DB의 경우 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | 사용자 정의 이벤트 ID는 sp_audit_write 인수로 전달되었습니다. 시스템 이벤트(기본값)에 대해 NULL, 사용자 정의 이벤트의 경우 0이 아닙니다. 자세한 내용은 [sp_audit_write(거래-SQL)을](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 참조하십시오. | smallint | int |
| user_defined_information | user_defined_information_s | 사용자 정의 정보는 sp_audit_write 인수로 전달되었습니다. 시스템 이벤트(기본값)에 대해 NULL, 사용자 정의 이벤트의 경우 0이 아닙니다. 자세한 내용은 [sp_audit_write(거래-SQL)을](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 참조하십시오. | nvarchar(4000) | 문자열 |

## <a name="next-steps"></a>다음 단계

[Azure SQL 데이터베이스 감사에](sql-database-auditing.md)대해 자세히 알아봅니다.
