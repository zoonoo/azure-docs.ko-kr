---
title: Emotion API FAQ | Microsoft Docs
description: Cognitive Services의 Emotion API에 대한 FAQ를 제공합니다.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372998"
---
# <a name="emotion-api-frequently-asked-questions"></a>Emotion API 질문과 대답
 
> [!IMPORTANT]
> Video API 미리 보기는 2017년 10월 30일에 종료됩니다. 새 [Video Indexer API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) 미리 보기를 사용하여 비디오에서 정보를 쉽게 추출하고 말이나 얼굴, 성격, 감정을 감지하여 검색 결과를 제시하는 등 콘텐츠 검색 경험을 향상하세요. [자세히 알아보기](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>이 FAQ에서 원하는 질문에 대한 답변을 찾을 수 없는 경우 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive)에서 Emotion API 커뮤니티에 물어보거나 [UserVoice](https://cognitive.uservoice.com/)의 도움말 및 지원에 문의하세요.  

-----

**질문**: *Emotion API에서 최상의 결과를 얻는 이미지 유형은 무엇입니까?*

**대답**: 장애물에 가리지 않고 얼굴 정면이 완전히 보이는 이미지를 사용하면 최상의 결과를 얻을 수 있습니다. 부분 정면 얼굴을 사용하면 신뢰성이 떨어지며 Emotion API는 얼굴이 45도 이상 회전된 이미지의 감정을 인식하지 못할 수 있습니다.

-----

**질문**: *Emotion API가 식별할 수 있는 감정은 몇 개입니까?*

**대답**: Emotion API는 일반적으로 수용되는 8가지 감정을 인식합니다. 
* 행복
* 슬픔
* 놀람
* 분노
* 두려움
* 경멸
* 역겨움 
* 무감정 

-----

**질문**: *API에 라이브 비디오 스트림을 전달하는 동시에 결과를 가져올 수 있는 방법이 있나요?*

**대답**: 이미지 기반 Emotion API를 사용하여 각 프레임에서 호출할 수도 있고 성능 향상을 위해 프레임을 건너뛸 수도 있습니다.  비디오 프레임 단위 분석 샘플이 제공됩니다.

-----

**질문**: *이진 이미지 데이터를 전달하는 중에 "잘못된 얼굴 이미지"라는 오류가 발생합니다.**

**대답**: 알고리즘에 이미지 구문 분석과 관련된 문제가 있다는 의미입니다.  
* 지원되는 입력 이미지 형식에 JPEG, PNG, GIF(첫 번째 프레임), BMP가 포함됩니다. 
* 이미지 파일 크기는 4MB를 넘지 않아야 합니다.
* 감지 가능한 얼굴 크기 범위는 36x36 픽셀부터 4096x4096 픽셀 사이입니다. 이 범위를 벗어난 얼굴은 감지되지 않습니다.
* 일부 얼굴은 기술적인 문제(예: 매우 큰 얼굴 각도(머리 포즈) 및 큰 폐색)로 인해 감지되지 않을 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과를 생성합니다.

-----
