---
title: 지역 간에 Azure 리소스 이동을 위한 도구 선택
description: 지역 간에 Azure 리소스 이동을 위한 옵션 및 도구 검토
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603359"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Azure 리소스 이동을 위한 도구 선택

Azure 내에서 다음과 같이 리소스를 이동할 수 있습니다.

- **지역 간 리소스 이동**: Resource Mover 허브 내에서 또는 리소스 그룹 내에서 리소스를 이동합니다. 
- **리소스 그룹/구독 간에 리소스 이동**: 리소스 그룹 내에서 이동합니다. 
- **Azure 클라우드 간에 리소스 이동**: Azure Site Recovery 서비스를 사용하여 퍼블릭 및 정부 클라우드 간에 리소스를 이동합니다.
- **동일한 지역의 가용성 영역 간에 리소스 이동**: Azure Site Recovery 서비스를 사용하여 동일한 Azure 지역의 가용성 영역 간에 리소스를 이동합니다.


## <a name="compare-move-tools"></a>이동 도구 비교

**도구** | **사용하는 경우** | **자세한 정보** 
--- | --- | ---
**리소스 그룹 내에서 이동** | 리소스를 다른 리소스 그룹/구독으로 이동하거나 지역 간에 이동합니다.<br/><br/> 지역 간에 이동하는 경우 리소스 그룹에서 이동할 리소스를 선택한 다음, Resource Mover 허브로 이동하여 종속성을 확인하고 리소스를 대상 지역으로 이동합니다. | [다른 리소스 그룹/구독으로 리소스를 이동합니다.](../azure-resource-manager/management/move-resource-group-and-subscription.md)<br/><br/> [리소스 그룹의 다른 지역으로 리소스를 이동합니다.](move-region-within-resource-group.md)
**Resource Mover 허브에서 이동** | 지역 간에 리소스를 이동합니다. <br/><br/> 대상 지역으로 또는 대상 지역 내의 특정 가용성 영역 또는 가용성 집합으로 이동할 수 있습니다. | [Resource Mover 허브의 지역 간에 리소스를 이동합니다.]()
**Site Recovery를 사용하여 VM 이동** | 정부 및 퍼블릭 클라우드 간에 Azure VM을 이동하는 데 사용합니다.<br/><br/> 동일한 지역의 가용성 영역 간에 VM을 이동하려는 경우 사용합니다. |[정부/퍼블릭 클라우드 간에 리소스를 이동](../site-recovery/region-move-cross-geos.md)하고 [동일한 지역의 가용성 영역으로 리소스를 이동](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)합니다.

## <a name="next-steps"></a>다음 단계

Resource Mover 구성 요소 및 이동 프로세스에 대해 [자세히 알아봅니다](about-move-process.md).
