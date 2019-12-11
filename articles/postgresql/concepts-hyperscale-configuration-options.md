---
title: 성능 옵션 – Hyperscale (Citus)-Azure Database for PostgreSQL
description: 노드 계산, 저장소 및 영역을 포함하여 하이퍼스케일(Citus) 서버 그룹을 사용하는 작업에 대한 옵션입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975638"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – Citus (Hyperscale) 성능 옵션

## <a name="compute-and-storage"></a>컴퓨팅 및 스토리지
 
하이퍼스케일(Citus) 서버 그룹의 작업자 노드와 코디네이터 노드에 대해 독립적으로 계산 및 저장소 설정을 선택할 수 있습니다.  컴퓨팅 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 프로비전을 위한 저장소 크기는 하이퍼스케일(Citus) 서버 그룹의 코디네이터 및 작업자 노드에 사용 가능한 용량을 가리킵니다. 저장소에는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 Postgres 서버 로그가 포함 됩니다. 또한 프로비전하는 저장소의 총 크기는 각 작업자 및 코디네이터 노드가 사용할 수 있는 I/O 용량을 정의합니다.
 
|                       | 작업자 노드           | 코디네이터 노드      |
|-----------------------|-----------------------|-----------------------|
| 계산, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| VCore 당 메모리, GiB | 8                     | 4                     |
| 저장소 크기, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 스토리지 유형          | 범용 (SSD) | 범용 (SSD) |
| IOPS                  | 최대 3 IOPS/GiB      | 최대 3 IOPS/GiB      |


## <a name="regions"></a>개 지역
Citus (hyperscale) 서버 그룹은 다음 Azure 지역에서 제공 됩니다.

* 아메리카:
    * 캐나다 중부 *
    * 미국 동부
    * 미국 동부 2
    * 미국 중 북부 *
    * 미국 서부 2
* 아시아 태평양:
    * 오스트레일리아 동부 *
    * 동남아시아
* 유럽
    * 북유럽
    * 영국 남부
    * 서유럽

별표가 있는 지역 (\*)은 아직 [고가용성](concepts-hyperscale-high-availability.md)을 지원 하지 않습니다.

## <a name="pricing"></a>가격
최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
원하는 구성 비용을 확인하려면, [Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)에서는 선택한 옵션에 따라 **구성** 탭에서 월별 비용을 표시합니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서, **항목 추가**를 선택, **데이터베이스** 범주를 확장하고 **Azure Database for PostgreSQL – 하이퍼스케일(Citus)** 옵션을 선택하여 사용자 지정합니다.
 
## <a name="next-steps"></a>다음 단계
[포털에서 하이퍼스케일(Citus) 서버 그룹을 만드는](quickstart-create-hyperscale-portal.md) 방법에 대해 알아봅니다.
