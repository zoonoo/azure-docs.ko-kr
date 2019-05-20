---
title: Azure NetApp Files의 서비스 수준 | Microsoft Docs
description: Azure NetApp Files의 서비스 수준에 대한 처리량 성능을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523115"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
서비스 수준은 용량 풀의 특성입니다. 서비스 수준 정의 되며 볼륨에 할당 된 할당량을 기반으로 용량 풀에서 볼륨에 대 한 허용 된 최대 처리량에 의해 구별 됩니다.

## <a name="supported-service-levels"></a>지원 되는 서비스 수준

Azure NetApp 파일에서는 세 가지 서비스 수준을 지원합니다. *Ultra*, *Premium*, 및 *표준*합니다. 

* <a name="Ultra"></a>Ultra 저장소

    최대 128 MiB/s 할당 볼륨 할당량 1tib 당 처리량의 Ultra 저장소 계층을 제공 합니다. 

* <a name="Premium"></a>Premium Storage

    최대 64 초 당 할당 된 볼륨 할당량 1tib 처리량의 프리미엄 저장소 계층을 제공 합니다. 

* <a name="Standard"></a>Standard Storage

    표준 저장소 계층만 16 초 당 할당 된 볼륨 할당량 1tib 처리량까지 제공 합니다.

## <a name="throughput-limits"></a>처리량 한도

볼륨에 대 한 처리량 한도 다음 요인의 조합에 의해 결정 됩니다.
* 볼륨이 속한 용량 풀의 서비스 수준
* 볼륨에 할당 된 할당량  

이 개념은 아래 다이어그램에 나와 있습니다.

![서비스 수준 그림](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

위의 예제 1과 2 TiB 할당량의 할당 된 프리미엄 저장소 계층을 사용 하 여 용량 풀에서 볼륨을 할당할 128 MiB/s의 처리량 제한은 (TiB 2 * 64 초)입니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 사용량에 관계 없이 적용 됩니다.

위의 예제 2에서는 100gib 할당량의 할당 된 프리미엄 저장소 계층을 사용 하 여 용량 풀에서 볼륨을 할당할 6.25 MiB/s의 처리량 제한은 (0.09765625 TiB * 64 초)입니다. 이 시나리오는 용량 풀 크기 또는 실제 볼륨 사용량에 관계 없이 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- 여러 서비스 수준의 가격은 [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)를 참조하세요.
- 참조 [NetApp Azure Files에 대 한 비용 모델](azure-netapp-files-cost-model.md) 용량 풀의 용량 소비에 대 한 계산에 대 한 
- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)