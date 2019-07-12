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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594553"
---
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일 및 하나 이상의 하위 폴더를 포함하는 루트 프로젝트 폴더에 있습니다. 각 하위 폴더에는 별도 함수에 대 한 코드를 포함합니다. 폴더 구조는 다음 표현에 표시 됩니다.

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

버전 2.x의 Functions 런타임, 함수 앱의 모든 함수는 동일한 언어 스택을 공유 해야 합니다.  

합니다 [host.json](../articles/azure-functions/functions-host-json.md) 파일 런타임 별 구성을 포함 하 고 함수 앱의 루트 폴더에 있습니다. A *bin* 패키지 및 함수 앱에 필요한 기타 라이브러리 파일 폴더에 포함 되어 있습니다. 함수 앱 프로젝트에 대한 언어별 요구 사항을 참조하세요.

* [C# 클래스 라이브러리(.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 스크립트(.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 스크립트](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



