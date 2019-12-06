---
title: 감사 로그 형식
description: 감사 로그 SQL Database 구성 하는 방법을 이해 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 3b7a3c295d2edd60c70f47ea155a5d747a3bfb03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873763"
---
# <a name="sql-database-audit-log-format"></a>SQL Database 감사 로그 형식

[Azure SQL Database 감사](sql-database-auditing.md) 는 데이터베이스 이벤트를 추적 하 고 Azure 저장소 계정의 감사 로그에 기록 하거나 다운스트림 처리 및 분석을 위해 이벤트 허브 또는 Log Analytics에 보냅니다.

## <a name="naming-conventions"></a>명명 규칙

### <a name="blob-audit"></a>Blob 감사

Blob storage에 저장 된 감사 로그는 Azure Storage 계정의 `sqldbauditlogs` 이라는 컨테이너에 저장 됩니다. 컨테이너 내의 디렉터리 계층 구조는 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`형식입니다. Blob 파일 이름 형식은 `<CreationTime>_<FileNumberInSession>.xel`입니다. 여기에서 `CreationTime`는 UTC `hh_mm_ss_ms` 형식이 며 세션 로그가 여러 Blob 파일에 걸쳐 있는 경우 `FileNumberInSession`는 실행 중인 인덱스입니다.

예를 들어 `Server1`의 데이터베이스 `Database1`에 대해 다음과 같은 유효한 경로를 사용할 수 있습니다.

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[읽기 전용 복제본](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-read-scale-out) 감사 로그는 동일한 컨테이너에 저장 됩니다. 컨테이너 내의 디렉터리 계층 구조는 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`형식입니다. Blob 파일 이름이 동일한 형식을 공유 합니다. 읽기 전용 복제본의 감사 로그는 동일한 컨테이너에 저장 됩니다.


### <a name="event-hub"></a>이벤트 허브

