---
title: 마크다운 형식 - QnA 메이커
description: 다음은 QnA Maker의 답변 텍스트에서 사용할 수 있는 마크다운 형식 목록입니다.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045400"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA 메이커 응답 텍스트에서 지원되는 마크다운 형식

QnA Maker는 응답 텍스트를 마크다운으로 저장합니다. 마크 다운의 많은 맛이 있다. 응답 텍스트가 반환되고 올바르게 표시되도록 하려면 이 참조를 사용합니다.

**[CommonMark](https://commonmark.org/help/tutorial/index.html)** 자습서를 사용하여 Markdown의 유효성을 검사합니다. 이 자습서에는 빠른 복사/붙여넣기 유효성 검사를 위한 **사용해보기** 기능이 있습니다.

## <a name="supported-markdown-format"></a>지원되는 마크다운 형식

다음은 QnA Maker의 답변 텍스트에서 사용할 수 있는 마크다운 형식 목록입니다.

|목적|형식|예제 Markdown|렌더링<br>채팅 봇에 표시된 대로|
|--|--|--|--|
2 문장 사이의 새로운 줄.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![두 문장 사이에 새 줄 형식 지정](./media/qnamaker-concepts-datasources/format-newline.png)|
|h1에서 h6까지의 헤더는 `#` 헤더 수를 나타냅니다. 1은 `#` h1입니다.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![마크다운 헤더가 있는 형식](./media/qnamaker-concepts-datasources/format-headers.png)<br>![마크다운 헤더 H1에서 H5까지 의 형식](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|기울임꼴 |`*text*`|`How do I create a bot with *QnA Maker*?`|![기울임꼴로 형식 지정](./media/qnamaker-concepts-datasources/format-italics.png)|
|강한 (굵게)|`**text**`|`How do I create a bot with **QnA Maker**?`|![대담한 에 대한 강력한 마킹 형식](./media/qnamaker-concepts-datasources/format-strong.png)|
|링크의 URL|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL(하이퍼링크)의 형식](./media/qnamaker-concepts-datasources/format-url.png)|
|* 공공 이미지에 대한 URL|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![공용 이미지 URL의 형식 ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|취소선|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![스트라이크 스루형식](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|대담하고 기울임꼴|`***text***`|`How can I create a ***QnA Maker*** bot?`|![굵게 및 기울임꼴의 형식](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|링크에 대한 굵은 URL|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![굵게 표시된 URL의 형식](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|링크에 대한 기울임꼴 URL|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![기울임꼴 URL의 형식](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|마크다운 기호 이스케이프|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![기울임꼴 URL의 형식](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|정렬된 목록|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>위의 예제에서는 마크다운에 내장된 자동 번호 매기기(자동 번호 매기기)를 사용합니다.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>앞의 예제에서는 명시적 번호 매기기 사용입니다.|![정렬된 목록의 형식](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|정렬되지 않은 목록|`\n * item1 \n * item2`<br>또는<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![정렬되지 않은 목록의 형식](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|중첩된 목록|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>정렬된 목록과 정렬되지 않은 목록을 함께 중첩할 수 있습니다. 탭은 `\t`자식 요소의 들여쓰기 수준을 나타냅니다.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![중첩되지 않은 목록의 형식](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![중첩 된 정렬 된 목록에 대 한 형식](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA 메이커는 어떤 방식으로 이미지를 처리하지 않습니다. 이미지를 렌더링하는 것은 클라이언트 응용 프로그램의 역할입니다.

업데이트/기술 자료 API를 사용하여 콘텐츠를 추가하고 콘텐츠/파일에 HTML 태그가 포함되어 있는 경우 태그의 열기 및 닫기가 인코딩된 형식으로 변환되도록 하여 파일의 HTML을 유지할 수 있습니다.

| HTML 보존  | API 요청의 표현  | KB의 표현 |
|-----------|---------|-------------------------|
| yes | \&lt;br\&gt; | &lt;Br&gt; |
| yes | \&lt;h3\&gt;헤더\&lt;/h3\&gt; | &lt;h3&gt;&lt;헤더 /h3&gt; |

또한 CR LF(\r\n)는 KB에서 \n으로 변환됩니다. LF(\n)는 있는 것처럼 유지됩니다. \t 또는 \n과 같은 이스케이프 시퀀스를 이스케이프시퀀스로 이스케이프하려면 백슬래시(예: 'r\\\\\\\\n' 및 't')를\\\\사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

일괄 처리 테스트 [파일 형식을 검토합니다.](reference-tsv-format-batch-testing.md)
