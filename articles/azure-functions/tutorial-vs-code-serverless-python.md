---
title: Visual Studio Code를 사용 하 여 Python에서 Azure Functions 만들기 및 배포
description: Azure Functions에 대해 Visual Studio Code 확장을 사용 하 여 Python에서 서버를 사용 하지 않는 함수를 만들고 Azure에 배포 하는 방법입니다.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: f5591a3e0ca73649b1ffc51c75aa95e86e286768
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639097"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Visual Studio Code를 사용 하 여 Azure Functions에 Python 배포

이 자습서에서는 Visual Studio Code 및 Azure Functions 확장을 사용 하 여 Python을 사용 하 여 서버 리스 HTTP 끝점을 만들고 저장소에 연결 (또는 "바인딩")도 추가 합니다. Azure Functions는 가상 컴퓨터를 프로 비전 하거나 웹 앱을 게시할 필요 없이 서버를 사용 하지 않는 환경에서 코드를 실행 합니다. Visual Studio Code에 대 한 Azure Functions 확장은 많은 구성 문제를 자동으로 처리 하 여 함수를 사용 하는 프로세스를 크게 간소화 합니다.

이 자습서의 단계 중 하나에 문제가 발생 하는 경우 세부 정보에 대 한 의견을 보내 주시기 바랍니다. 각 섹션의 끝에 있는 **문제가 발생** 했습니다. 단추를 사용 하 여 자세한 피드백을 제출 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure 구독](#azure-subscription).
- [Azure Functions 확장을 사용 하 여 Visual Studio Code](#visual-studio-code-python-and-the-azure-functions-extension) 합니다.
- [Azure Functions Core Tools](#azure-functions-core-tools)입니다.

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 없는 경우 Azure 크레딧을 사용 하 여 $200의 무료 30 일 계정에 [등록](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) 하 여 모든 서비스 조합을 사용해 보세요.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python 및 Azure Functions 확장

다음 소프트웨어를 설치 합니다.

- Azure Functions에 필요한 Python 3.6. x [Python 3.6.8](https://www.python.org/downloads/release/python-368/) 은 최신 3.6. x 버전입니다.
- 및[Visual Studio Code](https://code.visualstudio.com/)가 있습니다.
- Python [확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 은 [Visual Studio Code Python 자습서-필수 구성 요소](https://code.visualstudio.com/docs/python/python-tutorial)에 설명 되어 있습니다.
- [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)입니다. 일반 정보는 [vscode-Azurefunctions GitHub 리포지토리](https://github.com/Microsoft/vscode-azurefunctions)를 참조 하세요.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

[Azure Functions Core Tools에서 사용](functions-run-local.md#v2)하는 운영 체제에 대 한 지침을 따르세요. 도구 자체는 .NET Core로 작성 되며, 핵심 도구 패키지는 node.js 패키지 관리자 (npm)를 사용 하 여 설치 하는 것이 가장 좋습니다. 이러한 이유 때문에 Python 코드의 경우에도 .NET Core 및 node.js를 설치 해야 합니다. 하지만 앞서 언급 한 설명서에 설명 된 대로 "확장 번들"을 사용 하 여 .NET Core 요구 사항을 우회할 수 있습니다. 어떤 경우 든 이러한 구성 요소를 한 번만 설치 해야 하며, 그 후에 Visual Studio Code 업데이트를 설치 하 라는 메시지가 자동으로 표시 됩니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

함수 확장이 설치 되 면 **azure로 이동 하 여 azure 계정에 로그인 합니다. 함수** 탐색기에서 **Azure에 로그인**을 선택 하 고 프롬프트를 따릅니다.

![Visual Studio Code를 통해 Azure에 로그인](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

로그인 한 후에 Azure 구독의 전자 메일 계정이 상태 표시줄에 표시 되는지 확인 합니다.

![Azure 계정을 보여 주는 Visual Studio Code 상태 표시줄](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

구독에 할당 된 이름은 **Azure에도 표시 됩니다. 함수** 탐색기 (아래 이미지의 "기본"):

![구독을 보여 주는 Azure App Service 탐색기 Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> **"이름이 [SUBSCRIPTION ID] 인 구독을 찾을 수 없습니다**." 오류가 발생 하는 경우 프록시를 사용 하 고 Azure API에 연결할 수 없기 때문일 수 있습니다. 터미널 `HTTP_PROXY` 에서 `HTTPS_PROXY` 프록시 정보를 사용 하 여 및 환경 변수를 구성 합니다.
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>필수 조건 확인

모든 Azure Functions 도구가 설치 되어 있는지 확인 하려면 Visual Studio Code 명령 팔레트 (F1)를 열고 **터미널을 선택 합니다. 새 통합 터미널** 명령을 만들고 터미널이 열리면 다음 명령을 `func`실행 합니다.

![Azure Functions core 도구 필수 구성 요소 확인](media/tutorial-vs-code-serverless-python/check-prereqs.png)

Azure Functions 로고로 시작 하는 출력 (출력을 위쪽으로 스크롤 해야 함)은 Azure Functions Core Tools 있음을 나타냅니다.

`func` 명령이 인식 되지 않으면 Azure Functions Core Tools를 설치한 폴더가 PATH 환경 변수에 포함 되어 있는지 확인 합니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>함수 만들기

1. Azure Functions에 대 한 코드는 코드를 만들기 전에 먼저 만드는 함수 _프로젝트_내에서 관리 됩니다. Azure **에서: 함수** 탐색기 (왼쪽의 Azure 아이콘을 사용 하 여 열림), **새 프로젝트** 명령 아이콘을 선택 하거나 명령 팔레트 (F1)를 열고 Azure Functions를 선택 **합니다. 새 프로젝트 만들기**를 선택합니다.

    ![함수 탐색기에서 새 프로젝트 만들기 단추](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. 프롬프트에서 다음을 수행 합니다.

    | 프롬프트 | 값 | Description | 
    | --- | --- | --- |
    | 프로젝트에 대 한 폴더 지정 | 현재 열려 있는 폴더 | 프로젝트를 만들 폴더입니다. 하위 폴더에 프로젝트를 만들 수 있습니다. |
    | 함수 앱 프로젝트에 대한 언어를 선택합니다. | **Python** | 코드에 사용 되는 템플릿을 결정 하는 함수에 사용할 언어입니다. |
    | 프로젝트의 첫 번째 함수에 대한 템플릿 선택 | **HTTP 트리거** | Http 트리거를 사용 하는 함수는 함수 끝점에 대 한 HTTP 요청이 있을 때마다 실행 됩니다. Azure Functions에 대 한 다양 한 트리거가 있습니다. 자세히 알아보려면 [함수를 사용 하 여 무엇을 할 수 있나요?](functions-overview.md#what-can-i-do-with-functions)를 참조 하세요. |
    | 함수 이름 제공 | HttpExample | 이름은 구성 데이터와 함께 함수 코드를 포함 하는 하위 폴더에 사용 되며 HTTP 끝점의 이름도 정의 합니다. 기본 "Httpexample"를 허용 하는 대신 "HttpExample"을 사용 하 여 트리거와 함수 자체를 구분 합니다. |
    | 권한 수준 | **익명** | 익명 권한 부여는 함수를 누구나 공개적으로 액세스할 수 있도록 합니다. |
    | 프로젝트를 여는 방법을 선택합니다. | **현재 창에서 열기** | 현재 Visual Studio Code 창에서 프로젝트를 엽니다. |

1. 잠시 후 새 프로젝트가 생성 되었음을 나타내는 메시지입니다. **탐색기**에는 함수에 대해 만들어진 하위 폴더가 있으며 Visual Studio Code는 기본 함수 코드를 포함 하는 py 파일을 엽니다.  *\_ \_\_\_*

    [![새 Python 함수 프로젝트를 만든 결과](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Visual Studio Code 경우  *\_ \_py를\_열\_* 때 python 인터프리터를 선택 하지 않았다는 메시지가 표시 되 면 명령 팔레트 (F1)를 열고 python을선택합니다. **인터프리터** 명령을 선택한 다음 프로젝트의 일부로 생성 된 로컬 `.env` 폴더에서 가상 환경을 선택 합니다. 이 환경은 이전에 [사전 요구 사항](#prerequisites)에 명시 된 대로 Python 3.6 x를 기반으로 해야 합니다.
    >
    > ![프로젝트를 사용 하 여 만든 가상 환경 선택](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> 동일한 프로젝트에서 다른 함수를 만들려는 경우 **Azure에서 **create function** 명령을 사용 합니다. 함수** 탐색기를 클릭 하거나 명령 팔레트 (F1)를 열고 Azure Functions를 **선택 합니다. 함수 만들기** 명령을 검색하여 선택합니다. 두 명령 모두 끝점의 이름인 함수 이름을 묻는 메시지를 표시 한 다음 기본 파일을 사용 하 여 하위 폴더를 만듭니다.
>
> ![Azure의 새 함수 명령: 함수 탐색기](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>코드 파일 검사

새로 만든 함수 하위 폴더에는 세 개의 파일이  *\_ \_\_\_* 있습니다. py는 함수의 코드를 포함 합니다 *. json* 은 함수에 대해 설명 하 고, *샘플을 Azure Functions 합니다.* 는 샘플 데이터 파일입니다. 다른 파일을 하위 폴더에 추가할 수 있는 경우에만 존재 하므로 *샘플 .dat* 를 삭제할 수 있습니다.

먼저  *\_py의 코드를\_확인 한 다음 함수를 살펴보겠습니다.\_\_*

### <a name="functionjson"></a>function.json

함수 json 파일은 Azure Functions 끝점에 필요한 구성 정보를 제공 합니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

속성 `scriptFile` 은 코드의 시작 파일을 식별 하며,이 코드에는 라는 `main`Python 함수가 포함 되어 있어야 합니다. 여기에 지정 된 파일에 `main` 함수가 포함 되어 있으면 코드를 여러 파일로 나눌 수 있습니다.

요소 `bindings` 는 두 개체, 즉 들어오는 요청을 설명 하는 개체와 HTTP 응답을 설명 하는 개체를 포함 합니다. 들어오는 요청의 경우 (`"direction": "in"`) 함수는 HTTP GET 또는 POST 요청에 응답 하며 인증이 필요 하지 않습니다. 응답 (`"direction": "out"`)은 `main` Python 함수에서 반환 되는 값을 반환 하는 HTTP 응답입니다.

### <a name="__initpy__"></a>\_\_init.py\_\_

새 함수를 만들 때 Azure Functions는  *\_py\_ \_에서\_* 기본 Python 코드를 제공 합니다.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

코드의 중요 한 부분은 다음과 같습니다.

- `func` 에서`azure.functions`가져와야 합니다. 로깅 모듈 가져오기는 선택 사항 이지만 권장 됩니다.
- 필요한 `main` Python 함수는 라는 `func.request` `req`개체를 수신 하 고 형식의 `func.HttpResponse`값을 반환 합니다. Func에서 이러한 개체의 기능에 대해 자세히 알아볼 수 있습니다 [. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) 및 [func Httpresponse.cache](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) 참조
- 그러면의 `main` 본문에서 요청을 처리 하 고 응답을 생성 합니다. 이 경우 코드는 URL에서 `name` 매개 변수를 찾습니다. 실패 한 경우 요청 본문 `func.HttpRequest.get_json`에 json이 포함 되어 있는지, json에에서 `get_json`반환 된 json 개체의 `name` 메서드를 `get` 사용 하는 값이 포함 되어 있는지 확인 합니다.
- 이름이 발견 되 면 코드에서 이름이 추가 된 "Hello" 문자열을 반환 합니다. 그렇지 않은 경우 오류 메시지를 반환 합니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>로컬에서 디버그

1. 함수 프로젝트를 만들 때 Visual Studio Code 확장은 **Python 함수에 연결**이라는 단일 구성을 포함 `.vscode/launch.json` 하는 시작 구성도 만듭니다. 이 구성은 F5 키를 누르거나 디버그 탐색기를 사용 하 여 프로젝트를 시작할 수 있다는 것을 의미 합니다.

    ![함수 시작 구성을 보여 주는 디버그 탐색기](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. 디버거를 시작 하면 사용 가능한 끝점의 요약을 포함 하 여 Azure Functions의 출력이 표시 된 터미널이 열립니다. "HttpExample" 이외의 이름을 사용한 경우에는 URL이 다를 수 있습니다.

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. **Ctrl + 클릭** 또는 Cmd를 사용 하 여 Visual Studio Code **출력** 창에서 URL을 **클릭** 하 여 해당 주소로 브라우저를 열거나 브라우저를 시작 하 여 동일한 url에 붙여 넣습니다. 두 경우 모두 끝점은 이며, `api/<function_name>`이 경우 `api/HttpExample`에는입니다. 그러나 해당 URL에 이름 매개 변수가 포함 되어 있지 않기 때문에 브라우저 창에는 코드에서 해당 경로에 대 한 적절 한 "쿼리 문자열 또는 요청 본문에 이름을 전달 하십시오."가 표시 되어야 합니다.

1. 이제와 `http://localhost:7071/api/HttpExample?name=VS%20Code`같이 사용에 이름 매개 변수를 추가 해 봅니다. 그러면 브라우저 창에 "Hello Visual Studio Code!" 메시지가 표시 되어 해당 코드 경로를 실행 하는 것을 보여 줍니다.

1. JSON 요청 본문에 이름 값을 전달 하기 위해 다음과 같은 도구를 JSON 인라인과 함께 사용할 수 있습니다.

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    또는를 포함 `{"name":"Visual Studio Code"}` 하 고 명령을 `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`사용 하는 *데이터* 와 같은 파일을 만듭니다.

1. 함수 디버깅을 테스트 하려면 URL을 다시 읽고 `name = req.params.get('name')` 요청 하는 줄에 중단점을 설정 합니다. Visual Studio Code 디버거는 해당 줄에서 중지 되어 변수를 검사 하 고 코드를 단계별로 실행할 수 있습니다. 기본 디버깅에 대 한 간단한 연습은 [Visual Studio Code 자습서-디버거 구성 및 실행](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger)을 참조 하세요.)

1. 함수를 로컬로 철저 하 게 테스트 한 > 경우 디버깅**중지** 메뉴 명령 또는 디버깅 도구 모음의 **연결 끊기** 명령을 사용 하 여 디버거를 중지 합니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Azure Functions에 배포

이러한 단계에서는 함수 확장을 사용 하 여 다른 필수 Azure 리소스와 함께 Azure에서 함수 앱을 만듭니다. 함수 앱을 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리, 배포 및 리소스 공유할 수 있습니다. 또한 데이터 및 [호스팅 계획](functions-scale.md#hosting-plan-support)에 대 한 Azure Storage 계정이 필요 합니다. 이러한 리소스는 모두 단일 리소스 그룹 내에서 구성 됩니다.

1. **Azure: 함수** 탐색기에서 **함수 앱에 배포** 명령을 선택 하거나 명령 팔레트 (F1)를 열고 Azure Functions를 선택 합니다 **. 함수 앱** 명령에 배포 합니다. 또한 함수 앱은 Python 프로젝트가 Azure에서 실행 되는 위치입니다.

    ![함수 앱에 배포 명령](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. 메시지가 표시 되 면 **azure에서 새 함수 앱 만들기**를 선택 하 고 azure에서 고유한 이름을 제공 합니다 (일반적으로 개인 또는 회사 이름을 다른 고유 식별자와 함께 사용 하 여 문자, 숫자 및 하이픈을 사용할 수 있음). 이전에 함수 앱를 만든 경우이 옵션 목록에 해당 이름이 표시 됩니다.

1. 확장은 다음과 같은 작업을 수행 합니다 .이 작업은 Visual Studio Code 팝업 메시지와 **출력** 창에서 확인할 수 있습니다 (프로세스에 몇 분이 소요 됩니다).

    - 지정한 이름 (하이픈 제거)을 사용 하 여 리소스 그룹을 만듭니다.
    - 해당 리소스 그룹에서 저장소 계정, 호스팅 계획 및 함수 앱을 만듭니다. 기본적으로 [소비 계획이](functions-scale.md#consumption-plan) 만들어집니다. 전용 계획에서 함수를 실행 하려면 [고급 만들기 옵션](functions-develop-vs-code.md)을 사용 하 여 게시를 사용 하도록 설정 해야 합니다.
    - 함수 앱에 코드를 배포 합니다.

    **Azure: 함수** 탐색기에도 진행률이 표시 됩니다.

    ![Azure의 배포 진행률 표시기: 함수 탐색기](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. 배포가 완료 되 면 다음 세 가지 추가 작업에 대 한 단추가 포함 된 메시지를 Azure Functions 확장에 표시 합니다.

    ![추가 작업을 사용 하 여 성공적인 배포를 나타내는 메시지](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    **스트림 로그** 및 **업로드 설정은**다음 섹션을 참조 하세요. **보기 출력**의 경우 이어지는 5 단계를 참조 하세요.

1. 배포 후에 **출력** 창에는 Azure의 공용 끝점도 표시 됩니다.

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    이 끝점을 사용 하 여 요청 본문에서 JSON 데이터를 포함 하는 URL 매개 변수 및/또는 요청을 사용 하 여 로컬에서 수행한 동일한 테스트를 실행 합니다. 공용 끝점의 결과는 앞에서 테스트 한 로컬 끝점의 결과와 일치 해야 합니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>로그 스트리밍

Azure Functions 확장에 대 한 [문제 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) 에 설명 된 대로 현재 로그 스트리밍을 지원 합니다. 배포 메시지 팝업의 **스트림 로그** 단추는 궁극적으로 Azure의 로그 출력을 Visual Studio Code에 연결 합니다. 또한 함수 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **스트리밍 로그 시작** 또는 **스트리밍 로그 중지**를 선택 하 여 **Azure Functions** 탐색기에서 로그 스트림을 시작 하 고 중지할 수 있습니다.

그러나 현재 이러한 명령은 아직 작동 하지 않습니다. 대신 다음 명령을 실행 하 여 Azure에서 함수 앱의 이름으로 대체 `<app_name>` 하 여 로그 스트리밍을 브라우저에서 사용할 수 있습니다.

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Azure에 로컬 설정 동기화

배포 메시지 팝업의 **설정 업로드** 단추는 *로컬 설정 json* 파일에 대 한 변경 내용을 Azure에 적용 합니다. 함수 프로젝트 노드를 확장 하 고 **응용 프로그램 설정**을 마우스 오른쪽 단추로 클릭 한 다음 **로컬 설정 업로드**...를 선택 하 여 **Azure Functions** 탐색기에서 명령을 호출할 수도 있습니다. 명령 팔레트를 사용 하 여 **Azure Functions를 선택할 수도 있습니다. 로컬 설정** 업로드 명령입니다.

설정을 업로드 하면 기존 설정이 업데이트 되 고 *로컬. 설정 json*에 정의 된 새 설정이 추가 됩니다. 업로드 해도 로컬 파일에 나열 되지 않은 Azure의 설정은 제거 되지 않습니다. 이러한 설정을 제거 하려면 **Azure Functions** 탐색기에서 **응용 프로그램 설정** 노드를 확장 하 고 설정을 마우스 오른쪽 단추로 클릭 한 다음 **설정 삭제**...를 선택 합니다. Azure Portal에서 직접 설정을 편집할 수도 있습니다.

포털을 통해 또는 **Azure 탐색기** 를 통해 설정 된 모든 변경 내용을 *로컬 설정 json* 파일에 적용 하려면 **응용 프로그램 설정** 노드를 마우스 오른쪽 단추로 클릭 하 고 **원격 설정 다운로드** ... 명령을 선택 합니다. 명령 팔레트를 사용 하 여 **Azure Functions를 선택할 수도 있습니다. 원격 설정** 다운로드 명령입니다.

## <a name="add-a-second-function"></a>두 번째 함수 추가

첫 번째 배포 후에는 함수를 추가 하는 등의 코드를 변경 하 고 동일한 함수 앱에 다시 배포할 수 있습니다.

1. **Azure: 함수 탐색기에서 **함수 만들기** 명령을 선택 하거나 Azure Functions를 **사용 합니다.** 명령 팔레트** 에서 함수를 만듭니다. 함수에 대해 다음 세부 정보를 지정 합니다.

    - 템플릿: HTTP 트리거
    - 이름: "DigitsOfPi"
    - 권한 수준: Anonymous

1. Visual Studio Code 파일 탐색기는 함수 이름을 포함 하는 하위 폴더입니다 .이 하위 폴더에  *\_는\_py \_\_* , *함수인 json*및 *sample*이라는 파일이 다시 포함 됩니다.

1. *\_Py의 내용을다음\_코드와 일치 하도록 바꿉니다.그러면PI값을url에지정된자릿수로포함하는문자열을생성합니다.이코드는url매개변수만사용합니다.\_\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. 코드는 HTTP GET만 지원 하기 때문에 (즉, 제거 `"post"`) 컬렉션 `"methods"` 에만 `"get"` 포함 되도록 *함수 json* 을 수정 합니다. 전체 파일은 다음과 같이 표시 됩니다.

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. F5 키를 누르거나 **디버그** > **디버깅 시작** 메뉴 명령을 선택 하 여 디버거를 시작 합니다. 이제 **출력** 창에 프로젝트의 두 끝점이 모두 표시 됩니다.

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. 브라우저 또는 말아 넘기기에서에 `http://localhost:7071/api/DigitsOfPi?digits=125` 요청 하 고 출력을 관찰 합니다. 코드 알고리즘이 완전히 정확 하지는 않지만 향상 된 기능을 제공 하는 것을 알 수 있습니다. 완료 되 면 디버거를 중지 합니다.

1. **Azure에서 **함수 앱 배포를** 사용 하 여 코드를 다시 배포 합니다. 함수** 탐색기. 메시지가 표시 되 면 이전에 만든 함수 앱를 선택 합니다.

1. 배포가 완료 되 면 (몇 분이 소요 됩니다.) **출력** 창에는 테스트를 반복할 수 있는 공용 끝점이 표시 됩니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Azure storage에 메시지를 쓰는 바인딩을 추가 합니다.

_바인딩을_ 사용 하면 데이터 액세스 코드를 작성 하지 않고도 Azure storage와 같은 리소스에 함수 코드를 연결할 수 있습니다. 바인딩은 *함수 json* 파일에 정의 되며 입력과 출력을 모두 나타낼 수 있습니다. 함수는 여러 개의 입력 및 출력 바인딩을 사용할 수 있지만 트리거는 하나만 사용할 수 있습니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

이 섹션에서는이 자습서의 앞부분에서 만든 HttpExample 함수에 저장소 바인딩을 추가 합니다. 함수는이 바인딩을 사용 하 여 각 요청과 함께 저장소에 메시지를 씁니다. 문제의 저장소는 함수 앱에서 사용 하는 것과 동일한 기본 저장소 계정을 사용 합니다. 그러나 저장소를 과도 하 게 사용 하려는 경우 별도의 계정을 만드는 것이 좋습니다.

1. 명령 팔레트를 열고 Azure Functions를 선택 **하 여 Azure Functions 프로젝트에 대 한 원격 설정을 *로컬 설정 json* 파일에 동기화 합니다. 원격 설정을**다운로드 합니다. *Local. json* 을 열고에 대 한 `AzureWebJobsStorage`값이 포함 되어 있는지 확인 합니다. 이 값은 저장소 계정에 대 한 연결 문자열입니다.

1. 폴더에서 *함수. json*을 마우스 오른쪽 단추로 클릭 하 고 **바인딩 추가**를 선택 합니다. `HttpExample`

    ![Visual Studio Code 탐색기에서 바인딩 추가 명령](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Visual Studio Code에서 이어지는 프롬프트에서 다음 값을 선택 하거나 제공 합니다.

    | 프롬프트 | 제공할 값 |
    | --- | --- |
    | 바인딩 방향 설정 | out |
    | 방향이 out 인 바인딩 선택 | Azure Queue Storage |
    | 코드에서 이 바인딩을 식별하는 데 사용되는 이름입니다. | msg |
    | 메시지가 전송 될 큐입니다. | outqueue |
    | 로컬에서 설정 (저장소 연결 요청)을 선택 *합니다.* | AzureWebJobsStorage |

1. 이러한 항목을 선택 하 고 나 서 다음 바인딩이 함수 json 파일에 추가 되었는지 확인 *합니다.*

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. 이제 바인딩을 구성 했으므로 함수 코드에서 사용할 수 있습니다. 다시, 새로 정의 된 바인딩은 `main` 코드에서  *\_py \_\_\_* 의 함수에 대 한 인수로 표시 됩니다. 예를 들어, 다음에 사용 되는 이름을 사용 하 여 타임 스탬프 메시지를 작성 하는 `msg` 데 인수를 사용 하는 것을 보여 주는 httpexample의 py 파일을 수정할  *\_ \_\_\_* 수 있습니다. 요구. 설명은 특정 변경 내용을 설명 합니다.

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. 이러한 변경 내용을 로컬로 테스트 하려면 F5 키를 누르거나 **디버그** > **디버깅 시작** 메뉴 명령을 선택 하 여 Visual Studio Code에서 디버거를 다시 시작 합니다. 이전 처럼 **출력** 창에는 프로젝트의 끝점이 표시 됩니다.

1. 브라우저에서 URL `http://localhost:7071/api/HttpExample?name=VS%20Code` 을 방문 하 여 httpexample 끝점에 대 한 요청을 만듭니다. 또한 큐에 메시지를 써야 합니다.

1. 메시지를 바인딩에서 이름이 지정 된 "outqueue" 큐에 쓸지 확인 하려면 다음 세 가지 방법 중 하나를 사용할 수 있습니다.

    1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 함수 프로젝트를 포함 하는 리소스 그룹으로 이동 합니다. 해당 리소스 그룹 내에서 로컬 및 프로젝트의 저장소 계정으로 이동한 다음 **큐**로 이동 합니다. 해당 페이지에서 로그 된 모든 메시지를 표시 하는 "outqueue"로 이동 합니다.

    1. [Visual Studio Code를 사용 하 여 Azure Storage에 연결 함수](functions-add-output-binding-storage-queue-vs-code.md)에 설명 된 대로 Visual Studio와 통합 된 Azure Storage 탐색기으로 큐를 탐색 하 고 검사 합니다. 특히 [출력 큐 검사](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) 섹션을 참조 하세요.

    1. [저장소 큐 쿼리](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)에 설명 된 대로 Azure CLI를 사용 하 여 저장소 큐를 쿼리 합니다.
    
1. 클라우드에서 테스트 하려면 **Azure에서 **함수 앱 배포를** 사용 하 여 코드를 다시 배포 합니다. 함수** 탐색기. 메시지가 표시 되 면 이전에 만든 함수 앱를 선택 합니다. 배포가 완료 되 면 (몇 분이 소요 됩니다.) **출력** 창에는 테스트를 반복할 수 있는 공용 끝점이 다시 표시 됩니다.

> [!div class="nextstepaction"]
> [문제가 발생 했습니다.](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>리소스 정리

만든 함수 앱에는 최소한의 비용을 발생 시킬 수 있는 리소스가 포함 되어 있습니다 ( [함수 가격 책정](https://azure.microsoft.com/pricing/details/functions/)참조). 리소스를 정리 하려면 **Azure에서 함수 앱를 마우스 오른쪽 단추로 클릭 합니다. 함수** 탐색기를 열고 **함수 앱 삭제**를 선택 합니다. [Azure Portal](https://portal.azure.com)방문 하 여 왼쪽 탐색 창에서 **리소스 그룹** 을 선택 하 고이 자습서의 프로세스에서 만든 리소스 그룹을 선택한 다음 **리소스 그룹 삭제** 명령을 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Functions에 Python 코드를 배포 하는 연습을 완료 했습니다. 이제 더 많은 서버 리스 함수를 만들 준비가 되었습니다.

앞에서 설명한 것 처럼 GitHub 리포지토리 [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions)를 방문 하 여 함수 확장에 대해 자세히 알아볼 수 있습니다. 문제 및 기여도 환영 합니다.

사용할 수 있는 다른 트리거를 살펴보려면 [Azure Functions 개요](functions-overview.md) 를 참조 하세요.

AI 및 Machine Learning services와 함께 데이터 저장소를 비롯 하 여 Python에서 사용할 수 있는 Azure 서비스에 대 한 자세한 내용은 [Azure Python 개발자 센터](/azure/python/?view=azure-python)를 참조 하세요.

도움이 될 수 있는 Visual Studio Code에 대 한 다른 Azure 확장도 있습니다. 확장 탐색기에서 "Azure"를 검색 하기만 하면 됩니다.

![Visual Studio Code 용 Azure 확장](media/tutorial-vs-code-serverless-python/azure-extensions.png)

몇 가지 인기 있는 확장은 다음과 같습니다.

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI 도구](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
