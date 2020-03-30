---
title: '알려진 문제: PostgreSQL에 대한 오라클에서 Azure 데이터베이스로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure 데이터베이스 마이그레이션 서비스를 사용하여 Oracle에서 PostgreSQL-Single 서버용 Azure 데이터베이스로의 온라인 마이그레이션을 통해 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235255"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>PostgreSQL-Single 서버의 Oracle에서 Azure DB로의 온라인 마이그레이션으로 알려진 문제/마이그레이션 제한

Oracle에서 PostgreSQL-Single 서버용 Azure 데이터베이스로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="oracle-versions-supported-as-a-source-database"></a>원본 데이터베이스로 지원되는 오라클 버전

Azure 데이터베이스 마이그레이션 서비스는 다음에 대한 연결지원을 지원합니다.

- 오라클 버전 10g, 11g 및 12c.
- 오라클 엔터프라이즈, 스탠다드, 익스프레스 및 퍼스널 에디션.

Azure 데이터베이스 마이그레이션 서비스는 다중 테넌트 컨테이너 데이터베이스(CDB)에 대한 연결은 지원하지 않습니다.

## <a name="postgresql-versions-supported-as-a-target-database"></a>대상 데이터베이스로 지원되는 PostgreSQL 버전

Azure 데이터베이스 마이그레이션 서비스는 PostgreSQL-Single 서버 버전 9.5, 9.6, 10 및 11에 대한 Azure 데이터베이스로의 마이그레이션을 지원합니다. PostgreSQL-단일 서버에 대한 Azure 데이터베이스의 버전 지원에 대한 최신 정보는 [지원되는 PostgreSQL 데이터베이스 버전을](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) 참조하십시오.

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

다음 데이터 **형식은 마이그레이션되지 않습니다.**

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
- ROWID 열을 기반으로 한 구체화된 뷰

또한 빈 BLOB/CLOB 열은 대상에서 NULL로 매핑됩니다.

## <a name="lob-limitations"></a>LOB 제한 사항

- 제한된 크기의 LOB 모드를 사용하도록 설정하면 Oracle 소스의 빈 LOB가 NULL 값으로 복제됩니다.
- 긴 개체 이름(30바이트 이상)은 지원되지 않습니다.
- LONG 및 LONG RAW 열의 데이터는 64k를 초과할 수 없습니다. 64k를 초과하는 모든 데이터는 잘립니다.
- Oracle 12에서만 LOB 열에 대한 변경 내용은 지원되지 않습니다(마이그레이션).
- XMLTYPE 및 LOB 열에 대한 UPDAT는 지원되지 않습니다(마이그레이션).

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

- 고객은 오라클에 연결하려면 SYSDBA를 사용해야 합니다.
- 파티션/하위 파티션 작업(ADD, DROP, EXCHANGE 및 TRUNCATE)으로 인한 데이터 변경은 마이그레이션되지 않으며 다음과 같은 오류가 발생할 수 있습니다.
  - ADD 작업의 경우 추가된 데이터에 대한 업데이트 및 삭제가 "영향을 받는 0행" 경고를 반환할 수 있습니다.
  - DROP 및 TRUNCATE 작업의 경우 새 삽입으로 인해 "중복" 오류가 발생할 수 있습니다.
  - EXCHANGE 작업의 경우 "영향을 받는 0행" 경고와 "중복" 오류가 모두 발생할 수 있습니다.
- 아포스트로피를 포함하는 이름은 복제할 수 없습니다.
