---
title: 기술 자료 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 기술 자료는 질문/답변(QnA) 쌍, 각 QnA 쌍과 연결된 선택적 메타데이터의 집합으로 구성됩니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: cb3426a960a6644b3ae149f02055cdb083febca7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040764"
---
# <a name="knowledge-base"></a>기술 자료

QnA Maker 기술 자료는 질문/답변(QnA) 쌍, 각 QnA 쌍과 연결된 선택적 메타데이터의 집합으로 구성됩니다.

## <a name="key-knowledge-base-concepts"></a>주요 기술 자료 개념

* **질문** - 질문은 사용자 쿼리를 가장 잘 나타내는 텍스트를 포함합니다. 
* **답변** - 답변은 사용자 쿼리가 관련 질문과 일치할 경우 반환되는 응답입니다.  
* **메타데이터** - 메타데이터는 QnA 쌍과 연결된 태그이며 키-값 쌍으로 표현됩니다. 메타데이터는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.

숫자 QnA ID로 표현되는 단일 QnA에는 여러가지 변형된 질문(대체 질문)이 있으며, 모두 단일 답변으로 매핑됩니다. 또한 이러한 각 쌍에는 여러 메타데이터 필드가 연결될 수 있습니다.

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>기술 자료 콘텐츠 형식

기술 자료에 풍부한 콘텐츠를 수집하면 QnA Maker가 콘텐츠를 markdown으로 변환하려고 합니다. 대부분의 채팅 클라이언트에서 이해할 수 있는 markdown 형식을 이해하려면 [이](https://aka.ms/qnamaker-docs-markdown-support) 블로그를 읽어 보세요.

메타데이터 필드는 콜론으로 구분된 키-값 쌍으로 이루어져 있습니다 **(Product:Shredder)**. 키와 값은 모두 텍스트로만 구성되어야 합니다. 메타데이터 키에는 공백이 포함되어서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료의 개발 수명 주기](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>참고 항목

[QnA Maker 개요](../Overview/overview.md)
