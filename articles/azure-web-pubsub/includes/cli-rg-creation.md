---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: c2f05a81e52edc858bc13bc016e030236241a6f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785572"
---
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```
