---
title: Azure Cloud Services의 사용 가능한 크기 (확장 된 지원)
description: Azure Cloud Services (확장 지원) 배포에 사용할 수 있는 크기
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd8011782d134031393731a29594d44aba41b2ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713306"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure Cloud Services의 사용 가능한 크기 (확장 된 지원)

이 문서에서는 Cloud Services (확장 지원) 인스턴스에 대해 사용 가능한 가상 컴퓨터 크기를 설명 합니다.   

| SKU 제품군 |  ACU/코어 | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190*
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> *로 표시된 ACU는 Intel® 터보 기술을 사용하여 CPU 주파수를 증가시키고 성능 향상을 제공합니다. 상승량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 작업에 따라 달라질 수 있습니다.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Cloud Services 크기 구성 (확장 지원)

서비스 정의 파일에서 서비스 모델의 일부로 역할 인스턴스의 가상 머신 크기를 지정할 수 있습니다. 역할의 크기에 따라 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정 됩니다.

예를 들어 웹 역할 인스턴스 크기를로 설정 합니다 `Standard_D2` . 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>기존 역할의 크기 변경

기존 역할의 크기를 변경 하려면 서비스 정의 파일 (.csdef)에서 가상 머신 크기를 변경 하 고 클라우드 서비스를 다시 패키지 한 후 다시 배포 합니다. 

## <a name="get-a-list-of-available-sizes"></a>사용 가능한 크기 목록 가져오기 

사용 가능한 크기 목록을 검색 하려면 [리소스 sku](/rest/api/compute/resourceskus/list) 를 참조 하 고 다음 필터를 적용 합니다.


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.