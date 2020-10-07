---
title: '빠른 시작: Form Recognizer 클라이언트 라이브러리'
titleSuffix: Azure Cognitive Services
description: Form Recognizer 클라이언트 라이브러리를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 양식 처리, 자동화된 데이터 처리
ms.openlocfilehash: eb3fc3ebe5c3d9f659758a14ba620018671d95f5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318951"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>빠른 시작: Form Recognizer 클라이언트 라이브러리 사용

선택한 언어로 Form Recognizer 클라이언트 라이브러리를 시작합니다. Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 인식 서비스입니다. 양식 문서에서 텍스트, 키/값 쌍 및 테이블 데이터를 식별하고 추출합니다. &mdash;서비스는 원본 파일의 관계를 포함하는 구조화된 데이터를 출력합니다. 다음 단계에 따라 SDK 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 보세요.

Form Recognizer 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [양식 콘텐츠 인식](#recognize-form-content)
* [영수증 확인](#recognize-receipts)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end