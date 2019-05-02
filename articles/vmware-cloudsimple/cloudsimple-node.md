---
title: CloudSimple-Azure에서 VMware 솔루션에 대 한 노드 개요
description: CloudSimple 노드 및 개념에 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b3c8fca1dd93f379860cc3b084fbb14d4a0c6380
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577362"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 노드 개요

노드는:

* VMware ESXi 하이퍼바이저가 설치 되어 있는 호스트를 계산 하는 전용된 운영 체제 미 설치  
* 사설 클라우드를 만드는 구입할 수 있는 컴퓨팅 또는 예약 단위  
* CloudSimple 서비스를 사용할 수 있는 지역에서 예약을 구입 하거나 사용할 수 있습니다.

노드는 사설 클라우드의 구성 요소입니다.  사설 클라우드를 만들려면 동일한 SKU의 세 개 노드의 최소를 해야 합니다.  사설 클라우드를 확장 하려면 노드를 추가 합니다.  기존 클러스터에 노드를 추가할 수 있습니다. 또는 Azure portal에서 노드를 구입 하 고 CloudSimple 서비스에 연결 하 여 새 클러스터를 만들 수 있습니다.  구매 하는 모든 노드가 CloudSimple 서비스 아래에 표시 됩니다.  CloudSimple 포털에서 구입한 노드에서 사설 클라우드를 만듭니다.

## <a name="purchased-nodes"></a>구매한 노드

구매한 노드는 종 량 제 용량을 제공합니다. 노드를 구입 하면 수요에 따라 VMware 클러스터를 신속 하 게 확장 수 있습니다. 필요에 따라 노드를 추가 하거나 VMware 클러스터 크기를 조정 하는 구매한 노드를 삭제할 수 있습니다. 구매한 노드는 월별 기준으로 요금이 청구 되며 어디 구입 하는 구독에 청구 됩니다.

* Azure 구독에 대 한 신용 카드로 지불 카드 즉시 청구 됩니다.
* 청구서로 청구 하는 경우 요금은 다음 청구서에 나타납니다.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware 솔루션 CloudSimple 노드에서 SKU

다음 형식 노드 예약 구매에 사용할 수 있습니다.

| SKU | CS28-노드 | CS36-노드 |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz, 28 개 코어 (56 HT) | 2x2.3 GHz, 36 개 코어 (72 HT) |
| RAM | 256GB | 512 GB |
| 캐시 디스크 |  1.6 TB NVMe | 3.2-TB NVMe |
| 디스크 용량 | 5.625 원시 TB | 11.25 원시 TB |
| 저장소 유형 | 모든 플래시 | 모든 플래시 |

## <a name="limits"></a>제한

사설 클라우드에는 다음과 같은 노드 제한이 적용 됩니다.

| 리소스 | 제한 |
|----------|-------|
| 사설 클라우드를 만드는 노드의 최소 수 | 3 |
| 사설 클라우드 클러스터에서 노드의 최대 수 | 16 |
| 사설 클라우드에서 노드의 최대 수 | 64 |
| 새 클러스터에서 노드의 최소 수 | 3 |

## <a name="next-steps"></a>다음 단계

* 에 대해 알아봅니다 방법 [노드를 구입 합니다.](create-nodes.md)
* 에 대 한 자세한 [사설 클라우드](cloudsimple-private-cloud.md)