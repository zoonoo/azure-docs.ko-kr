---
title: 문서 형식 지침 가져오기 - QnA 메이커
description: QnA 쌍을 가져오고 만드는 데 URL 유형이 어떻게 사용되는지 알아보세요.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 799e85e2200d3caa29c9b76bc57a62fc883d246d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804336"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>가져온 문서 및 URL에 대한 형식 지침

콘텐츠에 가장 적합한 결과를 얻으려면 이러한 서식 지정 지침을 검토합니다.

## <a name="formatting-considerations"></a>서식 지정 고려 사항

파일 또는 URL을 가져온 후 QnA Maker는 콘텐츠를 [마크다운 형식으로](https://en.wikipedia.org/wiki/Markdown)변환하고 저장합니다. 변환 프로세스는 와 같은 `\n\n`텍스트에 새 줄을 추가합니다. 마크다운 형식에 대한 지식을 사용하면 변환된 콘텐츠를 이해하고 기술 자료 콘텐츠를 관리할 수 있습니다.

기술 자료에 직접 콘텐츠를 추가하거나 편집하는 경우 **마크다운 서식을** 사용하여 풍부한 텍스트 콘텐츠를 만들거나 답변에 이미 있는 마크다운 형식 콘텐츠를 변경합니다. QnA Maker는 콘텐츠에 풍부한 텍스트 기능을 제공하기 위해 많은 마크다운 형식을 지원합니다. 그러나 채팅 봇과 같은 클라이언트 응용 프로그램은 동일한 마크다운 형식 집합을 지원하지 않을 수 있습니다. 클라이언트 응용 프로그램의 답변 표시를 테스트하는 것이 중요합니다.

[전체 콘텐츠 유형 및 예제](./Concepts/content-types.md#file-and-url-data-types)목록을 참조하십시오.

## <a name="basic-document-formatting"></a>기본 문서 서식 지정

QnA Maker는 다음과 같은 시각적 단서를 기반으로 파일의 섹션 및 하위 섹션 및 관계를 식별합니다.

* 글꼴 크기
* 글꼴 스타일
* 번호 매기기
* 색

## <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

아래는 인덱스 페이지와 계층적 콘텐츠가 포함된 설명서의 예입니다.

 ![기술 자료를 위한 제품 설명서 예제](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.

## <a name="brochures-guidelines-papers-and-other-files"></a>브로슈어, 지침, 논문 및 기타 파일

그 외의 문서 유형도 구조와 레이아웃이 명확하다면 QA 쌍을 생성하도록 처리할 수 있습니다. 여기에는 브로셔, 지침, 보고서, 백서, 과학 논문, 정책, 책 등이 포함됩니다. [여기에](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)예제를 참조하십시오.

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

| 질문  | Answer  | 메타데이터(키 1개: 값 1개) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

원본 파일의 추가 열은 무시됩니다.

### <a name="example-of-structured-excel-file"></a>구조화 된 Excel 파일의 예

아래는 HTML 콘텐츠가 포함된 구조화된 QnA *.xls* 파일의 예입니다.

 ![기술 자료를 위한 구조화된 QnA 문서 예제](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 파일에서 단일 답변에 대한 대체 질문 예제

다음은 구조화 된 QnA *.xls* 파일의 예이며, 단일 답변에 대한 몇 가지 대체 질문입니다.

 ![Excel 파일에서 단일 답변에 대한 대체 질문 예제](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

파일을 가져온 후 질문 및 답변 쌍은 아래와 같이 기술 자료에 있습니다.

 ![기술 자료로 가져온 단일 답변에 대한 대체 질문 스크린샷](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 데이터 원본 정보를 포함하는 구조화된 .tsv 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속하는 데 도움이 됩니다.

| 질문  | Answer  | 원본| 메타데이터(키 1개: 값 1개) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>다중 턴 문서 서식 지정

* 머리글과 하위 머리글을 사용하여 계층구조를 나타냅니다. 예를 들어 h1은 부모 QnA와 h2를 나타내기 위해 프롬프트로 취해야 하는 QnA를 나타내기 위해 할 수 있습니다. 작은 제목 크기를 사용하여 후속 계층을 나타냅니다. 문서의 구조를 암시하는 스타일, 색상 또는 기타 메커니즘을 사용하지 마십시오.
* 제목의 첫 번째 문자는 대문자로 만들어야 합니다.
* 물음표로 제목을 끝내지 `?`마십시오.

**샘플 문서**:<br>[표면 프로 (닥스)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[콘토소 혜택 (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[콘토소 혜택 (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>다음 단계

[전체 콘텐츠 유형 및 예제](./Concepts/content-types.md#file-and-url-data-types) 보기