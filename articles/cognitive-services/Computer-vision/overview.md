---
title: Computer Vision이란?
titleSuffix: Azure Cognitive Services
description: Computer Vision 서비스는 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/11/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
keywords: 컴퓨터 비전, 컴퓨터 비전 애플리케이션, 컴퓨터 비전 서비스
ms.openlocfilehash: fa00265f3c591578f9970b77770fbf7290d87510
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907069"
---
# <a name="what-is-computer-vision"></a>Computer Vision이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure의 Computer Vision 서비스는 관심 있는 시각적 기능을 기반으로 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스 권한을 제공합니다. 예를 들어, Computer Vision은 이미지에 성인 콘텐츠가 포함되어 있는지, 특정 브랜드 또는 개체를 찾거나, 사람의 얼굴을 찾는지 여부를 확인할 수 있습니다.

클라이언트 라이브러리 SDK를 통해 또는 REST API를 직접 호출하여 Computer Vision 애플리케이션을 만들 수 있습니다. 이 페이지는 Computer Vision에서 수행할 수 있는 작업을 광범위하게 다룹니다.

## <a name="computer-vision-for-digital-asset-management"></a>디지털 자산 관리를 위한 Computer Vision

Computer Vision은 여러 DAM(디지털 자산 관리) 시나리오를 구현할 수 있습니다. DAM은 풍부한 미디어 자산을 구성, 저장, 검색하고 디지털 권한 및 사용 권한을 관리하는 비즈니스 프로세스입니다. 예를 들어 기업에서 시각적 로고, 면, 개체, 색 등을 기준으로 이미지를 그룹화하고 식별하기를 원할 수 있습니다. 또는 이미지를 검색할 수 있도록 [이미지에 대한 자막을 자동으로 생성](./Tutorials/storage-lab-tutorial.md)하고 키워드를 연결하기를 원할 수 있습니다. Cognitive Services, Azure Cognitive Search 및 인텔리전트 보고를 사용하는 올인원 DAM 솔루션은 GitHub의 [정보 마이닝 솔루션 가속기 가이드](https://github.com/Azure-Samples/azure-search-knowledge-mining)를 참조하세요. 다른 DAM 예제는 [Computer Vision 솔루션 템플릿](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) 리포지토리를 참조하세요.

## <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)

Computer Vision에는 [OCR(광학 문자 인식)](concept-recognizing-text.md) 기능이 포함되어 있습니다. 새 Read API를 사용하여 이미지 및 문서에서 인쇄 및 필기 텍스트를 추출할 수 있습니다. 최신 모델을 사용하고 다양한 표면 및 배경의 텍스트로 작업합니다. 여기에는 영수증, 포스터, 명함, 문자 및 화이트보드가 포함됩니다. 두 OCR API는 [여러 언어](./language-support.md)로 인쇄된 텍스트 추출을 지원합니다. [빠른 시작](#next-steps)을 따라 시작하세요.

## <a name="analyze-images-for-insight"></a>인사이트를 위한 이미지 분석

이미지를 분석하여 이미지의 시각적 기능 및 특성에 대한 인사이트를 제공할 수 있습니다. 아래 표의 모든 기능이 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API로 제공됩니다. [빠른 시작](#next-steps)을 따라 시작하세요.


### <a name="tag-visual-features"></a>시각적 기능 태그 지정

수천 개의 인식 가능한 사물, 생물, 풍경 및 동작 세트를 기반으로 하여 이미지의 시각적 기능을 식별하고 태그를 지정합니다. 태그가 모호하거나 누구나 알 수 있는 것이 아닌 경우 API 응답은 태그의 컨텍스트를 명확히 설명하는 ‘힌트’를 제공합니다. 태그 지정은 전면에 있는 사람과 같은 주요 주체로 국한되지 않고, 환경(실내 또는 실외), 가구, 도구, 식물, 동물, 액세서리, 가젯 등을 포함합니다. [시각적 기능 태그 지정](concept-tagging-images.md)

### <a name="detect-objects"></a>개체 감지

개체 검색은 태그 지정과 유사하지만, API는 적용된 각 태그의 경계 상자 좌표를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업을 실행하면 해당 개체를 해당 좌표와 함께 이미지에 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 추가로 처리할 수 있습니다. 또한 이 기능을 통해 이미지에 동일한 태그의 여러 인스턴스가 있는 경우 이를 알 수 있습니다. [개체 감지](concept-object-detection.md)

### <a name="detect-brands"></a>브랜드 감지

수천 개의 글로벌 로고 데이터베이스에서 이미지 또는 비디오에 있는 상업용 브랜드를 식별합니다. 예를 들어 이 기능을 사용하여 소셜 미디어에서 가장 인기 있거나 미디어 제품 배치에서 가장 일반적인 브랜드를 검색할 수 있습니다. [브랜드 감지](concept-brand-detection.md)

### <a name="categorize-an-image"></a>이미지 분류

부모/자식 유전적 계층 구조가 있는 [범주 분류](Category-Taxonomy.md)를 사용하여 전체 이미지를 식별하고 분류합니다. 범주는 단독으로 사용하거나 새 태그 지정 모델을 통해 사용할 수 있습니다.<br/>현재 영어는 이미지에 대한 태그 지정 및 분류에 지원되는 유일한 언어입니다. [이미지 분류](concept-categorizing-images.md)

### <a name="describe-an-image"></a>이미지 설명

완전한 문장을 사용하여 사람이 읽을 수 있는 언어로 전체 이미지에 대한 설명을 생성합니다. Computer Vision의 알고리즘은 이미지에서 식별된 개체에 따라 다양한 설명을 생성합니다. 설명이 각각 평가되고 신뢰도 점수가 생성됩니다. 그런 다음, 가장 높은 신뢰도 점수부터 가장 낮은 점수 순으로 정렬된 목록이 반환됩니다. [이미지 설명](concept-describing-images.md)

### <a name="detect-faces"></a>얼굴 감지

이미지에서 얼굴을 감지하고, 감지된 얼굴 각각에 대한 정보를 제공합니다. Computer Vision은 감지된 얼굴 각각에 대한 좌표, 사각형, 성별 및 나이를 반환합니다.<br/>Computer Vision은 [Face](/azure/cognitive-services/face/) 서비스 기능의 하위 집합을 제공합니다. 얼굴 인식 및 포즈 감지와 같이 더 자세한 분석을 위한 Face 서비스를 사용할 수 있습니다. [얼굴 감지](concept-detecting-faces.md)

### <a name="detect-image-types"></a>이미지 유형 감지

이미지가 선 그리기인지, 아니면 이미지가 클립 아트인지 여부와 같은 이미지에 대한 특성을 감지합니다. [이미지 유형 감지](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>도메인 특정 콘텐츠 검색

도메인 모델을 사용하여 유명인 및 랜드마크와 같은 이미지의 도메인 관련 콘텐츠를 감지하고 식별합니다. 예를 들어 이미지에 사람이 포함되어있는 경우 Computer Vision은 유명인에 대한 도메인 모델을 사용하여 이미지에서 감지된 사람이 알려진 유명인과 일치하는지 확인합니다. [도메인 특정 콘텐츠 감지](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>색 구성표 감지

이미지 내의 색 사용을 분석합니다. Computer Vision은 이미지가 흑백인지, 아니면 컬러인지 여부를 결정할 수 있고, 컬러 이미지의 경우 기준 컬러와 테마 컬러를 식별할 수 있습니다. [색 구성표 감지](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>썸네일 생성

이미지의 콘텐츠를 분석하여 해당 이미지에 대한 적절한 썸네일을 생성합니다. Computer Vision은 먼저 고품질 썸네일을 생성한 다음, 이미지 내의 개체를 분석하여 ‘관심 영역’을 결정합니다. 그런 다음, Computer Vision은 관심 영역의 요구 사항에 맞게 이미지를 자릅니다. 생성된 썸네일은 필요에 따라 원래 이미지의 가로 세로 비율과 다른 가로 세로 비율을 사용하여 표시할 수 있습니다. [썸네일 생성](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>관심 영역 가져오기

이미지의 콘텐츠를 분석하여 ‘관심 영역’의 좌표를 반환합니다. 이미지를 자르고 썸네일을 생성하는 대신 Computer Vision은 영역의 경계 상자 좌표를 반환하므로 호출 애플리케이션이 원하는 대로 원본 이미지를 수정할 수 있습니다. [관심 영역 가져오기](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>이미지 콘텐츠 조정

Computer Vision을 사용하여 이미지에서 [성인 콘텐츠를 감지](concept-detecting-adult-content.md)하고 다양한 분류에 대한 신뢰도 점수를 반환할 수 있습니다. 콘텐츠 플래그 지정 임계값은 기본 설정에 맞게 슬라이딩 배율로 설정할 수 있습니다.

## <a name="use-containers"></a>컨테이너 사용

Computer Vision Docker 컨테이너를 통해 온-프레미스에서 서비스를 사용합니다. 현재 두 개의 컨테이너를 사용할 수 있습니다.

* [Computer Vision 읽기 컨테이너](computer-vision-how-to-install-containers.md)를 사용하면 이미지에서 활자체 및 필기체 텍스트를 인식할 수 있습니다.

* [Computer Vision 공간 분석 컨테이너](spatial-analysis-container.md)를 사용하면 실시간 스트리밍 비디오를 분석하여 사용자와 사용자의 물리적 환경 간 이동의 공간 관계를 이해할 수 있습니다.

## <a name="image-requirements"></a>이미지 요구 사항

Computer Vision에서 이미지를 분석하는 데 충족되어야 하는 요구 사항은 다음과 같습니다.

- 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기가 50 x 50 픽셀보다 커야 합니다.
  - 읽기 API의 경우 이미지의 크기는 50 x 50 픽셀 및 10000 x 10000 픽셀 사이여야 합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Computer Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

빠른 시작 가이드에 따라 Computer Vision 시작:

- [빠른 시작: Computer Vision .NET 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [빠른 시작: Computer Vision Python 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-python)
- [빠른 시작: Computer Vision Java 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-java)
