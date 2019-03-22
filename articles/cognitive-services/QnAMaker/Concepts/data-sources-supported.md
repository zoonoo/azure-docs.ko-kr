---
title: 지원되는 데이터 원본 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 웹 페이지, PDF 파일 또는 MS Word doc 파일로 저장된 FAQ, 제품 설명서, 지침, 지원 문서, 정책 등의 반 구조화된 콘텐츠에서 질문-대답 쌍을 자동으로 추출합니다. 구조화된 QnA 콘텐츠 파일의 콘텐츠를 기술 자료에 추가할 수도 있습니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.openlocfilehash: 0e9e8456bb493ad4591c0f2a22d28bdf342f09e1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433113"
---
# <a name="data-sources-for-qna-maker-content"></a>QnA Maker 콘텐츠의 데이터 원본

QnA Maker는 웹 페이지, PDF 파일 또는 MS Word doc 파일로 저장된 FAQ, 제품 설명서, 지침, 지원 문서 및 정책 등의 반 구조화된 콘텐츠에서 질문-대답 쌍을 자동으로 추출합니다. 구조화된 QnA 콘텐츠 파일의 콘텐츠를 기술 자료에 추가할 수도 있습니다. 

다음 표에는 QnA Maker에서 지원되는 콘텐츠 형식 및 파일 형식이 요약되어 있습니다.

|원본 유형|콘텐츠 형식| 예|
|--|--|--|
|URL|FAQ<br> (플랫, 섹션 또는 토픽 홈페이지 포함)<br>지원 페이지 <br> (단일 페이지 방법 문서, 문제 해결 문서 등)|[일반 FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[하이퍼링크가 있는 FAQ](https://www.microsoft.com/software-download/faq),<br> [토픽 홈페이지가 있는 FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[지원 문서](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|FAQ,<br> 제품 설명서,<br> 브로슈어,<br> 페이퍼,<br> 전단 정책,<br> 지원 가이드,<br> 구조화된 QnA,<br> etc.|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Sample white paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|구조화된 QnA 파일<br> (RTF, HTML 지원 포함)|[샘플 QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|구조화된 QnA 파일|[샘플 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>데이터 원본 위치

모든 데이터 원본에 대해 공용 URL만 유효합니다. 인증을 요구하는 데이터 원본을 제출하지 마십시오. 인증된 사이트에서 파일을 다운로드한 다음, 파일 업로드 옵션을 사용하여 질문과 답변을 추출할 수 있습니다.

## <a name="faq-urls"></a>FAQ URL

QnA Maker는 3가지 형식으로 FAQ 웹 페이지를 지원할 수 있습니다. 일반 FAQ 페이지, 링크를 사용한 FAQ 페이지, 항목 홈 페이지를 사용한 FAQ 페이지.

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

QnA Maker는 글꼴 크기, 글꼴 스타일, 번호 매기기, 색상 등의 시각적 단서를 기반으로 파일에서 섹션 및 하위 섹션과 관계를 식별합니다. 반 구조화된 PDF 또는 DOC 파일은 설명서, FAQ, 지침, 정책, 브로슈어, 전단, 그 외 여러 종류의 파일입니다. 아래는 이러한 파일 유형의 예입니다.

### <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

아래는 인덱스 페이지와 계층적 콘텐츠가 포함된 설명서의 예입니다.

 ![기술 자료를 위한 제품 설명서 예제](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.

### <a name="brochures-guidelines-papers-and-other-files"></a>브로슈어, 지침, 논문 및 기타 파일

그 외의 문서 유형도 구조와 레이아웃이 명확하다면 QA 쌍을 생성하도록 처리할 수 있습니다. 내용은 다음과 같습니다. 브로슈어, 지침, 보고서, 백서, 과학 논문, 정책, 서적 등. [여기](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)에서 예제를 참조하세요.

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

| 질문  | 응답  | 메타 데이터 (1 키: 1 값) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

원본 파일의 추가 열은 무시됩니다.

아래는 HTML 콘텐츠가 포함된 구조화된 QnA *.xls* 파일의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 데이터 원본 정보를 포함하는 구조화된 .tsv 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속시키는 데 도움이 됩니다.

| 질문  | 응답  | 원본| 메타 데이터 (1 키: 1 값) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>편집자로서 기술 자료에 추가

기술 자료를 채울 기존 콘텐츠가 없는 경우 편집자로서 QnA Maker 기술 자료에 QnA를 추가할 수도 있습니다. [여기](../How-To/edit-knowledge-base.md)에서 기술 자료를 업데이트하는 방법을 알아봅니다.

## <a name="formatting-considerations"></a>서식 지정 고려 사항

파일 또는 URL을 가져온 후 Markdown으로 변환되며 해당 형식으로 저장됩니다. 변환 프로세스가 파일 및 URL의 링크를 올바르게 변환하지 않는 경우 **편집** 페이지에서 질문 및 답변을 편집해야 합니다. 

|형식|목적|
|--|--|
|`\n\n`| 새 줄|
|`\n*`|순서가 지정된 목록의 글머리 기호|

## <a name="editing-your-knowledge-base-locally"></a>로컬로 기술 자료 편집

기술 자료를 만든 후에는 로컬 파일을 통해 내보냈다가 다시 가져오는 것보다는 [QnA Maker 포털](https://qnamaker.ai)에서 기술 자료 텍스트를 편집하는 것이 좋습니다. 그러나 로컬로 기술 자료를 편집해야 하는 경우가 있을 수 있습니다. 

**설정** 페이지에서 기술 자료를 내보낸 후 Microsoft Excel을 사용하여 기술 자료를 편집합니다. 다른 애플리케이션을 사용하여 내보낸 TSV 파일을 편집하도록 선택할 경우 애플리케이션이 완전히 TSV 호환이 아니기 때문에 구문 오류가 발생할 수 있습니다. Microsoft Excel의 TSV 파일의 경우 일반적으로 서식 지정 오류가 발생하지 않습니다. 

편집을 완료했으면 **설정** 페이지를 통해 TSV 파일을 다시 가져옵니다. 그러면 현재 기술 자료가 가져온 기술 자료로 완전히 바뀝니다. 

## <a name="testing-your-markdown"></a>Markdown 테스트

**[CommonMark](https://commonmark.org/help/tutorial/index.html)** 자습서를 사용하여 Markdown의 유효성을 검사합니다. 이 자습서에는 빠른 복사/붙여넣기 유효성 검사를 위한 **사용해보기** 기능이 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 서비스 설정](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>참고 항목 

[QnA Maker 개요](../Overview/overview.md)
