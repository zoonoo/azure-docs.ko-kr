---
title: Bing Search SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 웹을 검색하는 응용 프로그램에 대한 Bing Web Search SDK입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/11/2018
ms.author: v-gedod
ms.openlocfilehash: 9d2f363d772febfb9fffd2d9c2924398e438d6e0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377943"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing Web Search API 샘플에는 다음과 같은 시나리오가 포함됩니다.
1. 단일 단어 쿼리를 사용하여 결과를 얻고, 각 웹, 이미지, 뉴스 및 비디오 결과의 첫 번째 항목에 대한 이름 및 URL을 출력합니다.
2. 구를 쿼리하고, 결과 수를 확인한 후 첫 번째 결과의 이름 및 URL을 출력합니다.
3. `news`로 설정된 응답 필터를 사용하여 검색어를 쿼리하고 뉴스 결과의 세부 정보를 출력합니다.
4. `answerCount` 및 `promote` 매개 변수를 사용하여 검색어를 쿼리한 후 결과의 세부 정보를 출력합니다.

Bing Search SDK를 사용하면 다음 프로그래밍 언어로 웹 검색 기능을 쉽게 이용할 수 있습니다.
* [.NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 시작 
    * [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
    * 정의 및 종속성은 [.NET 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingWebSearch)도 참조하세요.
* [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 시작 
    * 정의 및 종속성은 [Node.js 라이브러리](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/webSearch)를 참조하세요.
* [Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 시작 
    * 정의 및 종속성은 [Java 라이브러리](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch)도 참조하세요.
* [Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 시작 
    * 정의 및 종속성은 [Python 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-websearch)도 참조하세요.

각 언어에 대한 SDK 샘플에는 필수 구성 요소 및 샘플 설치/실행에 대한 세부 정보가 있는 ReadMe 파일이 포함됩니다.
