---
title: "관리 디스크를 Azure 가상 컴퓨터 크기 집합과 함께 사용 | Microsoft Docs"
description: "관리 디스크를 가상 컴퓨터 크기 집합과 함께 사용하는 이유 및 방법 알아보기"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/21/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 4ec20a30f423d4b30e953f873100be90167f987b
ms.lasthandoff: 03/29/2017


---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM 크기 집합 및 관리 디스크

이제 Azure [가상 컴퓨터 크기 집합](/azure/virtual-machine-scale-sets/)은 관리 디스크가 있는 가상 컴퓨터를 지원합니다. 크기 집합이 있는 관리 디스크를 사용하면 다음과 같은 여러 가지 이점이 있습니다.

* 크기 집합 VM에 대한 OS 디스크를 저장하기 위해 더 이상 저장소 계정을 미리 만들어서 관리할 필요가 없습니다.

* 크기 집합에 관리 데이터 디스크를 연결할 수 있습니다.

* 관리 디스크를 사용하면 크기 집합이 플랫폼 이미지 기반인 경우 VM 1,000대, 사용자 지정 이미지 기반인 경우 VM 100대의 용량을 확보할 수 있습니다.

## <a name="get-started"></a>시작

관리 디스크 크기 집합을 시작하는 간단한 방법은 Azure Portal에서 배포하는 것입니다. 자세한 내용은 [이 문서](./virtual-machine-scale-sets-portal-create.md)(영문)를 읽어보세요. 또 다른 간단한 방법은 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)을 사용하여 크기 집합을 배포하는 것입니다. 다음 예에서는 VM이 10대이고 각 VM에 50GB 및 100GB 데이터 디스크가 포함된 Ubuntu 기반 크기 집합을 만드는 방법을 보여줍니다.

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

또는 `vmss`가 포함된 폴더에 대한 [Azure 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 크기 집합을 배포하는 미리 작성된 템플릿 예제를 볼 수 있습니다. 이미 관리 디스크를 사용하는 템플릿을 알아보려면 [이 목록](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)을 참조하세요.

## <a name="api-versions"></a>API 버전

관리 디스크를 사용하는 크기 집합의 최신 일반 공급 API 버전은 `2016-04-30-preview`입니다. 비관리 디스크를 사용하는 크기 집합은 계속해서 현재와 같이 작동할 것이며, 심지어 관리 디스크를 지원하는 새 API 버전에서도 마찬가지입니다. 하지만 비관리 디스크를 사용하는 크기 집합은 새 api 버전에서도 관리 디스크의 이점을 누릴 수 없습니다.

## <a name="next-steps"></a>다음 단계

일반적인 관리 디스크에 대한 자세한 내용은 [이 문서](../storage/storage-managed-disks-overview.md)를 참조하세요.

Resource Manager 템플릿을 변환하여 크기 집합에 관리 디스크를 프로비전하는 방법은 [이 문서](./virtual-machine-scale-sets-convert-template-to-md.md)를 참조하세요. Resource Manager 템플릿의 수정 사항은 Azure REST API에도 똑같이 적용됩니다.

관리 디스크를 크기 집합과 함께 사용하는 자세한 방법은 [이 문서](./virtual-machine-scale-sets-attached-disks.md)를 참조하세요.

대규모 크기 집합을 시작하려면 [이 문서](./virtual-machine-scale-sets-placement-groups.md)를 참조하세요.



