---
title: Azure NetApp Files에 대 한 용량 풀 문제 해결 | Microsoft Docs
description: 용량 풀을 관리 하는 경우 발생할 수 있는 잠재적인 문제에 대해 설명 하 고 문제에 대 한 해결 방법을 제공 합니다.
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
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: c6194469837997108964feda82d406c9108641b9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369242"
---
# <a name="troubleshoot-capacity-pool-issues"></a>용량 풀 문제 해결

이 문서에서는 풀 변경 작업을 포함 하 여 용량 풀을 관리할 때 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다. 

## <a name="issues-managing-a-capacity-pool"></a>용량 풀 관리 문제 

|     오류 조건    |     해결 방법    |
|-|-|
| 용량 풀을 만드는 문제 |  용량 풀 수가 제한을 초과 하지 않는지 확인 하십시오. [Azure NetApp Files에 대 한 리소스 제한을](azure-netapp-files-resource-limits.md)참조 하세요.  개수가 제한 보다 작고 여전히 문제가 발생 하는 경우 지원 티켓을 파일에 지정 하 고 용량 풀 이름을 지정 합니다. |
| 용량 풀을 삭제 하는 문제  |  용량 풀을 삭제 하려는 구독에서 모든 Azure NetApp Files 볼륨 및 스냅숏을 제거 해야 합니다. <br> 모든 볼륨 및 스냅숏을 이미 제거 했 고 여전히 용량 풀을 삭제할 수 없는 경우 리소스에 대 한 참조는 포털에 표시 되지 않고 계속 존재할 수 있습니다. 이 경우 지원 티켓을 파일에 포함 하 고 위의 권장 단계를 수행 하도록 지정 합니다. |
| 오류가 발생 하 여 볼륨을 만들거나 수정할 때 오류가 발생 함 `Requested throughput not available` | 볼륨에 사용 가능한 처리량은 용량 풀의 크기와 서비스 수준에 따라 결정 됩니다. 충분 한 처리량이 없으면 풀 크기를 늘리거나 기존 볼륨 처리량을 조정 해야 합니다. | 

## <a name="issues-moving-a-capacity-pool"></a>용량 풀 이동 문제 

> [!IMPORTANT] 
> 볼륨 공개 미리 보기 등록 [의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md) 하는 것은 추가 공지가 있을 때까지 보류 중입니다.

|     오류 조건    |     해결 방법    |
|-|-|
| 볼륨의 용량 풀을 변경 하는 것은 허용 되지 않습니다. | 이 기능을 사용 하기 위한 권한이 아직 부여 되지 않았을 수 있습니다. <br> 볼륨을 다른 용량 풀로 이동 하는 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용 하는 경우 먼저 기능을 등록 하 고를 설정 해야 `-FeatureName ANFTierChange` 합니다. [볼륨의 서비스 수준 동적 변경](dynamic-change-volume-service-level.md)에서 등록 단계를 참조 하세요. |
| 총 볼륨 크기에 비해 용량 풀 크기가 너무 작습니다. |  이 오류는 이동 하는 볼륨에 사용 가능한 용량이 없는 대상 용량 풀의 결과입니다.  <br> 대상 풀의 크기를 늘리거나 더 큰 다른 풀을 선택 하십시오.  [용량 풀 또는 볼륨 크기 조정을](azure-netapp-files-resize-capacity-pools-or-volumes.md)참조 하세요.   |
|  호출한 볼륨이 `'{source pool name}'` 대상 풀에 이미 있으므로 풀 변경을 완료할 수 없습니다. `'{target pool name}'` | 이 오류는 동일한 이름의 볼륨이 대상 용량 풀에 이미 있는 경우에 발생 합니다.  이름이 같은 볼륨이 없는 다른 용량 풀을 선택 하십시오.   | 

## <a name="next-steps"></a>다음 단계  

* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
* [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)
* [용량 풀 또는 볼륨 크기 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)
