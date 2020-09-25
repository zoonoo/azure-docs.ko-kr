---
title: Azure NetApp Files에 대한 용량 풀 또는 볼륨의 크기 조정 | Microsoft Docs
description: 용량 풀 또는 볼륨의 크기를 변경 하는 방법에 대해 알아봅니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325507"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>용량 풀 또는 볼륨 크기 조정
필요에 따라 용량 풀 또는 볼륨의 크기를 변경할 수 있습니다. 

## <a name="resize-the-capacity-pool"></a>용량 풀 크기 조정 

용량 풀 크기는 1-TiB 증가 또는 감소에서 변경할 수 있습니다. 그러나 용량 풀 크기는 4 TiB 보다 작을 수 없습니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.

1. NetApp 계정 관리 블레이드에서 크기를 조정하려는 용량 풀을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 용량 풀 이름을 마우스 오른쪽 단추로 클릭하거나, 용량 풀의 행의 끝에 있는 "..." 아이콘을 클릭합니다. 
3. 상황에 맞는 메뉴 옵션을 사용하여 용량 풀을 크기 조정하거나 삭제합니다.

## <a name="resize-a-volume"></a>볼륨 크기 조정

필요에 따라 볼륨의 크기를 변경할 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 관리 블레이드에서 **볼륨**을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 크기를 조정하려는 볼륨 이름을 마우스 오른쪽 단추로 클릭하거나 볼륨의 행의 끝에 있는 "..." 아이콘을 클릭합니다.
3. 상황에 맞는 메뉴 옵션을 사용하여 볼륨을 크기 조정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
- [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 