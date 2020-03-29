---
title: 콘텐츠 유형 - QnA 메이커
description: 콘텐츠 유형에는 PDF, DOC 및 TXT와 같은 많은 표준 구조화 문서가 포함됩니다.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650200"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>기술 자료에 추가할 수 있는 문서의 콘텐츠 유형
콘텐츠 유형에는 PDF, DOC 및 TXT와 같은 많은 표준 구조화 문서가 포함됩니다.

## <a name="file-and-url-data-types"></a>파일 및 URL 데이터 형식

다음 표에는 QnA Maker에서 지원되는 콘텐츠 형식 및 파일 형식이 요약되어 있습니다.

|원본 유형|콘텐츠 형식| 예|
|--|--|--|
|URL|FAQ<br> (플랫, 섹션 또는 토픽 홈페이지 포함)<br>지원 페이지 <br> (단일 페이지 방법 문서, 문제 해결 문서 등)|[일반 FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[하이퍼링크가 있는 FAQ](https://www.microsoft.com/en-us/software-download/faq),<br> [토픽 홈페이지가 있는 FAQ](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[지원 문서](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|FAQ,<br> 제품 설명서,<br> 브로슈어,<br> 페이퍼,<br> 전단 정책,<br> 지원 가이드,<br> 구조화된 QnA,<br> etc.|**멀티 턴 제외**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [샘플 백서.pdf,](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**멀티 턴**:<br>[표면 프로 (닥스)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[콘토소 혜택 (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[콘토소 혜택 (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* 엑셀|구조화된 QnA 파일<br> (RTF, HTML 지원 포함)|[샘플 QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|* TXT / TSV|구조화된 QnA 파일|[샘플 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

데이터 원본에 대한 인증이 필요한 경우 해당 콘텐츠를 QnA Maker로 변환하려면 다음 방법을 고려하십시오.

* 수동으로 파일을 다운로드하고 QnA 메이커로 가져오기
* 인증된 [공유점 위치에서](../how-to/add-sharepoint-datasources.md) 파일 추가

## <a name="url-content"></a>URL 콘텐츠

QnA Maker의 **URL을** 통해 두 가지 유형의 문서를 가져올 수 있습니다.

* FAQ URL
* 지원 URL

각 형식은 예상되는 형식을 나타냅니다.

## <a name="file-based-content"></a>파일 기반 콘텐츠

[QnA Maker 포털에서](https://www.qnamaker.ai)공용 소스 또는 로컬 파일 시스템의 기술 자료에 파일을 추가할 수 있습니다.

## <a name="content-format-guidelines"></a>콘텐츠 형식 가이드라인

다른 파일에 대한 [형식 지침에](../reference-document-format-guidelines.md) 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[QnA(질문 및 답변) 집합에](question-answer-set.md)저장되는 정보를 이해합니다.