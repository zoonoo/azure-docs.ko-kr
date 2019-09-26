---
title: 개발자 리소스-Language Understanding
titleSuffix: Azure Cognitive Services
description: 개발자는 Language Understanding에 대 한 REST Api와 Sdk를 모두 포함 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 97684a4668c1d495fa690674f7bd94e064737bd4
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273960"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding에 대 한 개발자 리소스

개발자는 Language Understanding에 대 한 REST Api와 Sdk를 모두 사용할 수 있습니다. 

## <a name="azure-resource-management"></a>Azure 리소스 관리

Azure Cognitive Services 관리 계층을 사용 하 여 Language Understanding 또는 인지 서비스 리소스를 만들고, 편집 하 고, 나열 하 고, 삭제할 수 있습니다.

도구를 기반으로 하는 참조 설명서를 찾습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 작성 및 예측 요청

Language Understanding 서비스는 만들어야 하는 Azure 리소스에서 액세스할 수 있습니다. 작성 및 예측 끝점 리소스 라는 두 가지 리소스가 있습니다. 이러한 리소스를 모두 사용 하 여 LUIS 리소스를 제어할 수 있습니다. 

### <a name="rest-apis"></a>REST API

작성 및 예측 끝점 API는 REST Api에서 사용할 수 있습니다.

* [참조 문서](https://go.microsoft.com/fwlink/?linkid=2092087) 작성
* 예측 런타임 [참조 설명서](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>언어 기반 Sdk

|언어 |참조 설명서|패키지|샘플|빠른 시작|
|--|--|--|--|--|
|C#|[작성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[예측](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 제작](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[앱 만들기 및 관리](sdk-csharp-quickstart-authoring-app.md)<br>[쿼리 예측 끝점](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST를 사용 하 여 제작](luis-get-started-go-add-utterance.md)<br>[REST를 사용한 예측](luis-get-started-go-get-intent.md)|
|Java|[작성 및 예측](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 작성](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 예측](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[작성](luis-get-started-java-add-utterance.md)<br>[예측](luis-get-started-java-get-intent.md)
|Node.js|[작성](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 작성](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM 예측](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST를 사용 하 여 제작](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[REST를 사용한 예측](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[작성 및 예측](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[작성](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[작성](sdk-python-quickstart-authoring-app.md)<br>[REST를 사용한 예측](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>기타 도구 및 Sdk

Bot framework는 다양 한 언어의 [SDK](https://github.com/Microsoft/botframework) 로 제공 되며 [Azure Bot Service](https://dev.botframework.com/)를 사용 하는 서비스로 제공 됩니다. 

Bot framework는 다음을 비롯 하 여 Language Understanding에 도움이 되는 [여러 가지 도구](https://github.com/microsoft/botbuilder-tools) 를 제공 합니다.

* [Ludown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -빌드 LUIS 언어 markdown 파일을 사용 하 여 모델 이해
* [LUIS Cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -LUIS.ai 응용 프로그램 만들기 및 관리
* [디스패치](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-부모 및 자식 앱 관리
* [Luisgen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -LUIS 의도 및 C#엔터티에 대 한 백업/Typescript 클래스를 자동으로 생성 합니다.
* [Bot emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) -봇 개발자가 BOT Framework SDK를 사용 하 여 빌드한 봇을 테스트 하 고 디버그할 수 있도록 하는 데스크톱 응용 프로그램입니다.


## <a name="next-steps"></a>다음 단계

일반적인 [HTTP 오류 코드](luis-reference-response-codes.md) 에 대 한 자세한 정보