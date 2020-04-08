---
title: 데이터 원본에서 가져오기 - QnA 메이커
description: QnA Maker 기술 자료는 QnA(질의응답) 세트와 각 QnA 쌍과 연관된 선택적 메타데이터로 구성됩니다.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804285"
---
# <a name="importing-from-data-sources"></a>데이터 원본에서 가져오기

기술 자료는 공개 URL 및 파일에 의해 가져온 질문 과 답변 세트로 구성됩니다.

## <a name="data-source-locations"></a>데이터 원본 위치

콘텐츠는 데이터 원본의 기술 자료로 가져온다. 데이터 원본 위치는 인증이 필요하지 않은 **공용 URL 또는 파일입니다.**

인증으로 보호되는 [SharePoint 파일은](../how-to/add-sharepoint-datasources.md)예외입니다. SharePoint 리소스는 웹 페이지가 아닌 파일이어야 합니다. URL이 웹 확장(예: )으로 끝나는 경우) ASPX, 그것은 쉐어 포인트에서 QnA 메이커로 가져 되지 않습니다.

## <a name="chit-chat-content"></a>치트 채팅 콘텐츠

Chit 채팅 QnA 콘텐츠 세트는 여러 언어와 대화 스타일로 완전한 콘텐츠 데이터 소스로 제공됩니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다. 이 콘텐츠 [집합을](../how-to/chit-chat-knowledge-base.md) 기술 자료에 추가하는 방법을 알아봅니다.

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 질문과 `.tsv` 대답이 포함된 구조화 된 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 세트를 그룹화하고 특정 데이터 원본에 특성을 설정하는 데 도움이 됩니다.

| 질문  | Answer  | 원본| 메타데이터(키 1개: 값 1개) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>가져오기를 통한 구조화 된 멀티 턴 형식

`.tsv` 파일 형식으로 다중 회전 대화를 만들 수 있습니다. 이 형식은 이전 채팅 로그를 분석하여 다중 회전 대화를 만들 수 있는 기능을 제공합니다(QnA `.tsv` Maker를 사용하지 않는 다른 프로세스와 함께), 자동화를 통해 파일을 생성합니다. 기존 기술 자료를 대체하기 위해 파일을 가져옵니다.

> [!div class="mx-imgBorder"]
> ![멀티 턴 질문의 3 수준의 개념 모델](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

다중 턴에 대한 `.tsv`열은 [다중 턴에 특정]은 **프롬프트입니다.** Excel에 `.tsv`표시된 예제는 다중 회전 자식을 정의하는 데 포함할 정보를 보여 준다.

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**표시순서는** 숫자이고 **displayText는** 마크다운을 포함하지 않아야 하는 텍스트입니다.

> [!div class="mx-imgBorder"]
> ![Excel에 표시된 대로 다중 턴 질문 예제](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>예제로 내보내기

`.tsv` 파일에서 QnA 쌍을 나타내는 방법을 잘 모르는 경우:
* [GitHub에서](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true) 이 다운로드 가능한 예제 사용
* 또는 QnA Maker 포털에서 집합을 만들고 저장한 다음 집합을 나타내는 방법에 대한 예제로 기술 기반을 내보냅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료의 개발 수명 주기](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>참조

QnA 메이커 [마크다운 참조를](../reference-markdown-format.md) 사용하여 답변을 포맷할 수 있습니다.

[QnA Maker 개요](../Overview/overview.md)

다음을 사용하여 기술 자료 작성 및 편집:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

다음과 같은 경우 답변을 생성합니다.
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
