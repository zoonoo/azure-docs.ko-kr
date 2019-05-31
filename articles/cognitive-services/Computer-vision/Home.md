---
title: Computer Vision API란? - Computer Vision
titlesuffix: Azure Cognitive Services
description: Computer Vision 서비스는 개발자에게 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3a307c32d15aa8c7809d728d98220b0819e91c7c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357385"
---
# <a name="what-is-computer-vision"></a>Computer Vision이란?

Azure의 Computer Vision 서비스는 개발자에게 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다. 이미지를 분석하려면 이미지를 업로드하거나 이미지 URL을 지정할 수 있습니다. 이미지 처리 알고리즘은 관심 있는 시각적 기능에 따라 콘텐츠를 다양한 방식으로 분석할 수 있습니다. 예를 들어 Computer Vision에서 이미지에 성인용 또는 선정적인 콘텐츠가 포함되어 있는지 확인하거나 이미지에서 모든 사람 얼굴을 찾을 수 있습니다.

네이티브 SDK를 사용하거나 REST API를 직접 호출하여 애플리케이션에서 Computer Vision을 사용할 수 있습니다. 이 페이지는 Computer Vision에서 수행할 수 있는 작업을 광범위하게 다룹니다.

## <a name="analyze-images-for-insight"></a>인사이트를 위한 이미지 분석

이미지를 분석하여 이미지의 시각적 기능 및 특성에 대한 인사이트를 검색하고 제공할 수 있습니다. 아래 표의 모든 기능이 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API로 제공됩니다.

