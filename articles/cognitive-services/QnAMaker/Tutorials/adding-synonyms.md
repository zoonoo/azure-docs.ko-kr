---
title: 동의어를 사용하여 응답 품질 향상
description: 이 자습서에서는 동의어 및 대체 단어를 사용하여 응답을 개선하는 방법을 알아봅니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 9830eab526ad1cad4f7eb45d010958545ccec81b
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235332"
---
# <a name="improve-quality-of-response-with-synonyms"></a>동의어를 사용하여 응답 품질 향상

이 자습서에서는 동의어를 사용하여 응답의 품질을 향상시킬 수 있는 방법을 보여줍니다. 사용자가 대체 폼, 동의어 또는 단어 머리글자어를 사용하는 경우 쿼리에 대한 정확한 응답을 얻지 못한다고 가정합니다. 따라서 [변경 API](/rest/api/cognitiveservices-qnamaker/QnAMaker4.0/Alterations)를 사용해 키워드에 대한 동의어를 추가하여 응답 품질을 개선하기로 결정했습니다.

## <a name="add-synonyms-using-alterations-api"></a>변경 API를 사용하여 동의어 추가

결과를 향상시키기 위해 다음과 같은 단어와 변경 내용을 추가해 보겠습니다.

|Word | 변경|
|--------------|--------------------------------|
| 문제 해결 | `Troubleshoot`, `trouble-shoot`|
| 화이트보드   | `white-board`, `white board`   |
| bluetooth    | `blue-tooth`, `blue tooth`     |

```json
{
    "wordAlterations": [
        {
            "alterations": [
                "fix problems",
                "trouble shoot",
                "trouble-shoot",
                ]
        },
        {
            "alterations": [
                "whiteboard",
                "white-board",
                "white board"
            ]
        },
        {
            "alterations": [
                "bluetooth",
                "blue-tooth",
                "blue tooth"
            ]
        }
    ]
}

```

## <a name="response-after-adding-synonyms"></a>동의어 추가 후 응답

아래에 표시된 질문 및 답변 쌍 "Surface Pen으로 문제 해결"에 대해 동의어 "문제 해결"을 사용하여 생성된 쿼리에 대한 응답을 비교합니다.

> [!div class="mx-imgBorder"]
> [ ![빨간색으로 강조 표시된 Surface Pen 수정 문제를 포함하는 스크린샷]( ../media/adding-synonyms/fix-problems.png) ]( ../media/adding-synonyms/fix-problems.png#lightbox)

## <a name="response-before-addition-of-synonym"></a>동의어 추가 전 응답

> [!div class="mx-imgBorder"]
> [ ![71.82의 신뢰도 점수가 빨간색으로 강조 표시된 스크린샷]( ../media/adding-synonyms/confidence-score.png) ]( ../media/adding-synonyms/confidence-score.png#lightbox)

## <a name="response-after-addition-of-synonym"></a>동의어 추가 후 응답 

> [!div class="mx-imgBorder"]
> [ ![97의 신뢰도 점수가 빨간색으로 강조 표시된 스크린샷]( ../media/adding-synonyms/increase-score.png) ]( ../media/adding-synonyms/increase-score.png#lightbox)

여기에서 볼 수 있듯이 `trouble shoot`가 동의어로 추가되지 않은 경우 "표면 펜 문제를 해결하는 방법"에 대해 신뢰도가 낮은 응답을 받았습니다. 그러나 `trouble shoot`를 "문제 해결"에 동의어로 추가한 후에는 신뢰도 점수가 높은 쿼리에 대한 올바른 응답을 받게 됩니다. 이러한 단어 변경 내용이 추가되면 결과의 관련성이 개선되어 사용자 환경이 개선됩니다. 

> [!NOTE]
> 동의어는 대/소문자를 구분하지 않습니다. 중지 단어를 동의어로 추가하는 경우에도 동의어가 예상대로 작동하지 않을 수 있습니다. 중지 단어 목록은 [중지 단어 목록](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md)에서 확인할 수 있습니다.

예를 들어 정보 기술에 대한 약어 **IT** 를 추가하면 **IT** 는 중지 단어이고 쿼리가 처리될 때 필터링되기 때문에 시스템에서 정보 기술을 인식하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [여러 언어의 기술 자료 만들기](multiple-languages.md)