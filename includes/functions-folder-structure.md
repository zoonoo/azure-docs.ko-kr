---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205688"
---
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일 및 하나 이상의 하위 폴더를 포함하는 루트 프로젝트 폴더에 있습니다. 각 하위 폴더에는 별도의 함수에 대한 코드가 포함되어 있습니다. 폴더 구조는 다음 표현에 표시됩니다.

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

버전 2.x 이상에서 함수 런타임의 경우 함수 앱의 모든 함수가 동일한 언어 스택을 공유해야 합니다.  

[host.json](../articles/azure-functions/functions-host-json.md) 파일런타임 관련 구성을 포함 하며 함수 앱의 루트 폴더에 있습니다. *Bin* 폴더에는 함수 앱에 필요한 패키지 및 기타 라이브러리 파일이 포함되어 있습니다. 함수 앱 프로젝트에 대한 언어별 요구 사항을 참조하세요.

* [C# 클래스 라이브러리(.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# 스크립트(.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F# 스크립트](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [자바 스크립트](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
