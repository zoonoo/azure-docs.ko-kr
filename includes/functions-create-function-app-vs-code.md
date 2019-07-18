---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444616"
---
## <a name="create-an-azure-functions-project"></a>함수를 사용하여 Functions 프로젝트 만들기 

Visual Studio Code의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 함수 앱을 통해 함수를 논리적 단위로 그룹화하여 관리, 배포 및 리소스를 공유할 수 있습니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Create new project...`를 검색하여 선택합니다.

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택**을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 지시에 따라 다음 정보를 제공합니다.

    | prompt | 값 | 설명 |
    | ------ | ----- | ----------- |
    | 함수 앱 프로젝트에 대한 언어를 선택합니다. | C# 또는 JavaScript | 이 문서에서는 C# 및 JavaScript를 지원합니다. Python의 경우 [이 Python 문서](https://code.visualstudio.com/docs/python/tutorial-azure-functions), PowerShell의 경우 [이 PowerShell 문서](../articles/azure-functions/functions-create-first-function-powershell.md)를 참조하세요.  |
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 새 함수 앱에서 HTTP 트리거 함수를 만듭니다. |
    | 함수 이름 제공 | HttpTrigger | Enter 키를 눌러 기본 이름을 사용합니다. |
    | 네임스페이스 제공 | My.Functions | (C#만 해당) C# 클래스 라이브러리에 네임스페이스가 있어야 합니다.  |
    | 권한 부여 수준 | 함수 | 함수의 HTTP 엔드포인트를 호출하는 [함수 키](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)가 필요합니다. |
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 | 현재 작업 영역에 함수 앱을 만듭니다. |

Visual Studio Code는 새 작업 영역에서 함수 앱 프로젝트를 만듭니다. 이 프로젝트에는 [host.json](../articles/azure-functions/functions-host-json.md) 및 [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) 구성 파일과 언어별 프로젝트 파일이 포함됩니다. 

또한 새 HTTP 트리거 함수가 함수 앱 프로젝트의 HttpTrigger 폴더에 만들어집니다.