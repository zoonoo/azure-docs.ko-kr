---
title: '빠른 시작: .NET용 QnA Maker 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 2911c74226c3b682b75e8d10b0b4b7617a48ec64
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946269"
---
.NET 용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 기술 자료 만들기
* 기술 자료 관리
* 기술 자료 게시
* 기술 자료에서 답변 생성

[참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 QnA Maker용 리소스를 만듭니다.

리소스에 대한 키와 엔드포인트를 가져온 후 `QNAMAKER_SUBSCRIPTION_KEY`라는 키에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). 리소스 이름은 엔드포인트 URL의 일부로 사용됩니다.

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `qna-maker-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```dotnetcli
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>SDK 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 QnA Maker 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.


## <a name="object-model"></a>개체 모델

QnA Maker 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 개체입니다.

클라이언트를 만든 후 [기술 자료](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) 속성을 사용하여 기술 자료를 생성, 관리 및 게시합니다.

JSON 개체를 전송하여 기술 자료를 관리합니다. 즉각적인 작업의 경우 메서드는 일반적으로 상태를 나타내는 JSON 개체를 반환합니다. 장기 실행 작업의 경우 응답은 작업 ID입니다. [요청의 상태](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)를 확인하려면 해당 작업 ID와 함께 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) 메서드를 호출합니다.


## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [기술 자료 만들기](#create-a-knowledge-base)
* [기술 자료 업데이트](#update-a-knowledge-base)
* [기술 자료 다운로드](#download-a-knowledge-base)
* [기술 자료 게시](#publish-a-knowledge-base)
* [기술 자료 삭제](#delete-a-knowledge-base)
* [작업의 상태 가져오기](#get-status-of-an-operation)
* [기술 자료에서 답변 생성](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>종속성 추가

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 **Program.cs** 파일을 엽니다. 기존 `using` 코드를 다음 `using` 지시문으로 바꿉니다.

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>기술 자료 제작을 위한 클라이언트 인증

**main** 메서드에서 `QNAMAKER_SUBSCRIPTION_KEY`라는 환경 변수로부터 가져온 사용자 리소스의 Azure 키에 대한 변수를 만듭니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다. 메서드는 나중에 생성됩니다.

다음으로, 키를 사용하여 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) 개체를 만들고, 엔드포인트에 이를 사용하여 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 개체를 만듭니다.

|환경 변수|변수|예제|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|키는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이는 예측 엔드포인트 키와 동일하지 않습니다.|
|`QNAMAKER_HOST`|`Endpoint`| `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식의 작성 엔드포인트에는 **리소스 이름**이 포함됩니다. 이는 예측 엔드포인트를 쿼리하는 데 사용되는 URL과 동일하지 않습니다.|
||||

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>응답을 생성하기 위한 런타임 인증

**main** 메서드에서 `QNAMAKER_ENDPOINT_HOSTNAME` 및 `QNAMAKER_ENDPOINT_KEY`라는 환경 변수로부터 가져온 리소스의 인증에 대한 변수를 만듭니다. 기술 자료를 게시할 때 이러한 값이 반환됩니다. 게시한 후 QnA Maker 포털의 **설정** 페이지에서 이러한 설정을 찾을 수 있습니다.

기술 자료를 쿼리하는 [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet)를 만들어 답변을 생성하거나 활성 학습으로부터 학습합니다.

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠**의 경우 [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) 개체를 사용합니다.
* **파일**의 경우 [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) 개체를 사용합니다.
* **URL**의 경우 문자열 목록을 사용합니다.

[CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) 메서드를 호출한 다음, 반환된 작업 ID를 [MonitorOperation](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

다음 코드의 마지막 줄은 MonitorOperation의 응답에서 기술 자료 ID를 반환합니다.

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) 및 [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO 개체를 포함하는 [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet)를 [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. 성공적으로 업데이트되었는지 확인하려면 [MonitorOperation](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="download-a-knowledge-base"></a>기술 자료 다운로드

[DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) 메서드를 사용하여 [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) 목록으로 데이터베이스를 다운로드할 수 있습니다. 이 메서드의 결과가 TSV 파일이 아니므로 QnA Maker 포털에 있는 **설정** 페이지의 내보내기와 동일하지 않습니다. 

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) 메서드를 사용하여 기술 자료를 게시할 수 있습니다. 기술 자료 ID에서 참조하는 현재 저장 및 학습된 모델을 사용하며, 이를 엔드포인트에 게시합니다.

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>기술 자료에서 답변 생성

[RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) 메서드를 사용하여 게시된 기술 자료에서 답변을 생성합니다. 이 메서드는 기술 자료 ID 및 [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet)을 허용합니다. 채팅 봇에서 사용할 [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) 및 [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet)와 같은 QueryDTO의 추가 속성에 액세스합니다.

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) 메서드를 사용하여 기술 자료를 삭제할 수 있습니다.

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 [작업 ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId)를 사용하여 폴링합니다(다시 시도 논리 사용).

다음 코드 블록의 _loop_ 및 _Task.Delay_는 다시 시도 논리를 시뮬레이션하는 데 사용됩니다. 아래는 사용자의 자체 다시 시도 논리로 바꾸어야 합니다.

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

이 문서의 모든 코드 조각은 [사용 가능](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)이며 단일 파일로 실행할 수 있습니다.

```dotnetcli
dotnet run
```

[이 빠른 시작의 소스 코드](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs)는 QnA Maker C# 샘플 GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)