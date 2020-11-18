---
title: 지원 되는 버전-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL 단일 서버에서 지원 되는 Postgres 주 버전 및 부 버전을 설명 합니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658522"
---
# <a name="supported-postgresql-major-versions"></a>지원 되는 PostgreSQL 주 버전

지원 정책에 대 한 자세한 내용은 [Azure Database for PostgreSQL 버전 관리 정책](concepts-version-policy.md) 을 참조 하세요.

Azure Database for PostgreSQL 현재 다음 주 버전을 지원 합니다.

## <a name="postgresql-version-11"></a>PostgreSQL 버전 11
현재 부 릴리스는 11.6입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-6.html) 를 참조 하세요.

## <a name="postgresql-version-10"></a>PostgreSQL 버전 10
현재 부 릴리스는 10.11입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/10/static/release-10-11.html) 를 참조 하세요.

## <a name="postgresql-version-96"></a>PostgreSQL 버전 9.6
현재 부 버전은 9.6.16입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) 를 참조 하세요.

## <a name="postgresql-version-95"></a>PostgreSQL 버전 9.5
현재 부 버전은 9.5.20입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) 를 참조 하세요.

> [!NOTE]
> Postgres 커뮤니티 [버전 관리 정책과](https://www.postgresql.org/support/versioning/)맞추고, Azure Database for PostgreSQL는 2021 년 2 월 11 일에 postgres 버전 9.5을 사용 중지 합니다. 자세한 내용과 제한 사항은 [Azure Database for PostgreSQL 버전 관리 정책](concepts-version-policy.md) 을 참조 하세요.

## <a name="managing-upgrades"></a>업그레이드 관리
PostgreSQL 프로젝트는 보고 된 버그를 수정 하기 위해 부분적 릴리스를 정기적으로 실행 합니다. 서비스의 월간 배포 중에 보조 릴리스로 서버를 자동으로 패치 하는 Azure Database for PostgreSQL. 

주 버전에 대 한 자동 전체 업그레이드는 지원 되지 않습니다. 다음 주 버전으로 업그레이드 하려면 다음을 수행할 수 있습니다. 
   * [덤프 및 복원을 사용 하 여 주 버전 업그레이드](./how-to-upgrade-using-dump-and-restore.md) 를 수행 하는 다양 한 방법을 참조 하세요.
   * [Pg_dump 및 pg_restore](./howto-migrate-using-dump-and-restore.md) 를 사용 하 여 새 엔진 버전으로 만든 서버로 데이터베이스를 이동 합니다.
   * 또는 [Azure Database Migration service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) 를 사용 하 여 PostgreSQL 10에서 11로 업그레이드할 수 있습니다.

### <a name="version-syntax"></a>버전 구문
버전 10을 PostgreSQL 하기 전에 [PostgreSQL 버전 관리 정책은](https://www.postgresql.org/support/versioning/) 첫 번째 _또는_ 두 번째 숫자의 증가를 위해 _주 버전_ 업그레이드로 간주 됩니다. 예를 들어 9.5 ~ 9.6은 _주_ 버전 업그레이드로 간주 되었습니다. 버전 10부터 첫 번째 번호의 변경 내용만 주 버전 업그레이드로 간주 됩니다. 예를 들어 10.0에서 10.1은 _부_ 릴리스 업그레이드입니다. 버전 10 ~ 11은 _주_ 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
지원 되는 PostgreSQL 확장에 대 한 자세한 내용은 [extensions 문서](concepts-extensions.md)를 참조 하세요.
