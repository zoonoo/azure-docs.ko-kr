---
title: 몰입형 리더란?
titleSuffix: Azure Cognitive Services
description: 몰입형 리더는 학습 능력에 차이가 있는 사용자를 돕거나 새 리더 및 언어 학습자가 독해력을 갖도록 돕기 위해 고안된 도구입니다.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: 리더, 언어 학습자, 사진 표시, 읽기 개선, 콘텐츠 읽기, 번역
ms.openlocfilehash: 0661bd5f917a2bf071f11393ad8ac9af53ddfe97
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631242"
---
# <a name="what-is-immersive-reader"></a>몰입형 판독기란?

[몰입형 리더](https://www.onenote.com/learningtools)는 새 리더, 언어 학습자 및 학습 차이(예: 난독증)가 있는 사람들을 위해 독해력을 향상시키기 위해 입증된 기술을 구현하는 포괄적으로 설계된 도구입니다. 몰입형 리더 클라이언트 라이브러리를 사용하면 Microsoft Word 및 Microsoft One Note에 사용된 것과 동일한 기술을 활용하여 웹 애플리케이션을 개선할 수 있습니다. 

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>몰입형 리더를 사용하여 읽기 가능성 향상 

몰입형 리더는 모든 사용자가 보다 이해하기 쉽게 읽을 수 있도록 설계되었습니다. 몰입형 리더의 핵심 기능 중 몇 가지를 살펴보겠습니다.

### <a name="isolate-content-for-improved-readability"></a>가독성 향상을 위해 콘텐츠 격리

몰입형 리더는 가독성을 높이기 위해 콘텐츠를 격리합니다. 

  ![몰입형 리더를 통해 가독성 향상을 위해 콘텐츠 격리](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>일반적인 단어에 대한 그림 표시

일반적으로 사용되는 용어는 몰입형 리더에게 그림을 표시합니다.

  ![몰입형 리더가 있는 그림 사전](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>품사 강조

몰입형 리더를 사용하면 동사, 명사, 대명사 등을 강조 표시하여 학습자가 품사 및 문법의 일부를 이해하는 데 도움을 받을 수 있습니다.

  ![몰입형 리더를 사용하여 품사 표시](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>콘텐츠를 소리내어 읽기

음성 합성(또는 텍스트 음성 변환)은 몰입형 리더 서비스에 베이킹되어 리더가 텍스트를 소리내어 읽을 수 있도록 선택할 수 있습니다. 

  ![몰입형 리더로 텍스트를 소리내어 읽기](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>실시간으로 콘텐츠 번역

몰입형 리더는 실시간으로 텍스트를 여러 언어로 변환할 수 있습니다. 이는 새로운 언어를 배우는 리더의 이해력을 개선하는 데 유용합니다.

  ![몰입형 리더로 텍스트 번역](./media/translation.png)

### <a name="split-words-into-syllables"></a>단어를 음절로 분할

몰입형 리더를 사용하면 단어를 음절로 구분하여 가독성을 개선하거나 새 단어를 소리로 만들 수 있습니다.

  ![몰입형 리더로 단어를 음절로 분할](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>몰입형 리더의 작동 방식

몰입형 리더는 독립 실행형 웹 애플리케이션입니다. 몰입형 리더 클라이언트 라이브러리를 사용하여 호출하면 `iframe`의 기존 웹 애플리케이션 위에 표시됩니다. 웹 애플리케이션이 몰입형 리더 서비스를 호출할 때 리더에게 표시할 콘텐츠를 지정합니다. 몰입형 리더 클라이언트 라이브러리는 `iframe`의 생성 및 스타일 지정과 몰입형 리더 백 엔드 서비스와의 통신을 처리합니다. 몰입형 리더 서비스는 품사, 텍스트 음성 변환, 번역 등에 대한 콘텐츠를 처리합니다.

## <a name="get-started-with-immersive-reader"></a>몰입형 리더 시작

몰입형 리더 클라이언트 라이브러리는 C#, JavaScript, Java(Android), Kotlin(Android) 및 Swift(iOS)에서 사용할 수 있습니다. 다음을 사용하여 시작해 보세요.

* [빠른 시작: 몰입형 리더 클라이언트 라이브러리 사용](quickstarts/client-libraries.md)

## <a name="next-steps"></a>다음 단계

몰입형 판독기로 시작:

* [몰입형 리더 클라이언트 라이브러리 참조](./reference.md) 읽기
* [GitHub의 몰입형 리더 클라이언트 라이브러리](https://github.com/microsoft/immersive-reader-sdk) 살펴보기
