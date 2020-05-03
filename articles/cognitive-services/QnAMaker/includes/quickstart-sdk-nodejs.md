---
title: '빠른 시작: Node.js용 QnA Maker 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Node.js용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 48038c8e7e8250190d79aba7901567e18881e912
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204031"
---
Node.js용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 기술 자료 만들기
* 기술 자료 업데이트
* 기술 자료 게시
* 게시된 엔드포인트 키 가져오기
* 장기 실행 작업 대기
* 기술 자료 삭제

[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org)
* Azure 구독을 보유한 후에는 Azure Portal에서 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)를 만들어 제작 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 선택합니다.
    * 애플리케이션을 QnA Maker API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 QnA Maker용 리소스를 만듭니다.

리소스에서 키 및 엔드포인트를 가져온 후 빠른 시작 페이지에서 새 리소스에 대한 Azure Portal의 값을 가져옵니다.

`QNAMAKER_AUTHORING_KEY` 및 `QNAMAKER_ENDPOINT`라는 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) 파일을 `.env`에 복사하고, 해당 파일의 환경 변수를 사용할 수 있습니다.

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init -y` 명령을 실행합니다.

```console
npm init -y
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

필수 및 선택적 NPM 패키지를 설치합니다.

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

앱의 `package.json` 파일이 종속성으로 업데이트됩니다. `dotenv`는 선택 사항이며 텍스트 파일에 환경 변수를 설정할 수 있도록 하는 데 사용됩니다. `.env`를 소스 제어로 체크 인하지 마십시오.


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
* [게시된 엔드포인트 가져오기](#get-published-endpoint)
* [작업의 상태 가져오기](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>종속성 추가

`index.js`라는 파일을 만듭니다. QnA Maker 라이브러리와 종속성을 파일에 추가합니다.

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

|환경 변수|Node.js 변수|예제|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|키는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이는 예측 엔드포인트 키와 동일하지 않습니다.|
|`QNAMAKER_ENDPOINT`|`endpoint`| `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식의 작성 엔드포인트에는 **리소스 이름**이 포함됩니다. 이는 예측 엔드포인트를 쿼리하는 데 사용되는 URL과 동일하지 않습니다.|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음으로, 키를 사용하여 ApiKeyCredentials 개체를 만들고 엔드포인트와 함께 사용하여 [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) 개체를 만듭니다. 클라이언트 개체를 사용하여 [기술 자료 클라이언트](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) 개체를 가져옵니다.


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠**의 경우 [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 개체를 사용합니다.
* **파일**의 경우 [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 개체를 사용합니다.
* **URL**의 경우 문자열 목록을 사용합니다.

기술 자료 정보를 통해 [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 메서드를 호출합니다. 기술 자료 정보는 기본적으로 JSON 개체입니다.

create 메서드가 반환되면 반환된 작업 ID를 [wait_for_operation](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다. wait_for_operation 메서드는 작업이 완료되면 반환됩니다. 반환된 작업의 `resourceLocation` 헤더 값을 구문 분석하여 새 기술 자료 ID를 가져옵니다.

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`wait_for_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 및 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 개체를 포함하는 [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest)를 [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. DTO는 기본적으로 JSON 개체이기도 합니다. 성공적으로 업데이트되었는지 확인하려면 [wait_for_operation](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`wait_for_operation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 메서드를 사용하여 기술 자료를 게시합니다. 기술 자료 ID에서 참조하는 현재 저장 및 학습된 모델을 사용하며, 이를 엔드포인트에 게시합니다. HTTP 응답 코드를 확인하여 게시가 성공했는지 확인합니다.

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>게시된 엔드포인트 가져오기

기술 자료가 게시되면 쿼리 예측 런타임의 generateAnswer API를 통해 게시된 기술 자료에 액세스합니다. 이렇게 하려면 런타임의 엔드포인트 키가 필요합니다. 이는 작성 키와는 다릅니다.

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

호출에서 두 개의 엔드포인트 키가 반환됩니다. 런타임 엔드포인트에 액세스하려면 하나만 필요합니다.

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 메서드를 사용하여 기술 자료를 삭제합니다.

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 [작업 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid)를 사용하여 폴링합니다(다시 시도 논리 사용).

다음 코드 블록의 _delayTimer_ 호출은 다시 시도 논리를 시뮬레이션하는 데 사용됩니다. 이를 다시 시도 논리로 바꿉니다.

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `node index.js` 명령을 사용하여 애플리케이션을 실행합니다.

이 문서의 모든 코드 조각은 [사용 가능](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)이며 단일 파일로 실행할 수 있습니다.

```console
node index.js
```

프로그램이 콘솔에 상태를 출력합니다.

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)