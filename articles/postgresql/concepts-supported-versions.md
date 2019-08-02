---
title: Azure Database for PostgreSQL-단일 서버에서 지원 되는 버전
description: Azure Database for PostgreSQL 단일 서버에서 지원 되는 버전을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551360"
---
# <a name="supported-postgresql-database-versions"></a>지원되는 PostgreSQL 데이터베이스 버전
Microsoft는 Azure Database for PostgreSQL 단일 서버에서 n-2 버전의 PostgreSQL 엔진을 지원 합니다. 버전은 Azure(n)의 현재 주요 버전과 두 개의 이전 주요 버전(-2)이 됩니다.

PostgreSQL용 Azure 데이터베이스는 현재 다음 버전을 지원합니다.

## <a name="postgresql-version-112"></a>PostgreSQL 버전 11.2
이 부 버전의 향상된 기능 및 수정 내용에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/11/static/release-11-2.html)를 참조하세요.

## <a name="postgresql-version-107"></a>PostgreSQL 버전 10.7
이 부 버전의 향상된 기능 및 수정 내용에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/10/static/release-10-7.html)를 참조하세요.

## <a name="postgresql-version-9612"></a>PostgreSQL 버전 9.6.12
이 부 버전의 향상된 기능 및 수정 내용에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)를 참조하세요.

## <a name="postgresql-version-9516"></a>PostgreSQL 버전 9.5.16
이 부 버전의 향상된 기능 및 수정 내용에 대한 자세한 내용은 [PostgreSQL 설명서](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)를 참조하세요.

## <a name="managing-updates-and-upgrades"></a>업데이트 및 업그레이드 관리
Azure Database for PostgreSQL는 부 버전 패치를 자동으로 관리합니다. 현재, 주 버전 업그레이드는 지원되지 않습니다. 예를 들어 PostgreSQL 9.5에서 PostgreSQL 9.6으로의 업그레이드는 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](./howto-migrate-using-dump-and-restore.md)하는 데이터베이스를 만듭니다.

> PostgreSQL 버전 10 이전에는 [PostgreSQL 버전 관리 정책](https://www.postgresql.org/support/versioning/) 에서 첫 번째 _또는_ 두 번째 숫자 (예: 9.5 ~ 9.6)가 주 버전 업그레이드로 간주 되는 _주 버전_ 업그레이드로 간주 됩니다.
> 버전 10부터 첫 번째 번호의 변경 내용만 주 버전 업그레이드로 간주 됩니다. 예를 들어 10.0에서 10.1은 _부_ 버전 업그레이드이 고 10 ~ 11은 _주_ 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
다른 PostgreSQL 확장 지원에 대한 자세한 내용은 [PostgreSQL 확장](concepts-extensions.md)을 참조하세요.
