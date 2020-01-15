---
title: '빠른 시작: Node.js용 Personalizer 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 학습 루프를 사용하여 Node.js용 Personalizer 클라이언트 라이브러리를 시작하는 방법을 보여줍니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 91aee7f4a110490495a3cf840e6b3ef3282c91c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446365"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>빠른 시작: Node.js용 Personalizer 클라이언트 라이브러리

이 Node.js 시작에서는 Personalizer 서비스를 사용하여 맞춤형 콘텐츠를 표시합니다.

Node.js용 Personalizer 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

 * 개인 설정에 대한 작업 목록의 순위를 지정합니다.
 * 상위 작업의 성공을 나타내는 보상 점수를 보고합니다.

[라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [패키지(NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [샘플](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org) 및 NPM.

## <a name="using-this-quickstart"></a>이 빠른 시작 사용


이 빠른 시작을 사용하는 몇 가지 단계가 있습니다.

* Azure Portal에서 Personalizer 리소스 만들기
* Azure Portal의 Personalizer 리소스에 대한 **구성** 페이지에서 모델 업데이트 빈도 변경
* 코드 편집기에서 코드 파일을 만들고 코드 파일을 편집합니다.
* 명령줄 또는 터미널의 명령줄에서 SDK 설치
* 명령줄 또는 터미널에서 코드 파일 실행


## <a name="create-a-personalizer-azure-resource"></a>Personalizer Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Personalizer용 리소스를 만듭니다. 다음도 가능합니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services)를 가져옵니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.  
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져온 후 다음 두 가지 [환경 변수](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)를 만듭니다.

* 리소스 키에 대한 `PERSONALIZER_KEY`
* 리소스 엔드포인트에 대한 `PERSONALIZER_ENDPOINT`

Azure Portal의 **빠른 시작** 페이지에서 키와 엔드포인트 값을 모두 확인할 수 있습니다.


## <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`npm init -y` 명령을 실행하여 `package.json` 파일을 만듭니다. 

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>Personalizer용 Node.js 라이브러리 설치

다음 명령을 사용하여 Node.js용 Personalizer 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/cognitiveservices-personalizer --save
```

이 빠른 시작에 대해 남아 있는 NPM 패키지를 설치합니다.

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="change-the-model-update-frequency"></a>모델 업데이트 빈도 변경

Azure Portal의 **구성** 페이지에 있는 Personalizer 리소스에서 **모델 업데이트 빈도**를 10초로 변경합니다. 이렇게 하면 서비스가 빠르게 학습되어 각 반복에 대한 상위 작업이 변경되는 상태를 확인할 수 있습니다.

![모델 업데이트 빈도 변경](./media/settings/configure-model-update-frequency-settings.png)

Personalizer 루프가 처음 인스턴스화되면 학습을 위한 Reward API 호출이 없으므로 모델이 없습니다. 순위 호출은 각 항목에 대해 동일한 확률을 반환합니다. 애플리케이션은 RewardActionId의 출력을 사용하여 항상 콘텐츠의 순위를 매겨야 합니다.

## <a name="object-model"></a>개체 모델

Personalizer 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 PersonalizerClient 개체입니다.

콘텐츠의 순위를 요청하려면 RankRequest를 만든 다음, client.Rank 메서드에 전달합니다. Rank 메서드는 순위가 지정된 콘텐츠가 포함된 RankResponse를 반환합니다. 

보상을 Personalizer에 보내려면 RewardRequest를 만든 다음, client.Reward 메서드에 전달합니다. 

이 빠른 시작에서 보상을 결정하는 것은 간단합니다. 프로덕션 시스템에서 [보상 점수](concept-rewards.md)에 영향을 주는 요소와 크기를 결정하는 것은 복잡한 프로세스일 수 있으며, 시간이 지남에 따라 변경될 수 있습니다. 이는 Personalizer 아키텍처에서 기본 설계 결정 중 하나여야 합니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Node.js용 Personalizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [Personalizer 클라이언트 만들기](#create-a-personalizer-client)
* [순위 요청](#request-a-rank)
* [보상 보내기](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

선호하는 편집기 또는 `sample.js`라는 IDE에서 Node.js 애플리케이션을 새로 만듭니다. 

## <a name="add-the-dependencies"></a>종속성 추가

선호하는 편집기 또는 IDE에서 **sample.js** 파일을 엽니다. NPM 패키지를 추가하려면 다음 `requires`를 추가합니다.

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Personalizer 리소스 정보 추가

환경 변수에서 가져온 리소스의 Azure 키 및 엔드포인트에 대한 `PERSONALIZER_KEY` 및 `PERSONALIZER_ENDPOINT` 이름의 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 합니다. 이 메서드는 이 빠른 시작의 뒷부분에서 만들어집니다.

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>Personalizer 클라이언트 만들기

다음으로, Personalizer 클라이언트를 반환하는 메서드를 만듭니다. 메서드의 매개 변수는 `PERSONALIZER_RESOURCE_ENDPOINT`이고, ApiKey는 `PERSONALIZER_RESOURCE_KEY`입니다.

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>작업으로 표현되는 콘텐츠 선택 항목 가져오기

작업은 Personalizer에서 순위가 지정되도록 하려는 콘텐츠 선택 항목을 나타냅니다. 다음 메서드를 Program 클래스에 추가하여 시간 및 현재 음식 기본 설정에 대한 명령줄에서 사용자의 입력을 가져옵니다.

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>학습 루프 만들기

Personalizer 학습 루프는 [순위](#request-a-rank) 및 [보상](#send-a-reward) 호출의 주기입니다. 이 빠른 시작에서는 각 순위 호출을 수행하여 콘텐츠를 맞춤 설정한 다음, 보상 호출을 수행하여 서비스에서 콘텐츠의 순위를 얼마나 잘 지정했는지를 Personalizer에 알려줍니다. 

다음 루프 코드는 명령줄에서 사용자에게 해당 기본 설정을 요청하고, 해당 정보를 Personalizer로 보내어 순위를 지정하고, 고객에게 순위가 지정된 선택 항목을 제시하여 목록에서 선택한 다음, 서비스에서 선택 항목의 순위를 얼마나 잘 지정했는지 알려주는 주기를 반복합니다.

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

다음 섹션에서 순위 및 보상 호출에 대해 자세히 살펴보겠습니다.

코드 파일을 실행하기 전에 [콘텐츠 선택을 가져오는](#get-content-choices-represented-as-actions) 다음 메서드를 추가합니다.

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>순위 요청

프로그램에서는 순위 요청을 수행하기 위해 사용자의 기본 설정에서 콘텐츠 선택 항목을 만들도록 요청합니다. 이 프로세스는 순위에서 제외할 콘텐츠(`excludeActions`로 표시됨)를 만들 수 있습니다. 순위 요청에는 순위가 지정된 응답을 받을 수 있도록 [actions](concepts-features.md#actions-represent-a-list-of-options), currentContext, excludeActions 및 고유한 순위 이벤트 ID(GUID)가 필요합니다. 

이 빠른 시작에는 시간 및 사용자 음식 기본 설정에 대한 간단한 컨텍스트 기능이 있습니다. 프로덕션 시스템에서 [작업 및 기능](concepts-features.md)을 결정하고 [평가](concept-feature-evaluation.md)하는 것은 간단한 문제가 아닐 수 있습니다.  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>보상 보내기

프로그램에서는 보상 요청을 수행하기 위해 명령줄에서 사용자의 선택 항목을 가져와서 숫자 값을 각 선택 항목에 할당한 다음, 고유한 순위 이벤트 ID와 숫자 값을 보상 메서드로 보냅니다.

이 빠른 시작에서는 0 또는 1의 간단한 숫자를 보상으로 할당합니다. 프로덕션 시스템에서 특정 요구 사항에 따라 [보상](concept-rewards.md) 호출에 보내는 시기와 대상을 결정하는 것은 간단한 문제가 아닐 수 있습니다. 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>프로그램 실행

애플리케이션 디렉터리에서 Node.js를 사용하여 애플리케이션을 실행합니다.

```console
node sample.js
```

![빠른 시작 프로그램은 기능으로 알려진 사용자 기본 설정을 수집하기 위해 몇 가지 질문을 합니다.](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Personalizer의 작동 원리](how-personalizer-works.md)

* [Personalizer란?](what-is-personalizer.md)
* [Personalizer를 사용할 수 있는 위치](where-can-you-use-personalizer.md)
* [문제 해결](troubleshooting.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)에서 확인할 수 있습니다.
