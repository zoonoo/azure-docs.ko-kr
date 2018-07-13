---
title: Bing Search SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 웹을 검색하는 응용 프로그램에 대한 Bing Search SDK입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 41e4880eec0df16ee012226389d0c054baa7bd4d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377886"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing Entity Search API 샘플에는 다음과 같은 시나리오가 포함됩니다.
1.  Tom Cruise 같은 엔터티를 검색하고 풍부한 정보를 얻습니다.
2.  여러 의도가 있을 수 있는 쿼리에서 용어 명확성을 처리합니다.
3.  식당과 같은 로컬 엔터티를 검색하고 관련된 풍부한 정보를 얻습니다.
4.  식당과 같은 로컬 비즈니스를 검색하고 풍부한 정보를 얻습니다.
5.  잘못된 요청 및 오류 처리를 트리거합니다.

> [!NOTE] 
> 현재, 일부 SDK는 GA로 제공되며 설명서에 대한 변경은 보류 중입니다. 

Bing Search SDK를 사용하면 다음 프로그래밍 언어로 웹 검색 기능을 쉽게 이용할 수 있습니다.
* [.NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 시작 
    * [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)
    * 정의 및 종속성은 [.NET 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingEntitySearch)도 참조하세요.
* [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 시작 
    * 정의 및 종속성은 [Node.js 라이브러리](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/entitySearch)를 참조하세요.
* [Java 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 시작 
    * 정의 및 종속성은 [Java 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-cognitiveservices/search/bingentitysearch)도 참조하세요.
* [Python 샘플](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 시작 
    * 정의 및 종속성은 [Python 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-entitysearch)도 참조하세요.

각 언어에 대한 SDK 샘플에는 필수 구성 요소 및 샘플 설치/실행에 대한 세부 정보가 있는 ReadMe 파일이 포함됩니다.
