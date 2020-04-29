---
title: 구성 옵션 – Hyperscale (Citus)-Azure Database for PostgreSQL
description: 노드 계산, 저장소 및 지역을 포함 하 여 Citus (Hyperscale) 서버 그룹에 대 한 옵션입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804591"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – Citus (Hyperscale) 구성 옵션

## <a name="compute-and-storage"></a>컴퓨팅 및 스토리지
 
작업자 노드 및 Citus (Hyperscale) 서버 그룹의 코디네이터 노드에 대해 독립적으로 계산 및 저장소 설정을 선택할 수 있습니다.  컴퓨팅 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 프로 비전을 위한 저장소 크기는 Citus (Hyperscale) 서버 그룹의 코디네이터 및 작업자 노드에 사용 가능한 용량을 나타냅니다. 저장소에는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 Postgres 서버 로그가 포함 됩니다.
 
|                       | 작업자 노드           | 코디네이터 노드      |
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
    * 동남아시아
* 유럽
    * 북유럽
    * 영국 남부
    * 서유럽

이러한 지역 중 일부는 처음에는 모든 Azure 구독에서 활성화 되지 않을 수도 있습니다. 위의 목록에서 영역을 사용 하 고 구독에 표시 되지 않거나이 목록에 없는 영역을 사용 하려는 경우 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.

## <a name="pricing"></a>가격 책정
최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
원하는 구성에 대 한 비용을 확인 하기 위해 [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) 는 선택 하는 옵션에 따라 **구성** 탭의 월별 비용을 보여 줍니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가**를 선택 하 고, **데이터베이스** 범주를 확장 하 고 **Azure Database for PostgreSQL – hyperscale (Citus)** 을 선택 하 여 옵션을 사용자 지정 합니다.
 
## <a name="next-steps"></a>다음 단계
[포털에서 Citus (Hyperscale) 서버 그룹을 만드는](quickstart-create-hyperscale-portal.md)방법에 대해 알아봅니다.
