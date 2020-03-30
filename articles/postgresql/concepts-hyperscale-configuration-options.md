---
title: 성능 옵션 - 하이퍼 스케일 (Citus) - PostgreSQL용 Azure 데이터베이스
description: 노드 계산, 저장소 및 지역을 포함한 하이퍼스케일(Citus) 서버 그룹에 대한 옵션입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462414"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>PostgreSQL용 Azure 데이터베이스 – 하이퍼스케일(시터스) 성능 옵션

## <a name="compute-and-storage"></a>컴퓨팅 및 스토리지
 
하이퍼스케일(Citus) 서버 그룹의 작업자 노드 및 코디네이터 노드에 대해 독립적으로 계산 및 저장소 설정을 선택할 수 있습니다.  컴퓨팅 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 프로비저닝을 위한 저장소 크기는 하이퍼스케일(Citus) 서버 그룹의 코디네이터 및 작업자 노드에서 사용할 수 있는 용량을 나타냅니다. 저장소에는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 Postgres 서버 로그가 포함됩니다. 프로비저닝한 총 저장소 양은 각 작업자 및 코디네이터 노드에서 사용할 수 있는 I/O 용량을 정의합니다.
 
|                       | 작업자 노드           | 코디네이터 노드      |
|-----------------------|-----------------------|-----------------------|
| 컴퓨팅, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| vCore당 메모리, GiB | 8                     | 4                     |
| 수납 크기, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 스토리지 유형          | 범용(SSD) | 범용(SSD) |
| IOPS                  | 최대 3IOPS/GiB      | 최대 3IOPS/GiB      |


## <a name="regions"></a>영역
하이퍼스케일(Citus) 서버 그룹은 다음 Azure 지역에서 사용할 수 있습니다.

* 아메리카:
    * 캐나다 중부
    * 미국 중부
    * 미국 동부
    * 미국 동부 2
    * 미국 중북부
    * 미국 서부 2
* 아시아 태평양 지역:
    * 오스트레일리아 동부
    * 일본 동부
    * 한국 중부
    * 동남아시아
* 유럽:
    * 북유럽
    * 영국 남부
    * 서유럽

이러한 지역 중 일부는 일부 Azure 구독에서 처음에 활성화되지 않을 수 있습니다. 위의 목록에서 리전을 사용하고 구독에 해당 지역을 표시하지 않으려거나 이 목록에 없는 리전을 사용하려면 [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)엽니다.

## <a name="pricing"></a>가격 책정
최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.
원하는 구성 비용을 보려면 [Azure 포털에서](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) 선택한 옵션에 따라 **구성** 탭에 월별 비용이 표시됩니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. Azure [가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가를**선택하고 **데이터베이스 범주를** 확장하고 **PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스를** 선택하여 옵션을 사용자 지정합니다.
 
## <a name="next-steps"></a>다음 단계
[포털에서 하이퍼스케일(Citus) 서버 그룹을 만드는](quickstart-create-hyperscale-portal.md)방법에 대해 알아봅니다.
