---
title: 개발자 리소스 - 언어 이해
description: SDK, REST API, CLI를 사용하면 프로그래밍 언어로 언어 이해(LUIS) 앱을 개발할 수 있습니다. Azure 리소스 및 LUIS 예측을 관리합니다.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457987"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>언어 이해를 위한 SDK, REST 및 CLI 개발자 리소스(LUIS)

SDK, REST API, CLI를 사용하면 프로그래밍 언어로 언어 이해(LUIS) 앱을 개발할 수 있습니다. Azure 리소스 및 LUIS 예측을 관리합니다.

## <a name="azure-resource-management"></a>Azure 리소스 관리

Azure 인지 서비스 관리 계층을 사용하여 언어 이해 또는 인지 서비스 리소스를 생성, 편집, 목록 및 삭제합니다.

도구에 기반한 참조 문서를 찾습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>언어 이해 작성 및 예측 요청

언어 이해 서비스는 만들어야 하는 Azure 리소스에서 액세스됩니다. 두 가지 리소스가 있습니다.

* **교육의 작성 리소스를** 사용하여 작성, 편집, 학습 및 게시합니다.
* 런타임 에 대한 **예측을** 사용하여 사용자의 텍스트를 보내고 예측을 수신합니다.

[V3 예측 끝점에](luis-migration-api-v3.md)대해 자세히 알아봅니다.

[코그너티브 서비스 샘플 코드를](https://github.com/Azure-Samples/cognitive-services-quickstart-code) 사용하여 가장 일반적인 작업을 학습하고 사용합니다.

### <a name="rest-specifications"></a>REST 사양

모든 [Azure REST 사양과](https://github.com/Azure/azure-rest-api-specs)함께 LUIS [REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)사양은 GitHub에서 공개적으로 사용할 수 있습니다.

### <a name="rest-apis"></a>REST API

작성 및 예측 엔드포인트 API는 REST API에서 모두 사용할 수 있습니다.

|Type|버전|
|--|--|
|작성|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[V3 미리 보기](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|예측|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>나머지 끝점

LUIS에는 현재 두 가지 유형의 끝점이 있습니다.

* 교육 끝점에 대한 작성
* 런타임 엔드포인트에서 쿼리 예측을 할 수 있습니다.

|목적|URL|
|--|--|
|교육 끝점에 대한 작성|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 런타임 - 런타임 엔드포인트의 모든 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 런타임 - 런타임 엔드포인트의 버전 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 런타임 - 런타임 엔드포인트의 슬롯 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수|목적|
|--|--|
|`your-resource-name`|Azure 리소스 이름|
|`q` 또는 `query`|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|
|`version`|10자 버전 이름|
|`slot`| `production` 또는 `staging`|

### <a name="language-based-sdks"></a>언어 기반 SDK

|언어 |참조 설명서|패키지|샘플|빠른 시작|
|--|--|--|--|--|
|C#|[작성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[예측](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 작성](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[앱 만들기 및 관리](sdk-authoring.md?pivots=programming-language-csharp)<br>[예측 엔드포인트 쿼리](sdk-query-prediction-endpoint.md)|
|Go|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST를 사용한 작성 및 예측](luis-get-started-get-intent-from-rest.md)|
|Java|[작성 및 예측](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[메이븐 저작](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[메이븐 예측](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[작성 및 예측](luis-get-started-get-intent-from-rest.md)
|Node.js|[작성](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 작성](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 예측](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST를 사용한 작성 및 예측](luis-get-started-get-intent-from-rest.md)|
|Python|[작성 및 예측](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[작성](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[작성](sdk-authoring.md?pivots=programming-language-python)<br>[REST를 사용한 예측](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>컨테이너

언어 이해(LUIS)는 온-프레미스 및 포함된 버전의 앱을 제공하는 [컨테이너를](luis-container-howto.md) 제공합니다.

### <a name="export-and-import-formats"></a>내보내기 및 가져오기 형식

언어 이해는 JSON 형식, `.LU` [(LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)형식 및 언어 이해 컨테이너에 대한 압축 된 패키지로 앱 및 해당 모델을 관리하는 기능을 제공합니다.

이러한 형식을 가져오고 내보내는 것은 API와 LUIS 포털에서 사용할 수 있습니다. 포털은 앱 목록 및 버전 목록의 일부로 가져오기 및 내보내기를 제공합니다.

## <a name="other-tools-and-sdks"></a>기타 도구 및 SDK

봇 프레임워크는 다양한 언어로 [SDK로](https://github.com/Microsoft/botframework) 제공되며 [Azure Bot 서비스를](https://dev.botframework.com/)사용하는 서비스로 사용할 수 있습니다.

봇 프레임워크는 다음과 같은 언어 이해에 도움이 되는 [몇 가지 도구를](https://github.com/microsoft/botbuilder-tools) 제공합니다.

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - 마크다운 파일을 사용하여 LUIS 언어 이해 모델 빌드
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - LUIS.ai 애플리케이션 생성 및 관리
* [디스패치](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)- 부모 및 자식 앱 관리
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - LUIS 의도 및 엔터티에 대한 백업 C#/타이스크립트 클래스를 자동으로 생성합니다.
* [봇 프레임워크 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator/releases) - 봇 개발자가 봇 프레임워크 SDK를 사용하여 빌드된 봇을 테스트하고 디버깅할 수 있는 데스크톱 응용 프로그램입니다.
* [봇 프레임 워크 작곡가](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - 개발자와 다분야 팀이 마이크로 소프트 봇 프레임 워크와 봇 및 대화 경험을 구축하기위한 통합 개발 도구
* [마이크로 소프트 / NLU. DevOps](https://github.com/microsoft/NLU.DevOps) - NLU 서비스에 대한 지속적인 통합 및 배포를 지원하는 도구입니다.

## <a name="next-steps"></a>다음 단계

* 일반적인 HTTP [오류 코드에](luis-reference-response-codes.md) 대해 알아보기
* 모든 API 및 SDK에 대한 [참조 문서](https://docs.microsoft.com/azure/index)
* [봇 프레임워크](https://github.com/Microsoft/botbuilder-dotnet) 및 [Azure 봇 서비스](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [인지 용기](../cognitive-services-container-support.md)
