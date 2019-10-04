---
title: CloudSimple 노드의 Azure VMware 솔루션 개요
description: CloudSimple 노드 및 개념에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845539"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 노드 개요

노드는 사설 클라우드의 구성 요소입니다. 노드는 다음과 같습니다.

* VMware ESXi 하이퍼바이저가 설치 되는 전용 완전 계산 호스트  
* 사설 클라우드를 만들기 위해 구매 하거나 예약할 수 있는 컴퓨팅 단위
* CloudSimple 서비스를 사용할 수 있는 지역에서 구입 하거나 예약할 수 있습니다.

구매한 노드에서 사설 클라우드를 만듭니다. 사설 클라우드를 만들려면 동일한 SKU의 노드가 세 개 이상 필요 합니다. 사설 클라우드를 확장 하려면 노드를 더 추가 합니다.  Azure Portal 노드를 구매 하 고 CloudSimple 서비스와 연결 하 여 기존 클러스터에 노드를 추가 하거나 새 클러스터를 만들 수 있습니다.  구매한 모든 노드는 CloudSimple 서비스 아래에 표시 됩니다.  

## <a name="purchased-nodes"></a>구매한 노드

구매한 노드는 종 량 제 용량을 제공 합니다. 노드를 구매 하면 주문형 VMware 클러스터를 신속 하 게 확장할 수 있습니다. 필요에 따라 노드를 추가 하거나 구매한 노드를 삭제 하 여 VMware 클러스터 규모를 축소할 수 있습니다. 구매한 노드는 매달 청구 되며 구매한 구독에 대 한 요금이 청구 됩니다.

* 신용 카드로 Azure 구독에 대 한 요금을 지불 하는 경우 카드는 즉시 청구 됩니다.
* 청구서로 청구 되는 경우 요금은 다음 청구서에 표시 됩니다.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>CloudSimple 노드 SKU 별 VMware 솔루션

다음 유형의 노드를 구매 또는 예약에 사용할 수 있습니다.

| SKU           | CS28-노드                 | CS36-노드                 | CS36m-노드                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | 미국 동부, 미국 서부            | 미국 동부, 미국 서부            | 유럽 서부                 |
| CPU           | 22.2 g h z, 28 코어 (56 HT) | 22.3 g h z, 36 코어 (72 HT) | 22.3 g h z, 36 코어 (72 HT) |
| RAM           | 256GB                      | 512 GB                      | 576GB                      |
| 캐시 디스크    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 용량 디스크 | 5.625 TB 원시                | 11.25 TB 원시                | 15.36 TB 원시                |
| 스토리지 유형  | 모든 플래시                   | 모든 플래시                   | 모든 플래시                   |

## <a name="limits"></a>제한

사설 클라우드에는 다음 노드 제한이 적용 됩니다.

| 리소스 | 제한 |
|----------|-------|
| 사설 클라우드를 만들기 위한 최소 노드 수 | 3 |
| 사설 클라우드의 클러스터에 있는 최대 노드 수 | 16 |
| 사설 클라우드의 최대 노드 수 | 64 |
| 새 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [노드를 구매](create-nodes.md) 하는 방법 알아보기
* [사설 클라우드에](cloudsimple-private-cloud.md) 대 한 자세한 정보
