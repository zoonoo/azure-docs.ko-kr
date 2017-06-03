---
title: "확장용 Azure 가상 컴퓨터 크기 집합 설계 | Microsoft Docs"
description: "확장용 Azure 가상 컴퓨터 크기 집합을 설계하는 방법에 대해 알아보기"
keywords: "linux 가상 컴퓨터, 가상 컴퓨터 크기 집합"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0
ms.contentlocale: ko-kr
ms.lasthandoff: 02/14/2017


---
# <a name="designing-vm-scale-sets-for-scale"></a>확장용 VM 크기 집합 설계
이 항목은 가상 컴퓨터 크기 집합을 설계할 때 고려할 사항에 대해 논의합니다. 가상 컴퓨터 크기 집합에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md)를 참조하세요.

## <a name="storage"></a>저장소

### <a name="scale-sets-with-azure-managed-disks"></a>Azure Managed Disks를 사용하는 크기 집합
이제 [Azure Managed Disks](../storage/storage-managed-disks-overview.md)를 사용하여 크기 집합을 만들 수 있습니다. Managed Disks는 다음과 같은 이점을 제공합니다.
- 크기 집합 VM에 대한 Azure 저장소 계정의 집합을 미리 만들 필요가 없습니다.
- 크기 집합에서 VM에 대한 [연결된 데이터 디스크](virtual-machine-scale-sets-attached-disks.md)를 정의할 수 있습니다.
- [집합에서 최대 1,000개의 VM을 지원](virtual-machine-scale-sets-placement-groups.md)하도록 크기 집합을 구성할 수 있습니다. 

Azure Compute API의 버전 "2016-04-30-미리 보기"로 시작하는 Managed Disks를 사용하여 크기 집합을 만들 수 있습니다. 크기 집합 템플릿을 Managed Disks로 변환에 대한 자세한 내용은 [크기 집합 템플릿을 관리되는 디스크 크기 집합 템플릿으로 변환](virtual-machine-scale-sets-convert-template-to-md.md)을 참조하세요.

### <a name="user-managed-storage"></a>사용자 관리 저장소
Azure Managed Disks로 정의되지 않은 크기 집합은 사용자가 생성한 저장소 계정을 사용하여 집합에 VM의 OS 디스크를 저장합니다. 저장소 계정당 20개 이하의 VM 비율은 최대 IO를 수행하고 _오버프로비전_을 활용하는 것도 좋습니다(아래 참조). 또한 저장소 계정 이름의 시작하는 문자에 알파벳을 고르게 사용하는 것이 좋습니다. 그러면 여러 내부 시스템에서 부하를 분산할 수 있습니다. 

>[!NOTE]
>VM Scale Sets API 버전 `2016-04-30-preview`는 운영 체제 디스크 및 모든 추가 데이터 디스크에 대한 Azure Managed Disks 사용을 지원합니다. 자세한 내용은 [Managed Disks 개요](../storage/storage-managed-disks-overview.md) 및 [연결된 데이터 디스크 사용](virtual-machine-scale-sets-attached-disks.md)을 참조하세요. 

## <a name="overprovisioning"></a>오버프로비전
"2016-03-30" API 버전부터 VM 크기 집합은 기본적으로 "오버프로비전" VM이 됩니다. 오버프로비전을 켜면 크기 집합은 실제로 사용자가 요청한 것보다 더 많은 VM을 작동한 다음 요청된 VM 수가 성공적으로 프로비전되면 추가 VM을 삭제합니다. 오버프로비전은 프로비전 성공률을 높이고 배포 시간을 절약합니다. 추가 VM에 대한 추가 비용은 청구되지 않으며 할당량 한도에 포함되지 않습니다.

오버프로비전을 사용하면 프로비전 성공률이 향상되지만 나타난 다음 사라지는 추가 VM을 처리하도록 설계되지 않은 응용 프로그램에서 동작이 혼란스러워질 수 있습니다. 오버프로비전을 해제하려면 템플릿에 "overprovision": "false" 문자열이 있어야 합니다. 자세한 내용은 [VM 크기 집합 REST API 설명서](https://msdn.microsoft.com/library/azure/mt589035.aspx)에서 찾을 수 있습니다.

크기 집합이 사용자 관리 저장소를 사용하고 오버프로비전을 해제하는 경우 저장소 계정당 20개 이상의 VM을 가질 수 있지만 IO 성능상의 이유로 40개 이상은 권장되지 않습니다. 

## <a name="limits"></a>제한
Marketplace 이미지에 구축되고(플랫폼 이미지라고도 함) Azure Managed Disks를 사용하도록 구성된 크기 집합은 최대 1,000개의 VM 용량을 지원합니다. 100개 이상의 VM을 지원하도록 크기 집합을 구성하는 경우 모든 시나리오가 동일하게 동작하지 않습니다(예: 부하 분산). 자세한 내용은 [대규모 가상 컴퓨터 크기 집합과 작동](virtual-machine-scale-sets-placement-groups.md)을 참조하세요. 

사용자 관리 저장소 계정으로 구성된 크기 집합은 현재 100개의 VM으로 제한되어 있습니다(5개의 저장소 계정이 이 규모에 권장됨).

사용자 지정 이미지에 구축된 크기 집합은 Azure Managed Disks로 구성된 경우 최대 100개의 VM 용량을 가질 수 있습니다. 크기 집합이 사용자 관리 저장소 계정으로 구성된 경우 하나의 저장소 계정 내에서 모든 OS 디스크 VHD를 만들어야 합니다. 그 결과 사용자 지정 이미지 및 사용자 관리 저장소에 구축하는 크기 집합의 최대 추천 VM 수는 20개입니다. 오버프로비전을 해제할 경우 40개를 초과할 수 있습니다.

이러한 한도보다 더 많은 VM이 있는 경우 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)에 나오는 것처럼 여러 크기 집합을 배포해야 합니다.


