---
title: 관리 디스크를 Azure Virtual Machine Scale Sets와 함께 사용 | Microsoft Docs
description: 관리 디스크를 가상 머신 확장 집합과 함께 사용하는 이유 및 방법 알아보기
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 82fa518e6c0498a13f950ce33c51be8581918f9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201141"
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM 확장 집합 및 관리 디스크

Azure [가상 머신 확장 집합](/azure/virtual-machine-scale-sets/)에서는 관리 디스크가 있는 가상 머신을 지원합니다. 확장 집합이 있는 관리 디스크를 사용하면 다음과 같은 여러 가지 이점이 있습니다.

* 확장 집합 VM에 대한 OS 디스크를 저장하기 위해 더 이상 저장소 계정을 미리 만들어서 관리할 필요가 없습니다.

* 확장 집합에 관리 데이터 디스크를 연결할 수 있습니다.

* 관리 디스크를 사용하면 확장 집합이 플랫폼 이미지 기반인 경우 VM 1,000대, 사용자 지정 이미지 기반인 경우 VM 300대의 용량을 확보할 수 있습니다.

## <a name="get-started"></a>시작하기

관리 디스크 확장 집합을 시작하는 간단한 방법은 Azure Portal에서 배포하는 것입니다. 자세한 내용은 [이 문서](./virtual-machine-scale-sets-portal-create.md)(영문)를 읽어보세요. 또 다른 간단한 방법은 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)을 사용하여 확장 집합을 배포하는 것입니다. 다음 예에서는 VM이 10대이고 각 VM에 50GB 및 100GB 데이터 디스크가 포함된 Ubuntu 기반 확장 집합을 만드는 방법을 보여줍니다.

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

또는 `vmss`가 포함된 폴더에 대한 [Azure 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 확장 집합을 배포하는 미리 작성된 템플릿 예제를 볼 수 있습니다. 이미 관리 디스크를 사용하는 템플릿을 알아보려면 [이 목록](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

일반적인 관리 디스크에 대한 자세한 내용은 [이 문서](../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

Resource Manager 템플릿을 변환하여 확장 집합에 관리 디스크를 프로비전하는 방법은 [이 문서](./virtual-machine-scale-sets-convert-template-to-md.md)를 참조하세요. Resource Manager 템플릿의 수정 사항은 Azure REST API에도 똑같이 적용됩니다.

관리 디스크를 확장 집합과 함께 사용하는 자세한 방법은 [이 문서](./virtual-machine-scale-sets-attached-disks.md)를 참조하세요.

대규모 확장 집합을 시작하려면 [이 문서](./virtual-machine-scale-sets-placement-groups.md)를 참조하세요.


