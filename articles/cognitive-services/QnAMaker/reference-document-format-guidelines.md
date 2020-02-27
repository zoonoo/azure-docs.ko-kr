---
title: 문서 형식 가져오기 지침-QnA Maker
description: Url 형식을 사용 하 여 QnA 집합을 가져오고 만드는 방법을 이해 합니다.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651990"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>가져온 문서와 Url에 대 한 형식 지침

이러한 서식 지정 지침을 검토 하 여 콘텐츠에 대 한 최상의 결과를 얻을 수 있습니다.

## <a name="formatting-considerations"></a>서식 지정 고려 사항

파일이 나 URL을 가져온 후 QnA Maker는 콘텐츠를 [markdown 형식](https://en.wikipedia.org/wiki/Markdown)으로 변환 하 고 저장 합니다. 변환 프로세스는 텍스트에 `\n\n`와 같은 새 줄을 추가 합니다. Markdown 형식에 대 한 정보는 변환 된 콘텐츠를 이해 하 고 기술 자료 콘텐츠를 관리 하는 데 도움이 됩니다.

기술 자료에서 직접 콘텐츠를 추가 하거나 편집 하는 경우 **markdown 서식 지정** 을 사용 하 여 서식 있는 텍스트 콘텐츠를 만들거나 답변에 이미 있는 markdown 서식 콘텐츠를 변경 합니다. QnA Maker는 풍부한 텍스트 기능을 콘텐츠에 제공 하는 많은 markdown 형식을 지원 합니다. 그러나 채팅 봇과 같은 클라이언트 응용 프로그램은 동일한 markdown 형식 집합을 지원 하지 않을 수 있습니다. 클라이언트 응용 프로그램의 답변 표시를 테스트 하는 것이 중요 합니다.

## <a name="basic-document-formatting"></a>기본 문서 서식 지정

QnA Maker는 다음과 같은 시각적 단서를 기준으로 파일의 섹션과 하위 섹션 및 관계를 식별 합니다.

* 글꼴 크기
* 글꼴 스타일
* 매기기
* 색

|문서 예제|
|--|
||



## <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

아래는 인덱스 페이지와 계층적 콘텐츠가 포함된 설명서의 예입니다.

 ![기술 자료를 위한 제품 설명서 예제](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.

## <a name="brochures-guidelines-papers-and-other-files"></a>브로슈어, 지침, 논문 및 기타 파일

그 외의 문서 유형도 구조와 레이아웃이 명확하다면 QA 쌍을 생성하도록 처리할 수 있습니다. 여기에는 브로슈어, 지침, 보고서, 백서, 과학적 용지, 정책, 책 등이 포함 됩니다. [여기](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)에서 예제를 참조 하세요.

아래는 인덱스 없는 반 구조화된 문서의 예입니다.

 ![Azure Blob Storage 반 구조화된 Doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>구조화된 QnA 문서

DOC 파일에서 구조화된 질문-답변의 형식은 줄마다 질문과 답변이 번갈아 반복되는 양식으로, 아래와 같이 한 줄에 질문이 하나 나오면 그 다음 줄에 답변이 나옵니다.

```text
Question1

Answer1

Question2

Answer2
```

아래는 구조화된 QnA Word 문서의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>구조화된 *TXT*, *TSV* 및 *XLS* 파일

또한 구조화된 *.txt*, *.tsv* 또는 *.xls* 파일 양식의 QnA를 QnA Maker에 업로드하여 기술 자료를 만들거나 보강할 수 있습니다.  이러한 자료는 일반 텍스트일 수도 있고, RTF 또는 HTML 콘텐츠를 가질 수도 있습니다.

| 질문  | Answer  | 메타 데이터 (1 개 키: 1 값) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

원본 파일의 추가 열은 무시됩니다.

### <a name="example-of-structured-excel-file"></a>구조적 Excel 파일의 예

아래는 HTML 콘텐츠가 포함된 구조화된 QnA *.xls* 파일의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 파일의 단일 답변에 대 한 대체 질문 예제

다음은 단일 답변에 대 한 몇 가지 대체 질문이 있는 구조화 *된 QnA 파일* 의 예입니다.

 ![Excel 파일의 단일 답변에 대 한 대체 질문 예제](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

파일을 가져온 후에는 다음과 같이 질문 및 답변 쌍이 기술 자료에 있습니다.

 ![기술 자료로 가져온 단일 답변에 대 한 대체 질문 스크린샷](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 데이터 원본 정보를 포함하는 구조화된 .tsv 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속하는 데 도움이 됩니다.

| 질문  | Answer  | 원본| 메타 데이터 (1 개 키: 1 값) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>다중 문서 서식 지정

* 머리글 및 하위 머리글을 사용 하 여 계층을 나타냅니다. 예를 들어, 부모 QnA 및 h2를 표시 하 여 프롬프트로 사용 해야 하는 QnA를 나타낼 수 있습니다. 작은 머리글 크기를 사용 하 여 후속 계층을 나타냅니다. 문서에서 구조를 의미 하는 스타일, 색 또는 기타 메커니즘을 사용 하지 않습니다. QnA Maker는 다중 전환 프롬프트를 추출 하지 않습니다.
* 제목의 첫 번째 문자는 대문자 여야 합니다.
* 물음표, `?`제목을 끝에 표시 하지 않습니다.


|문서 예제|
|--|
||