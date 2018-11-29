---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/21/2018
ms.author: cephalin
ms.openlocfilehash: e7494db94c7d8f0fc610ab297798749bffd55e7c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331261"
---
## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것 같으면 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.