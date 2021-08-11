---
title: Azure Cloud Services(추가 지원)에서 사용 가능한 크기
description: Azure Cloud Services(추가 지원) 배포에 사용할 수 있는 크기
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166281"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에서 사용 가능한 크기

이 문서에서는 Cloud Services(추가 지원) 인스턴스에 사용 가능한 가상 머신 크기를 설명합니다.   

| SKU 제품군 |  ACU/코어 | 
|---|---|
| [A5-7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 - 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 - 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240* |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> *로 표시된 ACU는 Intel® 터보 기술을 사용하여 CPU 주파수를 증가시키고 성능 향상을 제공합니다. 상승량은 VM 크기, 워크로드 및 동일한 호스트에서 실행 중인 다른 작업에 따라 달라질 수 있습니다.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Cloud Services(추가 지원)의 크기 구성

서비스 정의 파일에서 서비스 모델의 일부로 역할 인스턴스의 가상 머신 크기를 지정할 수 있습니다. 역할의 크기에 따라 실행 인스턴스에 할당되는 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정됩니다.

예를 들어 웹 역할 인스턴스 크기를 `Standard_D2`로 설정합니다. 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>기존 역할의 크기 변경

기존 역할의 크기를 변경하려면 서비스 정의 파일(csdef)에서 가상 머신 크기를 변경하고 클라우드 서비스를 다시 패키징한 후 다시 배포합니다. 

## <a name="get-a-list-of-available-sizes"></a>사용 가능한 크기의 목록을 가져옵니다 

사용 가능한 크기 목록을 검색하려면 [리소스 SKU - 목록](/rest/api/compute/resourceskus/list)을 참조하고 다음 필터를 적용합니다.


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
