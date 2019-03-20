---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f953c7ae1bba774a69bffddb148c93609c9a85c
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "58114670"
---
## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스의 작업을 마쳤으면 다음 명령을 사용하여 해당 리소스를 구독에서 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

메시지가 표시되면 `y`를 입력합니다. 이 명령은 `myResourceGroup` 리소스 그룹과 이 리소스 그룹에 속한 모든 리소스를 제거합니다.