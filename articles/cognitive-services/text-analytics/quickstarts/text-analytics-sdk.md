---
title: '빠른 시작: Text Analytics 클라이언트 라이브러리 v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Cognitive Services의 Text Analytics API를 사용하여 언어를 감지합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/23/2020
ms.author: aahi
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 89680ad61296cd225bb87d2d6b46259586304da2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774122"
---
# <a name="quickstart-use-the-text-analytics-client-library-v3"></a>빠른 시작: Text Analytics 클라이언트 라이브러리 v3 사용

Text Analytics 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

Text Analytics 클라이언트 라이브러리 `3.0-preview`를 사용하여 다음을 수행합니다.

* 감정 분석(공개 미리 보기)
* 언어 검색
* 명명된 엔터티 인식(공개 미리 보기)
* 핵심 문구 추출

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# quickstart](../includes/quickstarts/v3/csharp.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python quickstart](../includes/quickstarts/v3/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java quickstart](../includes/quickstarts/v3/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/v3/nodejs-sdk.md)]

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
