---
title: SQL Database 감사 로그 형식
description: Azure SQL Database 감사 로그의 구성을 이해합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572224"
---
# <a name="sql-database-audit-log-format"></a>SQL Database 감사 로그 형식

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database 감사](auditing-overview.md)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 기록하거나 다운스트림 처리 및 분석을 위해 이벤트 허브 또는 Log Analytics로 보냅니다.

## <a name="naming-conventions"></a>명명 규칙

### <a name="blob-audit"></a>Blob 감사

Azure Blob Storage에 저장된 감사 로그는 Azure Storage 계정의 `sqldbauditlogs`라는 컨테이너에 저장됩니다. 컨테이너 내의 디렉터리 계층 구조는 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` 형식입니다. Blob 파일 이름 형식은 `<CreationTime>_<FileNumberInSession>.xel`이며, 여기서 `CreationTime`는 UTC `hh_mm_ss_ms` 형식이고 `FileNumberInSession`는 세션 로그가 여러 Blob 파일에 걸쳐 있는 경우에 실행 중인 인덱스입니다.

예를 들어 `Server1`의 `Database1` 데이터베이스에서 유효한 경로는 다음과 같습니다.

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

[읽기 전용 복제본](read-scale-out.md) 감사 로그는 동일한 컨테이너에 저장됩니다. 컨테이너 내의 디렉터리 계층 구조는 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` 형식입니다. Blob 파일 이름이 동일한 형식을 공유합니다. 읽기 전용 복제본의 감사 로그는 동일한 컨테이너에 저장됩니다.


### <a name="event-hub"></a>이벤트 허브

