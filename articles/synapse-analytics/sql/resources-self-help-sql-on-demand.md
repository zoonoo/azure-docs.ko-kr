---
title: 서버리스 SQL 풀(미리 보기) 자가 진단
description: 이 섹션에는 서버리스 SQL 풀(미리 보기)의 문제를 해결하는 데 도움이 되는 정보가 포함되어 있습니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 9753fc491cb5950d679ae3633a18cdd5c1170291
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317293"
---
# <a name="self-help-for-serverless-sql-pool-preview"></a>서버리스 SQL 풀(미리 보기)에 대한 자가 진단

이 문서에는 Azure Synapse Analytics에서 서버리스 SQL 풀(미리 보기)의 자주 발생하는 문제를 해결하는 방법에 대한 정보가 포함되어 있습니다.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Synapse Studio에서 서버리스 SQL 풀이 회색으로 표시됨

Synapse Studio가 서버리스 SQL 풀에 대한 연결을 설정할 수 없는 경우 서버리스 SQL 풀이 회색으로 표시되거나 "오프라인" 상태로 표시됩니다. 일반적으로 이 문제는 다음 중 하나에 해당할 때 발생합니다.

1) 네트워크에서 Azure Synapse 백 엔드와의 통신을 차단합니다. 대부분 1443 포트가 차단됩니다. 서버리스 SQL 풀을 작동시키려면 이 포트의 차단을 해제합니다. 다른 문제로 인해 서버리스 SQL 풀도 작동하지 않을 수 있습니다. [자세한 내용은 전체 문제 해결 가이드를 참조하세요](../troubleshoot/troubleshoot-synapse-studio.md).
2) 서버리스 SQL 풀에 로그인할 수 있는 권한이 없습니다. 액세스 권한을 얻으려면 Azure Synapse 작업 영역 관리자 중 한 명이 사용자를 작업 영역 관리자 또는 SQL 관리자 역할에 추가해야 합니다. [자세한 내용은 액세스 제어에 대한 전체 가이드를 참조하세요](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>파일을 열 수 없어 쿼리가 실패

'파일이 없거나 다른 프로세스에서 사용되고 있어서 파일을 열 수 없습니다'라는 내용의 오류 메시지와 함께 쿼리가 실패하고, 두 파일이 모두 존재하며 다른 프로세스에서 사용되고 있지 않다는 것이 확실한 경우에는 서버리스 SQL 풀이 파일에 액세스할 수 없다는 의미입니다. 이 문제는 일반적으로 Azure Active Directory ID가 파일에 액세스할 수 있는 권한이 없기 때문에 발생합니다. 기본적으로 서버리스 SQL 풀은 Azure Active Directory ID를 사용하여 파일에 액세스하려고 시도합니다. 이 이슈를 해결하려면 파일에 액세스할 수 있는 적절한 권한이 있어야 합니다. 가장 쉬운 방법은 쿼리하려는 스토리지 계정에 대한 'Storage Blob 데이터 기여자' 역할을 자신에게 부여하는 것입니다. [자세한 내용은 스토리지에 대한 Azure Active Directory 액세스 제어 전체 가이드를 참조하세요](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>현재 리소스 제약 조건으로 인해 쿼리를 실행할 수 없어 쿼리가 실패 

'현재 리소스 제약 조건으로 인해 이 쿼리를 실행할 수 없습니다.'라는 오류 메시지와 함께 쿼리가 실패하는 경우에는 리소스 제약 조건으로 인해 현재 서버리스 SQL 풀이 쿼리를 실행할 수 없음을 의미입니다. 

- 적절한 크기의 데이터 형식이 사용되는지 확인합니다. 또한 문자열 열의 Parquet 파일에 대한 스키마를 지정하세요. 기본적으로 VARCHAR(8000)입니다. 

- 쿼리 대상이 CSV 파일인 경우 [통계 만들기](develop-tables-statistics.md#statistics-in-serverless-sql-pool-preview)를 고려해 보세요. 

- 쿼리를 최적화하려면 [서버리스 SQL 풀의 성능 모범 사례](best-practices-sql-on-demand.md)를 참조하세요.  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE 'STATEMENT'는 마스터 데이터베이스에서 지원되지 않습니다.

다음 오류 메시지와 함께 쿼리가 실패하는 경우:

> '쿼리를 실행하지 못했습니다. 오류: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT은 마스터 데이터베이스에서 지원되지 않습니다.' 

즉, 서버리스 SQL 풀의 마스터 데이터베이스는 다음 생성을 지원하지 않습니다.
  - 외부 테이블
  - 외부 데이터 원본
  - 데이터베이스 범위 자격 증명
  - 외부 파일 형식

해결 방법:

  1. 사용자 데이터베이스를 만듭니다.

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. 마스터 데이터베이스에 대해 이전에 실패한 <DATABASE_NAME> 컨텍스트에서 create 문을 실행합니다. 
  
  외부 파일 형식 생성 예:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>다음 단계

다음 문서를 검토하여 서버리스 SQL 풀을 사용하는 방법에 대해 자세히 알아보세요.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)

- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)

- [특정 파일 쿼리](query-specific-files.md)

- [Parquet 파일 쿼리](query-parquet-files.md)

- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)

- [JSON 파일 쿼리](query-json-files.md)

- [보기 만들기 및 사용](create-use-views.md)

- [외부 테이블 만들기 및 사용](create-use-external-tables.md)

- [스토리지에 쿼리 결과 저장](create-external-table-as-select.md)
