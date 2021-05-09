---
title: 리소스 그룹, 구독 또는 지역 간에 Azure 리소스를 이동하세요.
description: Azure 리소스 종류를 리소스 그룹, 구독 또는 지역 간에 이동하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730395"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>리소스 그룹, 구독 또는 지역 간 Azure 리소스 이동

Azure 리소스를 새로운 리소스 그룹이나 구독으로 또는 지역 간에 이동할 수 있습니다.

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>리소스 그룹 또는 구독 간에 리소스 이동

Azure 리소스를 다른 Azure 구독 또는 동일한 구독의 다른 리소스 그룹으로 이동할 수 있습니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다. 방법을 알아보려면 [리소스를 새 리소스 그룹 또는 구독으로 이동](move-resource-group-and-subscription.md)을 참조하세요.

### <a name="upgrade-a-subscription"></a>구독 업그레이드

실제로 Azure 구독을 업그레이드하려면(예: 평가판에서 종량제로 업그레이드) 구독을 변환해야 합니다.

- 평가판을 업그레이드하려면 [평가판 또는 Microsoft Imagine Azure 구독을 종량제로 업그레이드](../../cost-management-billing/manage/upgrade-azure-subscription.md)를 참조하세요.
- 종량제 계정을 변경하려면 [다른 제안으로 Azure 종량제 구독 변경](../../cost-management-billing/manage/switch-azure-offer.md)을 참조하세요.

구독을 변환할 수 없으면 [Azure 지원 요청을 만듭니다](../../azure-portal/supportability/how-to-create-azure-support-request.md). 문제 유형으로 **구독 관리** 를 선택합니다.

## <a name="move-resources-across-regions"></a>지역 간 리소스 이동

Azure 지리, 지역, 가용성 영역은 Azure 글로벌 인프라의 토대를 형성합니다. Azure [지리](https://azure.microsoft.com/global-infrastructure/geographies/)에는 일반적으로 둘 이상의 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)이 포함됩니다. 지역은 가용성 영역과 여러 데이터 센터가 포함된 지리 내의 영역입니다.

특정 Azure 지역에 리소스를 배포한 후 여러 가지 이유로 리소스를 다른 지역으로 이동해야 할 수 있습니다.

- **지역 시작에 맞추기**: 이전에 사용할 수 없었던 새로 도입된 Azure 지역으로 리소스를 이동합니다.
- **서비스/기능에 맞추기**: 리소스를 이동하여 특정 지역에서 사용할 수 있는 서비스 또는 기능을 활용합니다.
- **비즈니스 개발에 대응**: 합병 또는 인수 등의 비즈니스 변화에 대응하여 리소스를 지역으로 이동합니다.
- **근접성에 맞추기**: 리소스를 비즈니스 지역으로 이동합니다.
- **데이터 요구 사항 충족**: 데이터 상주 요구 사항 또는 데이터 분류 요구 사항에 맞게 리소스를 이동합니다. [자세한 정보를 알아보세요](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **배포 요구 사항에 대응**: 오류로 배포된 리소스를 이동하거나 용량 요구 사항에 따라 이동합니다.
- **서비스 해제에 대응**: 지역이 서비스 해제되어서 리소스를 이동합니다.

### <a name="move-resources-with-resource-mover"></a>Resource Mover를 사용하여 리소스 이동

[Azure Resource Mover](../../resource-mover/overview.md)를 사용하여 리소스를 다른 지역으로 이동할 수 있습니다. Resource Mover는 다음을 제공합니다.

- 지역 간에 리소스를 이동하기 위한 단일 허브입니다.
- 이동 시간과 복잡성이 줄어듭니다. 필요한 모든 항목은 단일 위치에 있습니다.
- 다양한 유형의 Azure 리소스를 이동할 수 있는 간단하고 일관된 환경을 제공합니다.
- 이동하려는 리소스 간의 종속성을 쉽게 식별할 수 있는 방법입니다. 이를 통해 관련 리소스를 함께 이동함으로써 이동 후 대상 지역에서 모든 것이 예상대로 작동하도록 할 수 있습니다.
- 이동 후 리소스를 삭제하려는 경우 원본 지역의 리소스를 자동으로 정리합니다.
- 테스트 이동을 시도한 다음, 전체 이동을 수행하지 않으려면 취소할 수 있습니다.

몇 가지 방법을 사용하여 리소스를 다른 지역으로 이동할 수 있습니다.

- **리소스 그룹에서 리소스 이동 시작**: 이 방법을 사용하면 리소스 그룹 내에서 지역 이동을 시작합니다. 이동할 리소스를 선택한 후에는 Resource Mover 허브에서 프로세스가 계속 진행되어 리소스 종속성을 확인하고 이동 프로세스를 오케스트레이션합니다. [자세한 정보를 알아보세요](../../resource-mover/move-region-within-resource-group.md).
- **Resource Mover 허브에서 직접 리소스 이동 시작**: 이 방법을 사용하면 허브에서 직접 지역 이동 프로세스를 시작합니다. [자세히 알아보기](../../resource-mover/tutorial-move-region-virtual-machines.md).

## <a name="next-steps"></a>다음 단계

- 리소스 종류가 이동을 지원하는지 확인하려면 [리소스에 대한 이동 작업 지원](move-support-resources.md)을 참조하세요.
- 지역 이동 프로세스에 대해 자세히 알아보려면 [이동 프로세스 정보](../../resource-mover/about-move-process.md)를 참조하세요.
