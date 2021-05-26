---
title: '빠른 시작: Form Recognizer 클라이언트 라이브러리 또는 REST API'
titleSuffix: Azure Applied AI Services
description: Form Recognizer 클라이언트 라이브러리 또는 REST API를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 양식 처리, 자동화된 데이터 처리
ms.openlocfilehash: 34b2e591c8d050a168e82bb40c5167823c136e52
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374199"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>빠른 시작: Form Recognizer 클라이언트 라이브러리 또는 REST API 사용

선택한 개발 언어로 Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 인식 서비스입니다. 양식 문서에서 텍스트, 키/값 쌍, 선택 표시 및 테이블 데이터 등을 식별하고 추출합니다. &mdash;서비스는 원본 파일의 관계를 포함하는 구조화된 데이터를 출력합니다. REST API 또는 SDK를 통해 Form Recognizer를 사용할 수 있습니다. 기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한됩니다.

다음 API를 사용하여 양식 및 문서에서 정형 데이터를 추출합니다.

|이름 |설명 |
|---|---|
| **[레이아웃 분석](#analyze-layout)** | 스트림으로 전달된 문서를 분석하여 문서에서 텍스트, 선택 표시, 표 및 구조 추출 |
| **[영수증 분석](#analyze-receipts)** | 영수증 문서를 분석하여 키 정보 및 다른 영수증 텍스트를 추출합니다.|
| **[명함 분석](#analyze-business-cards)** | 명함을 분석하여 주요 정보 및 텍스트를 추출합니다.|
| **[송장 분석](#analyze-invoices)** | 송장을 분석하여 주요 정보, 표 및 기타 송장 텍스트를 추출합니다.|
| **[ID 문서 분석](#analyze-identity-documents)** | ID 문서를 분석하여 키 정보 및 기타 식별 카드 텍스트를 추출합니다.|
| **[사용자 지정 모델 학습](#train-a-custom-model)**| 동일한 형식의 5개 양식을 사용하여 양식을 분석하는 새 모델을 학습시킵니다. _useLabelFile_ 매개 변수를 `true`로 설정하여 수동 레이블 지정 데이터로 학습합니다. |
| **[사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)**|스트림으로 전달된 양식을 분석하여 사용자 지정 모델을 통해 양식에서 텍스트, 키/값 쌍 및 표를 추출합니다.  |
|**[사용자 지정 모델 관리](#manage-custom-models)**| Form Recognizer 계정에서 사용자 지정 모델 수를 확인하고, 해당 ID를 사용하여 특정 모델을 가져오고, 계정에서 모델을 삭제할 수 있습니다.|

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

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
