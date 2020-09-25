---
title: 데이터 원본에서 가져오기-QnA Maker
description: 데이터 원본에서 질문과 대답 쌍을 가져오는 방법에 대해 알아봅니다. QnA Maker.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: c66f81ccabd77e49ba1c492db25ad9e9edd34e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334636"
---
# <a name="importing-from-data-sources"></a>데이터 원본에서 가져오기

기술 자료는 공용 Url 및 파일에서 가져온 질문과 대답 쌍으로 구성 됩니다.

## <a name="data-source-locations"></a>데이터 원본 위치

콘텐츠는 데이터 원본에서 기술 자료로 가져옵니다. 데이터 원본 위치는 인증이 필요 하지 않은 **공용 url 또는 파일**입니다.

인증으로 보안이 설정 된 [SharePoint 파일](../how-to/add-sharepoint-datasources.md)은 예외입니다. SharePoint 리소스는 웹 페이지가 아닌 파일 이어야 합니다. URL은 .ASPX와 같은 웹 확장명으로 끝나는 경우 SharePoint에서 QnA Maker로 가져오지 않습니다.

## <a name="chit-chat-content"></a>Chit 채팅 콘텐츠

Chit chat QnA 콘텐츠 집합은 여러 언어 및 대화형 스타일로 전체 콘텐츠 데이터 원본으로 제공 됩니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다. 이 콘텐츠 집합을 기술 자료에 [추가 하는 방법](../how-to/chit-chat-knowledge-base.md) 에 대해 알아봅니다.

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 `.tsv` 질문 및 대답이 포함 된 구조적 파일이 필요 합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속하는 데 도움이 됩니다.

| 질문  | Answer  | 원본| 메타 데이터 (1 개 키: 1 값) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>가져오기를 통해 구조적 다중 전환 형식

파일 형식으로 다중 전환 대화를 만들 수 있습니다 `.tsv` . 형식은 이전 채팅 로그 (QnA Maker를 사용 하지 않는 다른 프로세스와 함께)를 분석 한 다음 자동화를 통해 파일을 만드는 기능을 제공 합니다 `.tsv` . 파일을 가져와 기존 기술 자료를 바꿉니다.

> [!div class="mx-imgBorder"]
> ![3 수준의 다중 전환 질문에 대 한 개념적 모델](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

다중 전환에 대 한 열에는 다중 전환에 대 한 `.tsv` **메시지가 표시**됩니다. `.tsv`Excel에 표시 된 예제는 다중 턴 자식을 정의 하기 위해 포함할 정보를 표시 합니다.

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** 은 숫자이 고 **인 displaytext** 는 markdown를 포함 하지 않아야 하는 텍스트입니다.

> [!div class="mx-imgBorder"]
> ![Excel에 표시 된 다중 전환 질문 예제](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>예제로 내보내기

파일에서 QnA 쌍을 표시 하는 방법을 잘 모르겠으면 `.tsv` 다음을 수행 합니다.
* [GitHub에서이 다운로드 가능한 예제](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true) 사용
* 또는 쌍을 나타내는 방법에 대 한 예제를 보려면 QnA Maker 포털에서 쌍을 만든 후 기술 자료를 내보냅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료의 개발 수명 주기](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>참고 항목

QnA Maker [Markdown 참조](../reference-markdown-format.md) 를 사용 하 여 답변을 쉽게 지정할 수 있습니다.

[QnA Maker 개요](../Overview/overview.md)

다음을 사용 하 여 기술 자료 만들기 및 편집:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

다음을 사용 하 여 답변 생성:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
