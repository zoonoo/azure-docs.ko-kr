---
title: 멀티 턴 프롬프트를 사용하여 단계별 대화 추가
description: 이 자습서에서는 멀티 턴 프롬프트를 사용하여 단계별 대화를 수행하는 방법을 알아봅니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/29/2021
ms.openlocfilehash: 5c664dd2d08bb743ebe3e813808c559a30d901fc
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113235295"
---
# <a name="add-guided-conversations-with-multi-turn-prompts"></a>멀티 턴 프롬프트를 사용하여 단계별 대화 추가

 이 자습서에서는 [Surface 펜 FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)를 사용하여 기술 자료를 만듭니다.

이 예에서는 사용자가 Surface 펜 제품에 대한 추가 세부 정보, 특히 Surface 펜 문제를 해결하는 방법을 요청하지만 올바른 대답을 얻지 못하고 있다고 가정합니다. 따라서 추가 시나리오를 지원하고 사용자에게 멀티 턴 프롬프트를 사용하여 올바른 대답을 안내하는 프롬프트를 더 추가합니다.

## <a name="view-qnas-with-context"></a>컨텍스트를 사용하여 QnA 보기
[Surface 펜 FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)에 대한 기술 자료를 만드는 동안 원본 문서에서 멀티 턴 추출을 사용하도록 선택합니다. 자세한 내용은 [문서에서 멀티 턴 대화 만들기](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure)를 참조하세요. 여기에는 QnA 쌍과 연결된 멀티 턴 프롬프트가 나열되며, 이러한 프롬프트는 **보기 옵션** 아래의 **컨텍스트 표시** 를 사용하여 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> [ ![빨간색 상자로 둘러싸인 보기 옵션-컨텍스트 표시가 있는 스크린샷]( ../media/guided-conversations/show-context.png) ]( ../media/guided-conversations/show-context.png#lightbox)

이는 QnA 쌍에 연결된 모든 후속 프롬프트가 표시되는 컨텍스트 트리를 표시합니다. 

> [!div class="mx-imgBorder"]
> [ ![일반적인 질문 및 대답이 포함된 Surface 펜 FAQ 페이지의 스크린샷]( ../media/guided-conversations/source.png) ]( ../media/guided-conversations/source.png#lightbox)

## <a name="add-new-qna-pair-with-follow-up-prompts"></a>후속 프롬프트를 사용하여 새 QnA 쌍 추가

사용자가 Surface 펜으로 문제를 해결하는 데 도움이 되도록 다음과 같이 후속 프롬프트를 추가합니다.

1.  두 개의 후속 프롬프트를 사용하여 새 QnA 쌍을 추가합니다.
2.  후속 프롬프트를 새로 추가된 프롬프트 중 하나에 추가합니다.

**1단계**: 두 개의 후속 프롬프트, 즉 **호환성 검사** 및 **펜 설정 검사** 를 사용하여 새 QnA 쌍을 추가합니다. 편집기에서 **QnA 쌍 추가** 를 클릭하여 후속 프롬프트를 통해 새 QnA 쌍을 추가합니다.

> [!div class="mx-imgBorder"]
> [ !["QnA 쌍 추가"가 빨간색 상자로 강조 표시된 UI의 스크린샷]( ../media/guided-conversations/add-pair.png) ]( ../media/guided-conversations/add-pair.png#lightbox)

아래와 같이 QnA 쌍을 입력할 수 있는 **편집** 의 새 행이 만들어집니다.

> [!div class="mx-imgBorder"]
> [ !["후속 프롬프트 추가"가 빨간색 상자로 강조 표시된 UI의 스크린샷]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

그런 다음, **후속 프롬프트 추가** 를 선택하여 후속 프롬프트를 새로 만든 QnA 쌍에 추가합니다. 클릭하면 다음과 같이 프롬프트에 대한 세부 정보가 채워집니다.

> [!div class="mx-imgBorder"]
> [ !["후속 프롬프트 추가"가 빨간색 상자로 강조 표시된 UI의 스크린샷]( ../media/guided-conversations/follow-up.png) ]( ../media/guided-conversations/follow-up.png#lightbox)

프롬프트에 대한 "표시 텍스트"로 **호환성 검사** 를 제공하고 이를 QnA에 연결하려고 시도합니다. 프롬프트에 연결하는 데 사용할 수 있는 관련 QnA 쌍이 없으므로 "Surface 펜 호환성 검사"를 검색할 때 **새로 만들기** 를 클릭하여 새 QnA 쌍을 만듭니다. 변경 내용을 **저장** 하면 다음과 같은 화면이 표시되며, 여기서 후속 프롬프트에 대한 새 QnA 쌍을 아래와 같이 입력할 수 있습니다.

> [!div class="mx-imgBorder"]
> [ ![Surface 펜 호환성 검사에 대한 질문 및 대답의 스크린샷]( ../media/guided-conversations/check-compatibility.png) ]( ../media/guided-conversations/check-compatibility.png#lightbox)

마찬가지로 사용자가 Surface 펜 문제를 해결하고 QnA 쌍을 추가하는 데 도움이 되도록 또 다른 **펜 설정 검사** 프롬프트를 추가합니다.

> [!div class="mx-imgBorder"]
> [ ![Surface 펜 설정 검사에 대한 질문 및 대답의 스크린샷]( ../media/guided-conversations/check-pen-settings.png) ]( ../media/guided-conversations/check-pen-settings.png#lightbox)

**2단계**: 다른 후속 프롬프트를 새로 만든 프롬프트에 추가합니다. 이제 "펜 팁 바꾸기"를 이전에 만든 "펜 설정 검사" 프롬프트에 대한 후속 프롬프트로 추가합니다.

> [!div class="mx-imgBorder"]
> [ ![펜 팁 바꾸기와 관련된 정보 주위에 빨간색 상자가 있는 Surface 펜 설정 검사에 대한 질문 및 대답의 스크린샷]( ../media/guided-conversations/replace-pen-tips.png) ]( ../media/guided-conversations/replace-pen-tips.png#lightbox)

마지막으로 변경 내용을 저장하고, [테스트] 창에서 다음과 같은 프롬프트를 테스트합니다. 

> [!div class="mx-imgBorder"]
> [ ![QnAMaker 테스트 창의 스크린샷]( ../media/guided-conversations/test-pane.png) ]( ../media/guided-conversations/test-pane.png#lightbox)

**Surface 펜 관련 문제** 에 대한 사용자 쿼리의 경우 시스템에서 대답을 반환하고 새로 추가된 프롬프트를 사용자에게 표시합니다. 그런 다음, **펜 설정 검사** 프롬프트 중 하나를 선택하면 관련 대답이 **펜 팁 바꾸기** 프롬프트와 함께 사용자에게 반환됩니다. 이를 선택하면 추가 정보가 사용자에게 추가로 제공됩니다. 따라서 멀티 턴은 사용자를 원하는 대답으로 안내하는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [활성 학습을 사용하여 기술 자료 보강](active-learning.md)