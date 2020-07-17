---
title: '알려진 문제: Oracle에서 Azure Database for PostgreSQL로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 Oracle에서 Azure Database for PostgreSQL-단일 서버로의 온라인 마이그레이션 시 알려진 문제와 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 05/20/2020
ms.openlocfilehash: 2cf8ff446fe3441fc039ef3c2afef6308224666f
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701211"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Oracle에서 Azure DB for PostgreSQL-단일 서버로의 온라인 마이그레이션 시 알려진 문제/마이그레이션 제한 사항

다음 섹션에서는 Oracle에서 Azure Database for PostgreSQL-단일 서버로의 온라인 마이그레이션과 관련된 알려진 문제와 제한 사항에 대해 설명합니다.

## <a name="oracle-versions-supported-as-a-source-database"></a>원본 데이터베이스로 지원되는 Oracle 버전

Azure Database Migration Service는 다음에 대한 연결을 지원합니다.

- Oracle 버전 10g, 11g, 12c
- Oracle Enterprise, Standard, Express 및 Personal Edition

Azure Database Migration Service는 CDB(다중 테넌트 컨테이너 데이터베이스)에 대한 연결을 지원하지 않습니다.

## <a name="postgresql-versions-supported-as-a-target-database"></a>대상 데이터베이스로 지원되는 PostgreSQL 버전

Azure Database Migration Service는 Azure Database for PostgreSQL-단일 서버 버전 9.5, 9.6, 10, 11로의 마이그레이션을 지원합니다. Azure Database for PostgreSQL-단일 서버의 버전 지원에 대한 최신 정보는 [지원되는 PostgreSQL 데이터베이스 버전](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 문서를 참조하세요.

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

다음 데이터 형식은 마이그레이션되지 **않습니다**.

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 중첩 테이블
- 사용자 정의 데이터 형식
- 메모
- 가상 열
- ROWID 열을 기반으로 구체화된 뷰

또한 빈 BLOB/CLOB 열은 대상에서 NULL로 매핑됩니다.

## <a name="lob-limitations"></a>LOB 제한 사항

- 제한된 크기의 LOB 모드를 사용하면 Oracle 원본의 빈 LOB가 NULL 값으로 복제됩니다.
- 긴 개체 이름(30바이트 초과)은 지원되지 않습니다.
- LONG 및 LONG RAW 열의 데이터는 64k를 초과할 수 없습니다. 64k가 넘는 데이터는 잘립니다.
- Oracle 12에서만 LOB 열에 대한 변경 내용이 지원되지 않습니다(마이그레이션됨).
- XMLTYPE 및 LOB 열에 대한 업데이트는 지원되지 않습니다(마이그레이션됨).

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

- 사용자는 Oracle Server에 대한 DBA 권한을 보유하고 있어야 합니다.
- 파티션/하위 파티션 작업(ADD, DROP, EXCHANGE 및 TRUNCATE)으로 인한 데이터 변경 내용은 마이그레이션되지 않고 다음과 같은 오류가 발생할 수 있습니다.
  - ADD 작업의 경우 추가된 데이터에 대한 업데이트/삭제 시 "0개 행 적용됨" 경고를 반환할 수 있습니다.
  - DROP 및 TRUNCATE 작업의 경우 새로 삽입하면 "중복" 오류가 발생할 수 있습니다.
  - EXCHANGE 작업의 경우 "0개 행 적용됨" 경고 및 "중복" 오류가 모두 발생할 수 있습니다.
- 이름에 아포스트로피가 포함된 테이블은 복제할 수 없습니다.
