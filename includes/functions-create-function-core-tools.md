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
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429999"
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
