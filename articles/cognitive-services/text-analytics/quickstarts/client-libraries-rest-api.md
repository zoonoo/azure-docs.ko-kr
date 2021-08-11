---
title: '빠른 시작: Text Analytics 클라이언트 라이브러리를 사용한 텍스트 마이닝'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 통해 Azure Cognitive Services에서 Text Analytics API를 사용하여 감정 분석 등을 수행합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/08/2021
ms.author: aahi
keywords: 텍스트 마이닝, 감정 분석, 텍스트 분석
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 69026d9abd8e0f3a978d9c327e9cb9c2e0140d04
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113565781"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>빠른 시작: Text Analytics 클라이언트 라이브러리 및 REST API 사용

이 문서를 사용하여 Text Analytics 클라이언트 라이브러리 및 REST API를 시작합니다. 다음 단계에 따라 텍스트 마이닝을 위한 예제 코드를 사용해 봅니다.

* 감정 분석
* 오피니언 마이닝
* 언어 검색
* 엔터티 인식
* 개인 식별 정보 인식
* 핵심 문구 추출


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
> * Text Analytics for health 또는 비동기 작업을 사용하려면 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 또는 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)에 대한 Github의 예를 참조하세요.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
Text Analytics for health 또는 비동기 작업을 사용하려면 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 또는 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)에 대한 Github의 예를 참조하세요.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
> * [브라우저에서](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) 이 버전의 Text Analytics 클라이언트 라이브러리를 실행할 수도 있습니다.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요. Text Analytics for health 또는 비동기 작업을 사용하려면 [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) 또는 [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)에 대한 Github의 예를 참조하세요.

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.1`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [솔루션 살펴보기](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
