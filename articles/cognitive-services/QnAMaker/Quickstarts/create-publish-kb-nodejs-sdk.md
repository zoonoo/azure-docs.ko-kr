---
title: '빠른 시작: Node.js용 QnA Maker 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: Node.js용 QnA Maker 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/21/2019
ms.author: diberry
ms.openlocfilehash: 6759b8fdf6e68d4f0030fc4eda5eee5d1ce608b1
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203214"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>빠른 시작: Node.js용 QnA Maker 클라이언트 라이브러리

Node.js용 QnA Maker 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다. 

Node.js용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 기술 자료 만들기 
* 기술 자료 관리
* 기술 자료 게시

[참조 설명서](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org)

## <a name="setting-up"></a>설치

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 QnA Maker용 리소스를 만듭니다. 

리소스에서 키를 가져온 후 `QNAMAKER_SUBSCRIPTION_KEY` 및 `QNAMAKER_HOST`라는 리소스에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). Azure Portal의 리소스 **키**와 **개요** 페이지에 있는 키와 호스트 값을 사용합니다.

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init -y` 명령을 실행합니다. 

```console
npm init -y
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ms-rest-azure` 및 `azure-cognitiveservices-qnamaker` NPM 패키지를 설치합니다.

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

앱의 `package.json` 파일이 종속성으로 업데이트됩니다.


## <a name="object-model"></a>개체 모델

QnA Maker 클라이언트는 키가 포함된 ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 개체입니다.

클라이언트를 만든 후 [기술 자료](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) 속성을 사용하여 기술 자료를 만들고 관리하고 게시합니다. 

JSON 개체를 전송하여 기술 자료를 관리합니다. 즉각적인 작업의 경우 메서드는 일반적으로 상태를 나타내는 JSON 개체를 반환합니다. 장기 실행 작업의 경우 응답은 작업 ID입니다. 작업 ID와 함께 [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) 메서드를 호출하여 [요청의 상태](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)를 확인합니다. 

 
## <a name="code-examples"></a>코드 예제

이 코드 조각은 Node.js용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [기술 자료 만들기](#create-a-knowledge-base)
* [기술 자료 업데이트](#update-a-knowledge-base)
* [기술 자료 게시](#publish-a-knowledge-base)
* [기술 자료 삭제](#delete-a-knowledge-base)
* [작업의 상태 가져오기](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>종속성 추가

`index.js`라는 파일을 만듭니다. QnA Maker 라이브러리와 Azure REST 라이브러리를 파일에 추가합니다.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.


|환경 변수|Node.js 변수|예|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32자 GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://westus.api.cognitive.microsoft.com`|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음으로, 키를 사용하여 ServiceClientCredentials 개체를 만들고 엔드포인트와 함께 사용하여 [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) 개체를 만듭니다. 클라이언트 개체를 사용하여 [기술 자료](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) 개체를 가져옵니다.


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠**의 경우 [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 개체를 사용합니다.
* **파일**의 경우 [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 개체를 사용합니다. 
* **URL**의 경우 문자열 목록을 사용합니다.

[create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 메서드를 호출한 다음, 반환된 작업 ID를 [Operations.getDetails](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다. 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`wait_for_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다. 

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 및 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 개체를 포함하는 [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest)를 [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. 성공적으로 업데이트되었는지 확인하려면 [Operation.getDetail](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`wait_for_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다. 

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 메서드를 사용하여 기술 자료를 게시합니다. 기술 자료 ID에서 참조하는 현재 저장 및 학습된 모델을 사용하며, 이를 엔드포인트에 게시합니다. 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 메서드를 사용하여 기술 자료를 삭제합니다. 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 [작업 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid)를 사용하여 폴링합니다(다시 시도 논리 사용). 

다음 코드 블록의 _setTimeout_ 호출은 비동기 코드를 시뮬레이션하는 데 사용됩니다. 재시도 논리로 바꿉니다. 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `node index.js` 명령을 사용하여 애플리케이션을 실행합니다.


이 문서의 모든 코드 조각은 [사용 가능](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)이며 단일 파일로 실행할 수 있습니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[자습서: KB에서 만들기 및 대답](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API란?](../Overview/overview.md)
* [기술 자료 편집](../how-to/edit-knowledge-base.md)
* [사용량 현황 분석 가져오기](../how-to/get-analytics-knowledge-base.md)