감사 이벤트는 감사 구성 중에 정의 된 네임 스페이스 및 이벤트 허브에 기록 되 고, [Apache Avro](https://avro.apache.org/) 이벤트의 본문에서 캡처되고 utf-8 인코딩을 사용 하 여 JSON 형식 지정을 사용 하 여 저장 됩니다. 감사 로그를 읽으려면 [Avro 도구](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

감사 이벤트는 구성 하는 동안 정의 된 Log Analytics 작업 영역에 기록 되 고 범주 `SQLSecurityAuditEvents``AzureDiagnostics` 테이블에 기록 됩니다. Log Analytics 검색 언어 및 명령에 대한 유용한 추가 정보는 [Log Analytics 검색 참조](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)를 참조하세요.

## <a id="subheading-1"></a>감사 로그 필드

| 이름 (Blob) | 이름 (Event Hubs/Log Analytics) | 설명 | Blob 형식 | Event Hubs/Log Analytics 형식 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 동작의 ID입니다. | varchar(4) | 문자열 |
| action_name | action_name_s | 작업의 이름입니다. | N/A | 문자열 |
| additional_information | additional_information_s | XML로 저장 된 이벤트에 대 한 추가 정보 | nvarchar(4000) | 문자열 |
| affected_rows | affected_rows_d | 쿼리의 영향을 받는 행 수 | bigint | int |
| application_name | application_name_s| 클라이언트 응용 프로그램의 이름 | nvarchar(128) | 문자열 |
| audit_schema_version | audit_schema_version_d | 항상 1 | int | int |
| class_type | class_type_s | 감사가 수행 되는 감사 가능한 엔터티의 형식 | varchar(2) | 문자열 |
| class_type_desc | class_type_description_s | 감사가 수행 되는 감사 가능한 엔터티에 대 한 설명 | N/A | 문자열 |
| client_ip | client_ip_s | 클라이언트 응용 프로그램의 원본 IP | nvarchar(128) | 문자열 |
| connection_id | N/A | 서버에 있는 연결의 ID입니다. | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 데이터베이스의 분류 된 열을 기반으로 감사 된 쿼리에서 반환 되는 정보 유형 및 민감도 레이블입니다. [Azure SQL Database 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md) 에 대 한 자세한 정보 | nvarchar(4000) | 문자열 |
| database_name | database_name_s | 동작이 발생 한 데이터베이스 컨텍스트입니다. | sysname | 문자열 |
| database_principal_id | database_principal_id_d | 작업이 수행 되는 데이터베이스 사용자 컨텍스트의 ID입니다. | int | int |
| database_principal_name | database_principal_name_s | 작업이 수행 되는 데이터베이스 사용자 컨텍스트의 이름 | sysname | 문자열 |
| duration_milliseconds | duration_milliseconds_d | 쿼리 실행 기간 (밀리초) | bigint | int |
| event_time | event_time_t | 감사 가능한 동작이 발생 한 날짜 및 시간 | datetime2 | Datetime |
| host_name | N/A | 클라이언트 호스트 이름 | 문자열 | N/A |
| is_column_permission | is_column_permission_s | 열 수준 사용 권한임을 나타내는 플래그입니다. 1 = true, 0 = false | bit | 문자열 |
| N/A | is_server_level_audit_s | 이 감사가 서버 수준에 있는지를 나타내는 플래그입니다. | N/A | 문자열 |
| object_ id | object_id_d | 감사가 수행된 대상 엔터티의 ID입니다. 여기에는 서버 개체, 데이터베이스, 데이터베이스 개체, 스키마 개체 등이 포함 됩니다. 엔터티가 서버 자체 이거나 개체 수준에서 감사가 수행 되지 않는 경우 0입니다. | int | int |
| object_name | object_name_s | 감사가 수행된 대상 엔터티의 이름입니다. 여기에는 서버 개체, 데이터베이스, 데이터베이스 개체, 스키마 개체 등이 포함 됩니다. 엔터티가 서버 자체 이거나 개체 수준에서 감사가 수행 되지 않는 경우 0입니다. | sysname | 문자열 |
| permission_bitmask | permission_bitmask_s | 해당되는 경우 부여, 거부 또는 취소된 사용 권한을 표시합니다. | varbinary(16) | 문자열 |
| response_rows | response_rows_d | 결과 집합에 반환 된 행 수 | bigint | int |
| schema_name | schema_name_s | 동작이 수행된 스키마 컨텍스트입니다. 스키마 외부에서 발생 하는 감사의 경우 NULL | sysname | 문자열 |
| N/A | securable_class_type_s | 감사 중인 class_type에 매핑되는 보안 개체입니다. | N/A | 문자열 |
| sequence_group_id | sequence_group_id_g | 고유 식별자 | varbinary | GUID |
| sequence_number | sequence_number_d | 너무 커서 감사에 대 한 쓰기 버퍼에 맞지 않는 단일 감사 레코드 내의 레코드 시퀀스를 추적 합니다. | int | int |
| server_instance_name | server_instance_name_s | 감사가 수행 된 서버 인스턴스의 이름입니다. | sysname | 문자열 |
| server_principal_id | server_principal_id_d | 작업이 수행 되는 로그인 컨텍스트의 ID입니다. | int | int |
| server_principal_name | server_principal_name_s | 현재 로그인 | sysname | 문자열 |
| server_principal_sid | server_principal_sid_s | 현재 로그인 SID | varbinary | 문자열 |
| session_id | session_id_d | 이벤트가 발생 한 세션의 ID입니다. | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 세션에 대 한 서버 보안 주체 | sysname | 문자열 |
| statement | statement_s | 실행 된 t-sql 문 (있는 경우) | nvarchar(4000) | 문자열 |
| succeeded | succeeded_s | 이벤트를 발생시킨 동작의 성공 여부를 나타냅니다. 로그인 및 일괄 처리 외의 이벤트의 경우에는이 작업을 수행 하는 것이 아니라 권한 검사의 성공 또는 실패 여부를 보고 합니다. 1 = 성공, 0 = 실패 | bit | 문자열 |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 데이터베이스 보안 주체입니다. 적용 되지 않는 경우 0 | int | int |
| target_database_principal_name | target_database_principal_name_s | 동작의 대상 사용자입니다. 적용할 수 없는 경우 NULL입니다. | 문자열 | 문자열 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 서버 보안 주체입니다. 해당 하지 않는 경우 0을 반환 합니다. | int | int |
| target_server_principal_name | target_server_principal_name_s | 동작의 대상 로그인입니다. 적용할 수 없는 경우 NULL입니다. | sysname | 문자열 |
| target_server_principal_sid | target_server_principal_sid_s | 대상 로그인의 SID입니다. 적용할 수 없는 경우 NULL입니다. | varbinary | 문자열 |
| transaction_id | transaction_id_d | SQL Server (2016부터)-Azure SQL DB의 경우 0입니다. | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Sp_audit_write에 인수로 전달 되는 사용자 정의 이벤트 id입니다. 시스템 이벤트 (기본값)의 경우 NULL이 고 사용자 정의 이벤트의 경우 0이 아닙니다. 자세한 내용은 [sp_audit_write (transact-sql)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 를 참조 하세요. | smallint | int |
| user_defined_information | user_defined_information_s | Sp_audit_write에 인수로 전달 되는 사용자 정의 정보입니다. 시스템 이벤트 (기본값)의 경우 NULL이 고 사용자 정의 이벤트의 경우 0이 아닙니다. 자세한 내용은 [sp_audit_write (transact-sql)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) 를 참조 하세요. | nvarchar(4000) | 문자열 |

## <a name="next-steps"></a>다음 단계

[Azure SQL Database 감사](sql-database-auditing.md)에 대해 자세히 알아보세요.
