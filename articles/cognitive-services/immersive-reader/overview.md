---
title: 몰입형 리더 API란?
titleSuffix: Azure Cognitive Services
description: 몰입형 리더 API는 학습 능력에 차이가 있는 여러 사용자를 수용하거나 새 리더 및 언어 학습자에게 도움을 주는 데 사용할 수 있는 도구입니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metan
ms.openlocfilehash: 5acd6e4be178c7e645eb6855a79cd0f58303ba9a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846427"
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
* [GitHub의 몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 탐색
* [몰입형 리더 SDK 참조](./reference.md) 읽기