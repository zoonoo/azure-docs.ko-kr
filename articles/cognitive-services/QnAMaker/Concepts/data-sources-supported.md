---
title: 지원되는 데이터 원본 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: 지원되는 데이터 원본
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376366"
---
# <a name="data-sources"></a>데이터 원본 
QnA Maker는 일반적인 반구조화된 콘텐츠 형식(예: FAQ 및 제품 설명서)에서 질문-답변 쌍을 자동으로 추출할 수 있습니다. 구조화된 파일의 콘텐츠를 기술 자료에 추가할 수도 있습니다.

## <a name="plain-faq-pages"></a>일반 FAQ 페이지
가장 일반적인 형식의 FAQ 페이지로, 동일한 페이지에서 답변이 질문 바로 뒤에 표시됩니다. 

![일반 FAQ 페이지](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>섹션 링크가 있는 FAQ 페이지 
이 FAQ 페이지 형식에서는 질문이 함께 집계되며 동일한 페이지의 다른 섹션에 있는 답변에 연결됩니다.

 ![섹션 링크 FAQ 페이지](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>다른 페이지에 대한 링크가 있는 FAQ 페이지 
이 FAQ 페이지 형식은 링크가 다른 페이지로 리디렉션된다는 점을 제외하고 섹션에 연결된 FAQ 페이지와 유사합니다. QnA Maker는 연결된 페이지를 모두 크롤링하여 해당 답변을 추출합니다.

 ![딥 링크 FAQ 페이지](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.


## <a name="structured-data-format-through-file-upload"></a>파일 업로드를 통한 구조화된 데이터 형식

기술 자료를 만드는 동안 서식이 지정된 열을 포함하는 구조화된 파일(예: .tsv, .xlsx)을 QnA Maker로 업로드할 수도 있습니다. 기술 자료의 **설정** 탭에서 파일을 업로드할 수도 있습니다.

| 질문  | 응답  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | `Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 |      `Key:Value`           |
원본 파일의 추가 열은 무시됩니다.

## <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식
기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 데이터 원본 정보를 포함하는 구조화된 .tsv 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속하는 데 도움이 됩니다.

| 질문  | 응답  | 원본| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1|`Key1:Value1\|Key2:Value2` |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

## <a name="editorial"></a>Editorial
기술 자료를 채울 기존 콘텐츠가 없는 경우 QnA Maker 기술 자료에 편집자로 추가할 수도 있습니다. [여기](../How-To/edit-knowledge-base.md)에서 기술 자료를 업데이트하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 서비스 설정](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>참고 항목 

[QnA Maker 개요](../Overview/overview.md)