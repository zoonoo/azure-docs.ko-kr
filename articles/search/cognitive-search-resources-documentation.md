---
title: AI 보강에 대한 설명서 링크
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 작업과 관련된 문서, 자습서, 샘플 및 블로그 게시물에 대해 주석이 달린 목록입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9f8a14c348f4f8eddcd60c6946c831c90262757b
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588997"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search의 AI 보강에 대한 설명서 리소스

AI 보강은 텍스트가 아닌 원본 및 구분되지 않는 텍스트에서 잠재적인 정보를 찾아 이를 Azure Cognitive Search에서 전체 텍스트 검색 가능한 콘텐츠로 변환하는 Azure Cognitive Search 인덱싱 기능입니다.

다음 문서는 AI 보강에 대한 전체 설명서입니다.

## <a name="getting-started"></a>시작
+ [Azure Cognitive Search의 AI 소개](cognitive-search-concept-intro.md)
+ [빠른 시작: Azure Portal에서 인지 기술 만들기](cognitive-search-quickstart-blob.md)
+ [자습서: AI를 사용 하 여 보강 인덱싱](cognitive-search-tutorial-blob.md)
+ [예: AI 보강에 대 한 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>방법 가이드
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합의 주석 참조하는 방법](cognitive-search-concept-annotations-syntax.md)
+ [필드를 인덱스에 매핑하는 방법](cognitive-search-output-field-mapping.md)
+ [이미지에서 정보 처리 및 추출하는 방법](cognitive-search-concept-image-scenarios.md)
+ [Azure Cognitive Search 인덱스를 다시 빌드하는 방법](search-howto-reindex.md)
+ [사용자 정의 기술 인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md)
+ [문제 해결 팁](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 사용자 지정 기술
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [사용되지 않는 기술](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [기술 세트 만들기(api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [인덱서 만들기(api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure Cognitive Search란?](search-what-is-azure-search.md)
