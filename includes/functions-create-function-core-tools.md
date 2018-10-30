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
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987980"
---
## <a name="create-a-function"></a>함수 만들기

다음 명령은 이름이 `MyHtpTrigger`인 HTTP 트리거 함수를 만듭니다.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```