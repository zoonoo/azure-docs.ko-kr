---
title: 사용자 정의 기술 예제 (파이썬)
titleSuffix: Azure Cognitive Search
description: 파이썬 개발자의 경우 Azure Functions 및 Visual Studio를 사용하여 사용자 지정 기술을 빌드하기 위한 도구와 기술을 알아봅니다. 사용자 지정 기술에는 Azure Cognitive Search에서 AI가 풍부한 인덱싱 파이프라인에 추가할 수 있는 사용자 정의 모델 또는 논리가 포함되어 있습니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210468"
---
# <a name="example-create-a-custom-skill-using-python"></a>예: 파이썬을 사용하여 사용자 정의 기술 만들기

이 Azure 인지 검색 기술 집합 예제에서는 파이썬 및 Visual Studio 코드를 사용하여 웹 API 사용자 지정 기술을 만드는 방법을 배웁니다. 이 예제에서는 [사용자 지정 기술 인터페이스를](cognitive-search-custom-skill-interface.md)구현하는 Azure [Function을](https://azure.microsoft.com/services/functions/) 사용합니다.

사용자 정의 기술은 Python의 사용자 정의 기술 개발에 사용되는 도구와 기술에 집중할 수 있도록 설계가 간단합니다 (두 개의 문자열을 연결). 간단한 기술로 성공하면 더 복잡한 시나리오로 분기할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 사용자 [지정 기술이](cognitive-search-custom-skill-interface.md) 구현해야 하는 입력/출력 인터페이스에 대한 소개를 위해 사용자 지정 기술 인터페이스를 검토합니다.

+ 환경을 설정합니다. 이 [자습서를 수행하여](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) Visual Studio 코드 및 Python 확장을 사용하여 서버리스 Azure 함수를 설정했습니다. 이 자습서에서는 다음과 같은 도구 및 구성 요소를 설치합니다. 

  + [파이썬 3.75](https://www.python.org/downloads/release/python-375/)
  + [비주얼 스튜디오 코드](https://code.visualstudio.com/)
  + [비주얼 스튜디오 코드에 대한 파이썬 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure 기능 핵심 도구](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Visual Studio Code용 Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 예제에서는 Azure Function을 사용하여 웹 API를 호스팅하는 개념을 보여 주지만 다른 접근 방식은 가능합니다. [인식 기술에 대한 인터페이스 요구 사항](cognitive-search-custom-skill-interface.md)을 충족하기만 하면, 사용하는 방식은 중요하지 않습니다. 그러나 Azure Functions를 사용하면 사용자 지정 기술을 쉽게 만들 수 있습니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

Visual Studio Code의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 함수 앱을 통해 함수를 논리적 단위로 그룹화하여 관리, 배포 및 리소스를 공유할 수 있습니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Create new project...`을 검색하여 선택합니다.

1. 프로젝트 작업 영역에 대한 디렉터리 위치를 선택하고 **선택**을 선택합니다.

    > [!NOTE]
    > 다음 단계는 작업 영역 외부에서 완료하도록 설계되었습니다. 따라서 작업 영역의 일부인 프로젝트 폴더를 선택하지 마십시오.

1. 함수 앱 프로젝트의 언어를 선택합니다. 이 자습서에서는 **파이썬을**선택합니다.
1. 파이썬 버전을 선택합니다(버전 3.7.5는 Azure 함수에서 지원됩니다).
1. 프로젝트의 첫 번째 함수에 대한 템플릿을 선택합니다. 새 함수 앱에서 HTTP 트리거 함수를 만들려면 **HTTP 트리거를** 선택합니다.
1. 함수 이름을 제공합니다. 이 **경우, 동헌을** 사용하자 
1. 권한 부여 수준으로 **함수를** 선택합니다. 즉, 함수의 HTTP 끝점을 호출하는 [함수 키를](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) 제공합니다. 
1. 프로젝트를 여는 방법을 선택합니다. 이 단계에서는 **작업 영역에 추가를** 선택하여 현재 작업 영역에서 함수 앱을 만듭니다.

Visual Studio Code는 새 작업 영역에서 함수 앱 프로젝트를 만듭니다. 이 프로젝트에는 [host.json](../azure-functions/functions-host-json.md) 및 [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) 구성 파일과 언어별 프로젝트 파일이 포함됩니다. 

함수 앱 프로젝트의 **연결자** 폴더에도 새 HTTP 트리거 함수가 만들어집니다. 그 안에는 이 내용이\_\_있는 "init__.py"라는 파일이 있습니다.

```py
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

이제 [사용자 지정 기술 인터페이스를](cognitive-search-custom-skill-interface.md)따르도록 해당 코드를 수정해 보겠습니다. 다음 내용으로 코드를 수정합니다.

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

**transform_value** 메서드는 단일 레코드에서 작업을 수행합니다. 특정 요구 사항에 맞게 메서드를 수정할 수 있습니다. 필요한 입력 유효성 검사를 수행 하 고 레코드에 대 한 작업을 완료할 수 없는 경우 생성 된 오류 및 경고를 반환 해야 합니다.

### <a name="debug-your-code-locally"></a>로컬로 코드 디버깅

비주얼 스튜디오 코드를 사용하면 코드를 쉽게 디버깅할 수 있습니다. 'F5'를 누르거나 **디버그** 메뉴로 이동하여 **디버깅 시작을**선택합니다.

관심 있는 줄에 'F9'을 누르면 코드의 중단점을 설정할 수 있습니다.

디버깅을 시작하면 함수가 로컬로 실행됩니다. Postman 또는 Fiddler와 같은 도구를 사용하여 로컬 호스트에 요청을 발행할 수 있습니다. 터미널 창에서 로컬 끝점의 위치를 확인합니다. 

## <a name="publish-your-function"></a>함수 게시

함수 동작에 만족하면 게시할 수 있습니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 **함수 앱에 배포를**검색하고 선택합니다. . . 

1. 응용 프로그램을 배포할 Azure 구독을 선택합니다.

1. 선택 **+ Azure에서 새 함수 앱 만들기**

1. 함수 앱에 대해 전역적으로 고유한 이름을 입력합니다.

1. 파이썬 버전을 선택하십시오 (파이썬 3.7.x는이 함수에 대해 작동합니다).

1. 새 리소스의 위치를 선택합니다(예: 미국 서부 2).

이 시점에서 Azure 구독에서 Azure에서 새 Azure Function을 호스트하는 데 필요한 리소스가 만들어집니다. 배포가 완료될 때가지 기다립니다. 출력 창에는 배포 프로세스의 상태가 표시됩니다.

1. Azure [포털에서](https://portal.azure.com)모든 **리소스로** 이동하여 해당 이름으로 게시한 함수를 찾습니다. **연결자라는**이름을 지정한 경우 리소스를 선택합니다.

1. **</> 기능 URL 받기** 버튼을 클릭합니다. 이렇게 하면 함수를 호출하는 URL을 복사할 수 있습니다.

## <a name="test-the-function-in-azure"></a>Azure에서 함수 테스트

이제 기본 호스트 키가 있으므로 다음과 같이 함수를 테스트합니다.

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>요청 본문
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

이 예제에서는 로컬 환경에서 함수를 실행할 때 이전에 보았던 것과 동일한 결과를 생성해야 합니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결

이제 새 사용자 지정 기술이 있으므로 기능에 추가할 수 있습니다. 아래 예제에서는 제목과 문서 작성기를 merged_title_author 호출하는 단일 필드에 통합하는 기술을 호출하는 방법을 보여 주며 있습니다. 새 `[your-function-url-here]` Azure 함수의 URL로 바꿉습니다.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계
축하합니다! 첫 번째 사용자 지정 기술을 만들었습니다. 이제 동일한 패턴을 따라 고유한 사용자 지정 기능을 추가할 수 있습니다. 자세한 내용을 보려면 다음 링크를 클릭하십시오.

+ [파워 스킬: 사용자 지정 기술의 리포지토리](https://github.com/Azure-Samples/azure-search-power-skills)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
