---
title: Azure Resource Manager vCPU 할당량 증가 요청
description: Azure Resource Manager vCPU 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843670"
---
# <a name="quota-increase-requests"></a>할당량 증가 요청

가상 시스템 및 가상 시스템 규모 집합에 대한 리소스 관리자 vCPU 할당량은 각 지역의 구독에 대해 두 계층으로 적용됩니다.

첫 번째 계층은 모든 VM 시리즈의 총 지역 vCPU 제한입니다. 두 번째 계층은 D 시리즈 vCPU와 같은 VCpu당 vCPU 제한입니다. 새 가상 컴퓨터를 배포할 때마다 해당 VM 시리즈에 대한 새 vCPU 사용량과 기존 vCPU 사용량의 합계가 특정 VM 시리즈에 대해 승인된 vCPU 할당량을 초과해서는 안 됩니다. 또한 모든 VM 시리즈에 배포된 총 신규 및 기존 vCPU 수는 구독에 대해 승인된 총 지역 vCPU 할당량을 초과해서는 안 됩니다. 이러한 할당량 중 하나를 초과하면 VM 배포가 허용되지 않습니다.
Azure 포털에서 VM 시리즈에 대한 vCPU 할당량 제한 증가를 요청할 수 있습니다. VM 계열 할당량이 증가하면 총 지역 vCPU 한도가 동일한 양만큼 자동으로 증가합니다.

새 구독이 생성되면 기본 총 지역 vCPU가 모든 개별 VM 계열에 대한 기본 vCPU 할당량합계와 같지 않을 수 있습니다. 이 사실은 배포하려는 각 개별 VM 시리즈에 대해 충분한 할당량을 가진 구독을 초래할 수 있습니다. 모든 배포에 대해 총 지역 vCPU에 대한 할당량이 부족할 수 있습니다. 이 경우 총 지역 vCPU 제한을 명시적으로 늘리려면 요청을 제출해야 합니다. 총 지역 vCPU 한도는 리전의 모든 VM 계열에서 승인된 할당량의 합계를 초과할 수 없습니다.

할당량에 대한 자세한 내용은 [가상 컴퓨터 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 Azure 구독 및 서비스 [제한, 할당량 및 제약 조건을](../../azure-resource-manager/management/azure-subscription-service-limits.md)참조하십시오.

