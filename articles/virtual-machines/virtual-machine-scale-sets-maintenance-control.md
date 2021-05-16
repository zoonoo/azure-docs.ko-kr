---
title: Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어 개요
description: 유지 관리 제어를 사용하여 Azure 가상 머신 확장 집합에 자동 OS 이미지 업그레이드가 출시되는 시기를 제어하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532709"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>미리 보기: Azure 가상 머신 확장 집합에 대한 유지 관리 제어 

유지 관리 제어를 사용하여 가상 머신 확장 집합에 대한 [자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)를 관리합니다.

유지 관리 제어를 사용하면 더 쉽고 예측 가능한 환경을 통해 가상 머신 확장 집합의 OS 디스크에 업데이트를 적용할 시기를 결정할 수 있습니다. 

유지 관리 구성은 구성 및 리소스 그룹에서 작동합니다.

전체 워크플로는 다음 단계로 나뉩니다. 
- 유지 관리 구성을 만듭니다.
- 유지 관리 구성에 가상 머신 확장 집합을 연결합니다.
- 자동 OS 업그레이드를 사용하도록 설정합니다.

> [!IMPORTANT]
> Azure 가상 머신 확장 집합의 OS 이미지 업그레이드에 대한 유지 관리 제어는 현재 공개 미리 보기로 제공되고 있습니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="limitations"></a>제한 사항

- VM은 확장 집합에 있어야 합니다.
- 사용자에게 **리소스 참가자** 액세스 권한이 있어야 합니다.
- 유지 관리 기간은 유지 관리 구성에서 5시간 이상이어야 합니다.
- 유지 관리 구성에서 유지 관리 되풀이를 'Day'로 설정해야 합니다.


## <a name="management-options"></a>관리 옵션

다음 옵션 중 하나를 사용하여 유지 관리 구성을 만들고 관리할 수 있습니다.

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>다음 단계

유지 관리 제어 및 PowerShell을 사용하여 Azure 가상 머신 확장 집합에 유지 관리가 적용되는 시점을 제어하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 가상 머신 확장 집합 유지 관리 제어](virtual-machine-scale-sets-maintenance-control-powershell.md)
