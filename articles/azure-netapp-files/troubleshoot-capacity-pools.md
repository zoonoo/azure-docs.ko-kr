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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343318"
---
# <a name="troubleshoot-capacity-pool-issues"></a>용량 풀 문제 해결

이 문서에서는 용량 풀을 관리할 때 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다. 

## <a name="issues-creating-a-capacity-pool"></a>용량 풀을 만드는 문제

용량 풀 수가 제한을 초과 하지 않는지 확인 하십시오. [Azure NetApp Files에 대 한 리소스 제한을](azure-netapp-files-resource-limits.md)참조 하세요.  개수가 제한 보다 작고 여전히 문제가 발생 하는 경우 지원 티켓을 파일에 지정 하 고 용량 풀 이름을 지정 합니다.

## <a name="issues-deleting-a-capacity-pool"></a>용량 풀을 삭제 하는 문제

용량 풀을 삭제 하려는 구독에서 모든 Azure NetApp Files 볼륨 및 스냅숏을 제거 했는지 확인 합니다.   

모든 볼륨 및 스냅숏을 이미 제거 했 고 여전히 용량 풀을 삭제할 수 없는 경우 리소스에 대 한 참조는 포털에 표시 되지 않고 계속 존재할 수 있습니다. 이 경우 지원 티켓을 파일에 포함 하 고 위의 권장 단계를 수행 하도록 지정 합니다. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>"요청 된 처리량을 사용할 수 없음" 오류가 발생 하 여 볼륨을 만들거나 수정 하지 못했습니다.

볼륨에 사용 가능한 처리량은 용량 풀의 크기와 서비스 수준에 따라 결정 됩니다. 충분 한 처리량이 없으면 풀 크기를 늘리거나 기존 볼륨 처리량을 조정 해야 합니다.


## <a name="next-steps"></a>다음 단계  

* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
