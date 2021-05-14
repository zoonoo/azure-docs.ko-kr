---
title: Azure VMware Solution by CloudSimple - 노드 개요
description: 노드, 프로비저닝된 노드, 프라이빗 클라우드, VMware Solution by CloudSimple 노드 SKU 등 CloudSimple의 개념에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140839"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple 노드 개요

노드는 프라이빗 클라우드의 구성 요소입니다. 노드는 다음과 같습니다.

* VMware ESXi 하이퍼바이저가 설치된 전용 운영 체제 미설치 컴퓨팅 호스트입니다.  
* 프라이빗 클라우드를 만들기 위해 프로비저닝하거나 예약할 수 있는 컴퓨팅 단위입니다.
* CloudSimple 서비스를 사용할 수 있는 지역에서 프로비저닝하거나 예약할 수 있습니다.

프로비저닝된 노드에서 프라이빗 클라우드를 만듭니다. 프라이빗 클라우드를 만들려면 SKU가 동일한 노드가 세 개 이상 있어야 합니다. 프라이빗 클라우드를 확장하려면 노드를 더 추가합니다.  Azure Portal에서 노드를 프로비저닝하고 CloudSimple 서비스와 연결하여 기존 클러스터에 노드를 추가하거나 새 클러스터를 만들 수 있습니다.  프로비저닝된 노드는 모두 CloudSimple 서비스 아래에 표시됩니다.  

## <a name="provisioned-nodes"></a>프로비저닝된 노드

프로비저닝된 노드는 종량제 용량을 제공합니다. 노드를 프로비저닝하면 요청 시 VMware 클러스터를 신속하게 스케일링하는 데 도움이 됩니다. 필요에 따라 노드를 추가하거나 프로비저닝된 노드를 삭제하여 VMware 클러스터를 스케일 다운할 수 있습니다. 프로비저닝된 노드는 매달 요금이 청구되며 프로비저닝된 구독에 대한 요금도 청구됩니다.

* 신용 카드를 통해 Azure 구독에 대한 요금을 지불하는 경우 해당 카드로 즉시 요금이 청구됩니다.
* 청구서를 통해 요금이 청구되는 경우 다음 번 청구서에 요금이 표시됩니다.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware Solution by CloudSimple 노드 SKU

프로비저닝 또는 예약에는 다음과 형식의 노드를 사용할 수 있습니다.

| SKU           | CS28 - 노드                 | CS36 - 노드                 | CS36m - 노드                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| 지역        | 미국 동부, 미국 서부            | 미국 동부, 미국 서부            | 서유럽                 |
| CPU           | 2x2.2GHz, 28코어(56HT) | 2x2.3GHz, 36코어(72HT) | 2x2.3GHz, 36코어(72HT) |
| RAM           | 256GB                      | 512 GB                      | 576GB                      |
| 캐시 디스크    | 1.6-TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 용량 디스크 | 5.625TB 원시                | 11.25TB 원시                | 15.36TB 원시                |
| 스토리지 유형  | 올 플래시                   | 올 플래시                   | 올 플래시                   |

## <a name="limits"></a>제한

프라이빗 클라우드에는 다음 노드 한도가 적용됩니다.

| 리소스 | 제한 |
|----------|-------|
| 프라이빗 클라우드를 만드는 데 필요한 최소 노드 수 | 3 |
| 프라이빗 클라우드의 한 클러스터 내 최대 노드 수 | 16 |
| 프라이빗 클라우드의 최대 노드 수 | 64 |
| 새 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [노드 프로비저닝](create-nodes.md) 방법 알아보기
* [프라이빗 클라우드](cloudsimple-private-cloud.md)에 대한 자세한 정보