| 조치 | 설명 |
| ------ | ----------- |
|**[시각적 기능 태그 지정](concept-tagging-images.md)**|수천 개의 인식 가능한 사물, 생물, 풍경 및 동작 세트를 기반으로 하여 이미지의 시각적 기능을 식별하고 태그를 지정합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 알려진 설정의 컨텍스트에서 태그의 의미를 명확히 설명하는 ‘힌트’를 제공합니다. 태그 지정은 전면에 있는 사람과 같은 주요 주체로 국한되지 않고, 환경(실내 또는 실외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등을 포함합니다.|
|**[개체 검색](concept-object-detection.md)**| 개체 검색은 태그 지정과 유사하지만, API는 적용된 각 태그의 경계 상자 좌표를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업은 해당 개체를 해당 좌표와 함께 이미지에 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 추가로 처리할 수 있습니다. 또한 이 기능을 통해 이미지에 동일한 태그의 여러 인스턴스가 있는 경우 이를 알 수 있습니다.|
|**[브랜드 검색](concept-brand-detection.md)**|수천 개의 글로벌 로고 데이터베이스에서 이미지 또는 비디오에 있는 상업용 브랜드를 식별합니다. 예를 들어, 이 기능을 사용하여 소셜 미디어에서 가장 인기 있거나 미디어 제품 게재 위치에서 가장 유행하는 브랜드를 찾을 수 있습니다.|
|**[이미지 분류](concept-categorizing-images.md)**|부모/자식 유전적 계층 구조가 있는 [범주 분류](Category-Taxonomy.md)를 사용하여 전체 이미지를 식별하고 분류합니다. 범주는 단독으로 사용하거나 새 태그 지정 모델을 통해 사용할 수 있습니다.<br/>현재 영어는 이미지에 대한 태그 지정 및 분류에 지원되는 유일한 언어입니다.|
|**[이미지 설명](concept-describing-images.md)**|완전한 문장을 사용하여 사람이 읽을 수 있는 언어로 전체 이미지에 대한 설명을 생성합니다. Computer Vision의 알고리즘은 이미지에서 식별된 개체에 따라 다양한 설명을 생성합니다. 설명이 각각 평가되고 신뢰도 점수가 생성됩니다. 그런 다음, 가장 높은 신뢰도 점수부터 가장 낮은 점수 순으로 정렬된 목록이 반환됩니다.|
|**[얼굴 감지](concept-detecting-faces.md)** |이미지에서 얼굴을 감지하고, 감지된 얼굴 각각에 대한 정보를 제공합니다. Computer Vision은 감지된 얼굴 각각에 대한 좌표, 사각형, 성별 및 나이를 반환합니다.<br/>Computer Vision은 [Face](/azure/cognitive-services/face/)에서 찾을 수 있는 기능의 하위 집합을 제공하며, 얼굴 식별 및 자세 감지와 같은 더 자세한 분석을 위해 Face 서비스를 사용할 수 있습니다.|
|**[이미지 유형 감지](concept-detecting-image-types.md)**|이미지가 선 그리기인지, 아니면 이미지가 클립 아트인지 여부와 같은 이미지에 대한 특성을 감지합니다.|
|**[도메인 특정 콘텐츠 감지](concept-detecting-domain-content.md)**|도메인 모델을 사용하여 유명인 및 랜드마크와 같은 이미지의 도메인 관련 콘텐츠를 감지하고 식별합니다. 예를 들어 이미지에 사람이 포함되어있는 경우 Computer Vision은 서비스에 포함된 유명인에 대한 도메인 모델을 사용하여 이미지에서 감지된 사람이 알려진 유명인과 일치하는지 확인합니다.|
|**[색 구성표 감지](concept-detecting-color-schemes.md)**|이미지 내의 색 사용을 분석합니다. Computer Vision은 이미지가 흑백인지, 아니면 컬러인지 여부를 결정할 수 있고, 컬러 이미지의 경우 기준 컬러와 테마 컬러를 식별할 수 있습니다.|
|**[썸네일 생성](concept-generating-thumbnails.md)**|이미지의 콘텐츠를 분석하여 해당 이미지에 대한 적절한 썸네일을 생성합니다. Computer Vision은 먼저 고품질 썸네일을 생성한 다음, 이미지 내의 개체를 분석하여 ‘관심 영역’을 결정합니다.  그런 다음, Computer Vision은 관심 영역의 요구 사항에 맞게 이미지를 자릅니다. 생성된 썸네일은 필요에 따라 원래 이미지의 가로 세로 비율과 다른 가로 세로 비율을 사용하여 표시할 수 있습니다.|
|**[관심 영역 가져오기](concept-generating-thumbnails.md#area-of-interest)**|이미지의 콘텐츠를 분석하여 ‘관심 영역’의 좌표를 반환합니다.  이는 썸네일을 생성하는 데 사용되는 것과 동일한 기능이지만, 이미지를 자르는 대신 Computer Vision은 영역의 경계 상자 좌표를 반환하므로 호출 애플리케이션이 원하는 대로 원본 이미지를 수정할 수 있습니다.|

## <a name="extract-text-from-images"></a>이미지에서 텍스트 추출

Computer Vision을 사용하여 [OCR(광학 문자 인식)을 통해 이미지의 텍스트를 머신에서 읽을 수 있는 문자 스트림으로 추출](concept-recognizing-text.md#ocr-optical-character-recognition-api)할 수 있습니다. 필요한 경우 OCR은 인식된 텍스트의 회전을 수정하고 각 단어에 대한 프레임 좌표를 제공합니다. OCR은 25개 언어를 지원하며, 인식된 텍스트의 언어를 자동으로 감지합니다.

[Read API](concept-recognizing-text.md#read-api)를 사용하여 이미지 및 텍스트가 많은 문서에서 인쇄 및 필기 텍스트를 추출할 수도 있습니다. Read API는 업데이트된 모델을 사용하고, 여러 다른 표면 및 배경을 갖는 다양한 개체(예: 영수증, 포스터, 명함, 편지 및 화이트보드)에 사용할 수 있습니다. 현재 지원되는 언어는 영어뿐입니다.

## <a name="moderate-content-in-images"></a>이미지 콘텐츠 조정

Computer Vision을 사용하여 이미지에서 [성인 및 외설 콘텐츠를 감지](concept-detecting-adult-content.md)하고 둘 모두에 대한 신뢰도 점수를 반환할 수 있습니다. 성인용 및 선정적인 콘텐츠 감지 필터는 기본 설정에 맞게 슬라이딩 눈금에 설정할 수 있습니다.

## <a name="use-containers"></a>컨테이너 사용

데이터에 더 가깝게 표준화된 Docker 컨테이너를 설치함으로써 [Computer Vision 컨테이너를 사용](computer-vision-how-to-install-containers.md)하여 인쇄 텍스트와 필기 텍스트를 로컬로 인식합니다.

## <a name="image-requirements"></a>이미지 요구 사항

Computer Vision에서 이미지를 분석하는 데 충족되어야 하는 요구 사항은 다음과 같습니다.

- 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기가 50 x 50 픽셀보다 커야 합니다.
  - OCR의 경우 이미지의 크기는 50 x 50 픽셀 및 4,200 x 4,200 픽셀 사이여야 합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Computer Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

빠른 시작 가이드에 따라 Computer Vision 시작:

- [빠른 시작: 이미지 분석](quickstarts-sdk/csharp-analyze-sdk.md)
- [빠른 시작: 필기 텍스트 추출](quickstarts-sdk/csharp-hand-text-sdk.md)
- [빠른 시작: 썸네일 생성](quickstarts-sdk/csharp-thumb-sdk.md)
