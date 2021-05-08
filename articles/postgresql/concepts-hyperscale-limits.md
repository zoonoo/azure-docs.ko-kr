---
title: 한도 및 제한 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) 서버 그룹의 현재 한도
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023849"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus) 한도 및 제한

다음 섹션에서는 하이퍼스케일(Citus) 서비스의 용량 및 기능 한도에 대해 설명합니다.

## <a name="maximum-connections"></a>최대 연결 수

모든 PostgreSQL 연결(유휴 연결인 경우에도)은 10MB 이상의 메모리를 사용하므로 동시 연결을 제한하는 것이 중요합니다. 다음은 노드를 정상 상태로 유지하기 위해 선택한 한도입니다.

* 코디네이터 노드
   * 최대 연결 수: 300
   * 최대 사용자 연결: 297
* 작업자 노드
   * 최대 연결 수: 600
   * 최대 사용자 연결: 597

> [!NOTE]
> [미리 보기 기능](hyperscale-preview-features.md)이 사용하도록 설정된 서버 그룹에서 코디네이터에 대한 연결 한도는 약간 다릅니다.
>
> * 코디네이터 노드 최대 연결 수
>    * 0~3개 vCore의 경우 300
>    * 4~15개 vCore의 경우 500
>    * 16개 이상 vCore의 경우 1,000

이러한 한도를 초과하여 연결하려고 하면 오류가 발생하여 실패합니다. 시스템은 모니터링 노드에 대해 세 개의 연결을 예약하는데, 그 이유는 사용자 쿼리에 사용할 수 있는 연결이 총 연결보다 세 개 더 적기 때문입니다.

### <a name="connection-pooling"></a>연결 풀링

새 연결을 설정하려면 시간이 걸립니다. 이는 많은 일시적인 연결을 요청하는 대부분의 애플리케이션에 대해 적용됩니다. 유휴 트랜잭션을 줄이고 기존 연결을 다시 사용하려면 연결 풀러를 사용하는 것이 좋습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조하세요.

사용자 고유의 연결 풀러를 실행하거나 Azure에서 관리하는 PgBouncer를 사용할 수 있습니다.

#### <a name="managed-pgbouncer-preview"></a>관리형 PgBouncer(미리 보기)

> [!IMPORTANT]
> 하이퍼스케일(Citus)의 관리형 PgBouncer 연결 풀러는 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

PgBouncer와 같은 연결 풀러를 사용하면 더 많은 클라이언트가 동시에 코디네이터 노드에 연결할 수 있습니다. 애플리케이션은 풀러에 연결하고 풀러는 대상 데이터베이스에 명령을 릴레이합니다.

클라이언트가 PgBouncer를 통해 연결하는 경우 데이터베이스에서 적극적으로 실행될 수 있는 연결 수는 변경되지 않습니다. 대신 PgBouncer는 초과 연결을 큐에 넣고 데이터베이스가 준비되면 실행합니다.

현재 하이퍼스케일(Citus)은 서버 그룹에 대해 PgBouncer의 관리형 인스턴스를 제공합니다(미리 보기). 또한 최대 2,000개의 동시 클라이언트 연결을 지원합니다.
PgBouncer를 통해 연결하려면 다음 단계를 수행합니다.

1. Azure Portal에서 서버 그룹에 대한 **연결 문자열** 페이지로 이동합니다.
2. **PgBouncer 연결 문자열** 확인란을 사용하도록 설정합니다. 나열되는 연결 문자열이 변경됩니다.
3. 새 문자열로 연결하도록 클라이언트 애플리케이션을 업데이트합니다.

## <a name="storage-scaling"></a>스토리지 스케일링

코디네이터 및 작업자 노드의 스토리지는 스케일 업(증가)할 수 있지만 스케일 다운(감소)할 수는 없습니다.

## <a name="storage-size"></a>스토리지 크기

최대 2TiB의 스토리지가 코디네이터 및 작업자 노드에서 지원됩니다. [위](concepts-hyperscale-configuration-options.md#compute-and-storage)의 사용 가능한 스토리지 옵션 및 IOPS 계산에서 노드 및 클러스터 크기를 참조하세요.

## <a name="database-creation"></a>데이터베이스 만들기

Azure Portal은 하이퍼스케일(Citus) 서버 그룹당 정확히 하나의 데이터베이스(`citus` 데이터베이스)에 연결하는 자격 증명을 제공합니다. 현재 다른 데이터베이스를 만들 수 없으며 CREATE DATABASE 명령이 실패하여 오류가 발생합니다.

## <a name="columnar-storage"></a>열 형식 스토리지

현재 하이퍼스케일(Citus) [열 형식 테이블](concepts-hyperscale-columnar.md)에는 다음과 같은 제한이 있습니다.

* 압축이 메모리가 아닌 디스크에 있음
* 추가 전용(UPDATE/DELETE 지원 안 함)
* 공간 재사용 안 함(예: 롤백된 트랜잭션이 디스크 공간을 계속 사용할 수 있음)
* 인덱스 지원, 인덱스 검색 또는 비트맵 인덱스 검색 안 함
* tidscans 없음
* 샘플 검색 안 함
* TOAST 지원 안 함(인라인으로 지원되는 큰 값)
* ON CONFLICT 문 지원 안 함(대상이 지정되지 않은 DO NOTHING 작업 제외).
* 튜플 잠금 지원 안 함(SELECT ... FOR SHARE, SELECT ... FOR UPDATE)
* 직렬화 가능한 격리 수준 지원 안 함
* PostgreSQL 서버 버전 12 이상만 지원
* 외래 키, 고유 제약 조건 또는 제외 제약 조건 지원 안 함
* 논리적 디코딩 지원 안 함
* 노드 내 병렬 검색 지원 안 함
* AFTER ... FOR EACH ROW 트리거 지원 안 함
* UNLOGGED 열 형식 테이블 없음
* TEMPORARY 열 형식 테이블 없음

## <a name="next-steps"></a>다음 단계

[포털에서 하이퍼스케일(Citus) 서버 그룹 만들기](quickstart-create-hyperscale-portal.md) 방법을 알아봅니다.
