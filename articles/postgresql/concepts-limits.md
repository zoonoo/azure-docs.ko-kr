---
title: 제한 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: '이 문서에서는 PostgreSQL - 단일 서버(예: 연결 및 저장소 엔진 옵션)에 대한 Azure 데이터베이스의 제한에 대해 설명합니다.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836459"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 제한
다음 섹션에서는 데이터베이스 서비스의 용량 및 기능 제한에 대해 설명합니다. 리소스(계산, 메모리, 저장소) 계층에 대해 알아보려면 가격 책정 계층 문서를 [참조하세요.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>최대 연결 수
가격 책정 계층 및 vCore당 최대 연결 수는 다음과 같습니다. Azure 시스템에는 Azure Database for PostgreSQL 서버를 모니터링하기 위해 5개의 연결이 필요합니다. 

|**가격 책정 계층**| **vCore**| **최대 연결** | **최대 사용자 연결** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|범용| 2| 150| 145|
|범용| 4| 250| 245|
|범용| 8| 480| 475|
|범용| 16| 950| 945|
|범용| 32| 1500| 1495|
|범용| 64| 1900| 1895|
|메모리 최적화| 2| 300| 295|
|메모리 최적화| 4| 500| 495|
|메모리 최적화| 8| 960| 955|
|메모리 최적화| 16| 1900| 1895|
|메모리 최적화| 32| 1987| 1982|

연결 한도를 초과하면 다음과 같은 오류가 발생할 수 있습니다.
> 오류: 너무 많은 클라이언트가 이미 연결되어 있습니다.

> [!IMPORTANT]
> 최상의 환경을 위해 pgBouncer와 같은 연결 풀러를 사용하여 연결을 효율적으로 관리하는 것이 좋습니다.

PostgreSQL 연결, 심지어 유휴, 메모리의 약 10 MB를 차지할 수 있습니다. 또한 새 연결을 만드는 데는 시간이 걸립니다. 대부분의 응용 프로그램은 이 상황을 복잡하게 하는 많은 단기 연결을 요청합니다. 그 결과 실제 워크로드에 사용할 수 있는 리소스가 줄어들어 성능이 저하됩니다. 유휴 연결을 줄이고 기존 연결을 다시 사용하는 연결 풀러는 이를 방지하는 데 도움이 됩니다. 자세한 내용은 블로그 [게시물을](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)방문하십시오.

## <a name="functional-limitations"></a>기능 제한 사항
### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층 간의 동적 크기 조정은 현재 지원되지 않습니다.
- 서버 스토리지 크기를 줄이는 것은 현재 지원되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간에 자동화된 마이그레이션은 현재 지원되지 않습니다. 다음의 주 버전으로 업그레이드하려는 경우 새 엔진 버전을 사용하여 만든 서버에 주 버전을 [덤프 및 복원](./howto-migrate-using-dump-and-restore.md)합니다.

> PostgreSQL 버전 10 [이전에PostgreSQL 버전 지정 정책은](https://www.postgresql.org/support/versioning/) _주 버전_ 업그레이드를 첫 번째 _또는_ 두 번째 숫자의 증가로 간주했습니다(예: 9.5 ~ 9.6은 _주_ 버전 업그레이드로 간주).
> 버전 10에서는 첫 번째 숫자의 변경만 주 버전 업그레이드로 간주됩니다(예: 10.0 ~ 10.1은 _마이너_ 버전 업그레이드, 10~11번째는 _주_ 버전 업그레이드).

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
