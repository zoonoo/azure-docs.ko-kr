---
title: SQL 주문형(미리 보기) 자가 진단
description: 이 섹션에는 SQL 주문형(미리 보기)의 문제를 해결하는 데 도움이 되는 정보가 포함되어 있습니다.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421197"
---
# <a name="self-help-for-sql-on-demand-preview"></a>SQL 주문형(미리 보기) 자가 진단

이 문서에는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)의 자주 발생하는 문제를 해결하는 방법에 대한 정보가 포함되어 있습니다.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>Synapse Studio에서 SQL 주문형이 회색으로 표시됨

Synapse Studio가 SQL 주문형에 대한 연결을 설정할 수 없는 경우 SQL 주문형이 회색으로 표시되거나 "오프라인" 상태로 표시됩니다. 일반적으로 이 문제는 다음 중 하나에 해당할 때 발생합니다.

1) 네트워크에서 Azure Synapse 백 엔드와의 통신을 차단합니다. 대부분 1443 포트가 차단됩니다. SQL 주문형이 작동하려면 이 포트의 차단을 해제해야 합니다. 다른 문제로 인해 SQL 주문형이 작동하지 않을 수 있습니다. [자세한 내용은 전체 문제 해결 가이드를 참조하세요](../troubleshoot/troubleshoot-synapse-studio.md).
2) SQL 주문형에 로그인할 수 있는 권한이 없습니다. 액세스 권한을 얻으려면 Azure Synapse 작업 영역 관리자 중 한 명이 사용자를 작업 영역 관리자 또는 SQL 관리자 역할에 추가해야 합니다. [자세한 내용은 액세스 제어에 대한 전체 가이드를 참조하세요](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>파일을 열 수 없어 쿼리가 실패

'파일이 없거나 다른 프로세스에서 사용되고 있어서 파일을 열 수 없습니다'라는 내용의 오류 메시지와 함께 쿼리가 실패하고, 두 파일이 모두 존재하며 다른 프로세스에서 사용되고 있지 않다는 것이 확실한 경우에는 SQL 주문형이 파일에 액세스할 수 없다는 의미입니다. 이 문제는 일반적으로 Azure Active Directory ID가 파일에 액세스할 수 있는 권한이 없기 때문에 발생합니다. 기본적으로 SQL 주문형은 Azure Active Directory ID를 사용하여 파일에 액세스하려고 시도합니다. 이 이슈를 해결하려면 파일에 액세스할 수 있는 적절한 권한이 있어야 합니다. 가장 쉬운 방법은 쿼리하려는 스토리지 계정에 대한 'Storage Blob 데이터 기여자' 역할을 자신에게 부여하는 것입니다. [자세한 내용은 스토리지에 대한 Azure Active Directory 액세스 제어 전체 가이드를 참조하세요](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>현재 리소스 제약 조건으로 인해 쿼리를 실행할 수 없어 쿼리가 실패 

'현재 리소스 제약 조건으로 인해 이 쿼리를 실행할 수 없습니다'라는 내용의 오류 메시지와 함께 쿼리가 실패하는 경우에는 리소스 제약 조건으로 인해 현재 SQL OD가 이 쿼리를 실행할 수 없다는 의미입니다. 

- 적절한 크기의 데이터 형식이 사용되는지 확인하세요. 또한 문자열 열의 Parquet 파일에 대한 스키마를 지정하세요. 기본적으로 VARCHAR(8000)입니다. 

- 쿼리 대상이 CSV 파일인 경우 [통계 만들기](develop-tables-statistics.md#statistics-in-sql-on-demand-preview)를 고려해 보세요. 

- [SQL 주문형의 성능 모범 사례](best-practices-sql-on-demand.md)를 방문하여 쿼리를 최적화하세요.  

## <a name="next-steps"></a>다음 단계

다음 문서를 검토하여 SQL 주문형 사용 방법에 대해 자세히 알아보세요.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)

- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)

- [특정 파일 쿼리](query-specific-files.md)

- [Parquet 파일 쿼리](query-parquet-files.md)

- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)

- [JSON 파일 쿼리](query-json-files.md)

- [보기 만들기 및 사용](create-use-views.md)

- [외부 테이블 만들기 및 사용](create-use-external-tables.md)

- [스토리지에 쿼리 결과 저장](create-external-table-as-select.md)
