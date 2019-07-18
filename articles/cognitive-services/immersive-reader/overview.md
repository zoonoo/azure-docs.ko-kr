---
title: 몰입형 리더 API란?
titleSuffix: Azure Cognitive Services
description: 몰입형 리더 API에 대해 알아봅니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718440"
---
# <a name="what-is-immersive-reader"></a>몰입형 판독기란?

[몰입형 리더](https://www.onenote.com/learningtools)는 신흥 독자, 언어 학습자 및 학습 차이(예: 난독증)가 있는 사람들을 위해 독해력을 향상시키기 위해 입증된 기술을 구현하는 포괄적으로 설계된 도구입니다.

몰입형 판독기 SDK를 사용하여 웹 애플리케이션에서 몰입형 판독기를 사용할 수 있습니다.

## <a name="what-does-immersive-reader-do"></a>몰입형 리더의 기능

몰입형 리더는 모든 사용자가 보다 이해하기 쉽게 읽을 수 있도록 설계되었습니다.

* 최소 읽기용 보기로 콘텐츠 표시

  ![몰입형 판독기](./media/immersive-reader.png)

* 일반적으로 사용되는 단어의 사진 표시

  ![그림 사전](./media/picture-dictionary.png)

* 명사, 동사, 형용사 및 부사 강조 표시

  ![품사](./media/parts-of-speech.png)

* 콘텐츠를 소리 내어 읽기

  ![소리 내어 읽기](./media/read-aloud.png)

* 콘텐츠를 다른 언어로 번역

  ![Translation](./media/translation.png)

* 단어를 음절로 나누기

  ![음절 구분](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>몰입형 리더의 작동 방식

몰입형 리더는 독립 실행형 웹앱으로서 몰입형 리더 JavaScript SDK를 사용하여 호출될 때 `iframe`을 통해 기존 웹앱 상단에 표시됩니다. API를 호출하여 몰입형 리더를 시작할 때 몰입형 리더에 표시할 콘텐츠를 지정합니다. SDK는 `iframe` 만들기 및 스타일 지정을 처리하고, 품사, 텍스트 음성 변환, 번역 등의 콘텐츠를 처리하는 몰입형 리더 백 엔드 서비스와의 통신을 처리합니다.

## <a name="next-steps"></a>다음 단계

몰입형 판독기로 시작:

* [빠른 시작](./quickstart.md)으로 이동
* [GitHub의 몰입형 리더 SDK](https://github.com/Microsoft/immersive-reader-sdk) 탐색
* [몰입형 리더 SDK 참조](./reference.md) 읽기