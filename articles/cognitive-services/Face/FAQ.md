---
title: 질문과 대답 - Face API
titlesuffix: Azure Cognitive Services
description: 다음은 Face API Service에 대해 가장 자주 묻는 질문과 대답입니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351338"
---
# <a name="face-api-frequently-asked-questions"></a>Face API 질문과 대답

> [!TIP]
> 이 FAQ에서 원하는 질문에 대한 답변을 찾을 수 없는 경우 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive)에서 Face API 커뮤니티에 물어보거나 [UserVoice](https://cognitive.uservoice.com/)의 도움말 및 지원에 문의하세요.

-----
**질문**: 요인은 무엇 인식, 확인 또는 유사한 찾을 Face API의 정확도 줄일 수 있습니다?

**대답**: 일반적으로 사람 포함 하 여 사용자를 식별 하는 데 문제가 있는 동일한 사례 것:
* 한 쪽 눈 또는 양쪽 눈을 가리는 장애물
* (예: 심각한 역광) harsh 조명
* 헤어 스타일 또는 얼굴에 난 모발의 변화
* 나이에 따른 변화
* (예: 차지) 극단적인 얼굴 표정

Face API 이지만, 위와 같은 까다로운 사례를 성공적으로 종종 정확도 줄일 수 있습니다. 인식 기능을 강화하고 이러한 과제를 해결하려면 다양한 각도와 조명을 포함하고 있는 사진을 통해 Persons를 교육하세요.

-----
**질문**:  이진 이미지 데이터를 전달하는 중에 "잘못된 얼굴 이미지" 오류가 발생합니다.

**대답**:  이 오류는 알고리즘이 이미지를 구문 분석 하는 문제를 의미 합니다. 가능한 원인은 다음과 같습니다.
* 지원되는 입력 이미지 형식에 JPEG, PNG, GIF(첫 번째 프레임), BMP가 포함됩니다.
* 이미지 파일 크기는 4MB를 넘지 않아야 합니다.
* 감지 가능한 얼굴 크기 범위는 36x36 픽셀부터 4096x4096 픽셀 사이입니다. 이 범위를 벗어난 얼굴은 감지되지 않습니다.
* 일부 얼굴 기술적인 문제, 예를 들어 큰 얼굴 각도 (머리 포즈), 큰 폐색으로 인해 검색 되지 않을 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과를 생성합니다.

-----
