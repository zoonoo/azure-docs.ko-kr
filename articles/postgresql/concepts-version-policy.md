---
title: 버전 관리 정책-Azure Database for PostgreSQL-단일 서버 및 유연한 서버 (미리 보기)
description: Azure Database for PostgreSQL 단일 서버에서 Postgres 주 버전 및 부 버전에 대 한 정책을 설명 합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f92864cea4332157b0bf8b171a9d88b34f79a5ac
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422113"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL 버전 관리 정책

이 페이지에서는 Azure Database for PostgreSQL 버전 관리 정책에 대해 설명 하 고 Azure Database for PostgreSQL 단일 서버 및 Azure Database for PostgreSQL 유연한 서버 (미리 보기) 배포 모드에 적용 됩니다.

## <a name="supported--postgresql-versions"></a>지원 되는 PostgreSQL 버전

Azure Database for PostgreSQL는 다음 데이터베이스 버전을 지원 합니다.

| 버전 | 단일 서버 | 유연한 서버(미리 보기) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9.6 | X |  |
| PostgreSQL 9.5 | X |  |

## <a name="major-version-support"></a>주 버전 지원
PostgreSQL의 각 주 버전은 [PostgreSQL 커뮤니티 버전 관리 정책](https://www.postgresql.org/support/versioning/)에서 제공 하는 것과 같이 PostgreSQL 커뮤니티에서 버전을 사용할 수 없을 때까지 Azure에서 버전을 지원 하기 시작 하는 날짜의 Azure Database for PostgreSQL에 의해 지원 됩니다.

## <a name="minor-version-support"></a>부 버전 지원
정기적 유지 관리의 일환으로 Azure 기본 PostgreSQL 버전으로 부 버전 업그레이드를 자동으로 수행 Azure Database for PostgreSQL 합니다. 

## <a name="major-version-retirement-policy"></a>주 버전 사용 중지 정책
다음 표에서는 PostgreSQL 주 버전에 대 한 사용 중지 정보를 제공 합니다. 이 날짜는 [PostgreSQL 커뮤니티 버전 관리 정책](https://www.postgresql.org/support/versioning/)을 따릅니다.

| 버전 | What's New | Azure 지원 시작 날짜 | 사용 중지 날짜|
| ----- | ----- | ------ | ----- |
| PostgreSQL 9.5| [기능](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018년 4월 18일    | 2021 년 2 월 11 일
| [PostgreSQL 9.6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [기능](https://wiki.postgresql.org/wiki/NewIn96) | 2018년 4월 18일  | 2021 년 11 월 11 일
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [기능](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018년 6월 4일  | 2022 년 11 월 10 일
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [기능](https://www.postgresql.org/docs/11/release-11.html) | 2019년 7월 24일  | 2023 년 11 월 9 일
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [기능](https://www.postgresql.org/docs/12/release-12.html) | 2020 년 9 월 22 일  | 2024 년 11 월 14 일

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>사용 중지 된 PostgreSQL 엔진 버전은 Azure Database for PostgreSQL에서 지원 되지 않습니다.

각 PostgreSQL 데이터베이스 버전에 대 한 사용 중지 날짜 이후에는 사용 중지 된 버전을 계속 실행 하는 경우 다음 제한 사항을 확인 하세요.
- 커뮤니티는 추가 버그 수정 또는 보안 수정 사항을 릴리스할 수 없으므로 PostgreSQL 용 Azure는 버그 또는 보안 문제에 대 한 사용 중지 된 데이터베이스 엔진을 패치 하거나 사용 중지 된 데이터베이스 엔진과 관련 하 여 보안 조치를 취합니다. 따라서 보안 취약성이 나 기타 문제가 발생할 수 있습니다. 그러나 Azure는 호스트, OS, 컨테이너 및 기타 서비스 관련 구성 요소에 대해 정기적으로 유지 관리 및 패치를 계속 수행 합니다.
- PostgreSQL 데이터베이스와 관련 하 여 발생할 수 있는 지원 문제가 있는 경우 사용자에 게 지원을 제공 하지 못할 수 있습니다. 이러한 경우에는 지원을 제공 하기 위해 데이터베이스를 업그레이드 해야 합니다.
- 사용 중지 된 버전에 대해서는 새 데이터베이스 서버를 만들 수 없습니다. 그러나 지정 시간 복구를 수행 하 고 기존 서버에 대 한 읽기 복제본을 만들 수 있습니다.
- Azure Database for PostgreSQL에서 개발한 새 서비스 기능은 지원 되는 데이터베이스 서버 버전 에서만 사용할 수 있습니다.
- 작동 시간 Sla는 Azure Database for PostgreSQL 서비스 관련 문제에만 적용 되며 데이터베이스 엔진 관련 버그로 인 한 가동 중지 시간에는 적용 되지 않습니다.  
- 사용 중지 된 데이터베이스 버전에서 식별 된 PostgreSQL 데이터베이스 엔진 취약성으로 인해 발생 하는 서비스에 심각한 위협이 발생 하는 극단적인 경우 Azure는 데이터베이스 서버의 계산 노드를 중지 하 여 서비스를 보호 하도록 선택할 수 있습니다. 이러한 경우 서버를 온라인 상태로 전환 하기 전에 서버를 업그레이드 한다는 알림이 표시 될 수 있습니다.

## <a name="postgresql-version-syntax"></a>PostgreSQL version 구문
버전 10을 PostgreSQL 하기 전에 [PostgreSQL 버전 관리 정책은](https://www.postgresql.org/support/versioning/) 첫 번째 _또는_ 두 번째 숫자의 증가를 위해 _주 버전_ 업그레이드로 간주 됩니다. 예를 들어 9.5 ~ 9.6은 _주_ 버전 업그레이드로 간주 되었습니다. 버전 10부터 첫 번째 번호의 변경 내용만 주 버전 업그레이드로 간주 됩니다. 예를 들어 10.0에서 10.1은 _부_ 릴리스 업그레이드입니다. 버전 10 ~ 11은 _주_ 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL-단일 서버 [지원 버전](./concepts-supported-versions.md) 을 참조 하세요.
- Azure Database for PostgreSQL-유연한 서버 (미리 보기) [지원 버전](flexible-server/concepts-supported-versions.md) 을 참조 하세요.
- 주 버전 업그레이드를 수행 하는 방법에 대 한 자세한 내용은 [주 버전 업그레이드](how-to-upgrade-using-dump-and-restore.md) 설명서를 참조 하세요.
- 지원 되는 PostgreSQL 확장에 대 한 자세한 내용은 [extensions 문서](concepts-extensions.md)를 참조 하세요.
