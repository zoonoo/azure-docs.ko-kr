---
title: 포함 파일
description: 포함 파일
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 3d2581d9accb7d9f98b456b54f88fc25074d3f65
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721798"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정

이전에 Azure 구독에서 Event Grid를 사용하지 않은 경우 Event Grid 리소스 공급자를 등록해야 할 수 있습니다. 다음 명령을 실행합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

등록을 완료하는 데 잠시 시간이 걸릴 수 있습니다. 상태를 확인하려면 다음을 실행합니다.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

`RegistrationStatus`가 `Registered`이면 진행할 준비가 된 것입니다.
