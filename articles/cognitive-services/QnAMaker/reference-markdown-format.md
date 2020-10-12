---
title: Markdown 형식-QnA Maker
description: 다음은 QnA Maker의 대답 텍스트에서 사용할 수 있는 markdown 형식 목록입니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f5f640f8895cd6defe31a68b790ce841c4679a6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777354"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker 대답 텍스트에서 지원 되는 Markdown 형식

QnA Maker 답변 텍스트를 markdown으로 저장 합니다. Markdown에는 여러 가지 종류가 있습니다. 대답 텍스트가 반환 되 고 올바르게 표시 되는지 확인 하려면이 참조를 사용 합니다.

**[CommonMark](https://commonmark.org/help/tutorial/index.html)** 자습서를 사용하여 Markdown의 유효성을 검사합니다. 이 자습서에는 빠른 복사/붙여넣기 유효성 검사를 위한 **사용해보기** 기능이 있습니다.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>서식 있는 텍스트 편집 및 markdown를 사용 하는 경우

서식 [있는 텍스트](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) 를 수정 하면 서식 도구 모음을 사용 하 여 텍스트를 빠르게 선택 하 고 서식을 지정할 수 있습니다.

Markdown는 CI/CD 파이프라인의 일부로 또는 [일괄 테스트](Quickstarts/batch-testing.md)를 위해 가져올 기술 자료를 만들기 위해 콘텐츠를 자동 생성 해야 하는 경우에 더 나은 도구입니다.

## <a name="supported-markdown-format"></a>지원 되는 markdown 형식

다음은 QnA Maker의 대답 텍스트에서 사용할 수 있는 markdown 형식 목록입니다.

|목적|형식|예제 Markdown|렌더링<br>채팅 봇에 표시 된 대로|
|--|--|--|--|
두 문장 사이에 있는 새 줄입니다.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![두 문장 사이에 새 줄 서식 지정](./media/qnamaker-concepts-datasources/format-newline.png)|
|H1부터 h6 까지의 헤더,의 수는 `#` 헤더를 나타냅니다. 1 `#` 은 h1입니다.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![markdown 헤더를 사용 하 여 형식 지정](./media/qnamaker-concepts-datasources/format-headers.png)<br>![markdown 헤더 H1을 H5로 서식 지정](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|기울임꼴 |`*text*`|`How do I create a bot with *QnA Maker*?`|![기울임꼴로 서식 지정](./media/qnamaker-concepts-datasources/format-italics.png)|
|강함 (굵게)|`**text**`|`How do I create a bot with **QnA Maker**?`|![굵은 글꼴로 표시 된 서식](./media/qnamaker-concepts-datasources/format-strong.png)|
|링크 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL 형식 (하이퍼링크)](./media/qnamaker-concepts-datasources/format-url.png)|
|* 공용 이미지에 대 한 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![공용 이미지 URL의 형식 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|취소선|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![취소선 서식](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|굵게 및 기울임꼴|`***text***`|`How can I create a ***QnA Maker*** bot?`|![굵게 및 기울임꼴 서식 지정](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|링크의 굵은 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![굵게 표시 되는 URL의 형식](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|링크에 대 한 기울임꼴 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![기울임꼴 URL 형식](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|이스케이프 markdown 기호|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![이스케이프 markdown 기호에 대 한 형식입니다.](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|정렬된 목록|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>앞의 예제에서는 markdown에 기본 제공 되는 자동 번호 매기기를 사용 합니다.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>앞의 예제에서는 명시적 번호 매기기를 사용 합니다.|![정렬 된 목록의 형식](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|정렬되지 않은 목록|`\n * item1 \n * item2`<br>또는<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![순서가 지정 되지 않은 목록의 서식](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|중첩된 목록|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>정렬 된 목록과 순서가 지정 되지 않은 목록을 함께 중첩할 수 있습니다. 탭은 `\t` 자식 요소의 들여쓰기 수준을 나타냅니다.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![순서가 지정 되지 않은 중첩 된 목록의 형식](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![중첩 된 정렬 된 목록의 형식](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker는 어떤 방식으로든 이미지를 처리 하지 않습니다. 이미지를 렌더링 하는 클라이언트 응용 프로그램의 역할입니다.

업데이트/대체 기술 자료 Api를 사용 하 여 콘텐츠를 추가 하 고 콘텐츠/파일에 html 태그가 포함 된 경우 태그를 열고 닫는 것이 인코딩된 형식으로 변환 되도록 하 여 파일에 HTML을 유지할 수 있습니다.

| HTML 유지  | API 요청의 표현  | 표시 (KB) |
|-----------|---------|-------------------------|
| 예 | \&l t; br \& gt; | &lt;br&gt; |
| 예 | \&l t; h3 \& gt; 헤더 \& l t;/h 3 \& gt; | &lt;h3 &gt; 헤더 &lt; /h 3&gt; |

또한 CR LF (\r\n)는 KB의 \n로 변환 됩니다. LF (\n)는 그대로 유지 됩니다. \T, \n 등의 이스케이프 시퀀스를 이스케이프 하려면 백슬래시를 사용할 수 있습니다 (예: ' \\ \\ r \\ \\ n ' 및 ' \\ \\ t ').

## <a name="next-steps"></a>다음 단계

일괄 처리 테스트 [파일 형식을](reference-tsv-format-batch-testing.md)검토 합니다.
