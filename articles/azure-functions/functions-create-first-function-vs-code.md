---
title: Visual Studio Code를 사용하여 Azure에서 첫 번째 함수 만들기
description: Visual Studio Code에서 Azure Functions 확장을 사용하여 간단한 HTTP 트리거 함수를 Azure에 만들고 게시합니다.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842259"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 Azure Functions 프로젝트 만들기

이 문서에서는 Visual Studio Code를 사용하여 HTTP 요청에 응답하는 함수를 만듭니다. 코드를 로컬로 테스트한 후 Azure Functions의 서버리스 환경에 배포합니다. 이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다. 

이 문서의 [CLI 기반 버전](functions-create-first-azure-function-azure-cli.md)도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/). 
::: zone pivot="programming-language-csharp"
+ Visual Studio Code용 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
::: zone-end
::: zone pivot="programming-language-python"
+ Azure Functions에서 지원되는 [Python 3.7](https://www.python.org/downloads/release/python-375/) 또는 [Python 3.6](https://www.python.org/downloads/release/python-368/). Python 3.8은 아직 지원되지 않습니다. 

+ Visual Studio Code용 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download)

+ [Visual Studio Code용 PowerShell 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 

+ 활성 구독이 있는 [Azure 계정](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만드세요.

## <a name="create-an-azure-functions-project"></a>로컬 프로젝트 만들기 

이 섹션에서는 Visual Studio Code를 사용하여 선택한 언어로 로컬 Azure Functions 프로젝트를 만듭니다. 이 문서의 뒷부분에서 함수 코드를 Azure에 게시합니다. 

1. 작업 막대에서 Azure 아이콘을 선택한 다음, **Azure: Functions** 영역에서 **새 프로젝트 만들기...** 아이콘을 선택합니다.

    ![새 프로젝트 만들기 선택](media/functions-create-first-function-vs-code/create-new-project.png)

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택**을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 이 경우 작업 영역에 포함된 프로젝트 폴더를 선택하지 마십시오.

1. 프롬프트에서 다음 정보를 제공합니다.

    ::: zone pivot="programming-language-csharp"

    | prompt | 값 | 
    | ------ | ----- | 
    | 함수 프로젝트에 대한 언어 선택 | C# |
    | 버전 선택 | Azure Functions v2 | 
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 
    | 함수 이름 제공 | HttpExample | 
    | 네임스페이스 제공 | My.Functions | 
    | 권한 부여 수준 | 익명 | 
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | prompt | 값 | 
    | ------ | ----- | 
    | 함수 프로젝트에 대한 언어 선택 | JavaScript | 
    | 버전 선택 | Azure Functions v2 | 
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 
    | 함수 이름 제공 | HttpExample | 
    | 권한 부여 수준 | 익명 | 
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | prompt | 값 | 
    | ------ | ----- | 
    | 함수 프로젝트에 대한 언어 선택 | TypeScript | 
    | 버전 선택 | Azure Functions v2 | 
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 
    | 함수 이름 제공 | HttpExample | 
    | 권한 부여 수준 | 익명 | 
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | prompt | 값 | 
    | ------ | ----- | 
    | 함수 프로젝트에 대한 언어 선택 | PowerShell | 
    | 버전 선택 | Azure Functions v2 | 
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 
    | 함수 이름 제공 | HttpExample | 
    | 권한 부여 수준 | 익명 | 
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | prompt | 값 | 
    | ------ | ----- | 
    | 함수 프로젝트에 대한 언어 선택 | Python | 
    | 버전 선택 | Azure Functions v2 | 
    | 가상 환경을 만들려면 Python 별칭을 선택합니다. | Python 별칭 | 
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | HTTP 트리거 | 
    | 함수 이름 제공 | HttpExample | 
    | 권한 부여 수준 | 익명 | 
    | 프로젝트를 여는 방법을 선택합니다. | 작업 영역에 추가 | 

    ::: zone-end

1. Visual Studio Code는 다음을 수행합니다.

    + [host.json](functions-host-json.md) 및 [local.settings.json](functions-run-local.md#local-settings-file) 구성 파일이 포함된 새 작업 영역에서 Azure Functions 프로젝트를 만듭니다. 

    ::: zone pivot="programming-language-csharp"

    + 함수를 구현하는 [HttpExample.cs 클래스 라이브러리 파일](functions-dotnet-class-library.md#functions-class-library-project)을 만듭니다.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + 루트 폴더에 package.json 파일을 만듭니다.

    + [function.json 정의 파일](functions-reference-node.md#folder-structure) 및 함수 코드가 포함된 Node.js 파일인 [index.js 파일](functions-reference-node.md#exporting-a-function)을 포함하는 HttpExample 폴더를 만듭니다.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + 루트 폴더에 package.json 파일과 tsconfig.json 파일을 만듭니다.

    + [function.json 정의 파일](functions-reference-node.md#folder-structure) 및 함수 코드가 포함된 TypeScript 파일인 [index.js 파일](functions-reference-node.md#typescript)을 포함하는 HttpExample 폴더를 만듭니다.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + [function.json 정의 파일](functions-reference-python.md#programming-model) 및 함수 코드가 포함된 run.ps1 파일을 포함하는 HttpExample 폴더를 만듭니다.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + 함수에 필요한 패키지를 나열하는 프로젝트 수준 requirements.txt 파일을 만듭니다.
    
    + [function.json 정의 파일](functions-reference-python.md#programming-model) 및 함수 코드가 포함된 \_\_init\_\_.py 파일을 포함하는 HttpExample 폴더를 만듭니다.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 Visual Studio Code를 사용하여 프로젝트를 Azure에 직접 게시합니다. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Azure에서 함수 실행

1. **출력** 패널에서 HTTP 트리거의 URL을 복사합니다. 다시 이 URL 마지막에 `name` 쿼리 문자열을 `?name=<yourname>`으로 추가하고 요청을 실행합니다.

    HTTP 트리거 함수를 호출하는 URL은 다음 형식이어야 합니다.

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 다음 예제에서는 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 함수 응답](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 단계인 [함수에 Azure Storage 큐 바인딩 추가](functions-add-output-binding-storage-queue-vs-code.md)를 계속 진행하는 경우 이미 수행한 작업을 기반으로 모든 리소스를 제자리에 유지해야 합니다.

그렇지 않으면 다음 단계를 수행하여 추가 비용이 발생하지 않도록 함수 앱 및 관련 리소스를 삭제할 수 있습니다.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

함수 비용에 대한 자세한 내용은 [소비 계획 비용 예측](functions-consumption-costs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Visual Studio Code를 사용하여 간단한 HTTP 트리거 함수가 있는 함수 앱을 만들었습니다. 다음 문서에서는 출력 바인딩을 추가하여 해당 함수를 확장합니다. 이 바인딩은 HTTP 요청의 문자열을 Azure Queue Storage 큐의 메시지에 씁니다. 

> [!div class="nextstepaction"]
> [함수에 Azure Storage 큐 바인딩 추가](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
