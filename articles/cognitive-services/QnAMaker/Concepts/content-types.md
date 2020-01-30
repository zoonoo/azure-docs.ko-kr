---
title: 콘텐츠 형식-QnA Maker
description: 콘텐츠 형식에는 PDF, DOC 및 TXT와 같은 많은 표준 구조적 문서가 포함 됩니다.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846359"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>기술 자료에 추가할 수 있는 문서 내용 유형
콘텐츠 형식에는 PDF, DOC 및 TXT와 같은 많은 표준 구조적 문서가 포함 됩니다.

## <a name="file-and-url-data-types"></a>파일 및 URL 데이터 형식

다음 표에는 QnA Maker에서 지원되는 콘텐츠 형식 및 파일 형식이 요약되어 있습니다.

|원본 유형|콘텐츠 형식| 예시|
|--|--|--|
|URL|FAQ<br> (플랫, 섹션 또는 토픽 홈페이지 포함)<br>지원 페이지 <br> (단일 페이지 방법 문서, 문제 해결 문서 등)|[일반 FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[하이퍼링크가 있는 FAQ](https://www.microsoft.com/en-us/software-download/faq),<br> [토픽 홈페이지가 있는 FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[지원 문서](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|FAQ,<br> 제품 설명서,<br> 브로슈어,<br> 페이퍼,<br> 전단 정책,<br> 지원 가이드,<br> 구조화된 QnA,<br> etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [샘플 백서 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br>[샘플 multi-turn](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|구조화된 QnA 파일<br> (RTF, HTML 지원 포함)|[샘플 QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|구조화된 QnA 파일|[샘플 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

데이터 원본에 대 한 인증이 필요한 경우 다음 방법을 고려 하 여 해당 콘텐츠를 QnA Maker으로 가져와야 합니다.

* 수동으로 파일 다운로드 및 QnA Maker로 가져오기
* 인증 된 [Sharepoint 위치의](../how-to/add-sharepoint-datasources.md) 파일 추가

## <a name="url-content"></a>URL 콘텐츠

QnA Maker에서 **URL** 을 통해 두 가지 형식의 문서를 가져올 수 있습니다.

* FAQ URL
* 지원 Url

각 형식은 필요한 형식을 나타냅니다.

## <a name="file-based-content"></a>파일 기반 콘텐츠

[QnA Maker 포털](https://www.qnamaker.ai)에서 공용 원본 또는 로컬 파일 시스템의 기술 자료에 파일을 추가할 수 있습니다.

## <a name="content-format-guidelines"></a>콘텐츠 형식 지침

QnA Maker는 markdown (`.md`)로 저장 된 QnA 집합으로 콘텐츠를 변환 하는 파일 및 URL 콘텐츠를 추가 합니다. 콘텐츠는 일부 구조화 된 형식으로 콘텐츠를 구성 하 고 잘 정의 된 섹션으로 표시 되는 기술 자료에 적합 합니다. 섹션을 하위 섹션 또는 하위 항목으로 구분할 수 있습니다. 추출은 계층 제목이 포함된 명확한 구조를 가진 콘텐츠에서 최적으로 작동합니다.

QnA Maker는 글꼴 크기, 글꼴 스타일, 번호 매기기, 색 등의 시각적 단서를 기준으로 콘텐츠에서 섹션과 하위 섹션 및 관계를 식별 합니다. 반 구조화 된 콘텐츠에는 설명서, Faq, 지침, 정책, 브로슈어, 안내물 및 기타 많은 파일 형식이 포함 됩니다.

## <a name="faq-urls"></a>FAQ URL

QnA Maker는 일반 FAQ 페이지, 링크가 포함된 FAQ 페이지, 토픽 홈페이지가 포함된 FAQ 페이지의 3가지 양식으로 FAQ 웹 페이지를 지원할 수 있습니다.


### <a name="plain-faq-pages"></a>일반 FAQ 페이지

가장 일반적인 형식의 FAQ 페이지로, 동일한 페이지에서 답변이 질문 바로 뒤에 표시됩니다.

아래는 일반 FAQ 페이지의 예입니다.

![기술 자료를 위한 일반 FAQ 페이지 예제](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>링크가 포함된 FAQ 페이지

이러한 종류의 FAQ 페이지에서는 질문이 함께 집계되어 동일한 페이지의 다른 섹션 또는 다른 페이지에 있는 답변에 연결됩니다.

아래는 동일한 페이지에 있는 섹션의 링크가 포함된 FAQ 페이지의 예입니다.

 ![기술 자료를 위한 섹션 링크 FAQ 페이지 예제](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>토픽 홈페이지가 포함된 FAQ 페이지

이러한 종류의 FAQ에는 토픽이 포함된 홈페이지가 있으며 각 토픽은 다른 페이지에 있는 관련 QnA에 대한 링크입니다. 여기서 QnA Maker는 연결된 모든 페이지를 크롤링하여 해당하는 질문과 답변을 추출합니다.

아래는 토픽 홈페이지에 다른 페이지의 FAQ 섹션에 대한 링크가 있는 FAQ 페이지의 예입니다.

 ![기술 자료를 위한 딥 링크 FAQ 페이지 예제](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>지원 URL

QnA Maker는 지정된 태스크를 수행하는 방법, 지정된 문제를 진단하고 해결하는 방법 및 지정된 프로세스에 대한 어떤 모범 사례가 있는지를 설명하는 웹 문서와 같은 반정형화된 지원 웹 페이지를 처리할 수 있습니다. 추출은 계층 제목이 포함된 명확한 구조를 가진 콘텐츠에서 최적으로 작동합니다.

> [!NOTE]
> 지원 문서에 대한 추출은 새 기능이며 초기 단계에 있습니다. 잘 구조화된 단순 페이지에서 최적으로 작동하며 복잡한 헤더/바닥글을 포함하지 않습니다.

![QnA Maker는 계층 제목과 함께 명확한 구조가 제시되는 반정형화된 웹 페이지에서 추출을 지원합니다.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC 파일

QnA Maker는 PDF 또는 DOC 파일의 반 구조화된 콘텐츠를 처리하고 QnA로 변환할 수 있습니다. 잘 추출할 수 있는 좋은 파일이란 콘텐츠가 구조화된 양식으로 정리되어 있고 잘 정의된 섹션에 표시되는 파일을 말합니다. 섹션을 하위 섹션 또는 하위 토픽으로 더 자세히 분류할 수 있습니다. 추출은 계층 머리글을 사용하여 구조를 명확하게 표시해 놓은 문서에서 가장 잘 작동합니다.

QnA Maker 글꼴 크기, 글꼴 스타일, 번호 매기기, 색 등의 시각적 단서를 기준으로 파일의 섹션과 하위 섹션 및 관계를 식별 합니다. 반 구조화 된 PDF 또는 DOC 파일은 설명서, Faq, 지침, 정책, 브로슈어, 안내물 및 기타 많은 파일 유형입니다. 아래는 이러한 파일 유형의 예입니다.

### <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

아래는 인덱스 페이지와 계층적 콘텐츠가 포함된 설명서의 예입니다.

 ![기술 자료를 위한 제품 설명서 예제](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.

### <a name="brochures-guidelines-papers-and-other-files"></a>브로슈어, 지침, 논문 및 기타 파일

그 외의 문서 유형도 구조와 레이아웃이 명확하다면 QA 쌍을 생성하도록 처리할 수 있습니다. 여기에는 브로슈어, 지침, 보고서, 백서, 과학적 용지, 정책, 책 등이 포함 됩니다. [여기](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)에서 예제를 참조 하세요.

아래는 인덱스 없는 반 구조화된 문서의 예입니다.

 ![Azure Blob Storage 반 구조화된 Doc](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>구조화된 QnA 문서

DOC 파일에서 구조화된 질문-답변의 형식은 줄마다 질문과 답변이 번갈아 반복되는 양식으로, 아래와 같이 한 줄에 질문이 하나 나오면 그 다음 줄에 답변이 나옵니다.

```text
Question1

Answer1

Question2

Answer2
```

아래는 구조화된 QnA Word 문서의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>구조화된 *TXT*, *TSV* 및 *XLS* 파일

또한 구조화된 *.txt*, *.tsv* 또는 *.xls* 파일 양식의 QnA를 QnA Maker에 업로드하여 기술 자료를 만들거나 보강할 수 있습니다.  이러한 자료는 일반 텍스트일 수도 있고, RTF 또는 HTML 콘텐츠를 가질 수도 있습니다.

| 질문  | 답변  | 메타 데이터 (1 개 키: 1 값) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

원본 파일의 추가 열은 무시됩니다.

### <a name="example-of-structured-excel-file"></a>구조적 Excel 파일의 예

아래는 HTML 콘텐츠가 포함된 구조화된 QnA *.xls* 파일의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 파일의 단일 답변에 대 한 대체 질문 예제

다음은 단일 답변에 대 한 몇 가지 대체 질문이 있는 구조화 *된 QnA 파일* 의 예입니다.

 ![Excel 파일의 단일 답변에 대 한 대체 질문 예제](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

파일을 가져온 후에는 다음과 같이 질문 및 답변 쌍이 기술 자료에 있습니다.

 ![기술 자료로 가져온 단일 답변에 대 한 대체 질문 스크린샷](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>서식 지정 고려 사항

파일이 나 URL을 가져온 후 QnA Maker는 콘텐츠를 [markdown 형식](https://en.wikipedia.org/wiki/Markdown)으로 변환 하 고 저장 합니다. 변환 프로세스는 텍스트에 `\n\n`와 같은 새 줄을 추가 합니다. Markdown 형식에 대 한 정보는 변환 된 콘텐츠를 이해 하 고 기술 자료 콘텐츠를 관리 하는 데 도움이 됩니다.

기술 자료에서 직접 콘텐츠를 추가 하거나 편집 하는 경우 **markdown 서식 지정** 을 사용 하 여 서식 있는 텍스트 콘텐츠를 만들거나 답변에 이미 있는 markdown 서식 콘텐츠를 변경 합니다. QnA Maker는 풍부한 텍스트 기능을 콘텐츠에 제공 하는 많은 markdown 형식을 지원 합니다. 그러나 채팅 봇과 같은 클라이언트 응용 프로그램은 동일한 markdown 형식 집합을 지원 하지 않을 수 있습니다. 클라이언트 응용 프로그램의 답변 표시를 테스트 하는 것이 중요 합니다.

[QnA Maker markdown 참조 설명서](../reference-markdown-format.md)에서 자세히 알아보세요.

## <a name="testing-your-markdown"></a>Markdown 테스트

**[CommonMark](https://commonmark.org/help/tutorial/index.html)** 자습서를 사용하여 Markdown의 유효성을 검사합니다. 이 자습서에는 빠른 복사/붙여넣기 유효성 검사를 위한 **사용해보기** 기능이 있습니다.

## <a name="next-steps"></a>다음 단계

* [QnA (질문 및 답변) 집합](question-answer-set.md) 을 디자인 하 고 관리 하는 방법 이해