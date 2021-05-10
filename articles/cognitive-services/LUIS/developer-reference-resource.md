---
title: 개발자 리소스 - Language Understanding
description: SDK, REST API, CLI는 사용하는 프로그래밍 언어로 LUIS(Language Understanding) 앱을 개발하는 데 도움이 됩니다. Azure 리소스 및 LUIS 예측을 관리합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 04c7d4a7e725d99c7dba94779d365312f8b960af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787110"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>LUIS(Language Understanding)을 위한 SDK, REST 및 CLI 개발자 리소스

SDK, REST API, CLI는 사용하는 프로그래밍 언어로 LUIS(Language Understanding) 앱을 개발하는 데 도움이 됩니다. Azure 리소스 및 LUIS 예측을 관리합니다.

## <a name="azure-resource-management"></a>Azure 리소스 관리

Azure Cognitive Services 관리 계층을 사용하여 Language Understanding 또는 Cognitive Service 리소스를 만들고, 편집하고, 나열하고, 삭제할 수 있습니다.

도구를 기반으로 하는 참조 설명서를 찾습니다.

* [Azure CLI](/cli/azure/cognitiveservices#az_cognitiveservices_list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 작성 및 예측 요청

Language Understanding 서비스는 사용자가 만들어야 하는 Azure 리소스에서 액세스할 수 있습니다. 두 가지 리소스가 있습니다.

* 만들기, 편집, 학습 및 게시를 위해 학습에 **작성** 리소스를 사용합니다.
* 사용자의 텍스트를 보내고 예측을 받기 위해 런타임에 **예측** 을 사용합니다.

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 알아봅니다.

[Cognitive Services 샘플 코드](https://github.com/Azure-Samples/cognitive-services-quickstart-code)를 사용하여 가장 일반적인 작업을 학습하고 사용합니다.

### <a name="rest-specifications"></a>REST 사양

[LUIS REST 사양](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)은 모든 [Azure REST 사양](https://github.com/Azure/azure-rest-api-specs)과 함께 GitHub에서 공개적으로 사용할 수 있습니다.

### <a name="rest-apis"></a>REST API

작성 및 예측 엔드포인트 API는 REST API에서 사용할 수 있습니다.

|유형|버전|
|--|--|
|작성|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[미리 보기 V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|예측|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST 엔드포인트

LUIS에는 현재 두 가지 유형의 엔드포인트가 있습니다.

* 학습 엔드포인트의 **작성**
* 런타임 엔드포인트의 쿼리 **예측**

|목적|URL|
|--|--|
|학습 엔드포인트에서 V2 작성|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|학습 엔드포인트에서 V3 작성|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 예측 - 런타임 엔드포인트에서의 모든 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 예측 - 런타임 엔드포인트에서의 버전 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 예측 - 런타임 엔드포인트에서의 슬롯 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수|목적|
|--|--|
|`your-resource-name`|Azure 리소스 이름|
|`q` 또는 `query`|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|
|`version`|10자 버전 이름|
|`slot`| `production` 또는 `staging`|

### <a name="rest-query-string-parameters"></a>REST 쿼리 문자열 매개 변수

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>앱 스키마

[앱 스키마](app-schema-definition.md)는 `.json` 또는 `.lu` 형식으로 가져오고 내보냅니다.

### <a name="language-based-sdks"></a>언어 기반 SDK

|언어 |참조 설명서|패키지|빠른 시작|
|--|--|--|--|
|C#|[작성](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring)</br>[예측](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime)|[NuGet 작성](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[작성](./client-libraries-rest-api.md?pivots=rest-api)<br>[쿼리 예측](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[작성 및 예측](/java/api/overview/azure/cognitiveservices/client/languageunderstanding)|[Maven 작성](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 예측](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[작성](/javascript/api/@azure/cognitiveservices-luis-authoring/)<br>[예측](/javascript/api/@azure/cognitiveservices-luis-runtime/)|[NPM 작성](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 예측](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[작성](./client-libraries-rest-api.md?pivots=rest-api)<br>[예측](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[작성 및 예측](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[작성](./client-libraries-rest-api.md?pivots=rest-api)<br>[예측](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>컨테이너

LUIS(Language Understanding)는 앱의 온-프레미스 및 포함된 버전을 제공하는 [컨테이너](luis-container-howto.md)를 제공합니다.

### <a name="export-and-import-formats"></a>내보내기 및 가져오기 형식

Language Understanding은 JSON 형식, `.LU`([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 형식 및 Language Understanding 컨테이너용 압축된 패키지에서 앱 및 해당 모델을 관리하는 기능을 제공합니다.

이러한 형식의 가져오기 및 내보내기는 API와 LUIS 포털에서 사용할 수 있습니다. 이 포털은 앱 목록 및 버전 목록의 일부로 가져오기 및 내보내기를 제공합니다.

## <a name="workshops"></a>워크샵

* GitHub: (워크샵) [대화형 AI: LUIS를 사용한 NLU](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>지속적인 통합 도구

* GitHub: (미리 보기) [DevOps 방법을 사용하여 LUIS 앱 개발](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) - NLU 서비스에 대한 지속적인 통합 및 배포를 지원하는 도구.

## <a name="bot-framework-tools"></a>Bot Framework 도구

Bot Framework는 다양한 언어의 [SDK](https://github.com/Microsoft/botframework)로 또한 [Azure Bot Service](https://dev.botframework.com/)를 사용하는 서비스로 제공됩니다.

Bot Framework는 다음을 비롯하여 Language Understanding에 유용한 [여러 가지 도구](https://github.com/microsoft/botbuilder-tools)를 제공합니다.
* [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) - 봇 개발자가 Bot Framework SDK를 사용하여 빌드된 봇을 테스트 및 디버그할 수 있게 해주는 데스크톱 애플리케이션
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - 개발자 및 다분야 팀이 Microsoft Bot Framework를 사용하여 봇 및 대화형 환경을 빌드하는 통합 개발 도구
* [Bot Framework 샘플](https://github.com/microsoft/botbuilder-samples) - #C, JavaScript, TypeScript 및 Python로 작성

## <a name="next-steps"></a>다음 단계

* 일반적인 [HTTP 오류 코드](luis-reference-response-codes.md)에 대해 알아보기
* 모든 API 및 SDK에 대한 [참조 설명서](../../index.yml)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) 및 [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Cognitive 컨테이너](../cognitive-services-container-support.md)
