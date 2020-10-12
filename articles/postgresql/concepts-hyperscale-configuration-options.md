---
title: 구성 옵션 – Hyperscale (Citus)-Azure Database for PostgreSQL
description: 노드 계산, 저장소 및 지역을 포함 하 여 Citus (Hyperscale) 서버 그룹에 대 한 옵션입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/1/2020
ms.openlocfilehash: 8dc70eaeb9e2c2f5d4cdfef37619e4b04217782e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964518"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – Citus (Hyperscale) 구성 옵션

## <a name="compute-and-storage"></a>컴퓨팅 및 스토리지
 
작업자 노드 및 Citus (Hyperscale) 서버 그룹의 코디네이터 노드에 대해 독립적으로 계산 및 저장소 설정을 선택할 수 있습니다.  컴퓨팅 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 프로 비전을 위한 저장소 크기는 Citus (Hyperscale) 서버 그룹의 코디네이터 및 작업자 노드에 사용 가능한 용량을 나타냅니다. 저장소에는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 Postgres 서버 로그가 포함 됩니다.
 
| 리소스              | 작업자 노드           | 코디네이터 노드      |
|-----------------------|-----------------------|-----------------------|
| 계산, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| VCore 당 메모리, GiB | 8                     | 4                     |
| 저장소 크기, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 스토리지 유형          | 범용 (SSD) | 범용 (SSD) |
| IOPS                  | 최대 3 IOPS/GiB      | 최대 3 IOPS/GiB      |

단일 Hyperscale (Citus) 노드의 총 RAM 크기는 선택한 vCores 수를 기반으로 합니다.

| vCore 수 | 단일 작업자 노드, GiB RAM | 코디네이터 노드, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

프로 비전 하는 총 저장소 양은 각 작업자 및 코디네이터 노드에 사용할 수 있는 i/o 용량을 정의 합니다.

| 저장소 크기, TiB | 최대 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6148        |

전체 Citus (Hyperscale) 클러스터의 경우 집계 된 IOPS는 다음 값으로 작동 합니다.

| 작업자 노드 | 0.5 TiB, 총 IOPS | 1 TiB, 총 IOPS | 2 TiB, 총 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12296            |
| 3            | 4608               | 9216             | 18444            |
| 4            | 6,144               | 12,288            | 24592            |
| 5            | 7680               | 15,360            | 30740            |
| 6            | 9216               | 18432            | 36888            |
| 7            | 10752              | 21504            | 43036            |
| 8            | 12,288              | 24576            | 49184            |
| 9            | 13824              | 27648            | 55332            |
| 10           | 15,360              | 30720            | 61480            |
| 11           | 16896              | 33792            | 67628            |
| 12           | 18432              | 36864            | 73776            |
| 13           | 19968              | 39936            | 79924            |
| 14           | 21504              | 43008            | 86072            |
| 15           | 23040              | 46080            | 92220            |
| 16           | 24576              | 49152            | 98368            |
| 17           | 26112              | 52224            | 104516           |
| 18           | 27648              | 55296            | 110664           |
| 19           | 29184              | 58368            | 116812           |
| 20           | 30720              | 61440            | 122960           |

## <a name="regions"></a>영역
Citus (hyperscale) 서버 그룹은 다음 Azure 지역에서 제공 됩니다.

* 아메리카:
    * 캐나다 중부
    * 미국 중부
    * 미국 동부
    * 미국 동부 2
    * 미국 중북부
    * 미국 서부 2
* 아시아 태평양:
    * 오스트레일리아 동부
    * 일본 동부
    * 한국 중부
    * 동남 아시아
* 유럽
    * 북유럽
    * 영국 남부
    * 서유럽

이러한 지역 중 일부는 처음에는 모든 Azure 구독에서 활성화 되지 않을 수도 있습니다. 위의 목록에서 영역을 사용 하 고 구독에 표시 되지 않거나이 목록에 없는 영역을 사용 하려는 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.

## <a name="limits-and-limitations"></a>제한 및 제한 사항

다음 섹션에서는 Citus (Hyperscale) 서비스의 용량 및 기능 제한에 대해 설명 합니다.

### <a name="maximum-connections"></a>최대 연결 수

모든 PostgreSQL 연결 (유휴 상태에 있는 경우에도)은 적어도 10mb의 메모리를 사용 하므로 동시 연결을 제한 하는 것이 중요 합니다. 노드를 정상 상태로 유지 하기 위해 선택한 제한 사항은 다음과 같습니다.

* 코디네이터 노드
   * 최대 연결: 300
   * 최대 사용자 연결: 297
* 작업자 노드
   * 최대 연결: 600
   * 최대 사용자 연결: 597

이러한 한도를 초과 하 여 연결 하려고 하면 오류가 발생 하 여 실패 합니다. 시스템은 모니터링 노드에 대 한 세 개의 연결을 예약 합니다. 따라서 연결 합계 보다 사용자 쿼리에 사용할 수 있는 연결 수는 세 개 미만입니다.

새 연결을 설정 하는 데 시간이 걸립니다. 이는 대부분의 응용 프로그램에 대해 작동 하며, 많은 단기 연결을 요청 합니다. 연결 풀을 사용 하 여 유휴 트랜잭션을 줄이고 기존 연결을 다시 사용 하는 것이 좋습니다. 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)을 참조 하세요.

### <a name="storage-scaling"></a>저장소 크기 조정

코디네이터 및 작업자 노드의 저장소는 확장 (증가) 할 수 있지만 축소 (감소) 할 수 없습니다.

### <a name="storage-size"></a>스토리지 크기

코디네이터 및 작업자 노드에서 최대 2 TiB의 저장소를 지원 합니다. 노드 및 클러스터 크기는 [위의](#compute-and-storage) 사용 가능한 저장소 옵션 및 IOPS 계산을 참조 하세요.

## <a name="pricing"></a>가격 책정
최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
원하는 구성에 대 한 비용을 확인 하기 위해 [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) 는 선택 하는 옵션에 따라 **구성** 탭의 월별 비용을 보여 줍니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가**를 선택 하 고, **데이터베이스** 범주를 확장 하 고 **Azure Database for PostgreSQL – hyperscale (Citus)** 을 선택 하 여 옵션을 사용자 지정 합니다.
 
## <a name="next-steps"></a>다음 단계
[포털에서 Citus (Hyperscale) 서버 그룹을 만드는](quickstart-create-hyperscale-portal.md)방법에 대해 알아봅니다.
