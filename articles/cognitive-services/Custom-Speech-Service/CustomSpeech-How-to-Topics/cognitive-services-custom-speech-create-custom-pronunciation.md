---
title: Azure에서 Custom Speech Service로 사용자 지정 발음 사용 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service를 통해 언어 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: 1e9de9755ef4baee7e2a0e6c093e2cf27cb4281f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339817"
---
# <a name="enable-custom-pronunciation"></a>사용자 지정 발음을 사용하도록 설정

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

사용자 지정 발음을 사용하면 사용자가 발음 유형과 단어 또는 용어의 표시를 결정할 수 있습니다. 제품 이름 또는 머리글자어와 같은 사용자 지정된 용어를 처리하는 데 유용합니다. 발음 파일(간단한 .txt 파일)만 있으면 됩니다.

방법은 다음과 같습니다. .txt 파일 하나에 여러 사용자 지정 발음 항목을 입력할 수 있습니다. 구조는 다음과 같습니다.

```
Display form <Tab> Spoken form <Newline>
```

*예제*:

| 표시 형식 | 발성 형식 |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>발성 형식에 대한 요구 사항
발음 형식은 소문자여야 하며, 가져오는 동안 강제 변경될 수 있습니다. 또한 데이터 가져오기 도구에 검사를 제공해야 합니다. 발성 형식 또는 표시 형식에서는 탭이 허용되지 않습니다. 단, 표시 형식에 몇 가지 금지된 문자가 있을 수 있습니다(예: ~ 및 ^).

각 .txt 파일에는 여러 항목이 있을 수 있습니다. 예를 들어 다음과 같은 스크린샷을 참조하세요.

![머리글자어 발음에 대한 여러 항목이 포함된 메모장의 스크린샷](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

발성 형식은 표시 형식의 음성 시퀀스입니다. 문자, 단어 또는 음절로 구성됩니다. 현재 발성 형식을 표현하는 데 도움이 되는 더 자세한 지침 또는 표준은 없습니다. 

## <a name="supported-pronunciation-characters"></a>지원되는 발음 문자
사용자 지정 발음은 현재 미국 영어(en-US) 및 독일어(de-de)에만 지원됩니다. 용어의 발성 형식을 표현(사용자 지정 발음 파일에서)하는 데 사용할 수 있는 문자 집합이 다음 표에 나와 있습니다. 

| 언어 | 문자 |
|---------- |----------|
| 미국 영어(en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| 독일어(de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

>[참고] 용어의 표시 형식(발음 파일에서)은 언어 적응 데이터 집합에서와 같은 방식으로 작성되어야 합니다.

## <a name="requirements-for-the-display-form"></a>표시 형식에 대한 요구 사항
표시 형식은 사용자 지정 단어, 용어, 약어 또는 기존 단어를 결합한 복합 단어일 수 있습니다. 또한 일반적인 단어에 대한 대체 발음을 입력할 수 있습니다. 

>[!NOTE]
일반적인 단어를 다시 작성하거나 발성 형식을 수정할 때는 이 기능을 사용하지 않는 것이 좋습니다. 일부 비정상적인 단어(예: 약어, 기술 단어 및 외국어)가 제대로 디코딩되지 않는지 확인하려면 디코더를 실행하는 것이 좋습니다. 제대로 디코딩되지 않는 경우 사용자 지정 발음 파일에 추가합니다. 언어 모델에서 항상 단어의 표시 형식만 사용해야 합니다. 

## <a name="requirements-for-the-file-size"></a>파일 크기에 대한 요구 사항
발음 항목을 포함하는 .txt 파일의 크기는 1MB로 제한됩니다. 일반적으로 이 파일을 통해 많은 양의 데이터를 업로드할 필요가 없습니다. 대부분의 사용자 지정 발음 파일은 크기가 수 KB에 불과합니다. 모든 로캘에 대한 .txt 파일의 인코딩은 UTF-8 BOM이어야 합니다. 영어 로캘의 경우 ANSI가 허용됩니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 어쿠스틱 모델](cognitive-services-custom-speech-create-acoustic-model.md)을 만들어 인식 정확도를 향상시킵니다.
* 앱에서 사용할 수 있는 [사용자 지정 음성-텍스트 엔드포인트를 만듭니다](cognitive-services-custom-speech-create-endpoint.md).
