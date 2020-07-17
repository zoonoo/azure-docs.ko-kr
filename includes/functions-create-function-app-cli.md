---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950008"
---
## <a name="create-the-local-function-app-project"></a>로컬 함수 앱 프로젝트 만들기

명령줄에서 다음 명령을 실행하여 현재 로컬 디렉터리의 `MyFunctionProj` 폴더에 함수 앱 프로젝트를 만듭니다. GitHub 리포지토리도 `MyFunctionProj`에서 생성됩니다.

```command
func init MyFunctionProj
```

메시지가 표시되면 다음 언어 선택에서 작업자 런타임을 선택합니다.

+ `dotnet`: .NET 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
+ `node`: Node.js 기반 프로젝트를 만듭니다. `javascript` 또는 `typescript` 중 하나를 선택합니다. 
+ `python`: Python 프로젝트의 경우 대신 [Azure에서 HTTP 트리거 함수 만들기](../articles/azure-functions/functions-create-first-function-python.md)를 완료하세요.
+ `powershell`: PowerShell 프로젝트의 경우 대신 [Azure에서 첫 번째 PowerShell 함수 만들기](../articles/azure-functions/functions-create-first-function-powershell.md)를 완료하세요. 


프로젝트를 만든 후 다음 명령을 사용하여 새 `MyFunctionProj` 프로젝트 폴더로 이동합니다.

```command
cd MyFunctionProj
```
