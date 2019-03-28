---
title: 발음 사용자 지정 - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service를 사용하여 발음을 사용자 지정하는 방법을 알아봅니다. 사용자 지정 발음을 사용하여 사용자가 발음 유형과 단어 또는 용어의 표시를 정의할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 발음 파일만 있으면 시작 가능 - 간단한 .txt 파일
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 139c5d47fe6ea82148e2d5e1cf2f5fcb72d4020e
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339400"
---
# <a name="enable-custom-pronunciation"></a>사용자 지정 발음을 사용하도록 설정

사용자 지정 발음을 사용 하 여 음성 폼 및 단어 또는 용어 (머리글자어)의 표시를 정의할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 발음 파일만 있으면 시작 가능 - 간단한 .txt 파일

방법은 다음과 같습니다. .txt 파일 하나에 여러 사용자 지정 발음 항목을 입력할 수 있습니다. 구조는 다음과 같습니다.

```
Display form <Tab> Spoken form <Newline>
```

다음 표에 여러 예제가 나와 있습니다.

| 표시 형식 | 발성 형식 |
|----------|-------|
| 3CPO | see three pea o |
| L8R | late are |
| CNTK | c n t k|

## <a name="requirements-for-the-spoken-form"></a>발성 형식에 대한 요구 사항

발음 형식은 소문자여야 하며, 가져오는 동안 강제 변경될 수 있습니다. 또한 데이터 가져오기 도구에 검사를 제공해야 합니다. 발성 형식 또는 표시 형식에서는 탭이 허용되지 않습니다. 단, 표시 형식에 몇 가지 금지된 문자가 있을 수 있습니다(예: ~ 및 ^).

다음 이미지처럼 각 .txt 파일은 여러 항목을 포함할 수 있습니다.

![머리글자어 발음의 예](media/stt/custom-speech-pronunciation-file.png)

발성 형식은 표시 형식의 음성 시퀀스입니다. 이 형식은 문자, 단어 또는 음절로 구성됩니다. 현재 발성 형식을 구성하는 데 도움이 되는 더 자세한 지침이나 표준은 없습니다.

## <a name="supported-pronunciation-characters"></a>지원되는 발음 문자

사용자 지정 발음은 현재 미국 영어(en-US) 및 독일어(de-de)에만 지원됩니다. 용어의 발성 형식을 표현(사용자 지정 발음 파일에서)하는 데 사용할 수 있는 문자 집합이 다음 표에 나와 있습니다.

| 언어 | 문자 |
|---------- |----------|
| 미국 영어(en-US) | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| 독일어(de-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> 용어의 표시 형식(발음 파일에서)은 언어 적응 데이터 세트에서와 같은 방식으로 작성되어야 합니다.

## <a name="requirements-for-the-display-form"></a>표시 형식에 대한 요구 사항

사용자 지정 단어, 머리글자어 또는 기존 단어를 결합 하는 복합어만 표시 될 수 있습니다.

>[!NOTE]
>일반적인 단어를 다시 구성하거나 발성 형식을 수정할 때는 이 기능을 사용하지 않는 것이 좋습니다. 일부 비정상적인 단어(예: 약어, 기술 단어 또는 외국어)가 제대로 디코딩되지 않는지 확인하려면 디코더를 실행하는 것이 좋습니다. 제대로 디코딩되지 않는 경우 사용자 지정 발음 파일에 추가합니다. 언어 모델에서 항상 단어의 표시 형식만 사용해야 합니다.

## <a name="requirements-for-the-file-size"></a>파일 크기에 대한 요구 사항
발음 항목을 포함하는 .txt 파일의 크기는 1MB(체험 계층 키의 경우 1KB)로 제한됩니다. 일반적으로 이 파일을 통해 많은 양의 데이터를 업로드할 필요가 없습니다. 대부분의 사용자 지정 발음 파일은 크기가 수 KB에 불과합니다. 모든 로캘에 대한 .txt 파일의 인코딩은 UTF-8 BOM이어야 합니다. 영어 로캘의 경우 ANSI가 허용됩니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 음향 모델](how-to-customize-acoustic-models.md)을 만들어 인식 정확도를 향상시킵니다.
* [사용자 지정 언어 모델](how-to-customize-language-model.md)을 만들어 인식 정확도를 향상시킵니다.
