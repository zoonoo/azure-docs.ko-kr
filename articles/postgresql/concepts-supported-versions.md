---
title: 지원 되는 버전-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL 단일 서버에서 지원 되는 Postgres 주 버전 및 부 버전을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792225"
---
# <a name="supported-postgresql-major-versions"></a>지원 되는 PostgreSQL 주 버전
Microsoft는 Azure Database for PostgreSQL 단일 서버에서 n-2 버전의 PostgreSQL 엔진을 지원 합니다. 버전은 Azure(n)의 현재 주요 버전과 두 개의 이전 주요 버전(-2)이 됩니다.

Azure Database for PostgreSQL 현재 다음 주 버전을 지원 합니다.

## <a name="postgresql-version-11"></a>PostgreSQL 버전 11
현재 부 릴리스는 11.5입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-5.html) 를 참조 하세요.

## <a name="postgresql-version-10"></a>PostgreSQL 버전 10
현재 부 릴리스는 10.10입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/10/static/release-10-10.html) 를 참조 하세요.

## <a name="postgresql-version-96"></a>PostgreSQL 버전 9.6
현재 부 버전은 9.6.15입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) 를 참조 하세요.

## <a name="postgresql-version-95"></a>PostgreSQL 버전 9.5
현재 부 버전은 9.5.19입니다. 이 부 릴리스의 개선 사항 및 수정 내용에 대 한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) 를 참조 하세요.

## <a name="managing-upgrades"></a>업그레이드 관리
PostgreSQL 프로젝트는 보고 된 버그를 수정 하기 위해 부분적 릴리스를 정기적으로 실행 합니다. 서비스의 월간 배포 중에 보조 릴리스로 서버를 자동으로 패치 하는 Azure Database for PostgreSQL. 

자동 주 버전 업그레이드는 지원되지 않습니다. 예를 들어 PostgreSQL 9.5에서 PostgreSQL 9.6으로 자동 업그레이드되지 않습니다. 다음의 주 버전으로 업그레이드하려면 새 엔진 버전을 사용하여 만든 서버에 [데이터베이스 덤프 및 복원](./howto-migrate-using-dump-and-restore.md)을 만듭니다.

### <a name="version-syntax"></a>버전 구문
버전 10을 PostgreSQL 하기 전에 [PostgreSQL 버전 관리 정책은](https://www.postgresql.org/support/versioning/) 첫 번째 _또는_ 두 번째 숫자의 증가를 위해 _주 버전_ 업그레이드로 간주 됩니다. 예를 들어 9.5 ~ 9.6은 _주_ 버전 업그레이드로 간주 되었습니다. 버전 10부터 첫 번째 번호의 변경 내용만 주 버전 업그레이드로 간주 됩니다. 예를 들어 10.0에서 10.1은 _부_ 릴리스 업그레이드입니다. 버전 10 ~ 11은 _주_ 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
지원 되는 PostgreSQL 확장에 대 한 자세한 내용은 [extensions 문서](concepts-extensions.md)를 참조 하세요.
