---
title: 파일 포함
description: 포함 파일
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 579ea74c4767ba1afb6e0128c9f12ff83f7f3998
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97826589"
---
## <a name="enable-the-event-grid-resource-provider"></a>Event Grid 리소스 공급자 사용

이전에 Azure 구독에서 Event Grid를 사용하지 않은 경우 Event Grid 리소스 공급자를 등록해야 할 수 있습니다. 공급자를 등록하는 다음 명령을 실행합니다.

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

등록을 완료하는 데 잠시 시간이 걸릴 수 있습니다. 상태를 확인하려면 다음을 실행합니다.

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

`registrationState`가 `Registered`이면 진행할 준비가 된 것입니다.
