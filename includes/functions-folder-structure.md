---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809471"
---
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일 및 하나 이상의 하위 폴더를 포함하는 루트 폴더(`wwwroot`)에 있습니다. 각 하위 폴더에는 다음 예제와 같이 개별 함수에 대한 코드가 포함됩니다.

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

host.json 파일은 일부 런타임별 구성을 포함하며 함수 앱의 루트 폴더에 있습니다. 사용할 수 있는 설정에 대한 자세한 내용은 [host.json 참조](../articles/azure-functions/functions-host-json.md)를 참조하세요.

각 함수에는 하나 이상의 코드 파일, function.json 구성 및 기타 종속성을 포함하는 폴더가 있습니다. C # 클래스 라이브러리 프로젝트의 경우 컴파일된 클래스 라이브러리(.dll) 파일은 `bin` 하위 폴더에 배포됩니다.

