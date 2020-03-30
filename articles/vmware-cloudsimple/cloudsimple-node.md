---
title: 클라우드에 의한 Azure VMware 솔루션 단순함 - 노드 개요
description: CloudSimple 노드 및 개념에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024928"
---
# <a name="cloudsimple-nodes-overview"></a>클라우드 단순 노드 개요

노드는 프라이빗 클라우드의 구성 요소입니다. 노드는 다음과 같은 것입니다.

* VMware ESXi 하이퍼바이저가 설치된 전용 베어 메탈 컴퓨팅 호스트  
* 프라이빗 클라우드를 만들기 위해 프로비저닝하거나 예약할 수 있는 컴퓨팅 단위
* CloudSimple 서비스를 사용할 수 있는 리전에서 프로비저닝 또는 예약 가능

프로비저닝된 노드에서 프라이빗 클라우드를 만듭니다. 프라이빗 클라우드를 만들려면 동일한 SKU의 최소 3개의 노드가 필요합니다. 프라이빗 클라우드를 확장하려면 노드를 추가합니다.  Azure 포털에서 노드를 프로비전하고 CloudSimple 서비스와 연결하여 기존 클러스터에 노드를 추가하거나 새 클러스터를 만들 수 있습니다.  프로비저닝된 모든 노드는 CloudSimple 서비스 아래에 표시됩니다.  

## <a name="provisioned-nodes"></a>프로비저닝된 노드

프로비저닝된 노드는 종량제 용량을 제공합니다. 노드프로비저닝을 통해 필요에 따라 VMware 클러스터를 신속하게 확장할 수 있습니다. 필요에 따라 노드를 추가하거나 프로비저닝된 노드를 삭제하여 VMware 클러스터를 축소할 수 있습니다. 프로비저닝된 노드는 매월 청구되며 프로비전된 구독에 요금이 청구됩니다.

* 신용 카드로 Azure 구독요금을 결제하면 카드에 즉시 요금이 청구됩니다.
* 인보이스로 청구되는 경우 청구액이 다음 인보이스에 표시됩니다.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>클라우드단순 노드 SKU에 의한 VMware 솔루션

프로비저닝 또는 예약에 다음과 같은 유형의 노드를 사용할 수 있습니다.

| SKU           | CS28 - 노드                 | CS36 - 노드                 | CS36m - 노드                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 지역        | 미국 동부, 미국 서부            | 미국 동부, 미국 서부            | 서유럽                 |
| CPU           | 2x2.2GHz, 28코어(56HT) | 2x2.3GHz, 36코어(72HT) | 2x2.3GHz, 36코어(72HT) |
| RAM           | 256GB                      | 512 GB                      | 576GB                      |
| 캐시 디스크    | 1.6TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 용량 디스크 | 5.625 TB 원시                | 11.25 TB 원시                | 15.36 TB 원시                |
| 스토리지 유형  | 모든 플래시                   | 모든 플래시                   | 모든 플래시                   |

## <a name="limits"></a>제한

다음 노드 제한은 프라이빗 클라우드에 적용됩니다.

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드를 만들 수 있는 최소 노드 수 | 3 |
| 프라이빗 클라우드의 클러스터에 있는 최대 노드 수 | 16 |
| 프라이빗 클라우드의 최대 노드 수 | 64 |
| 새 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [노드 프로비전](create-nodes.md) 방법 알아보기
* 프라이빗 [클라우드에](cloudsimple-private-cloud.md) 대해 알아보기
