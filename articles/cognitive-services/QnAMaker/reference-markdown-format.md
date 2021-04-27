---
title: Markdown 형식 - QnA Maker
description: 다음은 QnA Maker의 대답 텍스트에서 사용할 수 있는 Markdown 형식 목록입니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352272"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 응답 텍스트에서 지원되는 Markdown 형식

QnA Maker는 응답 텍스트를 Markdown으로 저장합니다. Markdown에는 여러 버전이 있습니다. 응답 텍스트가 반환되고 올바르게 표시되는지 확인하려면 이 참조를 사용합니다.

**[CommonMark](https://commonmark.org/help/tutorial/index.html)** 자습서를 사용하여 Markdown의 유효성을 검사합니다. 이 자습서에는 빠른 복사/붙여넣기 유효성 검사를 위한 **사용해보기** 기능이 있습니다.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>서식 있는 텍스트 편집 및 Markdown 사용 시기

응답의 [서식 있는 텍스트 편집](How-To/edit-knowledge-base.md#add-an-editorial-qna-set)을 사용하면 작성자가 서식 도구 모음을 사용하여 텍스트를 신속하게 선택하고 형식을 지정할 수 있습니다.

Markdown은 CI/CD 파이프라인의 일부로 가져오거나 [일괄 처리 테스트](./index.yml)를 위해 가져올 기술 자료를 만드는 데 콘텐츠를 자동으로 생성해야 하는 경우 더 유용한 도구입니다.

## <a name="supported-markdown-format"></a>지원되는 Markdown 형식

다음은 QnA Maker의 대답 텍스트에서 사용할 수 있는 Markdown 형식 목록입니다.

|목적|형식|예제 Markdown|렌더링<br>채팅 봇에 표시된 대로|
|--|--|--|--|
두 문장 사이의 새 줄.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![두 문장 사이의 새 줄의 형식 지정](./media/qnamaker-concepts-datasources/format-newline.png)|
|H1부터 h6까지의 헤더, `#`의 수는 어떤 헤더인지를 나타냅니다. 1개의 `#`은 h1입니다.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![Markdown 헤더의 서식](./media/qnamaker-concepts-datasources/format-headers.png)<br>![Markdown 헤더 H1부터 H5까지의 서식](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|기울임꼴 |`*text*`|`How do I create a bot with *QnA Maker*?`|![기울임꼴 서식](./media/qnamaker-concepts-datasources/format-italics.png)|
|강함(굵게)|`**text**`|`How do I create a bot with **QnA Maker**?`|![강조를 위한 굵은 글꼴 서식](./media/qnamaker-concepts-datasources/format-strong.png)|
|링크에 대한 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 형식(하이퍼링크)](./media/qnamaker-concepts-datasources/format-url.png)|
|\* 공용 이미지에 대한 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![공용 이미지 URL 형식 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|취소선|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![취소선 서식](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|굵게 및 기울임꼴|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![굵게 및 기울임꼴 서식](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|굵게 URL 링크|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![굵게 URL 서식](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|기울임꼴 URL 링크|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![기울임꼴 URL 서식](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|이스케이프 Markdown 기호|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![이스케이프 Markdown 기호 서식](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|정렬된 목록|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>앞의 예에서는 Markdown에 기본 제공되는 자동 번호 매기기를 사용합니다.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>앞의 예에서는 명시적 번호 매기기를 사용합니다.|![정렬된 목록 서식](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|정렬되지 않은 목록|`\n * item1 \n * item2`<br>또는<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![정렬되지 않은 목록 서식](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|중첩된 목록|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>정렬된 목록과 정렬되지 않은 목록을 함께 중첩할 수 있습니다. 탭(`\t`)은 자식 요소의 들여쓰기 수준을 나타냅니다.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![중첩된 정렬되지 않은 목록 서식](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![중첩된 정렬된 목록 서식](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker는 어떤 방식으로든 이미지를 처리하지 않습니다. 이미지를 렌더링하는 것은 클라이언트 애플리케이션의 역할입니다.

업데이트/대체 기술 자료 API를 사용하여 콘텐츠를 추가하려는 경우 콘텐츠/파일에 HTML 태그가 포함되어 있으면 태그 열기 및 닫기가 인코딩된 형식으로 변환되도록 함으로써 파일의 HTML을 유지할 수 있습니다.

| HTML 유지  | API 요청의 표현  | 표시(KB) |
|-----------|---------|-------------------------|
| 예 | \&lt;br\&gt; | &lt;br&gt; |
| 예 | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;header&lt;/h3&gt; |

또한 CR LF(\r\n)는 KB에서 \n으로 변환됩니다. LF(\n)는 그대로 유지됩니다. \t 또는 \n 등의 이스케이프 시퀀스를 이스케이프하려면 백슬래시를 사용할 수 있습니다(예: '\\\\r\\\\n' 및 '\\\\t').

## <a name="next-steps"></a>다음 단계

일괄 처리 테스트 [파일 형식](reference-tsv-format-batch-testing.md)을 검토합니다.