감사 이벤트는 감사 구성 중에 정의된 네임스페이스 및 이벤트 허브에 기록되고, [Apache Avro](https://avro.apache.org/) 이벤트의 본문에 캡처되고, UTF-8 인코딩과 함께 JSON 형식을 사용하여 저장됩니다. 감사 로그를 읽으려면 [Avro 도구](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

감사 이벤트는 감사 구성 중에 정의된 Log Analytics 작업 영역, 범주가 `SQLSecurityAuditEvents`인 `AzureDiagnostics` 테이블에 기록됩니다. Log Analytics 검색 언어 및 명령에 대한 유용한 추가 정보는 [Log Analytics 검색 참조](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="audit-log-fields"></a><a id="subheading-1"></a>감사 로그 파일

| 이름(Blob) | 이름(Event Hubs/Log Analytics) | 설명 | Blob 유형 | Event Hubs/Log Analytics 유형 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 동작의 ID입니다. | varchar(4) | 문자열 |
| action_name | action_name_s | 작업의 이름입니다. | 해당 없음 | 문자열 |
| additional_information | additional_information_s | 이벤트에 대한 추가 정보이며 XML로 저장됩니다. | nvarchar(4000) | 문자열 |
| affected_rows | affected_rows_d | 쿼리의 영향을 받는 행 수입니다. | bigint | int |
| application_name | application_name_s| 클라이언트 애플리케이션의 이름입니다. | nvarchar(128) | 문자열 |
| audit_schema_version | audit_schema_version_d | 항상 1 | int | int |
| class_type | class_type_s | 감사가 수행되는 감사 가능한 엔터티의 형식 | varchar(2) | 문자열 |
| class_type_desc | class_type_description_s | 감사가 수행되는 감사 가능한 엔터티에 대한 설명 | 해당 없음 | 문자열 |
| client_ip | client_ip_s | 클라이언트 애플리케이션의 소스 IP입니다. | nvarchar(128) | 문자열 |
| connection_id | 해당 없음 | 서버의 연결 ID입니다. | GUID | 해당 없음 |
| data_sensitivity_information | data_sensitivity_information_s | 데이터베이스의 분류된 열을 기반으로 감사된 쿼리에서 반환하는 정보 유형 및 민감도 레이블입니다. [Azure SQL Database 데이터 검색 및 분류](data-discovery-and-classification-overview.md) 에 대해 자세히 알아보세요. | nvarchar(4000) | 문자열 |
| database_name | database_name_s | 동작이 수행된 데이터베이스 컨텍스트입니다. | sysname | 문자열 |
| database_principal_id | database_principal_id_d | 동작을 수행한 데이터베이스 사용자 컨텍스트의 ID입니다. | int | int |
| database_principal_name | database_principal_name_s | 동작을 수행한 데이터베이스 사용자 컨텍스트의 이름입니다. | sysname | 문자열 |
| duration_milliseconds | duration_milliseconds_d | 쿼리 실행 시간(밀리초) | bigint | int |
| event_time | event_time_t | 감사 가능한 동작이 발생한 날짜 및 시간입니다. | datetime2 | Datetime |
| host_name | 해당 없음 | 클라이언트 호스트 이름 | 문자열 | 해당 없음 |
| is_column_permission | is_column_permission_s | 열 수준 사용 권한임을 나타내는 플래그입니다. 1 = true, 0 = false | bit | 문자열 |
| 해당 없음 | is_server_level_audit_s | 이 감사가 서버 수준인지를 나타내는 플래그입니다. | 해당 없음 | 문자열 |
| object_ id | object_id_d | 감사가 수행된 대상 엔터티의 ID입니다. 여기에는 서버 개체, 데이터베이스, 데이터베이스 개체, 스키마 개체가 포함됩니다. 엔터티가 서버 자체이거나 감사가 개체 수준에서 수행되지 않으면 0입니다. | int | int |
| object_name | object_name_s | 감사가 수행된 대상 엔터티의 이름입니다. 여기에는 서버 개체, 데이터베이스, 데이터베이스 개체, 스키마 개체가 포함됩니다. 엔터티가 서버 자체이거나 감사가 개체 수준에서 수행되지 않으면 0입니다. | sysname | 문자열 |
| permission_bitmask | permission_bitmask_s | 해당되는 경우 부여, 거부 또는 취소된 사용 권한을 표시합니다. | varbinary(16) | 문자열 |
| response_rows | response_rows_d | 결과 집합에 반환된 행 수입니다. | bigint | int |
| schema_name | schema_name_s | 동작이 수행된 스키마 컨텍스트입니다. 스키마 외부에서 수행되는 감사의 경우 NULL입니다. | sysname | 문자열 |
| 해당 없음 | securable_class_type_s | 감사할 class_type에 매핑되는 보안 개체입니다. | 해당 없음 | 문자열 |
| sequence_group_id | sequence_group_id_g | 고유 식별자 | varbinary | GUID |
| sequence_number | sequence_number_d | 너무 커서 감사에 대한 쓰기 버퍼에 맞지 않는 단일 감사 레코드 내의 레코드 시퀀스를 추적합니다. | int | int |
| server_instance_name | server_instance_name_s | 감사가 수행된 서버 인스턴스의 이름입니다. | sysname | 문자열 |
| server_principal_id | server_principal_id_d | 동작을 수행한 로그인 컨텍스트의 ID입니다. | int | int |
| server_principal_name | server_principal_name_s | 현재 로그인입니다. | sysname | 문자열 |
| server_principal_sid | server_principal_sid_s | 현재 로그인 SID입니다. | varbinary | 문자열 |
| session_id | session_id_d | 이벤트가 발생한 세션의 ID입니다. | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 세션에 대한 서버 보안 주체입니다. | sysname | 문자열 |
| statement | statement_s | 실행된 T-SQL 문입니다(있는 경우). | nvarchar(4000) | 문자열 |
| 성공 | succeeded_s | 이벤트를 발생시킨 동작의 성공 여부를 나타냅니다. 로그인 및 일괄 처리가 아닌 이벤트의 경우 작업이 아닌 권한 검사의 성공 또는 실패 여부만 보고합니다. 1 = 성공, 0 = 실패 | bit | 문자열 |
| target_database_principal_id | target_database_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 데이터베이스 보안 주체입니다. 적용되지 않는 경우 0입니다. | int | int |
| target_database_principal_name | target_database_principal_name_s | 동작의 대상 사용자입니다. 적용되지 않는 경우 NULL입니다. | 문자열 | 문자열 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 작업이 수행되는 서버 보안 주체입니다. 적용되지 않는 경우 0을 반환합니다. | int | int |
| target_server_principal_name | target_server_principal_name_s | 동작의 대상 로그인입니다. 적용되지 않는 경우 NULL입니다. | sysname | 문자열 |
| target_server_principal_sid | target_server_principal_sid_s | 대상 로그인의 SID입니다. 적용되지 않는 경우 NULL입니다. | varbinary | 문자열 |
| transaction_id | transaction_id_d | SQL Server만 해당(2016부터) - Azure SQL Database의 경우 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | sp_audit_write에 인수로 전달되는 사용자 정의 이벤트 ID입니다. 시스템 이벤트의 경우 NULL(기본값)이고 사용자 정의 이벤트의 경우 0이 아닌 값입니다. 자세한 내용은 [sp_audit_write(Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql)를 참조하세요. | smallint | int |
| user_defined_information | user_defined_information_s | sp_audit_write에 인수로 전달되는 사용자 정의 정보입니다. 시스템 이벤트의 경우 NULL(기본값)이고 사용자 정의 이벤트의 경우 0이 아닌 값입니다. 자세한 내용은 [sp_audit_write(Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql)를 참조하세요. | nvarchar(4000) | 문자열 |

## <a name="next-steps"></a>다음 단계

[Azure SQL Database 감사](auditing-overview.md)에 대해 자세히 알아봅니다.