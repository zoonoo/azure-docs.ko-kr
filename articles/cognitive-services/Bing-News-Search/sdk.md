---
title: Bing News Search SDK
titleSuffix: Azure Cognitive Services
description: 웹을 검색하는 애플리케이션에 대한 Bing News Search SDK입니다.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801236"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing News Search API 샘플에는 다음과 같은 시나리오가 포함됩니다.
1. `market` 및 `count` 매개 변수를 사용하여 검색 용어가 포함된 뉴스를 쿼리하고, 결과 수를 확인한 후 첫 번째 뉴스 결과의 `totalEstimatedMatches`, 이름, URL, 설명, 게시된 시간 및 공급자 이름을 출력합니다.
2. `freshness` 및 `sortBy` 매개 변수를 사용하여 검색 용어가 포함된 최신 뉴스를 쿼리하고, 결과 수를 확인한 후 첫 번째 뉴스 결과의 `totalEstimatedMatches`, URL, 설명, 게시된 시간 및 공급자 이름을 출력합니다.
3. 안전 검색을 사용하여 `movie` 및 `TV entertainment`에 대한 범주 뉴스를 쿼리하고, 결과 수를 확인한 후 첫 번째 뉴스 결과의 범주, 이름, URL, 설명, 게시된 시간 및 공급자 이름을 출력합니다.
4. Bing에서 최신 뉴스 항목을 쿼리하고, 결과 수를 확인한 후 첫 번째 뉴스 결과의 이름, 쿼리 텍스트, `webSearchUrl`, `newsSearchUrl` 및 이미지 URL을 출력합니다.

Bing Search SDK를 사용하면 다음 프로그래밍 언어로 웹 검색 기능을 쉽게 이용할 수 있습니다.
* [.NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 시작
    * [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * 정의 및 종속성은 [.NET 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch)도 참조하세요.
* [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 시작 
    * 정의 및 종속성은 [Node.js 라이브러리](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch)를 참조하세요.
* [Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 시작 
    * 정의 및 종속성은 [Java 라이브러리](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch)도 참조하세요.
* [Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 시작 
    * 정의 및 종속성은 [Python 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch)도 참조하세요.

각 언어에 대한 SDK 샘플에는 필수 구성 요소 및 샘플 설치/실행에 대한 세부 정보가 있는 ReadMe 파일이 포함됩니다.