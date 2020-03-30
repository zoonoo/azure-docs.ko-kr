---
title: 포털을 사용하여 Azure 스팟 VM 배포
description: Azure PowerShell을 사용하여 스팟 VM을 배포하여 비용을 절감하는 방법을 알아봅니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158981"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>미리 보기: Azure 포털을 사용하여 스팟 VM 배포

[스팟 VM을](spot-vms.md) 사용하면 사용되지 않는 용량을 크게 절감할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스팟 VM을 제거합니다. 따라서 스팟 VM은 일괄 처리 작업, 개발/테스트 환경, 대규모 컴퓨팅 워크로드 등과 같은 중단을 처리할 수 있는 워크로드에 적합합니다.

스팟 VM의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows용](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)VM 가격을 참조하십시오. 최대 가격 설정에 대한 자세한 내용은 [스팟 VM - 가격 책정을](spot-vms.md#pricing)참조하십시오.

VM에 대해 시간당 지불할 최대 가격을 설정할 수 있습니다. 스팟 VM의 최대 가격은 소수점 이하 5자리까지 사용하여 미국 달러(USD)로 설정할 수 있습니다. 예를 들어 값은 `0.05701`시간당 0.05701 USD의 최대 가격입니다. 최대 가격을 `-1`설정하면 가격에 따라 VM이 제거되지 않습니다. VM의 가격은 현재 현물 가격 또는 표준 VM의 가격이 될 것이며, 용량과 할당량이 있는 한 더 적습니다.

> [!IMPORTANT]
> 스팟 인스턴스는 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>


## <a name="create-the-vm"></a>VM 만들기

스팟 VM을 사용하는 VM을 만드는 프로세스는 빠른 [시작에](quick-create-portal.md)자세히 설명된 것과 동일합니다. VM을 배포할 때 Azure 스팟 인스턴스를 사용하도록 선택할 수 있습니다.


**기본** 탭에서 **인스턴스 세부 정보** 섹션에서 **아니요는** Azure 스팟 인스턴스를 사용하는 기본값입니다.

![아니요를 선택하기 위한 화면 캡처, Azure 스팟 인스턴스를 사용하지 마십시오.](media/spot-portal/no.png)

**예,** 섹션이 확장되고 [제거 유형 및 제거 정책을](spot-vms.md#eviction-policy)선택할 수 있습니다. 

![예 선택을 위한 화면 캡처, Azure 스팟 인스턴스 사용](media/spot-portal/yes.png)


## <a name="next-steps"></a>다음 단계

[PowerShell](spot-powershell.md)을 사용하여 스팟 VM을 만들 수도 있습니다.