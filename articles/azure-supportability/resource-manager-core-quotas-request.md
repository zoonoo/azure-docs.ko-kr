---
title: Azure Resource Manager vCPU 할당량 증가 요청 | Microsoft Docs
description: Azure Resource Manager vCPU 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076820"
---
# <a name="quota-increase-requests"></a>할당량 증가 요청

Virtual machines 및 virtual machine scale sets에 대 한 resource Manager vCPU 할당량은 각 지역의 각 구독에 대 한 두 가지 계층에서 적용 됩니다. 

첫 번째 계층은 지역별 총 Vcpu 제한 (모든 VM 시리즈)에 걸쳐 이며 두 번째 계층은 합니다 (예: D-시리즈 Vcpu) VM 시리즈 Vcpu 당 합니다. 새 VM을 배포 해야 하는 해당 VM 시리즈에 대 한 신규 및 기존 Vcpu 사용 합계는 특정 VM 시리즈에 대 한 승인 vCPU 할당량을 넘지 않아야 합니다. 또한 모든 VM 시리즈 간에 배포 된 총 신규 및 기존 vCPU 수는 구독에 대 한 승인 하는 지역별 총 Vcpu 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.
Azure portal에서 VM 시리즈에 대 한 Vcpu 할당량 제한 증가 요청할 수 있습니다. VM 시리즈 할당량 증가 같은 크기 만큼 지역별 총 Vcpu 한계를 자동으로 증가합니다. 

새 구독이 생성 된 기본 지역별 총 Vcpu 모든 개별 VM 시리즈에 대 한 기본 vCPU 할당량의 합으로 같지 않을 수 있습니다. 이 구독에 배포 하려는 각 개별 VM 시리즈에 대 한 충분 한 할당량이 있지만 모든 배포에 대 한 지역별 총 vcpu 할당량이 충분 하지 발생할 수 있습니다. 이 경우에 늘리려면 지역별 총 Vcpu 명시적으로 요청을 제출 해야 합니다. 지역별 총 Vcpu 제한 된 영역에 대 한 모든 VM 시리즈에서 승인 된 할당량의 합계를 초과할 수 없습니다.

에 대 한 자세한 할당량에는 [가상 머신 vCPU 할당량 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한](https://aka.ms/quotalimits) 페이지입니다. 

