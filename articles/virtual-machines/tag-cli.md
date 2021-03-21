---
title: CLI를 사용 하 여 Azure 가상 머신에 태그를 만드는 방법
description: Azure CLI를 사용 하 여 가상 컴퓨터에 태그를 지정 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897445"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>CLI를 사용 하 여 VM에 태그를 만드는 방법

이 문서에서는 Azure CLI를 사용 하 여 VM에 태그를 만드는 방법을 설명 합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50 개의 태그를 지원 합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다. Azure [PowerShell](tag-powershell.md)을 사용 하 여 가상 컴퓨터에 태그를 지정할 수도 있습니다.


을 사용 하 여 태그를 포함 하 여 지정 된 VM에 대 한 모든 속성을 볼 수 있습니다 `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Azure CLI를 통해 새 VM 태그를 추가 하려면 `azure vm update` 태그 매개 변수와 함께 명령을 사용 하면 됩니다 `--set` .

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

태그를 제거 하려면 `--remove` 명령에서 매개 변수를 사용할 수 있습니다 `azure vm update` .

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

이제 리소스 Azure CLI 및 포털에 태그를 적용 했으므로 청구 포털에서 태그를 확인 하는 데 사용 정보를 살펴보겠습니다.

### <a name="next-steps"></a>다음 단계

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그를 사용 하 여 Azure 리소스의 사용을 관리 하는 방법을 알아보려면 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md)를 참조 하세요.
