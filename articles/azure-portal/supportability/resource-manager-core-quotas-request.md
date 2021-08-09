---
title: Azure Resource Manager vCPU 할당량 증가 요청
description: Azure Resource Manager vCPU 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745250"
---
# <a name="quota-increase-requests"></a>할당량 증가 요청

가상 머신 및 가상 머신 확장 집합에 대한 Resource Manager의 vCPU 할당량은 각 지역의 구독 두 가지 계층으로 적용됩니다.

첫 번째 계층은 모든 VM 시리즈에 대한 지역 vCPU의 총한도입니다. 두 번째 계층은 D 시리즈 vCPU와 같은 VM 시리즈당 vCPU 한도입니다. 새 가상 머신을 배포할 때마다 해당 VM 시리즈의 신규 및 기존 vCPU 사용량의 합계는 특정 VM 시리즈에 대해 승인된 vCPU 할당량을 초과하지 않아야 합니다. 또한 모든 VM 시리즈에 배포된 신규 및 기존 vCPU 수는 해당 구독에 대해 승인된 총 지역 vCPU 할당량을 초과하지 않아야 합니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.
Azure Portal에서 VM 시리즈에 대한 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 총 지역 vCPU 한도가 같은 양만큼 자동으로 증가합니다.

새 구독을 만들 때 기본 총 지역 vCPU는 모든 개별 VM 시리즈에 대한 기본 vCPU 할당량의 합계와 같지 않을 수 있습니다. 이 경우 배포하려는 개별 VM 시리즈에 대한 할당량이 충분한 구독이 생성될 수 있습니다. 그러면 모든 배포의 총 지역 vCPU에 대해서는 할당량이 부족할 수 있습니다. 이 경우 총 지역 vCPU 한도를 명시적으로 늘리기 위해 요청을 제출해야 합니다. 총 지역 vCPU 한도는 해당 지역의 모든 VM 시리즈에서 승인된 할당량의 합계를 초과할 수 없습니다.

> [!NOTE]
> 기본 한도 이상으로 한도 또는 할당량을 높이려는 경우 비용 없이 [온라인 고객 지원 요청을 개설](../../azure-resource-manager/templates/error-resource-quota.md#solution)합니다.

자세한 내용은 [가상 머신 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독과 서비스 한도, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.