---
title: Computer Vision의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Computer Vision에 대한 뉴스가 포함되어 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f007ddf2625bf9e8b9d4365902ac1511ea95fa81
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90904100"
---
# <a name="whats-new-in-computer-vision"></a>Computer Vision의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목은 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보입니다. 이 페이지에 책갈피를 설정하여 서비스를 최신 상태로 유지하세요.

## <a name="september-2020"></a>2020년 9월

### <a name="spatial-analysis-container-preview"></a>공간 분석 컨테이너 미리 보기

[공간 분석 컨테이너](spatial-analysis-container.md)는 현재 미리 보기로 제공됩니다. Computer Vision의 공간 분석 기능을 사용하면 실시간 스트리밍 비디오를 분석하여 사용자와 사용자의 물리적 환경 간 이동의 공간 관계를 이해할 수 있습니다. 공간 분석은 온-프레미스에서 사용할 수 있는 Docker 컨테이너입니다. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Read API v3.1 공개 미리 보기에 일본어용 OCR 추가
Computer Vision의 Read API v3.1 공개 미리 보기에 다음 기능이 추가되었습니다.
* 일본어용 OCR
* 각 텍스트 줄에 글꼴이 필기체인지, 활자체인지와 신뢰도 점수(라틴어만 해당)를 지정합니다.
* 다중 페이지 문서의 경우 선택한 페이지 또는 페이지 범위에 대해서만 텍스트를 추출합니다.

* 이 미리 보기 버전의 Read API는 영어, 네덜란드어, 프랑스어, 독일어, 이탈리아어, 일본어, 포르투갈어, 중국어 간체 및 스페인어를 지원합니다.

자세한 내용은 [Read API 개요](concept-recognizing-text.md)를 참조하세요.

> [!div class="nextstepaction"]
> [Read API v3.1 공개 미리 보기 2에 대한 자세한 정보](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>2020년 7월

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Read API v3.1 공개 미리 보기에 중국어(간체)용 OCR 추가
Computer Vision의 Read API v3.1 공개 미리 보기에는 중국어(간체) 지원이 추가되었습니다.

* 이 미리 보기 버전의 Read API는 영어, 네덜란드어, 프랑스어, 독일어, 이탈리아어, 포르투갈어, 중국어 간체 및 스페인어를 지원합니다.

자세한 내용은 [Read API 개요](concept-recognizing-text.md)를 참조하세요.

> [!div class="nextstepaction"]
> [Read API v3.1 공개 미리 보기 1에 대한 자세한 정보](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>2020년 5월
Computer Vision API v3.0에서 [읽기 API](concept-recognizing-text.md)에 대한 업데이트와 함께 일반 공급으로 전환되었습니다.

* 영어, 네덜란드어, 프랑스어, 독일어, 이탈리아어, 포르투갈어 및 스페인어 지원
* 정확도 개선
* 추출된 각 단어에 대한 신뢰도 점수
* 새 출력 형식

## <a name="march-2020"></a>2020년 3월

* 이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

## <a name="january-2020"></a>2020년 1월

### <a name="read-api-30-public-preview"></a>Read API 3.0 공개 미리 보기

이제 Read API의 버전 3.0을 사용하여 이미지에서 인쇄 또는 필기한 텍스트를 추출할 수 있습니다. 이전 버전과 비교하여 3.0은 다음을 제공합니다.
* 정확도 개선
* 새 출력 형식
* 추출된 각 단어에 대한 신뢰도 점수
* 추가 언어 매개 변수를 사용하여 스페인어 및 영어 모두 지원

3\.0 API 사용을 시작하려면 [텍스트 추출 빠른 시작](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text?tabs=version-3)을 따르세요.

## <a name="cognitive-service-updates"></a>Cognitive Service 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)
