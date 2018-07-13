---
title: Face API Service에 대한 질문과 대답 | Microsoft Docs
description: 다음은 Face API Service에 대해 가장 자주 묻는 질문과 대답입니다.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372974"
---
# <a name="face-api-frequently-asked-questions"></a>Face API 질문과 대답
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>이 FAQ에서 원하는 질문에 대한 답변을 찾을 수 없는 경우 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive)에서 Face API 커뮤니티에 물어보거나 [UserVoice](https://cognitive.uservoice.com/)의 도움말 및 지원에 문의하세요.

-----
**질문**: Face API의 인식, 확인 또는 유사 얼굴 찾기의 정확도를 떨어트리는 요인은 무엇입니까?

**대답**: 일반적으로 우리가 다른 사람을 인식하는 데 어려움을 겪는 경우와 동일하며 다음과 같은 요인이 있습니다.
* 한 쪽 눈 또는 양쪽 눈을 가리는 장애물
* 강한 역광처럼 거친 조명
* 헤어 스타일 또는 얼굴에 난 모발의 변화
* 나이에 따른 변화
* 극단적인 표정(예: 비명)

Face API는 이와 같은 어려운 상황에서도 종종 얼굴 인식에 성공하지만, 정확도가 떨어질 수 있습니다. 인식 기능을 강화하고 이러한 과제를 해결하려면 다양한 각도와 조명을 포함하고 있는 사진을 통해 Persons를 교육하세요.

-----
**질문**: 이진 이미지 데이터를 전달하는 중에 "잘못된 얼굴 이미지" 오류가 발생합니다.

**대답**: 알고리즘에 이미지 구문 분석과 관련된 문제가 있다는 의미입니다. 가능한 원인은 다음과 같습니다.
* 지원되는 입력 이미지 형식에 JPEG, PNG, GIF(첫 번째 프레임), BMP가 포함됩니다.
* 이미지 파일 크기는 4MB를 넘지 않아야 합니다.
* 감지 가능한 얼굴 크기 범위는 36x36 픽셀부터 4096x4096 픽셀 사이입니다. 이 범위를 벗어난 얼굴은 감지되지 않습니다.
* 일부 얼굴은 기술적인 문제(예: 매우 큰 얼굴 각도(머리 포즈) 및 큰 폐색)로 인해 감지되지 않을 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과를 생성합니다.

-----
