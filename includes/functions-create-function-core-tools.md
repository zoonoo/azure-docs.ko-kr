---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181982"
---
## <a name="create-a-function"></a>함수 만들기

다음 명령은 이름이 `MyHttpTrigger`인 HTTP 트리거 함수를 만듭니다.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
