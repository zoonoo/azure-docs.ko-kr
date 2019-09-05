---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "68444141"
---
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 함수 앱, 데이터베이스, 스토리지 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 `myResourceGroup`이라는 리소스 그룹을 만듭니다.  
Cloud Shell을 사용하지 않는 경우 먼저 `az login`을 사용하여 로그인합니다.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

일반적으로 사용자와 가까운 [지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 리소스 그룹 및 리소스를 만듭니다. 
