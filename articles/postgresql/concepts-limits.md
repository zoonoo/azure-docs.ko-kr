---
title: Azure Database for PostgreSQL-단일 서버에에서 대 한 제한
description: 이 문서에는 PostgreSQL-연결 수 및 저장소 엔진 옵션과 같은 단일 서버에 대 한 Azure Database에서 제한을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: e4752112acf136d9ffb19a0b7383bc3aff5de5e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448095"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-단일 서버에에서 대 한 제한
다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다. 리소스 (계산, 메모리, 저장소) 계층에 대해 자세히 알아보려면 원하는 참조를 [가격 책정 계층](concepts-pricing-tiers.md) 문서.


## <a name="maximum-connections"></a>최대 연결 수
가격 책정 계층 및 vCore당 최대 연결 수는 다음과 같습니다. 

|**가격 책정 계층**| **vCore**| **최대 연결** |
|---|---|---|
|기본| 1| 50 |
|기본| 2| 100 |
|범용| 2| 150|
|범용| 4| 250|
|범용| 8| 480|
|범용| 16| 950|
|범용| 32| 1500|
|범용| 64| 1900|
|메모리 최적화| 2| 300|
|메모리 최적화| 4| 500|
|메모리 최적화| 8| 960|
|메모리 최적화| 16| 1900|
|메모리 최적화| 32| 1987|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

Azure 시스템에는 Azure Database for PostgreSQL 서버를 모니터링하기 위해 5개의 연결이 필요합니다. 

## <a name="functional-limitations"></a>기능 제한 사항
### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층 간의 동적 크기 조정은 현재 지원되지 않습니다.
- 서버 저장소 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](./howto-migrate-using-dump-and-restore.md)합니다.

> PostgreSQL 버전 10 전에 유의 합니다 [PostgreSQL 버전 관리 정책](https://www.postgresql.org/support/versioning/) 것으로 간주를 _주 버전_ 업그레이드를 첫 번째 증가 _또는_ 번호 (두 번째 예를 9.6 9.5 것으로 간주 되는 _주요_ 버전 업그레이드).
> 10 버전을 기준으로 첫 번째 숫자가 변경만 주 버전 업그레이드를 간주 됩니다 (10.0에 10.1는 예를 들어를 _사소한_ 버전 업그레이드 이며 10 ~ 11을 _주요_ 버전 업그레이드).

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

### <a name="restoring-a-server"></a>서버 복원
- PITR 기능을 사용하면 기반으로 하는 서버와 동일한 가격 책정 계층 구성을 사용하여 새 서버가 만들어집니다.
- 복원 동안 만든 새 서버에는 원래 서버에 존재했던 방화벽 규칙이 없습니다. 방화벽 규칙은 새 서버에 대해 개별적으로 설정돼야 합니다.
- 삭제된 서버 복원은 지원되지 않습니다.

### <a name="utf-8-characters-on-windows"></a>Windows의 UTF-8 문자
- 일부 시나리오에서는 UTF-8 문자가 Windows의 오픈 소스 PostgreSQL에서 완전히 지원되지 않으며, Azure Database for PostgreSQL에 영향을 줍니다. 자세한 내용은 [postgresql-archive의 버그 #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html)에 대한 스레드를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [각 가격 책정 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md) 이해
- [지원되는 PostgreSQL 데이터베이스 버전](concepts-supported-versions.md) 알아보기
- [Azure Portal을 사용하여 Azure Database for PostgreSQL에서 서버를 백업 및 복원하는 방법](howto-restore-server-portal.md) 검토
