---
title: 지원되는 버전 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: PostgreSQL - 단일 서버에 대한 Azure 데이터베이스에서 지원되는 Postgres 주 및 부 버전에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792225"
---
# <a name="supported-postgresql-major-versions"></a>지원되는 PostgreSQL 주 버전
마이크로소프트는 PostgreSQL에 대 한 Azure 데이터베이스에서 PostgreSQL 엔진의 n-2 버전을 지원 하는 것을 목표로 합니다- 단일 서버. 버전은 Azure(n)의 현재 주요 버전과 두 개의 이전 주요 버전(-2)이 됩니다.

PostgreSQL용 Azure 데이터베이스는 현재 다음과 같은 주요 버전을 지원합니다.

## <a name="postgresql-version-11"></a>포스트그레SQL 버전 11
현재 마이너 릴리스는 11.5입니다. 이 부릴리스의 개선 사항 및 수정 사항에 대해 자세히 알아보려면 [PostgreSQL 설명서를](https://www.postgresql.org/docs/11/static/release-11-5.html) 참조하십시오.

## <a name="postgresql-version-10"></a>포스트그레SQL 버전 10
현재 마이너 릴리스는 10.10입니다. 이 부릴리스의 개선 사항 및 수정 사항에 대해 자세히 알아보려면 [PostgreSQL 설명서를](https://www.postgresql.org/docs/10/static/release-10-10.html) 참조하십시오.

## <a name="postgresql-version-96"></a>포스트그레SQL 버전 9.6
현재 마이너 릴리스는 9.6.15입니다. 이 부릴리스의 개선 사항 및 수정 사항에 대해 자세히 알아보려면 [PostgreSQL 설명서를](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) 참조하십시오.

## <a name="postgresql-version-95"></a>포스트그레SQL 버전 9.5
현재 마이너 릴리스는 9.5.19입니다. 이 부릴리스의 개선 사항 및 수정 사항에 대해 알아보려면 [PostgreSQL 설명서를](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) 참조하십시오.

## <a name="managing-upgrades"></a>업그레이드 관리
PostgreSQL 프로젝트는 보고된 버그를 수정하기 위해 정기적으로 사소한 릴리스를 발행합니다. PostgreSQL용 Azure 데이터베이스는 서비스의 월별 배포 중에 부릴리스가 있는 서버를 자동으로 패치합니다. 

자동 주 버전 업그레이드는 지원되지 않습니다. 예를 들어 PostgreSQL 9.5에서 PostgreSQL 9.6으로 자동 업그레이드되지 않습니다. 다음의 주 버전으로 업그레이드하려면 새 엔진 버전을 사용하여 만든 서버에 [데이터베이스 덤프 및 복원](./howto-migrate-using-dump-and-restore.md)을 만듭니다.

### <a name="version-syntax"></a>버전 구문
PostgreSQL 버전 10 이전에 [PostgreSQL 버전 정책은](https://www.postgresql.org/support/versioning/) _주요 버전_ 업그레이드를 첫 번째 _또는_ 두 번째 숫자의 증가로 간주했습니다. 예를 들어 9.5에서 9.6까지는 _주요_ 버전 업그레이드로 간주되었습니다. 버전 10에서는 첫 번째 숫자의 변경만 주 버전 업그레이드로 간주됩니다. 예를 들어 10.0에서 10.1은 _마이너_ 릴리스 업그레이드입니다. 버전 10에서 11까지는 _주요_ 버전 업그레이드입니다.

## <a name="next-steps"></a>다음 단계
지원되는 PostgreSQL 확장에 대한 자세한 내용은 [확장 문서를](concepts-extensions.md)참조하십시오.
